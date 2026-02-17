# Claim Verification Contract

**Version:** v1.0
**Role:** Defines claim classification, verification rules, the Verification Ledger artifact, claim budgets, and STOP_OUTPUT policy for Module 3B (PVP Generator)
**Status:** Active

---

## Purpose

Every data point in a prospect-facing PVP deliverable must be verified before it enters the output. This contract defines how claims are classified, what verification means for each class, and what happens when verification fails.

**Why this exists:** Module 3B produced a Bid Radar Brief that attributed a contract (RFP 2022-020 Landscape Maintenance) to City of Dallas Parks. The contract actually belongs to Northside ISD in San Antonio. The cited source URL (dallasparks.org) didn't even contain the claim. This contract makes that class of error structurally impossible by requiring source-claim binding, entity attribution checks, and volatility classification.

---

## Claim Tiers

Every data point collected during Module 3B research must be classified into one or more tiers before it can enter the deliverable.

| Tier | What It Is | Verification Rule | Failure Action |
|------|-----------|-------------------|----------------|
| **Static** | Stable facts — revenue, funding, acreage, historical events, company descriptions | `verified_url` must contain the claimed data point | Remove claim or find a source that does contain it |
| **Dynamic** | Volatile facts — deadlines, active listings, contract expirations, current pricing, "currently active" status | `verified_url` must be visited directly AND must show the data is current as of `as_of_timestamp` | Exclude the claim entirely. Do not downgrade to "approximate" — a wrong deadline is worse than no deadline |
| **Attributed** | Entity-specific facts — "[Organization] did/posted/issued [thing]" | `verified_url` must name the specific entity. If a search result matched a pattern but the original source names a different entity, the claim fails | Kill the claim. Do not attempt to repair attribution — if the entity is wrong, what else is wrong? |

**A single claim can be multiple tiers.** "Dallas Parks contract expiring Feb 28" is both Dynamic (deadline) AND Attributed (entity). It must pass verification for ALL applicable tiers.

### Classification Triggers

These keyword triggers force tier classification — not a judgment call:

| IF claim contains... | THEN tier includes... |
|---------------------|----------------------|
| A date, deadline, "expiring," "currently," "active," "open," "as of," "posted on" | **Dynamic** |
| "[Entity name] did/has/posted/issued/awarded/published [thing]" | **Attributed** |
| Neither of the above | **Static** |

---

## Verification Ledger (Required Artifact)

Every Module 3B deliverable MUST produce a Verification Ledger as an internal appendix. The ledger is the proof that verification happened. **No ledger = no output.**

### Ledger Format

```markdown
## Verification Ledger

| claim_text | tier | discovered_via_url | verified_url | entity_on_page | entity_in_claim | as_of_timestamp | status |
|-----------|------|-------------------|-------------|---------------|----------------|----------------|--------|
```

### Field Definitions

| Field | Type | Description |
|-------|------|-------------|
| `claim_text` | string | The exact text as it would appear in the deliverable |
| `tier` | enum (multi) | One or more of: `Static`, `Dynamic`, `Attributed` |
| `discovered_via_url` | URL | Where the claim was first found. May be an aggregator (govcb.com, BidPrime listing, Google snippet). For traceability only — does NOT determine PASS/FAIL |
| `verified_url` | URL | The URL that was actually visited to verify the claim. **This is the field that determines PASS/FAIL.** Must be the original source (procurement portal, agency site, company page) — not an aggregator |
| `entity_on_page` | string | The entity name as it appears on the `verified_url` page. Use "N/A" for Static claims without entity attribution |
| `entity_in_claim` | string | The entity name as it appears in the claim. Use "N/A" for Static claims without entity attribution |
| `as_of_timestamp` | ISO 8601 | Timestamp of the actual verification moment (when `verified_url` was visited). Not the research start time |
| `status` | enum | `PASS` · `FAIL — [reason]` · `INACCESSIBLE — [reason]` · `AGGREGATOR_NEEDS_ORIGIN` |

### Aggregator Rule

If a claim is discovered via an aggregator (govcb.com, BidPrime listing page, Google snippet, news aggregator), the claim can only PASS when `verified_url` points to the original procurement portal or primary source — not the aggregator. The `discovered_via_url` field provides traceability but has no bearing on PASS/FAIL.

### Ledger Rules

1. Every claim in the deliverable body AND cover note MUST have a ledger row
2. Claims without a ledger row are excluded from output — no exceptions
3. Only claims with `status = PASS` enter the deliverable
4. The ledger is saved alongside the deliverable (internal use only, not shown to prospect)

---

## Claim Budget

High-specificity claims (dates, dollar amounts, entity attributions) are the highest-risk elements. Budget them:

| Location | Budget | Rationale |
|----------|--------|-----------|
| **Cover note** | Max 1–2 high-specificity claims | The hook should survive on the deliverable's value alone — don't front-load fragile facts. Prefer Static claims over Dynamic |
| **Deliverable body** | Cap Dynamic claims at ~3 | If the deliverable needs more than 3 time-sensitive data points, it probably needs a Structured data source (APIs, not web search) |
| **Static claims** | No budget | Stable facts with verified sources can be used freely |

---

## STOP_OUTPUT Policy

If a core claim fails verification, do NOT ship a weakened deliverable. Instead:

1. Output `STATUS: NEEDS_MORE_RESEARCH` at the top of the deliverable
2. Include the Verification Ledger showing which claims failed and why
3. Include whatever verified content exists (it's partial progress, not wasted)
4. The operator decides: find better sources, switch to a different PVP type, or skip this prospect

### STOP Triggers

| Condition | Why It Stops |
|-----------|-------------|
| Cover note hook claim status ≠ PASS | The deliverable has no opening |
| >50% of Dynamic claims FAIL | The data is too stale to be useful |
| Primary Attributed claim FAIL | Entity trust is broken |
| Any cover note claim missing from ledger or status ≠ PASS | **Hard invariant** — cover note claims must be a strict subset of ledger rows with status=PASS |

### Non-STOP Conditions

| Condition | What Happens |
|-----------|-------------|
| A single Static claim fails | Remove the claim and continue. Do NOT stop |
| A non-core Dynamic claim fails | Remove the claim and continue, unless it pushes >50% failure |

### Deterministic Definition of "Primary Attributed Claim"

1. If the cover note hook claim is Attributed → that is the primary Attributed claim
2. Else if the deliverable's thesis section contains Attributed claims → the first one is primary
3. Else → no primary Attributed claim exists, and the Attributed STOP rule does not apply

---

## Failure Propagation Rule

A claim that fails verification at ANY tier **never enters prospect-facing output.** Not as a footnote, not with a caveat, not with "approximately." Failed = excluded. The PVP must still work without that claim — if removing it breaks the deliverable's thesis, the deliverable needs more research, not a lower verification bar.

---

## Downstream Consumers

- **Module 3B (PVP Generator):** Step 3b runs the claim verification pass using this contract's rules. Step 6 quality gates enforce ledger completeness, claim budget, and STOP_OUTPUT conditions.
- **Module 3C (Execution Blueprint):** Pre-Send QA protocol references claim tiers when telling operators which claims to spot-check first (Dynamic and Attributed claims get priority).
- **Operator QA:** The Verification Ledger gives operators a checklist — click each `verified_url`, confirm the data still matches.
