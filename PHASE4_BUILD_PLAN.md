# Phase 4 Build Plan: Validation Layer + Data Source Registry

**Created:** 2026-02-22
**Purpose:** Complete handoff document for a fresh context window. Contains every decision, schema, and correction needed to execute the build without re-deriving anything.
**Branch:** `main`
**Prerequisite:** Register for a DOL API key at `https://dataportal.dol.gov/registration` and set `export DOL_API_KEY=<your_key>` before running Step 3d.

---

## What We're Building

A **data source registry** that maps signal claim types to validated API endpoints, and a **Step 3d** in the 1A skill that reads the registry, fires queries, and upgrades `[H]` markers to `[V1]` with real counts and timestamps.

### Architecture Summary

```
Brief (Step 3b)                    Registry                        APIs
┌─────────────┐                 ┌──────────────┐              ┌─────────────┐
│ Signal 1    │                 │ gov-dol-whd  │              │ DOL WHD API │
│  claim_type:│──── match ────▶│  validates:  │──── curl ──▶│  /v4/get/   │
│  enforce... │                 │  enforce...  │              │  WHD/...    │
│  struct_pms │──── inject ──▶│  query_tmpl  │              └─────────────┘
│  [H] volume │                 └──────────────┘
│             │◀── update ──── count + timestamp ──── [V1] 47 actions
└─────────────┘
```

**Step 3d is:**
- Optional (brief works without it, signals just keep `[H]` markers)
- Re-runnable (can validate a brief days or weeks after generation)
- A dumb loop (reads registry, fires curl, counts results — no LLM reasoning needed for the query execution itself)

---

## Decisions Already Locked

1. **Step 3d is a separate step, not integrated into 3b.** 3b validates plausibility ("is this signal real?"). 3d validates operability ("does the detection query return results at useful volume?"). Different questions, different steps.

2. **Flat markdown file with JSON execution specs.** No database. Claude Code reads `contracts/data-source-registry.md` natively. Each entry has human-readable metadata + machine-readable JSON `execution_spec` block.

3. **`claim_type` field added to Detection Spec.** This is how Step 3d routes signals to registry entries. Uses a standardized vocabulary defined in the registry.

4. **`structured_params` JSON block added to Detection Spec.** Sits alongside the human-readable `Query / Filter` string. Provides the structured variables Step 3d needs to inject into API query templates without LLM parsing.

5. **`maps_to` field bridges Detection Spec params to API params.** Each registry entry's `query_template.params` has a `maps_to` field pointing to the corresponding `structured_params` key.

6. **REST-first, MCP-ready.** Each registry entry maps 1:1 to a future MCP tool definition. Migration path is documented in the registry header.

7. **Candidate Sources section auto-populates.** When Step 3d's web search fallback finds a promising source, it appends a candidate entry for human review and promotion.

---

## DOL API Investigation Results (2026-02-22)

The registry v0.1 draft had several incorrect assumptions. A live API investigation against `apiprod.dol.gov` plus the [official DOL API User Guide](https://www.dataportal.dol.gov/pdf/dol-api-user-guide.pdf) revealed these corrections:

### Corrections to Apply

| Field | Draft v0.1 | Corrected |
|-------|-----------|-----------|
| URL pattern | `base_url/datasets/WHD_whisard?state=UT` | `base_url/get/WHD/enforcement/json?X-API-KEY=...&filter_object=...` |
| `base_url` | `https://apiprod.dol.gov/v4` | `https://apiprod.dol.gov/v4` (same, but endpoint pattern differs) |
| `endpoint` | `/datasets/WHD_whisard` | `/get/WHD/enforcement/json` |
| Auth method | Header `X-API-Key` | **Query parameter** `X-API-KEY` (uppercase, in URL) |
| Filter syntax | Direct query params (`state=UT&naics_code=236`) | `filter_object` JSON param: `{"field":"st_cd","operator":"eq","value":"UT"}` |
| Multiple filters | N/A | Wrap in `{"and":[...]}` array |
| `count_path` | `"total"` | **`null`** — no total field. Response IS the array. Count the array length. |
| `records_path` | `"results"` | **`"data"`** — response is `{"data": [...]}`, records nested under `data` key (confirmed live 2026-02-22) |
| Pagination max | 100 | **10,000** records (or 5MB, whichever first) |
| Column: city | `city_nm` | `cty_nm` |
| Column: NAICS | `naics_code` | `naic_cd` |
| Column: penalties | `cmp_assd_cnt` | `cmp_assd` |
| Column: back wages | `bw_amt` | `bw_atp_amt` |
| Filter operators | N/A | `eq`, `neq`, `gt`, `lt`, `in`, `not_in`, `like` |

### Corrected Curl Example

```bash
# URL-encoded filter_object for: st_cd = "UT" AND naic_cd LIKE "236%"
curl -s "https://apiprod.dol.gov/v4/get/WHD/enforcement/json?\
X-API-KEY=$DOL_API_KEY&\
limit=10000&\
filter_object=$(python3 -c 'import urllib.parse,json; print(urllib.parse.quote(json.dumps({"and":[{"field":"st_cd","operator":"eq","value":"UT"},{"field":"naic_cd","operator":"like","value":"236%"}]})))')"
```

### What Remains Unvalidated (needs real API key)

- Actual response JSON shape (docs + code snippets confirm root-level array, but not tested live)
- Whether `filter_object` on `st_cd` and `naic_cd` works as expected for this dataset
- Exact record counts for Utah construction
- Rate limits (documented in API user guide but not tested)

### Action Item

Register for DOL API key at `https://dataportal.dol.gov/registration` and set as environment variable before executing Step 3d. Then run the corrected curl to validate the response shape and update the registry entry's `response_parsing` if needed.

---

## Schema Changes to Apply

### 1. `contracts/data-source-registry.md` — New File

Copy from `/Users/lucas/Downloads/data-source-registry.md` and apply these changes:

**Schema-level additions:**
- Add `key_location` field to auth block: `"key_location": "header" | "query_param"` — indicates where the API key goes
- Document in schema reference that `"count_path": null` means "count the response array length"
- Document that `"records_path": "root"` means "response itself is the array (not nested)"

**DOL entry corrections (all from the investigation above):**
- `endpoint`: `/get/WHD/enforcement/json`
- `auth.type`: `"api_key"`
- `auth.key_env_var`: `"DOL_API_KEY"`
- `auth.header`: `null`
- `auth.key_location`: `"query_param"`
- `auth.key_param_name`: `"X-API-KEY"`
- All `query_template.params` become `filter_object` entries instead of direct params. The registry should store them as structured filter definitions that Step 3d assembles into the `filter_object` JSON.
- `response_parsing.count_path`: `null`
- `response_parsing.records_path`: `"root"`
- `response_parsing.pagination.max_per_request`: `10000`
- `key_fields`: `["trade_nm", "street_addr_1_txt", "cty_nm", "st_cd", "naic_cd", "naics_code_description", "bw_atp_amt", "cmp_assd"]`
- Update the example curl command to use the corrected URL pattern and filter_object syntax

**TheirStack entry:** Keep as-is (POST body with JSON params is correct for their API). Mark `validation_status: "untested"` since we haven't tested with a live key.

**Census entry:** Keep as-is. Mark `validation_status` accurately.

### 2. `contracts/detection-spec.md` — Add Fields

Add two new fields to the Detection Spec format:

| Field | Type | Description |
|-------|------|-------------|
| `Claim Type` | enum | From the standardized vocabulary in `contracts/data-source-registry.md`. Enables Step 3d to route this signal to the right API source. Examples: `job_posting_volume`, `enforcement_action_volume`, `company_count_by_size` |
| `Structured Params` | JSON block | Machine-readable parameters that Step 3d injects into API query templates. Keys match the `maps_to` fields in registry entries. Example: `{"location": ["Utah"], "industry": "236", "date_range": "2025-02-22"}` |

Existing fields unchanged. The human-readable `Query / Filter` field stays for operator use.

### 3. 1A Brief Template (`skills/vendor-intelligence-brief/references/PROMPT_TEMPLATE.md`) — Update Detection Spec Table

Add two rows to the Detection Spec table in Section 5 (Individual Signal Hypotheses):

```markdown
**Detection Spec:**

| Field | Spec |
|-------|------|
| **Data Source** | [specific platform, database, or feed — name the actual product] |
| **Query / Filter** | [exact search query or filter criteria, copy-pasteable into the named platform] |
| **Claim Type** | [from registry vocabulary: job_posting_volume / enforcement_action_volume / company_count_by_size / insurance_rate_data / business_registration_volume / employment_stats / workers_comp_data / funding_events / company_headcount — or new type if none fit] |
| **Structured Params** | `{"location": [...], "industry": "...", "company_size_min": ..., "company_size_max": ..., "date_range": "...", "query_titles": [...]}` — include only the params relevant to this signal's claim type |
| **Refresh Cadence** | [Daily / Weekly / Biweekly / Monthly / Event-triggered] |
| **Monitoring Cost** | [$X/mo tool + Y hrs/mo operator time. **Mark `[H]` if pricing is estimated or unverified.**] |
| **Automation Feasibility** | [Full / Partial / Manual] — [one sentence on tooling needed] |
```

### 4. 1A SKILL.md (`skills/vendor-intelligence-brief/SKILL.md`) — Two Changes

**Update Step 3b** — Add instructions to populate the new fields:

After the existing "Then populate the Detection Spec for every surviving signal" section, add:

```
**Then classify the claim type and output structured params:**
- Claim Type: Choose from the standardized vocabulary in `contracts/data-source-registry.md` (Claim Types table). If none fit, define a new claim type and note it — the registry will be updated.
- Structured Params: Output a JSON block with the key variables needed for API validation. Include only params relevant to this signal's claim type. Common params:
  - `location`: array of state names or codes (e.g., `["Utah", "Idaho"]`)
  - `industry`: NAICS code prefix (e.g., `"236"` for construction)
  - `company_size_min` / `company_size_max`: employee count range
  - `query_titles`: array of job title keywords for job posting signals
  - `date_range`: ISO 8601 start date for time-bounded queries
```

**Add Step 3d** — New section after Step 3c (Quality Gates), before Step 4 (Deliver):

```markdown
### Step 3d: Empirical Validation & Re-Scoring (Optional / Re-runnable)

This step grounds the theoretical brief in hard data. It runs the queries 3b wrote, counts results, timestamps them, and upgrades `[H]` to `[V1]`.

**Prerequisites:** At least one API key configured (check `contracts/data-source-registry.md` for required env vars). If no keys are available, skip this step — signals keep their `[H]` markers.

**Execution loop:**

1. **Parse:** Read the generated `brief.md`. Extract every Detection Spec from Section 5.
2. **Route:** Read `contracts/data-source-registry.md` and match each signal's `Claim Type` to registry entries via the `validates.claim_types` field.
3. **Execute:** For each matching source where `agent_ready: true`:
   - Read the signal's `Structured Params` JSON
   - Map each param to the API's query template using the `maps_to` field
   - Build the API request (handle auth based on `key_location`: header vs query param)
   - Execute via `curl` in the terminal
   - Parse the response using `response_parsing` rules (if `count_path` is null, count the response array length)
4. **Update & Upgrade:**
   - Replace the `[H]` marker on the volume estimate with `[V1]`
   - Append the exact count and ISO 8601 timestamp: `[V1] {count} {description}. Source: {source_name}, queried {timestamp}.`
   - If the API returned sample records, include 2-3 examples (using `key_fields`) as evidence
5. **Re-score:** If the verified volume shifts the Volume dimension score by >1 point, recalculate the Composite Score.
6. **Enforce Threshold:** If the new composite drops below 2.5, immediately move the signal to Section 8 ("Signals killed by scoring") with the updated score and reason.
7. **Fallback:** If no registry entry matches a signal's claim type, or if execution fails:
   - Fall back to web search validation (existing 3b behavior)
   - If web search finds a promising structured source, append it to the Candidate Sources section of the registry for future promotion
   - Signal keeps its `[H]` marker

**After the loop:** Report a summary — how many signals were validated, how many kept `[H]`, any signals killed by re-scoring, any new candidate sources discovered.
```

**Update Step 3c (Quality Gates)** — Add:

```
- [ ] Every signal has a `Claim Type` from the registry vocabulary (or a new type noted for registry update)
- [ ] Every signal has `Structured Params` with the relevant variables for its claim type
- [ ] If Step 3d was run: validated signals show `[V1]` with count, source, and timestamp
- [ ] If Step 3d was run: any signal re-scored below 2.5 has been moved to Section 8
```

### 5. `CANONICAL.md` — Add Registry Entry

Add to the Contracts section:

```markdown
| Data Source Registry | v1.0 | Machine-readable catalog of validated API sources for signal validation (Step 3d) — Active | `contracts/data-source-registry.md` |
```

Update detection-spec version if appropriate.

---

## Build Sequence

Execute in this order. Each step depends on the previous one.

| Step | File | Action | Why This Order |
|------|------|--------|---------------|
| 1 | `contracts/data-source-registry.md` | Create from Downloads draft + apply all DOL corrections from investigation above + schema additions (key_location, count_path null docs) | Registry must exist before skill files can reference it |
| 2 | `contracts/detection-spec.md` | Add `claim_type` and `structured_params` field definitions | Contract defines the interface before templates implement it |
| 3 | `skills/vendor-intelligence-brief/references/PROMPT_TEMPLATE.md` | Add `Claim Type` and `Structured Params` rows to Detection Spec table in Section 5 | Template implements the contract |
| 4 | `skills/vendor-intelligence-brief/SKILL.md` | Update Step 3b (populate new fields) + Add Step 3d (validation loop) + Update Step 3c (quality gates) | Skill file orchestrates everything |
| 5 | `CANONICAL.md` | Add registry entry + update detection-spec version | Manifest reflects reality |

---

## Verification Checklist (Run After Build)

1. Read `contracts/data-source-registry.md` end-to-end — confirm DOL entry has corrected endpoint, auth, filter syntax, column names, response parsing
2. Read `contracts/detection-spec.md` — confirm `claim_type` and `structured_params` fields are defined
3. Read the 1A brief template — confirm Detection Spec table has the two new rows
4. Read the 1A SKILL.md — confirm Step 3b mentions claim type + structured params, Step 3d exists with the full loop, Step 3c has the new quality gates
5. Read `CANONICAL.md` — confirm registry is listed
6. Grep for `WHD_whisard` across the repo — should return zero matches (old dataset ID fully replaced)
7. Grep for `data-source-registry` in SKILL.md — should appear in Step 3d
8. If DOL API key is available: run the corrected curl command and verify response shape matches registry's `response_parsing`

---

## Post-Build: Register for API Key

After the build is complete, register for a DOL API key:
1. Go to `https://dataportal.dol.gov/registration`
2. Complete the questionnaire
3. Get API key from `https://dataportal.dol.gov/api-keys`
4. Set: `export DOL_API_KEY=<your_key>` in your shell profile
5. Run the corrected curl from the DOL registry entry to validate the response shape
6. If response shape differs from registry, update `response_parsing` accordingly

---

## Files Referenced

| File | Current State | Action |
|------|--------------|--------|
| `/Users/lucas/Downloads/data-source-registry.md` | v0.1 draft with DOL + TheirStack + Census + candidates | Source for Step 1 (apply corrections) |
| `contracts/detection-spec.md` | v1.0 — no claim_type or structured_params | Update in Step 2 |
| `skills/vendor-intelligence-brief/references/PROMPT_TEMPLATE.md` | Current 1A template — Detection Spec has 5 rows | Update in Step 3 (add 2 rows) |
| `skills/vendor-intelligence-brief/SKILL.md` | Steps 3b/3c exist, no Step 3d | Update in Step 4 |
| `CANONICAL.md` | No registry entry | Update in Step 5 |
| `contracts/data-source-registry.md` | Does not exist yet | Create in Step 1 |
