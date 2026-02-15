---
name: signal-performance-updater
description: >
  Analyze outbound outcome data and produce updated signal scores, messaging variant performance,
  and kill/promote recommendations. Use this skill when the user wants to update signal scores based
  on outbound results, analyze play performance, identify winning messaging variants, or close the
  learning loop on their GTM plays. Trigger when the user says "update signal scores", "analyze
  outcomes", "run the learning engine", "what's working", "which signals should we kill", or provides
  an outcome log for analysis.
---

# GTM Alpha: Signal Performance Updater

## What This Skill Does

Takes an outcome log (50+ entries of outbound results) and produces a Performance Update report that:
- Updates signal composite scores based on actual conversion data
- Identifies winning A/B messaging variants
- Flags signals to kill (consistently zero results)
- Flags signals to promote (consistently outperform)
- Surfaces emerging patterns from the notes field

This is Module 4B in the GTM Alpha system. It closes the learning loop — making every cycle of outbound smarter than the last.

**Input:** Outcome log (JSON array per `contracts/outcome-log.md` schema), 50+ entries minimum
**Output:** Performance Update report per `contracts/performance-update.md` format

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Outcome log data** (required) — Either:
   - Paste the JSON array directly
   - Provide a file path to the outcome log JSON
   - Paste natural language entries (the skill will structure them first)
2. **Target domain** (required) — Which vendor's plays are being analyzed
3. **Current signal scores** (optional but recommended) — The most recent signal scorecard from Module 1A or Module 2C, so the skill can calculate score changes

If the user provides natural language entries instead of structured JSON, first parse them into the outcome log schema (per `contracts/outcome-log.md`) before proceeding with analysis.

### Step 2: Data Validation

Before analysis, validate the outcome log:

1. **Minimum entry count:** Require 50+ entries. If fewer:
   - 20-49 entries: Proceed but flag every section with "Based on [N] entries — below recommended minimum of 50. Treat all recommendations as preliminary."
   - <20 entries: Decline analysis. Tell the user: "Need at least 20 entries for any meaningful analysis. You have [N]. Keep logging and come back when you hit 50+."

2. **Required fields check:** Every entry must have `account.company_name`, `play.play_name`, `signal.signal_name`, `messaging.subject_line_variant`, `messaging.opening_line_variant`, and `outcome.result`. Flag entries with missing required fields.

3. **Data distribution check:** Report how entries are distributed across plays and signals. If any signal has <10 entries, flag it as "Insufficient data for reliable analysis."

### Step 3: Analyze Performance

Read the output template at `references/PERFORMANCE_UPDATE_TEMPLATE.md` and produce the 5-section Performance Update report.

**Analysis sequence:**

1. **Signal Performance Scorecard (§1)**
   - Group entries by `signal.signal_name`
   - Calculate reply rate, positive reply rate, and meeting rate per signal
   - Compare against current composite scores
   - Apply the Score Update Rules from `contracts/performance-update.md`
   - Recalculate composite scores using the formula from `contracts/signal-scorecard.md`

2. **Messaging Variant Performance (§2)**
   - Group entries by play × variant combination
   - Calculate open rate (if available) and reply rate per variant
   - Declare winners only when confidence criteria are met (50+ sends per variant, >2pp difference)
   - Recommend new challengers for the next cycle

3. **Signals to Kill (§3)**
   - Identify signals with positive reply rate < 3% after 20+ sends
   - Or meeting rate = 0% after 30+ sends
   - Or predominantly negative reply sentiment
   - Recommend zeroing their composite scores

4. **Signals to Promote (§4)**
   - Identify signals with meeting rate > 5% after 15+ sends
   - Or positive reply rate > 20% after 20+ sends
   - Recommend score boosts per the contract rules

5. **Emerging Patterns (§5)**
   - Mine the `notes` field for recurring themes
   - Look for industry, company size, or role patterns in response rates
   - Look for timing patterns (day of week, seasonality)
   - Analyze PVP performance by type and deployment timing
   - Produce 2-3 specific recommendations for the next cycle

### Step 4: Quality Gates

Before finalizing, verify:
- [ ] Every signal with 10+ entries has a performance assessment
- [ ] No score update recommendation is made on fewer than 10 sends
- [ ] Every percentage is accompanied by the raw count (N)
- [ ] Variant winners are only declared when confidence criteria are met
- [ ] Kill recommendations require 20+ sends with poor performance — not just small samples
- [ ] Promote recommendations require outperformance on at least 2 metrics
- [ ] Emerging patterns cite specific data from the log, not general observations
- [ ] The report notes any signals or plays with insufficient data

### Step 5: Deliver

Save the Performance Update report as:
- **Markdown** (default): `{domain}-performance-update-{date}.md`
- Present updated signal scores as a table the user can use to update their Module 1A brief

Tell the user:
1. Which signals to update in their next brief refresh (with new composite scores)
2. Which email variants to promote to default (with new challengers to test)
3. Which signals to remove from active plays
4. What to watch for in the next cycle

## Anti-Patterns

- **Declaring winners on small samples.** 10 sends is not enough data. Flag it, don't decide on it.
- **Computing correlations.** LLMs are bad at statistics. Report co-occurrences and counts, not correlations or statistical significance.
- **Ignoring the notes field.** This is where the most interesting emerging signals hide. Always mine it.
- **Recommending score changes without citing data.** Every score update must reference the specific reply rate, meeting rate, and send count that justifies it.
- **Killing signals too early.** 20 sends minimum before a kill recommendation. The signal might be fine — the messaging might be the problem.
