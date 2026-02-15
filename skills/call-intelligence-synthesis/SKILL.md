---
name: call-intelligence-synthesis
description: >
  Synthesize patterns across multiple sales call extractions into actionable intelligence. Use this
  skill when the user wants to analyze call data in aggregate, find patterns across discovery calls,
  build a buyer language map, or produce a Call Intelligence Synthesis Report from Module 2A extraction
  data. Trigger when the user says "synthesize calls", "analyze call data", "what are buyers saying",
  "run call synthesis", "buyer language map", or provides Module 2A extraction output for analysis.
---

# GTM Alpha: Call Intelligence Synthesis

## What This Skill Does

Takes aggregated call extraction data (JSON output from Module 2A across 15+ calls) and produces a 7-section Call Intelligence Synthesis Report that maps:
- Signal validation patterns — which triggers appear in real calls and how often
- Buyer language map — the exact vocabulary buyers use (pain, urgency, aspiration, indigenous terminology)
- Problem framing analysis — how buyers describe their problems in their own words
- Alternative attempts and objection patterns — what they've tried, why it failed, trust barriers
- Engagement moment analysis — what makes buyers lean in vs. lean out
- Key insights — the most surprising finding, strongest trigger, biggest engagement killer

This is Module 2B in the GTM Alpha system. Its output feeds into Module 2C (Reconciliation).

**Input:** JSON array of Module 2A call extractions (15+ calls, `extraction_status` = `"complete"`)
**Output:** 7-section Call Intelligence Synthesis Report

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Call extraction data** (required) — Either:
   - Paste the JSON array directly
   - Provide a file path to the extraction data
   - Provide a Clay table export (CSV/JSON)
2. **Target domain** (optional) — Which vendor's calls these are for (helps with context)

If the user already provided data in their message, skip the questions and proceed.

### Step 2: Data Validation

Before synthesis, validate the input:

1. **Format check:** Verify input is a JSON array of Module 2A extraction objects
2. **Status filter:** Only include entries where `extraction_status` = `"complete"`. Count and report excluded entries.
3. **Tier 2 inventory:** Count how many calls have `tier_2_extracted` = `true` vs `false`. Report the split.
4. **Minimum count:** Require 15+ complete calls. If fewer:
   - 10-14 calls: Proceed but flag as "Below recommended minimum. Patterns may not be representative."
   - <10 calls: Decline. Tell the user: "Need at least 10 complete call extractions for meaningful synthesis. You have [N]."

### Step 3: Generate Synthesis

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the analysis framework.

Follow the template exactly — every section, every table, every quality check. The 7 sections are:
1. Dataset Overview
2. Signal Validation Patterns
3. Buyer Language Map
4. Problem Framing Analysis
5. Alternative Attempts & Objection Patterns
6. Engagement Moment Analysis
7. Key Insights Summary

### Step 4: Quality Gates

Before finalizing, verify:
- [ ] Every pattern includes sample size (N calls)
- [ ] Patterns appearing in <15% of calls are flagged as "emerging, needs validation"
- [ ] Patterns primarily supported by low-quality calls are flagged (when Tier 2 call quality data available)
- [ ] Tier 2-dependent sections note the reduced sample size
- [ ] Buyer quotes are used liberally — their words, not summaries
- [ ] Each section ends with an actionable implication
- [ ] Surprising or contradictory findings are highlighted, not buried
- [ ] Contradictions & Tensions in section 7 is populated
- [ ] All enum values match the Module 2A schema exactly (see `contracts/call-extraction-schema.md`)

### Step 5: Deliver

Save the synthesis report as `{domain}-call-synthesis-{date}.md` and present to the user.

Tell the user this output feeds into Module 2C (Reconciliation) — they should run it alongside the Module 1A brief to produce the calibrated intelligence.

## Anti-Patterns

- **Reporting patterns without sample sizes.** Every claim needs an N.
- **Paraphrasing when you can quote.** Buyer language is the product. Use their words.
- **Treating all patterns as equally valid.** Weight by frequency, intensity, and call quality.
- **Burying contradictory evidence.** Surface it in section 7 — contradictions are where insights live.
- **Computing correlations.** Report co-occurrences instead. LLMs are bad at statistics.
- **Assuming Tier 2 fields exist on all calls.** Check `tier_2_extracted` before aggregating Tier 2 data.
