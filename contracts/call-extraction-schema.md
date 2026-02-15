# Call Extraction Schema Contract

**Version:** v1.0
**Role:** Defines the structured output format for Module 2A (Call Intelligence Extractor / Clay prompt)
**Status:** TBD — populate from existing schema contract when Prompt 2 v2.0 is built (Phase 2)

---

## Schema Fields

### Tier 1 (Always Extract)

| Field | Type | Description |
|-------|------|-------------|
| `buyer_problem_framing` | string | TBD |
| `triggering_context` | string | TBD |
| `alternative_attempts` | string | TBD |
| `indigenous_terminology` | string | TBD |
| `vocabulary_gap` | string | TBD |
| `pain_vocabulary` | string | TBD |
| `engagement_moments` | string | TBD |

### Tier 2 (Extract if Rich)

| Field | Type | Description |
|-------|------|-------------|
| `decision_ecosystem` | string | TBD |
| `trust_and_risk` | string | TBD |
| `call_quality_score` | number | TBD |
| `messaging_goldmine` | string | TBD |
| `recommended_actions` | string | TBD |

---

## Tier Logic

If `buyer_talk_ratio_estimate` = Low → extract Tier 1 only.

---

## Breaking Changes Log

None yet.
