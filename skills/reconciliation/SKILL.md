---
name: signal-reconciliation
description: >
  Reconcile research hypotheses against call evidence to produce calibrated intelligence. Use this
  skill when the user wants to validate signal hypotheses against call data, update signal scores
  based on buyer evidence, compare what we predicted vs. what buyers actually said, or produce a
  Calibrated Intelligence Brief. Trigger when the user says "reconcile signals", "validate hypotheses",
  "calibrate the brief", "compare brief to calls", "run reconciliation", or provides both a Module 1A
  brief and Module 2B synthesis for comparison.
---

# GTM Alpha: Signal & Messaging Reconciliation

## What This Skill Does

Takes a Strategic Intelligence Brief (Module 1A) and a Call Intelligence Synthesis (Module 2B) and produces a 5-section reconciliation that:
- Validates each signal hypothesis against call evidence (with updated composite scores)
- Calibrates messaging language — replaces our words with buyer words
- Identifies missed signals — patterns from calls not in our original hypotheses
- Reality-checks objections — what we guessed vs. what buyers actually said
- Produces calibrated recommendations grounded in call evidence
- Outputs a Re-Scored Signal List ranked by updated composite — directly consumed by Module 3A

This is Module 2C in the GTM Alpha system. It bridges Layer 1 (Research) and Layer 3 (Execution).

**Input:** Module 1A output (Strategic Brief) + Module 2B output (Call Synthesis)
**Output:** 5-section reconciliation + Re-Scored Signal List

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Strategic Intelligence Brief** (required) — The full output from Module 1A
2. **Call Intelligence Synthesis** (required) — The full output from Module 2B
3. **Target domain** (required) — Which vendor this is for

If the user already provided both documents in their message, skip the questions and proceed.

### Step 2: Input Validation

Before reconciliation, validate:

1. **Module 1A check:** Verify the brief contains signal hypotheses with composite scores (Section 5 of the brief). If scores are missing, warn: "Brief does not contain signal scores. Reconciliation will produce verdicts but cannot update composite scores."
2. **Module 2B check:** Verify the synthesis contains the 7 required sections. If incomplete, warn about which sections are missing and how that limits the reconciliation.

### Step 3: Generate Reconciliation

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the analysis framework.

Follow the template exactly. The 5 sections + Re-Scored Signal List are:
1. Signal Validation Scorecard (with score updates per Signal Scorecard contract)
2. Language Calibration Map (word-swap table + high-value phrases)
3. Missed Signals (with provisional scoring)
4. Objection Reality Check (hypothesized vs. actual + what worked/failed)
5. Calibrated Recommendations (evidence-based, "Do X instead of Y because [evidence]")
6. Re-Scored Signal List (ranked output for Module 3A)

**Score Update Rules:**
- Validated signals: Boost Specificity +1 (cap at 5). Boost Timing Precision +1 if call evidence reveals tighter windows.
- Partially Validated: Adjust individual dimensions with rationale if evidence supports it.
- Unvalidated: No score change.
- Contradicted: Zero the composite score (0.00).

### Step 4: Quality Gates

Before finalizing, verify:
- [ ] Every signal from Module 1A has been assessed with a clear verdict
- [ ] Composite scores are updated per the Score Update Rules — math is correct
- [ ] At least 3 missed signals identified (if call data is rich enough)
- [ ] Language calibration includes 5+ direct buyer quotes that replace our language
- [ ] Every hypothesized objection checked against call reality
- [ ] At least 2 unexpected objections identified with recommended responses
- [ ] Every recommendation cites specific call evidence with quotes
- [ ] Re-Scored Signal List is complete, ranked, and directly consumable by Module 3A

### Step 5: Deliver

Save the reconciliation as `runs/{domain}/reconciliation-{date}.md` (create the directory if it doesn't exist) and present to the user.

Tell the user this output feeds into Module 3A (Play Design) — they should use the Re-Scored Signal List as the signal priority order for play design.

## Anti-Patterns

- **Validating signals because they "make sense."** Require evidence from calls.
- **Ignoring contradictory evidence.** If calls say we were wrong, say so and zero the score.
- **Paraphrasing buyer language.** Use exact quotes from Module 2B.
- **Skipping missed signals analysis.** This is where the real insights live.
- **Generic recommendations.** Every recommendation must cite specific call evidence.
- **Repeating upstream content.** Buyer psychology, alternative attempts, and decision ecosystem live in Module 2B — do not duplicate them here.
