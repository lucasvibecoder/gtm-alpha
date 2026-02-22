---
name: gtm-alpha
description: >
  Generate a Strategic Intelligence Brief for any B2B company domain. Use this skill whenever the user
  wants to research a company for outbound GTM, build signal-based plays, understand a vendor's buyer
  landscape, or create an intelligence brief for sales targeting. Also trigger when the user says
  "run GTM Alpha", "research [domain]", "build a brief for [company]", "signal stack for [domain]",
  or any variation of strategic company research for go-to-market purposes. Even if the user just
  pastes a domain and says "go" — this is probably the skill they want.
---

# GTM Alpha: Strategic Intelligence Brief

## What This Skill Does

Takes a target company domain as input and produces a deep Strategic Intelligence Brief that maps:
- What the company actually sells and who buys it
- The buyer personas with situational qualifiers (not just titles)
- Signal Stacks — combinations of observable evidence that prove a prospect is in pain
- Individual validated signals with real-world examples, timing windows, and decay rates
- Scored signals (5-dimension weighted composite) with cut threshold — signals below 2.5 are killed
- Detection specs for every surviving signal — where to monitor, what query to run, refresh cadence, cost
- Competitive gaps

The core question the brief answers: **What observable combinations of evidence indicate a prospect is experiencing the exact pain this vendor solves — before the prospect has started actively shopping?**

**Audience:** The brief is an internal operator document — not shared with clients. System notation, `[H]` markers, and operator-level detail are appropriate here. See `CLAUDE.md` for the full audience model.

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Target domain** (required) — e.g., `ramp.com`, `gong.io`, `clay.com`
2. **Output format** (optional, default: markdown) — markdown, docx, or PDF

If the user already provided a domain in their message, skip the question and proceed.

### Step 2: Research Phase (Be Aggressive)

This is a research-heavy skill. Use **15-20+ web searches** across these dimensions. Do not skim. The quality of the brief depends entirely on research depth.

**Research sequence:**
1. Company website, product pages, pricing (2-3 searches)
2. Case studies, ROI claims, customer testimonials (2-3 searches)
3. G2 reviews, Gartner mentions, Reddit threads, customer complaints (2-3 searches)
4. Competitor landscape — identify 2-3 closest competitors (2-3 searches)
5. Recent news: funding, leadership, product launches, partnerships (2-3 searches)
6. Job postings — these reveal roadmap and internal priorities (1-2 searches)
7. Industry/macro trends creating urgency for this category (1-2 searches)
8. Signal validation — search for real-world examples of each proposed signal (3-5 searches)

Do not move to output until you have real examples for signals. If you cannot find an example for a signal, mark it as "theoretical — no example found" and demote its conviction level.

### Step 3: Generate Brief (Draft)

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the output structure.

Follow the template exactly — every section, every table, every validation check. The template is the product.

Generate the full brief as a draft, including initial signal hypotheses with your best estimates for validation, volume, and timing.

---

### Step 3b: Signal Validation + Scoring + Detection Spec Loop

After drafting the brief, run a dedicated validation pass on each individual signal (Section 5). This is what separates a useful brief from speculation. This step now also populates the Signal Score and Detection Spec for every signal.

**Determine which search tool is available (in priority order):**

1. **Perplexity MCP** — If available, use it. Perplexity returns synthesized answers with citations, which is ideal for signal validation. Use `sonar-pro` model for deeper research when available.
2. **Perplexity API via curl** — If the MCP isn't connecting but the user has a Perplexity API key (check environment variable `PERPLEXITY_API_KEY`), call the API directly:
   ```bash
   curl -s https://api.perplexity.ai/chat/completions \
     -H "Authorization: Bearer $PERPLEXITY_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"model":"sonar-pro","messages":[{"role":"user","content":"YOUR QUERY"}]}'
   ```
3. **Built-in web search** — If no Perplexity access, use Claude's native web search. Run 1-2 targeted searches per signal.
4. **No search available** — Generate the brief but mark ALL signals as "⚠️ Unvalidated — no search tool available. Treat all volume estimates and examples as hypothetical until manually verified."

**For each signal, run a targeted validation query that asks for:**
- A specific, named real-world example (company name, date, location)
- Concrete volume data (how many postings/announcements/filings exist right now)
- Source where this signal is observable (which platform, database, or publication)

**Example validation queries:**
- Signal: "Estimator job posting open 60+ days" → "landscape estimator job postings open 60+ days Indeed ZipRecruiter 2025 2026. Find specific company names, locations, salary ranges, and how many are currently active."
- Signal: "PE acquisition of landscape contractor" → "private equity acquisition commercial landscape contractor 2025 2026. Find specific deal announcements with PE firm name, target company, date, and deal size."
- Signal: "Residential firm wins first commercial project" → "residential landscape company first commercial project win announcement 2025. Find specific company names and project details."

**After validation, update each signal:**
- Replace placeholder examples with real ones (or mark as "theoretical — no example found")
- Update volume estimates with actual data
- Refine the signal definition if validation reveals a better version (e.g., "any estimator posting" may be stronger than "posting open 60+ days")
- **Mark unverified claims with `[H]`.** Any volume estimate, cost figure, or detection claim that wasn't confirmed by the validation search gets an inline `[H]` marker. If the search returned real data, the marker is removed. If the search found nothing or the number is extrapolated, `[H]` stays.

**Then score each signal (see `contracts/signal-scorecard.md` for full spec):**
- Score each of the 5 dimensions (Volume, Detectability, Specificity, Timing Precision, Actionability) on a 1–5 scale
- Calculate the weighted composite: (Vol×0.20) + (Det×0.25) + (Spec×0.25) + (Tim×0.15) + (Act×0.15)
- Kill signals scoring below 2.5 composite — move them to Section 8 (Research Confidence Assessment) under "Signals killed by scoring" with their score and the reason for the lowest dimension
- Better to have 6 high-scoring signals than 10 with half below threshold

**Then populate the Detection Spec for every surviving signal (see `contracts/detection-spec.md` for full spec):**
- Data Source: name the specific platform (not "job boards" — say "Indeed API + LinkedIn Recruiter")
- Query / Filter: write a copy-pasteable search query for that platform
- Refresh Cadence: how often to check (Daily / Weekly / Biweekly / Monthly / Event-triggered)
- Monitoring Cost: tool subscription cost + operator hours per month
- Automation Feasibility: Full / Partial / Manual with one sentence on tooling needed

**Then classify for API validation (see `contracts/data-source-registry.md`):**
- Claim Type: Choose from the standardized vocabulary in the registry's Claim Types table. If none fit, define a new claim type and note it for registry update.
- Structured Params: Output a JSON block with the variables needed for API validation. Include only params relevant to this signal's claim type. Common params:
  - `location`: state code or array of state names, depending on the target source
  - `industry`: NAICS code prefix (e.g., `"236"` for construction)
  - `company_size_min` / `company_size_max`: employee count range
  - `query_titles`: array of job title keywords for job posting signals
  - `date_range`: ISO 8601 start date for time-bounded queries
  - `state_fips`: FIPS state code for Census queries (e.g., `"49"` for Utah)
  - `employee_size_class`: Census size class code (e.g., `"232"` for 20-49 employees)

**Then assess Operational Feasibility for every surviving signal (immediately after its Detection Spec):**
- Detection Source: name the specific platform, API, or scraping method in one sentence
- Extraction Tier: classify as `structured` (API/feed with clean fields), `fragmented` (data exists but spread across sources), `buried` (requires scraping + NLP/AI extraction), or `nonexistent` (no public digital trail)
- Automation Path: describe in one sentence how to automate detection (e.g., "Clay workflow polling Indeed RSS + aging filter")
- Feasibility: classify as `high` (can build in a day with existing tools), `medium` (requires custom scripting or multi-tool pipeline), `low` (possible but expensive/brittle), or `manual_only` (human judgment required, no automation path)
- Each field is ONE SENTENCE MAX — this is a quick operational judgment call, not a research section

---

### Step 3c: Quality Gates

Before finalizing the brief, verify:
- Every signal has a recent real-world example cited, OR is explicitly marked as theoretical
- Every signal includes volume estimate and GTM motion implication
- Every signal includes decay rate and late-stage angle
- **Every signal has all 5 scoring dimensions populated with a composite score calculated**
- **No signal in the final brief scores below 2.5 composite** — killed signals are listed in Section 8 only
- **Every surviving signal has a complete Detection Spec** (all 5 fields populated, no "TBD" values)
- **Detection Spec queries are copy-pasteable** — an operator can run them on the named platform without interpretation
- **Every surviving signal has a complete Operational Feasibility block** (all 4 fields populated — Detection Source, Extraction Tier, Automation Path, Feasibility)
- **Section 8 includes Operational Feasibility Summary** with signal count, market observability classification, strongest automation paths, and manual-only signals listed
- Signal Stacks map to specific pain points with clear "current state → desired future" logic
- At least 2 adjacent/upstream signals included for early-warning detection
- Buyer personas include situational qualifiers, not just job titles
- Competitive analysis identifies specific gaps the target can exploit
- Research confidence assessment is honest about gaps, includes killed signals with scores
- Validation loop results are incorporated — no signal still has placeholder data if search was available
- **Every signal has a `Claim Type` from the registry vocabulary** (or a new type noted for registry update)
- **Every signal has `Structured Params`** with the relevant variables for its claim type
- If Step 3d was run: validated signals show `[V1]` with count, source, and timestamp
- If Step 3d was run: any signal re-scored below 2.5 has been moved to Section 8
- **Every volume estimate, cost figure, and detection claim is either sourced or marked `[H]`** — no unverified numbers presented as facts

### Step 3d: Empirical Validation (Optional / Re-runnable)

This step grounds the brief in hard data. It reads the Detection Specs from Section 5, matches them to API sources in `contracts/data-source-registry.md`, executes the queries, and upgrades `[H]` markers to `[V1]`.

**Prerequisites:** At least one API key configured (check `contracts/data-source-registry.md` for required env vars). If no keys are available, skip this step — signals keep their `[H]` markers.

**Execution loop — for each signal with a Claim Type and Structured Params:**

1. **Route:** Match the signal's `Claim Type` to registry entries via `validates.claim_types`. Skip if no match or `agent_ready: false`.

2. **Substitute:** Read the registry entry's template (`filter_template`, `body_template`, or `param_template`). Replace each `{{placeholder}}` with the corresponding value from the signal's `Structured Params` (using the `placeholders.maps_to` mapping). Drop any key whose placeholder value is null or absent.

3. **Execute:** Build and run the curl request:
   - **POST + body_template:** `curl -s -X POST "base_url/endpoint" -H "header: Bearer $key_env_var" -H "Content-Type: application/json" -d 'substituted_body'`
   - **GET + filter_template:** JSON-stringify the substituted filter, URL-encode it, then: `curl -s "base_url/endpoint?filter_param=encoded_filter&fixed_params&key_param_name=$key_env_var"`
   - **GET + param_template:** `curl -s "base_url/endpoint?substituted_params&fixed_params&key_param_name=$key_env_var"`

4. **Parse:** Read the response per `response_parsing` rules:
   - `records_path: "root"` → the response body IS the JSON array (not nested under a key)
   - `records_path: "data"` (or any string) → extract `response["data"]`
   - `count_path: null` → count the array length
   - `count_path: "total"` (or any string) → read `response["total"]`

5. **Update:** Replace the `[H]` marker with `[V1]`:
   `[V1] {count} {description}. Source: {source_name}, queried {timestamp}.`
   If the API returned records, include 2-3 examples using `key_fields` as evidence.

6. **Re-score:** If the verified count shifts the Volume dimension score by >1 point, recalculate the Composite Score. If the new composite drops below 2.5, move the signal to Section 8 with the updated score and reason.

7. **Fallback:** If no registry entry matches, or execution fails:
   - Fall back to web search validation (existing 3b behavior)
   - If web search finds a promising structured source, append it to the Candidate Sources section of the registry for future promotion
   - Signal keeps its `[H]` marker

**After the loop:** Report a summary — how many signals validated, how many kept `[H]`, any signals killed by re-scoring, any new candidate sources discovered.

---

### Step 4: Deliver

Save the brief based on the user's chosen format:
- **Markdown** (default): Save as `runs/{domain}/brief.md` (create the directory if it doesn't exist)
- **Docx**: Read `/mnt/skills/public/docx/SKILL.md`, then generate a formatted Word document
- **PDF**: Read `/mnt/skills/public/pdf/SKILL.md`, then generate a PDF

Present to the user.

## Anti-Patterns — Things That Make This Brief Useless

These are the failure modes that turn a sharp brief into generic slop. Avoid them:

- **Generic signals**: "They are hiring" / "They raised funding" / "They posted on LinkedIn" — everyone monitors these, zero differentiation. Every signal needs a causal chain specific to the target company's value prop.
- **Firmographic-only personas**: "[Title] at a [size] company" is useless. Situational qualifiers are required — what must be true about their *current moment* for them to be in-market.
- **Unvalidated signals**: If you cannot find a real-world example of a signal firing in the past 18 months, it's theoretical. Say so. Don't dress it up.
- **Missing decay analysis**: A signal without a timing window is just trivia. When does outreach peak? When is it too late?
- **Padded sections**: If a section doesn't add strategic value, cut it. The reader is a senior strategist, not a student.
- **Fake confidence**: If research hit a dead end, say so in the confidence assessment. Intellectual honesty > completeness theater.

## Reference Examples

For a completed brief and a validated signal, see:
- `references/example-brief-bobyard.md` — Full Strategic Intelligence Brief for bobyard.com
- `references/example-signal-validation.md` — Deep validation of a single signal (landscape estimator job postings) with specific company examples, volume estimates, and confidence ratings