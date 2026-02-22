# Detection Spec Contract

**Version:** v1.1
**Role:** Defines the format for signal detection specifications in Module 1A output
**Status:** Active

---

## Purpose

Every signal hypothesis in Section 5 of the brief must include a detection spec. The detection spec answers: "If an operator wanted to monitor this signal starting Monday, exactly where would they look, how often, and what would it cost?" Without this, signals are interesting but unactionable.

---

## Required Fields

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| **Data Source** | string | The specific platform, database, feed, or publication where this signal is observable. Name the actual product — not "job boards" but "Indeed API + LinkedIn Recruiter." | `Indeed API`, `SAM.gov`, `PitchBook alerts`, `LinkedIn Sales Navigator` |
| **Query / Filter** | string | The exact search query, filter criteria, or monitoring rule an operator would set up. Specific enough to be copy-pasted into the platform. | `"landscape estimator" AND ("commercial" OR "construction") posted:>30days` |
| **Claim Type** | enum | The signal's claim type from the standardized vocabulary in `contracts/data-source-registry.md`. Enables Step 3d to route this signal to the right API source for validation. | `enforcement_action_volume`, `job_posting_volume`, `company_count_by_size` |
| **Structured Params** | JSON | Machine-readable parameters for API query templates. Keys match `placeholders.maps_to` fields in registry entries. Include only params relevant to this signal's claim type. | `{"location": "UT", "industry": "236"}` |
| **Refresh Cadence** | enum | How often the data source should be checked to catch signals within their peak window. Values: `Daily`, `Weekly`, `Biweekly`, `Monthly`, `Event-triggered` | `Weekly` |
| **Estimated Monitoring Cost** | string | The cost to monitor this signal, including tool subscriptions and operator time. Format: `$X/mo tool + Y hrs/mo operator time`. If free, say `Free + Y hrs/mo`. | `$0 (Indeed free search) + 2 hrs/mo` |
| **Automation Feasibility** | enum + note | How automatable is detection? Values: `Full` (API available, can trigger alerts), `Partial` (structured data but manual review needed), `Manual` (requires human browsing/judgment). Include one sentence on what tooling would be needed. | `Partial — Indeed RSS feeds exist but aging analysis requires custom scripting or Clay enrichment` |

---

## Output Format (in PROMPT_TEMPLATE.md)

Each signal in Section 5 must include this table immediately after the Signal Score:

```markdown
**Detection Spec:**

| Field | Spec |
|-------|------|
| **Data Source** | [platform/database name] |
| **Query / Filter** | [exact query or filter criteria] |
| **Claim Type** | [from registry vocabulary: job_posting_volume / enforcement_action_volume / company_count_by_size / etc.] |
| **Structured Params** | `{"location": "...", "industry": "...", ...}` — include only params relevant to this signal's claim type |
| **Refresh Cadence** | [Daily / Weekly / Biweekly / Monthly / Event-triggered] |
| **Monitoring Cost** | [tool cost + operator time per month] |
| **Automation Feasibility** | [Full / Partial / Manual] — [one sentence on tooling needed] |
```

---

## Quality Rules

- **"Job boards" is not a data source.** Name the specific board and access method (API, RSS, manual search).
- **"Social media" is not a data source.** Name the platform and the specific monitoring approach.
- **Queries must be copy-pasteable.** An operator should be able to take the query string and run it on the named platform without interpretation.
- **Cost estimates must be honest.** If a signal requires a $500/mo PitchBook subscription, say so. Don't hide costs behind "various tools."
- **Claim Type must come from the registry vocabulary.** Use the standardized types in `contracts/data-source-registry.md`. If no type fits, define a new one and note it for registry update.
- **Structured Params must be machine-readable.** Values should match what the registry's `placeholders.maps_to` expects — state codes for DOL, full state names for TheirStack, FIPS codes for Census. Check the registry entry before writing params.

---

## Downstream Consumers

- **Module 3A (Play Design):** Uses detection specs to define the "Signal Detection Method" field in each play
- **Step 3d (Empirical Validation):** Reads `Claim Type` and `Structured Params` to route signals to API sources in `contracts/data-source-registry.md` and validate volume claims
- **Operator setup:** Detection specs are the checklist for building monitoring infrastructure
