# Data Source Registry

**Version:** v1.0
**Role:** Machine-readable catalog of validated data sources for signal validation (Step 3d). Step 3d reads this file, matches signals to sources, and executes the query templates.
**Status:** Active
**Last updated:** 2026-02-22

---

## How This File Works

This registry has two sections: **Base Sources** (universal across any vertical) and **Vertical Sources** (discovered during client-specific research). Each entry has human-readable metadata plus a machine-readable `execution_spec` JSON block.

**Step 3d execution loop:**

1. Read the signal's `Claim Type` and `Structured Params` from the brief's Detection Spec
2. Match `Claim Type` to registry entries via `validates.claim_types`
3. For each match where `agent_ready: true`:
   a. Read the template (`filter_template`, `body_template`, or `param_template`)
   b. Substitute `{{placeholders}}` with values from Structured Params
   c. Execute the request via `curl`
   d. Parse the response using `response_parsing` rules
4. Upgrade `[H]` → `[V1]` with count, source name, and timestamp

**Template substitution rules:**

- If a value is exactly `"{{key}}"` — replace with the typed value from Structured Params (arrays stay arrays, integers stay integers)
- If `{{key}}` is embedded in other text (e.g., `"{{industry}}%"`) — string replacement, result stays a string
- Required placeholder missing from Structured Params → skip this source for this signal
- Optional placeholder missing → drop that key from the request (don't send null)

**Fallback:** If no registry entry matches a signal's Claim Type, or if execution fails, Step 3d falls back to web search. If web search finds a promising structured source, append a candidate entry to `## Candidate Sources` for human review.

**MCP migration path:** Each execution_spec maps 1:1 to an MCP tool definition. Templates become `inputSchema.properties`. When this moves to MCP, convert each entry to a server tool — the spec doesn't change, only the execution wrapper.

---

## Schema Reference

### Metadata (human-readable)

| Field | Type | Description |
|-------|------|-------------|
| `source_id` | string | Unique ID. Format: `{category}-{name}` (e.g., `gov-dol-whd`) |
| `name` | string | Human-readable name |
| `description` | string | One sentence: what data this source provides |
| `url` | string | Base URL or documentation page |
| `tier` | enum | `base` (universal) or `vertical` (industry-specific) |
| `vertical` | string / null | If tier=vertical: which vertical. Null if base. |
| `cost` | string | `free`, `freemium:{details}`, `paid:{price}` |
| `last_validated` | ISO 8601 | When this source was last confirmed working |
| `validation_status` | enum | `confirmed`, `partial`, `failed`, `untested` |
| `notes` | string | Gotchas, rate limits, known issues |

### Execution Spec (machine-readable JSON)

```
{
  "agent_ready": boolean,
  "access": {
    "method": "GET" | "POST",
    "base_url": string,
    "auth": {
      "type": "none" | "api_key" | "bearer" | "subscription",
      "key_env_var": string,
      "key_location": "header" | "query_param",
      "header": string | null,
      "key_param_name": string | null
    },
    "response_format": "json" | "csv" | "xml"
  },
  "query_template": {
    "endpoint": string,
    "fixed_params": {},

    // ONE of three request patterns:

    // Pattern 1 — GET + JSON filter param (e.g., DOL filter_object):
    "filter_template": {},       // Pre-built JSON with {{placeholders}}
    "filter_param": string,      // Query param name (e.g., "filter_object")
    // Step 3d: substitute → JSON.stringify → URL-encode → ?filter_param=encoded

    // Pattern 2 — POST + JSON body (e.g., TheirStack):
    "body_template": {},         // Pre-built JSON with {{placeholders}}
    // Step 3d: substitute → send as -d body

    // Pattern 3 — GET + simple query params (e.g., Census):
    "param_template": {},        // Key=value with {{placeholders}}
    // Step 3d: substitute → append as ?key=value

    "placeholders": {
      "{{key}}": {
        "description": string,
        "maps_to": string,       // "structured_params.{field}" from Detection Spec
        "required": boolean
      }
    }
  },
  "response_parsing": {
    "count_path": string | null,
    "records_path": string,
    "key_fields": [string],
    "pagination": {
      "type": "offset" | "cursor" | "page" | "none",
      "param": string | null,
      "max_per_request": int | null
    }
  },
  "validates": {
    "claim_types": [string],
    "signal_dimensions": [string]
  }
}
```

**Special values:**

- **`count_path: null`** — API returns no total count field. Count the response array length instead.
- **`records_path: "root"`** — The response body IS a JSON array, not nested under a key. This is a sentinel value — there is no key literally named "root" in the response.

**Auth placement:**

- `key_location: "header"` → send as `-H "header: $key_env_var"` (for bearer type, prefix with `Bearer `)
- `key_location: "query_param"` → append `&key_param_name=$key_env_var` to URL

### Claim Types (standardized vocabulary)

Step 3d maps signal claims to these types for registry lookup:

| Claim Type | Description | Example |
|------------|-------------|---------|
| `job_posting_volume` | Count of job postings matching criteria | "~500 HR postings in UT/ID/WY/AZ" |
| `enforcement_action_volume` | Count of regulatory enforcement actions | "X DOL citations in Utah construction" |
| `company_count_by_size` | Count of companies in a size bracket | "~1,000 companies crossing 50 employees" |
| `insurance_rate_data` | Premium rates or rate changes by state | "Utah small group premiums +14.2%" |
| `business_registration_volume` | New or foreign entity registrations | "X new foreign entity filings in Idaho" |
| `employment_stats` | Employment counts, seasonal patterns | "Construction added 33,000 jobs in Jan" |
| `workers_comp_data` | Workers' comp rates, claims, EMR data | "NCCI proposed 1.3% increase" |
| `funding_events` | Seed/Series A announcements | "X funding rounds in UT/ID/AZ" |
| `company_headcount` | Employee count and growth data | "Company X has 47 employees, +12% YoY" |

This list grows as the system encounters new claim types.

---

## Base Sources

These sources are useful across any B2B vertical. They persist in the registry regardless of client or industry.

---

### `gov-dol-whd` — DOL Wage & Hour Division Enforcement Data

**Description:** All concluded WHD compliance actions since FY 2005 — violations, back wages, civil money penalties, employer names, by state and NAICS code.

**URL:** https://dataportal.dol.gov
**Tier:** base
**Cost:** free
**Last validated:** 2026-02-22
**Validation status:** confirmed — live data query returned 240 records for Utah construction (2026-02-22)
**Notes:** DOL Open Data Portal (launched 2026-02-19). Free API key required — register at dataportal.dol.gov/api-keys. Auth is via `X-API-KEY` query parameter (not header). Filtering uses `filter_object` JSON param with `{field, operator, value}` syntax. Operators: `eq`, `neq`, `gt`, `lt`, `in`, `not_in`, `like`. Multiple conditions wrapped in `{"and":[...]}`. Response is `{"data": [...]}` — records nested under `data` key, no total count field. Max 10,000 records or 5MB per request.

```json
{
  "agent_ready": true,
  "access": {
    "method": "GET",
    "base_url": "https://apiprod.dol.gov/v4",
    "auth": {
      "type": "api_key",
      "key_env_var": "DOL_API_KEY",
      "key_location": "query_param",
      "key_param_name": "X-API-KEY"
    },
    "response_format": "json"
  },
  "query_template": {
    "endpoint": "/get/WHD/enforcement/json",
    "fixed_params": {
      "limit": 10000
    },
    "filter_template": {
      "and": [
        {"field": "st_cd", "operator": "eq", "value": "{{location}}"},
        {"field": "naic_cd", "operator": "like", "value": "{{industry}}%"}
      ]
    },
    "filter_param": "filter_object",
    "placeholders": {
      "{{location}}": {
        "description": "Two-letter state code (e.g., UT)",
        "maps_to": "structured_params.location",
        "required": true
      },
      "{{industry}}": {
        "description": "NAICS code prefix (e.g., 236 for construction)",
        "maps_to": "structured_params.industry",
        "required": false
      }
    }
  },
  "response_parsing": {
    "count_path": null,
    "records_path": "data",
    "key_fields": ["trade_nm", "street_addr_1_txt", "cty_nm", "st_cd", "naic_cd", "naics_code_description", "bw_atp_amt", "cmp_assd"],
    "pagination": {
      "type": "offset",
      "param": "offset",
      "max_per_request": 10000
    }
  },
  "validates": {
    "claim_types": ["enforcement_action_volume"],
    "signal_dimensions": ["volume", "detectability"]
  }
}
```

**Example Step 3d execution:**

Signal says "DOL/OSHA enforcement actions in Utah construction `[H]`."
Structured Params: `{"location": "UT", "industry": "236"}`

Step 3d substitutes into filter_template, JSON-stringifies, URL-encodes, and runs:

```bash
curl -s "https://apiprod.dol.gov/v4/get/WHD/enforcement/json?\
X-API-KEY=$DOL_API_KEY&\
limit=10000&\
filter_object=$(python3 -c 'import urllib.parse,json; print(urllib.parse.quote(json.dumps({"and":[{"field":"st_cd","operator":"eq","value":"UT"},{"field":"naic_cd","operator":"like","value":"236%"}]})))')"
```

Response is `{"data": [...]}`. Count `data` array length.
Result: `[V1] 240 concluded WHD actions in Utah construction (NAICS 236). Source: DOL WHD Enforcement, queried 2026-02-22.`

---

### `jobs-theirstack` — TheirStack Job Postings API

**Description:** Aggregated, deduplicated job postings from LinkedIn, Indeed, Glassdoor, and 16,000+ ATS platforms. 169M+ jobs, 195 countries, daily updates.

**URL:** https://theirstack.com
**Tier:** base
**Cost:** freemium:200 credits/month free, $59/mo for 1,500 credits
**Last validated:** 2026-02-22
**Validation status:** confirmed
**Notes:** 1 credit = 1 job record returned. Free tier sufficient for validation-only use (counting volumes). POST endpoint with JSON body. Deduplicates across platforms. Company size filter available.

```json
{
  "agent_ready": true,
  "access": {
    "method": "POST",
    "base_url": "https://api.theirstack.com",
    "auth": {
      "type": "bearer",
      "key_env_var": "THEIRSTACK_API_KEY",
      "key_location": "header",
      "header": "Authorization"
    },
    "response_format": "json"
  },
  "query_template": {
    "endpoint": "/v1/jobs/search",
    "body_template": {
      "job_title_or": "{{query_titles}}",
      "job_location_or": "{{location}}",
      "min_employee_count": "{{company_size_min}}",
      "max_employee_count": "{{company_size_max}}",
      "posted_at_gte": "{{date_range}}",
      "limit": 1
    },
    "placeholders": {
      "{{query_titles}}": {
        "description": "Array of job title keywords (OR logic)",
        "maps_to": "structured_params.query_titles",
        "required": true
      },
      "{{location}}": {
        "description": "Array of location strings (state names or cities)",
        "maps_to": "structured_params.location",
        "required": true
      },
      "{{company_size_min}}": {
        "description": "Minimum company headcount",
        "maps_to": "structured_params.company_size_min",
        "required": false
      },
      "{{company_size_max}}": {
        "description": "Maximum company headcount",
        "maps_to": "structured_params.company_size_max",
        "required": false
      },
      "{{date_range}}": {
        "description": "ISO 8601 start date for time-bounded queries",
        "maps_to": "structured_params.date_range",
        "required": false
      }
    }
  },
  "response_parsing": {
    "count_path": "total",
    "records_path": "data",
    "key_fields": ["job_title", "company_name", "company_url", "location", "posted_at", "employee_count"],
    "pagination": {
      "type": "page",
      "param": "page",
      "max_per_request": 100
    }
  },
  "validates": {
    "claim_types": ["job_posting_volume", "company_headcount"],
    "signal_dimensions": ["volume", "detectability"]
  }
}
```

**Example Step 3d execution:**

Signal says "~500-1,000 first-time HR hires posted annually across UT/ID/WY/AZ `[H]`."
Structured Params: `{"query_titles": ["HR Manager", "HR Coordinator", "HR Generalist"], "location": ["Utah", "Idaho", "Wyoming", "Arizona"], "company_size_min": 20, "company_size_max": 150, "date_range": "2025-02-22"}`

Step 3d substitutes into body_template and runs:

```bash
curl -s -X POST "https://api.theirstack.com/v1/jobs/search" \
  -H "Authorization: Bearer $THEIRSTACK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"job_title_or":["HR Manager","HR Coordinator","HR Generalist"],"job_location_or":["Utah","Idaho","Wyoming","Arizona"],"min_employee_count":20,"max_employee_count":150,"posted_at_gte":"2025-02-22","limit":1}'
```

Response: `{"total": 312, "data": [...]}`. Read `total` field.
Result: `[V1] 312 matching HR postings across UT/ID/WY/AZ (company size 20-150) in trailing 12 months. Source: TheirStack, queried 2026-02-22.`

---

## Vertical Sources

Sources discovered during client-specific research. Each entry tagged with the vertical it applies to. These accumulate across runs — the system gets smarter with every client.

---

### `gov-census-susb` — Census Bureau Statistics of U.S. Businesses

**Description:** Firm counts by employee size class, by state, by NAICS code. Answers "how many companies in Utah have 20-49 employees in construction?"

**URL:** https://www.census.gov/programs-surveys/susb.html
**Tier:** vertical
**Vertical:** peo, any vertical using employee-count thresholds
**Cost:** free
**Last validated:** 2026-02-22
**Validation status:** partial — data available but most recent year is 2022, ~2-year lag
**Notes:** Not a live API — static annual dataset. Useful for baseline firm counts but cannot detect real-time threshold crossings. Best for validating the total addressable universe, not monthly flow rates.

```json
{
  "agent_ready": true,
  "access": {
    "method": "GET",
    "base_url": "https://data.census.gov/api",
    "auth": {
      "type": "api_key",
      "key_env_var": "CENSUS_API_KEY",
      "key_location": "query_param",
      "key_param_name": "key"
    },
    "response_format": "json"
  },
  "query_template": {
    "endpoint": "/data/2022/cbp",
    "fixed_params": {
      "get": "ESTAB,EMPSZES,NAICS2017,NAME"
    },
    "param_template": {
      "for": "state:{{state_fips}}",
      "EMPSZES": "{{employee_size_class}}",
      "NAICS2017": "{{industry}}"
    },
    "placeholders": {
      "{{state_fips}}": {
        "description": "FIPS state code (e.g., 49 for Utah, 16 for Idaho)",
        "maps_to": "structured_params.state_fips",
        "required": true
      },
      "{{employee_size_class}}": {
        "description": "Census size class code (e.g., 232 = 20-49 employees)",
        "maps_to": "structured_params.employee_size_class",
        "required": false
      },
      "{{industry}}": {
        "description": "NAICS code",
        "maps_to": "structured_params.industry",
        "required": false
      }
    }
  },
  "response_parsing": {
    "count_path": null,
    "records_path": "root",
    "key_fields": ["NAME", "ESTAB", "EMPSZES", "NAICS2017"],
    "pagination": {
      "type": "none",
      "param": null,
      "max_per_request": null
    }
  },
  "validates": {
    "claim_types": ["company_count_by_size"],
    "signal_dimensions": ["volume"]
  }
}
```

**Example Step 3d execution:**

Signal says "~1,000-2,000 companies crossing the 50-employee threshold annually `[H]`."
Structured Params: `{"state_fips": "49", "employee_size_class": "232"}`

Step 3d substitutes into param_template (drops `NAICS2017` since industry is absent) and runs:

```bash
curl -s "https://data.census.gov/api/data/2022/cbp?get=ESTAB,EMPSZES,NAICS2017,NAME&for=state:49&EMPSZES=232&key=$CENSUS_API_KEY"
```

Response is a root-level JSON array. Count rows (minus header row).
Result: `[V1] 4,212 establishments in Utah in the 20-49 employee size class (Census CBP, 2022). Addressable universe is substantial. Annual crossing rate remains [H] — Census is a snapshot, not a flow measure.`

---

## Candidate Sources

Sources identified by Step 3d web search fallback but not yet validated. Each candidate includes what it might provide, how it was discovered, and what needs verification before promotion.

*This section is auto-populated by Step 3d when web search finds a promising source not in the registry.*

---

### Candidate: `kyb-cobalt-intelligence` — Cobalt Intelligence (KYB API)

**Discovered during:** Helpside run, Signal 5 (Multi-State Expansion)
**What it might provide:** Aggregated Secretary of State business registration data via REST API. Normalizes across all 50 states.
**Verification needed:**
- [ ] Confirm API exists and is accessible (not enterprise-sales-only)
- [ ] Check if free tier or trial available
- [ ] Verify it covers foreign entity registrations (not just domestic formations)
- [ ] Test query for UT/ID/WY/AZ foreign entity filings in last 12 months
**Promote when:** Verified working with at least one successful test query

---

### Candidate: `kyb-middesk` — Middesk (Business Identity API)

**Discovered during:** Helpside run, Signal 5 (Multi-State Expansion)
**What it might provide:** Business verification and identity data including state registration status, formation details.
**Verification needed:**
- [ ] Confirm API access model (may be enterprise/sales-gated)
- [ ] Check if it exposes bulk search or only single-entity lookup
- [ ] Determine if it surfaces new registrations vs. just verification of known entities
**Promote when:** Verified accessible and confirms it can detect new foreign entity filings

---

### Candidate: `insurance-aca-signups` — ACA Signups Rate Data

**Discovered during:** Helpside run, Signal 3 (Health Insurance Renewal)
**What it might provide:** State-by-state health insurance rate change data from DOI filings. Published annually during rate filing season.
**Verification needed:**
- [ ] Check if acasignups.net publishes data in structured/downloadable format
- [ ] If HTML only, assess scraping feasibility
- [ ] Confirm data covers small group market (not just individual)
- [ ] Determine update cadence
**Promote when:** Confirmed extractable in structured format with state-level small group rate data
