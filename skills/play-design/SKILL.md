---
name: play-design
description: >
  Design executable outbound plays with signal detection, A/B variants, and PVP specs. Use this skill
  when the user wants to build outbound plays, create email sequences, design signal-triggered campaigns,
  or turn intelligence into executable outbound. Trigger when the user says "design plays", "build
  outbound plays", "create plays for [domain]", "run play design", "what plays should we run", or
  provides Module 1A or Module 2C output and wants to create executable campaigns.
---

# GTM Alpha: Play Design & PVP Architecture

## What This Skill Does

Takes intelligence input (from Module 1A or Module 2C) and produces 3 executable GTM plays with:
- Signal detection methods — copy-pasteable Clay/Sales Nav filters to find accounts NOW
- Target account list specs — ICP + signal + persona + disqualifiers = runnable query
- A/B variants — 2 subject lines + 2 opening lines per play (mandatory)
- Objection handling frameworks with response strategies
- Outcome tracking fields with numeric targets and performance floors
- PVP specs — detailed enough for Module 3B to produce the actual deliverable
- Play-PVP pairing matrix — deployment timing for each play-PVP combination

This is Module 3A in the GTM Alpha system. It bridges intelligence (Layers 1-2) and execution (sending outbound).

**Input:** Module 1A output (Fast Path) or Module 2C output (Full Path, preferred)
**Output:** 3 executable plays + 3 PVP specs + pairing matrix

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Intelligence input** (required) — Either:
   - Module 1A output (Strategic Brief) — Fast Path, no call data
   - Module 2C output (Reconciliation with Re-Scored Signal List) — Full Path, preferred
2. **Target domain** (required) — Which vendor's plays to design

If the user already provided input in their message, skip the questions and proceed.

### Step 2: Analyze Intelligence

Before designing plays, extract the key inputs:

1. **Signal priority:** If Module 2C input, use the Re-Scored Signal List ranking. If Module 1A input, use composite scores from the Signal Scorecard.
2. **Buyer language:** If Module 2C input, note the Language Calibration Map for messaging. If Module 1A input, use buyer topology language.
3. **Objection landscape:** If Module 2C input, use the Objection Reality Check. If Module 1A input, use hypothesized objections from buyer personas.
4. **Detection specs:** Pull from Module 1A signal detection specs for Signal Detection Method in each play.

### Step 3: Design Plays

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the design framework.

Follow the template exactly. Each play includes:
- Strategic Thesis
- Trigger Conditions (with signal validation status if Module 2C)
- Signal Detection Method (data source, query, cadence, volume)
- Target Account List Spec (ICP + signal + persona + disqualifiers)
- Target Persona
- Messaging Architecture
- Email Template (with A/B variants — 2 subject lines, 2 openings, mandatory)
- Objection Handling Framework
- A/B Test Plan (sample sizes, when to call winner)
- Outcome Tracking (targets + performance floor)
- Paired PVP reference

Then design 3 PVP specs and the Play-PVP Pairing Matrix.

### Step 4: Quality Gates

Before finalizing, verify:
- [ ] Each play has a Signal Detection Method with a copy-pasteable query
- [ ] Each play has a Target Account List Spec with estimated monthly volume
- [ ] Each play has 2 subject line variants and 2 opening line variants
- [ ] Each play has outcome tracking with specific numeric targets
- [ ] Each play has a performance floor (kill threshold)
- [ ] Email templates include specific personalization variables
- [ ] No play could be sent by a competitor without modification
- [ ] Each PVP spec is detailed enough for Module 3B to produce the deliverable
- [ ] Play-PVP Pairing Matrix is complete with deployment timing
- [ ] If using Module 2C: plays built around highest-scoring signals, buyer language used in copy

### Step 5: Deliver

Save the play design as `{domain}-play-design-{date}.md` and present to the user.

Tell the user:
- PVP specs can be run through Module 3B (PVP Generator) to produce actual deliverables
- Outcome tracking fields should be logged in Module 4A (Outcome Logger) as they run plays
- After 50+ touches, run Module 4B (Signal Performance Updater) to close the learning loop

## Anti-Patterns

- **Plays that are just "signal + generic pitch."** Every play needs a strategic thesis.
- **Emails starting with flattery or congratulations.** Pattern interrupt, not flattery.
- **PVP specs that are marketing collateral in disguise.** PVPs must be valuable independent of the sale.
- **Skipping Signal Detection Method or Target Account List Spec.** A play without a way to find accounts is not a play.
- **Skipping A/B variants.** The system needs variation to learn.
- **Skipping outcome tracking.** A play without measurable targets cannot be improved.
- **Ignoring the Language Calibration Map.** When Module 2C input is available, buyer language always beats your language.
- **Building plays on unvalidated signals when validated alternatives exist.**
