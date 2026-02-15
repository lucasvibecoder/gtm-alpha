# Performance Update Contract

**Version:** v2.0
**Role:** Defines the output format for Module 4B (Signal Performance Updater)
**Status:** Active

---

## Purpose

Module 4B reads the outcome log (50+ entries), analyzes performance patterns, and produces a Performance Update report. This report feeds back into Module 1A signal scores for the next brief refresh, making the system compound over time.

---

## Input Requirements

- **Minimum entries:** 50 outcome log entries (per `contracts/outcome-log.md` schema)
- **Recommended:** 100+ entries for statistically meaningful variant analysis
- **Required fields per entry:** account, play, signal (name + composite score), messaging variants, outcome result

---

## Output Structure

The Performance Update report has 5 sections. All sections are required.

### Section 1: Signal Performance Scorecard

For each signal that appeared in the outcome log:

| Signal Name | Entries | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommended Composite | Change | Action |
|-------------|---------|------------|---------------------|--------------|-------------------|-----------------------|--------|--------|
| [Signal] | [N] | [%] | [%] | [%] | [n.nn] | [n.nn] | [+/- n.nn] | [Keep / Boost / Demote / Kill] |

**Score Update Rules:**

| Condition | Action |
|-----------|--------|
| Meeting rate > 5% AND positive reply rate > 15% | Boost: +0.5 to Specificity and Actionability (cap at 5) |
| Positive reply rate 8-15% | Keep: no score change, signal is performing adequately |
| Positive reply rate 3-8% | Demote: -0.5 to Specificity (floor at 1) |
| Positive reply rate < 3% after 20+ sends | Kill: zero the composite score |
| Insufficient data (< 10 sends for this signal) | Flag: "Insufficient data — do not adjust score" |

**Action Definitions:**
- **Keep:** Signal score unchanged. Continue using.
- **Boost:** Increase composite score per the rule above. Recalculate using Signal Scorecard contract formula.
- **Demote:** Decrease composite score per the rule above. If new composite < 2.5, escalate to Kill.
- **Kill:** Zero the composite score. Remove from active plays. List in "Signals to Kill" section.

### Section 2: Messaging Variant Performance

For each A/B test across all plays:

| Play | Test Element | Variant A | Variant B | Variant A Rate | Variant B Rate | Winner | Confidence |
|------|-------------|-----------|-----------|----------------|----------------|--------|------------|
| [Play name] | Subject Line | [Subject A text] | [Subject B text] | [Open rate %] | [Open rate %] | [A / B / No Winner] | [High / Low / Insufficient Data] |
| [Play name] | Opening Line | [Opening A text] | [Opening B text] | [Reply rate %] | [Reply rate %] | [A / B / No Winner] | |

**Confidence Criteria:**
- **High:** 50+ sends per variant with >2 percentage point difference
- **Low:** 20-50 sends per variant, or <2 percentage point difference
- **Insufficient Data:** <20 sends per variant — do not declare a winner

**Recommendations:**
[For each test with a High-confidence winner: "Use [Winner] as the new default. Create a new Variant B challenger for the next cycle."]

### Section 3: Signals to Kill

Signals that consistently produce zero or near-zero results.

| Signal Name | Total Sends | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommendation |
|-------------|-------------|------------|---------------------|--------------|-------------------|----------------|
| [Signal] | [N] | [%] | [%] | [%] | [n.nn] | Kill — [one sentence explaining why] |

**Kill Criteria:**
- Positive reply rate < 3% after 20+ sends
- OR meeting rate = 0% after 30+ sends
- OR reply sentiment is predominantly Negative

### Section 4: Signals to Promote

Signals that consistently outperform expectations.

| Signal Name | Total Sends | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommended Composite | Recommendation |
|-------------|-------------|------------|---------------------|--------------|-------------------|-----------------------|----------------|
| [Signal] | [N] | [%] | [%] | [%] | [n.nn] | [n.nn] | Boost — [one sentence explaining why] |

**Promote Criteria:**
- Meeting rate > 5% after 15+ sends
- OR positive reply rate > 20% after 20+ sends
- OR significantly outperforms other signals in the same play portfolio

### Section 5: Emerging Patterns

Mine the `notes` field and outcome patterns for intelligence that doesn't fit the structured analysis above.

**Emerging Signal Candidates:**
[Patterns in the notes field that suggest new signals worth testing. Format: "N entries mention [pattern] — consider adding as a signal hypothesis."]

**Persona Insights:**
[Are certain industries, company sizes, or roles responding at notably different rates? Report co-occurrences, not correlations.]

**Timing Insights:**
[Are certain days of week, times, or seasonal patterns visible in response rates? Report observations.]

**PVP Performance:**
[Which PVPs generated the most engagement? Which PVP deployment timing (cold opener vs. follow-up vs. re-engagement) performed best?]

**Recommendations for Next Cycle:**
[2-3 specific actions for the operator based on the emerging patterns. Each must cite specific data from the outcome log.]

---

## Output Routing

The Performance Update output feeds into:

| Consumer | What It Uses |
|----------|-------------|
| **Module 1A (next brief refresh)** | Updated composite scores from §1 — overwrite the original signal scores |
| **Module 3A (play redesign)** | Variant winners from §2 — update email templates. Kill/promote signals from §3-4 — rebuild plays |
| **Module 4C (Pattern Library)** | Emerging patterns from §5 — add to cross-client pattern library |
| **Operator** | Full report — use for strategic decisions about play portfolio |

---

## Quality Rules

- Do not compute statistical significance — report sample sizes and let the operator judge
- Do not report percentages without the raw count (N) alongside them — "50% reply rate" on 2 sends is meaningless
- Do not recommend killing a signal with fewer than 20 sends — flag as "Insufficient data" instead
- Do not recommend promoting a signal based solely on one metric — require outperformance on at least 2 metrics (reply rate + meeting rate, or reply rate + sentiment)
- Always note when a pattern is based on a small sample — "Based on [N] entries, which is below the recommended minimum of 50"
