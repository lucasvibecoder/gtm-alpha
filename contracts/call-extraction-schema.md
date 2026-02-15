# Call Extraction Schema Contract

**Version:** v2.0
**Role:** Defines the structured output format for Module 2A (Call Intelligence Extractor), the Tier 1/Tier 2 extraction logic, and the canonical field/enum registry for the full prompt chain.
**Status:** Active

---

## System Data Flow

```
Module 1A (Vendor Intelligence Brief)
    → Outputs: structured markdown (Sections 1–8)
    → Consumed by: Module 2C, Module 3A (Fast Path)

Module 2A (Call Intelligence Extractor — Clay/Sonnet)
    → Outputs: P2_SCHEMA (JSON per call, Tier 1 always, Tier 2 if rich)
    → Consumed by: Module 2B

Module 2B (Call Intelligence Synthesis)
    → Outputs: structured markdown (7 sections)
    → Consumed by: Module 2C

Module 2C (Signal & Messaging Reconciliation)
    → Outputs: structured markdown (5 sections)
    → Consumed by: Module 3A (Full Path)

Module 3A (Play Design & PVP Architecture)
    → Outputs: Final plays and PVPs
    → Terminal node
```

---

## Tier 1 / Tier 2 Extraction Logic

Module 2A runs in Clay on Sonnet. The full schema is too heavy for reliable extraction on every call. Fields are split into two tiers:

**Tier 1 (Always Extract):** The fields that directly feed signal validation and language calibration. These are the minimum viable extraction for every call.

**Tier 2 (Extract if Rich):** Fields that add depth but are unreliable on short, rep-dominated, or surface-level calls. Only extract when the call has enough buyer dialogue to support it.

**Tier Logic:**
- If `buyer_talk_ratio_estimate` = `Low (<30%)` → extract Tier 1 only
- If `buyer_talk_ratio_estimate` = `Medium (30-50%)` or `High (>50%)` → extract Tier 1 + Tier 2

When Tier 2 is skipped, the JSON output should include `"tier_2_extracted": false` and omit all Tier 2 fields (do not return nulls for every Tier 2 field — omit them entirely to reduce token usage).

---

## Module 2A Output Schema (P2_SCHEMA)

### Top-Level Fields

| Field | Type | Required | Tier |
|-------|------|----------|------|
| `extraction_status` | enum: `complete`, `insufficient_signal` | Yes | — |
| `tier_2_extracted` | boolean | Yes | — |

### Insufficient Signal Response

When `extraction_status` = `insufficient_signal`:

| Field | Type |
|-------|------|
| `reason` | string |
| `call_duration_estimate` | enum: `Short`, `Medium`, `Long` |
| `buyer_talk_ratio_estimate` | enum: `Low (<30%)`, `Medium (30-50%)`, `High (>50%)` |
| `partial_data` | object (sparse) |

---

### Tier 1 Fields (Always Extract)

#### `call_metadata` (Tier 1 — partial)

`call_metadata.speakers_identified` and `call_metadata.buyer_context` are always extracted. `call_metadata.call_quality_score` is Tier 2.

**`call_metadata.speakers_identified.buyer_side[]`**

| Field | Type | Values |
|-------|------|--------|
| `name` | string | Name or `"Unknown"` |
| `role_normalized` | enum | See ROLE ENUM |
| `role_verbatim` | string | Exact title as stated |
| `talk_share` | string | Analyst estimate |

**`call_metadata.speakers_identified.seller_side`** — array of strings (names/roles)

**`call_metadata.buyer_context`**

| Field | Type | Values |
|-------|------|--------|
| `company_name` | string or null | |
| `industry_vertical` | enum | See INDUSTRY ENUM |
| `company_size_signal` | enum | See COMPANY SIZE ENUM |
| `geo_signal` | string or null | |
| `evidence` | string | Analyst: 1 sentence |

#### `buyer_problem_framing` (Tier 1)

| Field | Type | Values |
|-------|------|--------|
| `how_they_describe_it` | string (Quote:) or null | Exact buyer words |
| `problem_category` | enum | See PROBLEM CATEGORY ENUM |
| `problem_subcategory` | string | Analyst: 1 sentence |
| `who_owns_this_problem` | enum | See PROBLEM OWNER ENUM |
| `problem_maturity` | enum | See PROBLEM MATURITY ENUM |
| `is_primary_or_secondary` | enum | `Primary`, `Secondary`, `Tertiary` |

#### `triggering_context` (Tier 1)

| Field | Type | Values |
|-------|------|--------|
| `what_changed` | string (Quote:) or null | |
| `change_type` | enum | See CHANGE TYPE ENUM |
| `timeline_pressure` | string (Quote:) or null | |
| `urgency_level` | enum | See URGENCY ENUM |
| `status_quo_cost` | string (Quote:) or null | |
| `status_quo_cost_type` | enum | See STATUS QUO COST ENUM |
| `why_now_vs_later` | string | Analyst: 1-2 sentences |

#### `alternative_attempts[]` (Tier 1)

| Field | Type | Values |
|-------|------|--------|
| `solution_type` | enum | See SOLUTION TYPE ENUM |
| `specific_solution` | string | Name of tool/vendor/process |
| `why_they_tried_it` | string | Analyst: 1 sentence |
| `failure_quote` | string (Quote:) or null | |
| `failure_category` | enum | See FAILURE CATEGORY ENUM |
| `emotional_residue.sentiment` | enum | See SENTIMENT ENUM |
| `emotional_residue.evidence_quote` | string (Quote:) or null | |
| `switching_cost_signal` | enum | `Low`, `Medium`, `High`, `Locked In` |

#### `buyer_psychology.emotional_intensity` (Tier 1 — partial)

`pain_vocabulary` is Tier 1. The rest of `emotional_intensity` is Tier 1 because it feeds signal validation directly.

| Field | Type | Values |
|-------|------|--------|
| `pain_vocabulary` | array of strings | Emotionally charged words about problems |
| `urgency_vocabulary` | array of strings | Words signaling time pressure |
| `aspiration_vocabulary` | array of strings | Words about desired future state |
| `peak_emotional_moment` | string | Analyst: 1-2 sentences |
| `dominant_motivation` | enum | See MOTIVATION ENUM |
| `intensity_rating` | enum | `Flat`, `Moderate`, `High`, `Visceral` |

#### `native_language_map` (Tier 1 — partial)

`indigenous_terminology` and `vocabulary_gap` are Tier 1. `messaging_goldmine` is Tier 2.

| Field | Type |
|-------|------|
| `indigenous_terminology` | array of strings |
| `industry_context` | array of strings |
| `analogies_and_metaphors` | array of strings |
| `emotional_language` | array of strings |

**`native_language_map.vocabulary_gap[]`**

| Field | Type |
|-------|------|
| `buyer_term` | string |
| `market_standard` | string |
| `implication` | string (Analyst: 1 sentence) |

#### `engagement_moments` (Tier 1)

**`engagement_moments.lean_in`**

| Field | Type | Values |
|-------|------|--------|
| `stimulus` | string | Analyst: 1 sentence |
| `buyer_reaction` | string (Quote:) or null | |
| `resonance_category` | enum | See RESONANCE ENUM |
| `theory_of_resonance` | string | Analyst: 1 sentence |

**`engagement_moments.lean_out`**

| Field | Type | Values |
|-------|------|--------|
| `stimulus` | string | Analyst: 1 sentence |
| `buyer_reaction` | string (Quote:) or null | |
| `failure_category` | enum | See ENGAGEMENT FAILURE ENUM |
| `theory_of_failure` | string | Analyst: 1 sentence |

---

### Tier 2 Fields (Extract if Rich)

#### `call_metadata.call_quality_score` (Tier 2)

| Field | Type | Values |
|-------|------|--------|
| `discovery_depth` | enum | `Surface`, `Moderate`, `Deep` |
| `buyer_openness` | enum | `Guarded`, `Polite`, `Engaged`, `Candid` |
| `rep_discovery_skill` | enum | `Weak`, `Adequate`, `Strong`, `Exceptional` |
| `evidence` | string | Analyst: 1 sentence |

#### `decision_ecosystem` (Tier 2)

**`decision_ecosystem.economic_signals`**

| Field | Type | Values |
|-------|------|--------|
| `budget_exists` | enum | `Yes - Allocated`, `Yes - Unallocated`, `No Budget`, `Unknown` |
| `budget_evidence` | string (Quote:) or null | |
| `estimated_current_spend` | string | Analyst: 1 sentence |
| `price_sensitivity` | enum | `Price Driven`, `Value Driven`, `ROI Driven`, `Not a Factor`, `Unknown` |
| `spend_authority` | enum | `Full Authority`, `Shared Authority`, `Influencer Only`, `Unknown` |

**`decision_ecosystem.stakeholder_map`**

| Field | Type | Values |
|-------|------|--------|
| `champion.role_normalized` | enum or null | ROLE ENUM |
| `champion.champion_strength` | enum | `Strong`, `Moderate`, `Weak`, `None Identified` |
| `champion.evidence` | string (Quote:) or null | |
| `economic_buyer.role_normalized` | enum or null | ROLE ENUM |
| `economic_buyer.identified` | enum | `Yes - On Call`, `Yes - Mentioned`, `No` |
| `blockers[].role_normalized` | enum or null | ROLE ENUM |
| `blockers[].blocker_type` | enum | `Political`, `Technical`, `Financial`, `Inertia`, `Competing Priority` |
| `blockers[].evidence` | string (Quote:) or null | |
| `decision_process` | enum | See DECISION PROCESS ENUM |
| `decision_timeline` | enum | See DECISION TIMELINE ENUM |

#### `buyer_psychology.trust_and_risk` (Tier 2)

| Field | Type | Values |
|-------|------|--------|
| `trust_level` | enum | `High Trust`, `Open`, `Cautious`, `Skeptical`, `Hostile` |
| `trust_barriers` | string (Quote:) or null | |
| `proof_requirements` | enum (multi-select) | See PROOF ENUM |
| `risk_concerns` | string (Quote:) or null | |
| `risk_type` | enum | See RISK TYPE ENUM |

#### `native_language_map.messaging_goldmine` (Tier 2)

| Field | Type |
|-------|------|
| `best_quote_for_marketing` | string (Quote:) or null |
| `best_problem_framing_for_outbound` | string (Analyst: 1 sentence) |
| `seo_keyword_candidates` | array of strings |

#### `native_language_map.buyer_sophistication` (Tier 2)

| Field | Type | Values |
|-------|------|--------|
| `technical_depth` | enum | `Low`, `Medium`, `High` |
| `vendor_literacy` | enum | `First-Time Buyer`, `Experienced`, `Sophisticated/Jaded` |
| `evidence` | string | Analyst: 1 sentence |

#### `research_quality_meta` (Tier 2)

| Field | Type | Values |
|-------|------|--------|
| `insight_richness` | enum | `High`, `Medium`, `Low` |
| `quote_quality` | string | Analyst: 1 sentence |
| `persona_archetype` | enum | See PERSONA ARCHETYPE ENUM |
| `key_research_insight` | string | Analyst: 1-2 sentences |
| `hypothesis_seeds` | array of strings |

#### `recommended_actions` (Tier 2)

| Field | Type | Values |
|-------|------|--------|
| `deal_quality_signal` | enum | `Strong`, `Moderate`, `Weak`, `Disqualify` |
| `signal_rationale` | string | Analyst: 1-2 sentences |
| `immediate_next_step` | string | Analyst: 1 sentence |
| `messaging_guidance` | string | Analyst: 1-2 sentences |
| `content_to_send` | enum | See CONTENT ENUM |
| `follow_up_email_draft` | string | Analyst: 2-3 sentences |
| `internal_flags` | array of enums | See INTERNAL FLAGS ENUM |

---

## Module 1A Output Schema (P1_SCHEMA)

Module 1A outputs structured markdown. Sections after Phase 1:

| Section # | Section Name | Consumed By |
|-----------|-------------|-------------|
| 1 | Executive Context | Module 2C, Module 3A |
| 2 | Value Proposition Analysis | Module 2C, Module 3A |
| 3 | Buyer Topology | Module 2C, Module 3A |
| 4 | Signal Stacks | Module 2C, Module 3A |
| 5 | Individual Signal Hypotheses (with Signal Score + Detection Spec) | Module 2C, Module 3A |
| 6 | Adjacent & Upstream Signals | Module 2C, Module 3A |
| 7 | Competitive Intelligence Summary | Module 2C, Module 3A |
| 8 | Research Confidence Assessment (includes killed signals) | Module 2C |

### Signal Hypothesis Fields (Section 5)

Each signal includes these fields plus the Signal Score and Detection Spec tables (added in Phase 1):

| Field | Type | Description |
|-------|------|-------------|
| Signal Name | string | Descriptive name |
| Observable Indicator(s) | string | What to look for |
| Causal Logic | string | Why this indicates need |
| Exists in the wild? | string | Where announced |
| Recent example? | string | Real instance from past 18 months |
| Estimated annual volume | string | With reasoning |
| GTM motion implication | string | Automated vs. manual |
| Peak Window | string | When outreach is most effective |
| Decay Rate | string | How quickly signal loses value |
| Late-Stage Angle | string | Play if arriving late |
| Obviousness Level | enum | `High` / `Medium` / `Low` |
| Signal Score | table | 5 dimensions + composite (see `contracts/signal-scorecard.md`) |
| Detection Spec | table | 5 fields (see `contracts/detection-spec.md`) |
| Feeds Into Stack(s) | string | Which Pain Point stack(s) |

---

## Module 2B Output Schema (P3_SCHEMA)

Module 2B outputs structured markdown. Compressed to 7 sections in Phase 2:

| Section # | Section Name | Consumed By Module 2C |
|-----------|-------------|----------------------|
| 1 | Dataset Overview | Quality assessment |
| 2 | Signal Validation Patterns | Signal validation |
| 3 | Buyer Language Map | Language calibration |
| 4 | Problem Framing Analysis | Missed signals, objections |
| 5 | Alternative Attempts & Objection Patterns (merged) | Competitive patterns, objection reality check |
| 6 | Engagement Moment Analysis | What worked/failed |
| 7 | Key Insights Summary | Signal validation, missed signals |

### Critical Module 2A → Module 2B Field Mapping

These are the exact Module 2A field paths that Module 2B must reference when aggregating:

| Module 2B Section | Module 2A Field Path | Notes |
|-------------------|---------------------|-------|
| §1 — Call Quality | `call_metadata.call_quality_score.discovery_depth` | Tier 2 — may be absent |
| §1 — Problem Maturity | `buyer_problem_framing.problem_maturity` | Tier 1 |
| §1 — Problem Categories | `buyer_problem_framing.problem_category` | Tier 1 |
| §2 — Trigger Events | `triggering_context.change_type` | Tier 1 |
| §2 — Intensity | `buyer_psychology.emotional_intensity.intensity_rating` | Tier 1 |
| §2 — Status Quo Cost | `triggering_context.status_quo_cost` | Tier 1 |
| §3 — Pain Language | `buyer_psychology.emotional_intensity.pain_vocabulary` | Tier 1 |
| §3 — Urgency Language | `buyer_psychology.emotional_intensity.urgency_vocabulary` | Tier 1 |
| §3 — Aspiration Language | `buyer_psychology.emotional_intensity.aspiration_vocabulary` | Tier 1 |
| §3 — Indigenous Terminology | `native_language_map.indigenous_terminology` | Tier 1 |
| §3 — Vocabulary Gaps | `native_language_map.vocabulary_gap` | Tier 1 |
| §3 — Analogies | `native_language_map.analogies_and_metaphors` | Tier 1 |
| §4 — Problem Framing | `buyer_problem_framing.how_they_describe_it` | Tier 1 |
| §4 — Problem Ownership | `buyer_problem_framing.who_owns_this_problem` | Tier 1 |
| §5 — Alternative Attempts | `alternative_attempts[]` | Tier 1 |
| §5 — Trust Barriers | `buyer_psychology.trust_and_risk.trust_barriers` | Tier 2 — may be absent |
| §5 — Proof Requirements | `buyer_psychology.trust_and_risk.proof_requirements` | Tier 2 — may be absent |
| §6 — Lean-In Moments | `engagement_moments.lean_in` | Tier 1 |
| §6 — Lean-Out Moments | `engagement_moments.lean_out` | Tier 1 |

---

## Master Enum Registry

**All prompts must use these exact enum values. No synonyms, no variants, no custom values.**

### ROLE ENUM

```
CEO, CRO, CMO, CFO, CTO, COO,
VP Sales, VP Marketing, VP RevOps, VP Engineering, VP Product, VP CS,
Director Sales, Director Marketing, Director RevOps, Director Engineering, Director Product, Director CS,
Manager Sales, Manager Marketing, Manager RevOps, Manager Engineering, Manager Product, Manager CS,
IC Sales, IC Marketing, IC RevOps, IC Engineering, IC Product, IC CS,
Founder, Other
```

### INDUSTRY ENUM

```
SaaS/Software, Financial Services, Healthcare/Life Sciences, Manufacturing,
Retail/E-Commerce, Media/Entertainment, Professional Services, Education,
Government, Logistics/Supply Chain, Real Estate, Telecom, Energy, Non-Profit, Other
```

### COMPANY SIZE ENUM

```
Startup (1-50), SMB (51-200), Mid-Market (201-1000),
Enterprise (1001-5000), Large Enterprise (5000+), Unknown
```

### PROBLEM CATEGORY ENUM

```
Data Hygiene/Trust, Workflow Friction, Integration Complexity, Cost/Waste,
Targeting Precision, Growth Constraint, Risk/Compliance, Team Adoption,
Visibility/Reporting, Speed to Market, Talent/Hiring, Vendor Consolidation
```

### PROBLEM OWNER ENUM

```
Sales, Marketing, RevOps, Engineering, Product, CS/CX,
Finance, IT, Executive, Cross-Functional
```

### PROBLEM MATURITY ENUM

```
Unaware, Problem Aware, Solution Aware, Product Aware, Most Aware
```

### CHANGE TYPE ENUM

```
New Leadership, Reorg/Restructure, Missed Targets, Competitive Pressure,
Board/Investor Pressure, New Funding, Tech Stack Change, Headcount Change,
Market Shift, Regulatory Change, Customer Churn Signal, Expansion/New Market
```

### URGENCY ENUM

```
No Urgency, Soft Deadline, Hard Deadline, Hair on Fire
```

### STATUS QUO COST ENUM

```
Revenue Loss, Efficiency Loss, Opportunity Cost, Risk Exposure,
Employee Attrition, Customer Churn, Compliance Risk, Unknown
```

### SOLUTION TYPE ENUM

```
Direct Competitor, Adjacent Tool, Manual/DIY Process, Internal Build,
Outsourced/Agency, Spreadsheet/Hack, Did Nothing
```

### FAILURE CATEGORY ENUM

```
Too Complex, Too Expensive, Too Slow, Poor Results, Low Adoption,
Bad Support, Outgrew It, Wrong Fit, Integration Failure, Data Quality
```

### SENTIMENT ENUM

```
Bitter, Skeptical, Resigned, Neutral, Curious, Determined, Relieved
```

### MOTIVATION ENUM

```
Fear/Pain Avoidance, Aspiration/Growth, Mandate/Compliance,
Efficiency/Optimization, Competitive Response
```

### INTENSITY RATING ENUM

```
Flat, Moderate, High, Visceral
```

### TRUST LEVEL ENUM

```
High Trust, Open, Cautious, Skeptical, Hostile
```

### PROOF ENUM

```
Case Study, ROI Calculator, Free Trial, Pilot, Reference Call,
Technical Demo, Security Review, None Stated
```

### RISK TYPE ENUM

```
Implementation Risk, Adoption Risk, Integration Risk,
Performance Risk, Political Risk, Financial Risk, None Stated
```

### RESONANCE ENUM

```
Pain Validation, ROI/Value, Social Proof, Vision/Aspiration,
Competitive Edge, Simplicity, Speed to Value
```

### ENGAGEMENT FAILURE ENUM

```
Premature Pricing, Feature Dump, Assumed Pain, Jargon Overload,
Pushed Too Hard, Ignored Concern, Wrong Persona Focus, Irrelevant Proof
```

### DISCOVERY DEPTH ENUM

```
Surface, Moderate, Deep
```

### BUYER OPENNESS ENUM

```
Guarded, Polite, Engaged, Candid
```

### REP SKILL ENUM

```
Weak, Adequate, Strong, Exceptional
```

### DECISION PROCESS ENUM

```
Single Decision Maker, Committee, Pilot Required,
RFP/Procurement, Executive Sign-Off, Unknown
```

### DECISION TIMELINE ENUM

```
This Month, This Quarter, Next Quarter, This Year, No Timeline, Unknown
```

### PERSONA ARCHETYPE ENUM

```
The Frustrated Operator, The Data-Driven Executive, The Skeptical Evaluator,
The Visionary Champion, The Overwhelmed Manager, The Compliance-Driven Buyer,
The Cost Cutter, The Growth Chaser, The Technical Gatekeeper
```

### CONTENT TO SEND ENUM

```
Case Study, ROI Calculator, Competitive Comparison, Technical Documentation,
Implementation Guide, Executive Summary, Product Demo Recording,
Customer Reference, None
```

### DEAL QUALITY ENUM

```
Strong, Moderate, Weak, Disqualify
```

### INTERNAL FLAGS ENUM

```
Needs Exec Sponsor, Competitive Deal, Budget Not Confirmed,
Multi-Thread Required, Champion at Risk, Long Sales Cycle,
Procurement Involved, Technical Validation Needed,
Expansion Opportunity, Churn Risk Signal,
Product Gap Identified, Pricing Objection Likely
```

---

## Breaking Changes Log

| Date | Change | Status |
|------|--------|--------|
| v2.0 | Tier 1/Tier 2 extraction split added | Resolved — Module 2A and 2B updated |
| v2.0 | Module 2B compressed from 12 sections to 7 | Resolved — Module 2B updated |
| v2.0 | Module 1A Section 8 (Strategic Recommendations) killed; old Section 9 renumbered to 8 | Resolved — Phase 1 |
| v2.0 | Signal Score + Detection Spec tables added to Module 1A Section 5 | Resolved — Phase 1 |
| v2.0 | `pain_points_vocabulary` → `pain_vocabulary` | Resolved |
| v2.0 | `change_type` enums: 4 values → 12 values | Resolved |
| v2.0 | `intensity_rating`: numeric → enum (Flat/Moderate/High/Visceral) | Resolved |
| v2.0 | `failure_category` enums: 8 values → 10 values | Resolved |
| v2.0 | `sentiment` enums: added Neutral, Relieved | Resolved |
| v2.0 | `problem_maturity` enums changed to awareness model | Resolved |
| v2.0 | `analogies_metaphors` → `analogies_and_metaphors` | Resolved |
| v2.0 | `vocabulary_gap[].industry_standard` → `vocabulary_gap[].market_standard` | Resolved |
| v2.0 | `sophistication_markers` → `buyer_sophistication` | Resolved |
| v2.0 | `vendor_literacy` enums updated | Resolved |
| v2.0 | Added `call_metadata` | Resolved |
| v2.0 | Added `recommended_actions` | Resolved |
| v2.0 | Added `native_language_map.messaging_goldmine` | Resolved |
| v2.0 | Added `native_language_map.emotional_language` | Resolved |
| v2.0 | Added `engagement_moments.lean_in.resonance_category` | Resolved |
| v2.0 | Added `engagement_moments.lean_out.failure_category` | Resolved |

---

## Validation Checklist

When modifying any prompt, verify:

- [ ] All field paths match this schema contract exactly
- [ ] All enum values are copied verbatim from the Master Enum Registry
- [ ] No prompt references a field or enum not listed in this document
- [ ] Breaking Changes Log is updated for any schema modifications
- [ ] All downstream prompts are checked for impact
- [ ] Tier 1/Tier 2 boundary is respected — Tier 2 fields are never assumed present
