---
name: play-design
description: >
  Design executable outbound plays with signal detection, A/B variants, and inline PVP specs. Use this skill
  when the user wants to build outbound plays, create email sequences, design signal-triggered campaigns,
  or turn intelligence into executable outbound. Trigger when the user says "design plays", "build
  outbound plays", "create plays for [domain]", "run play design", "what plays should we run", or
  provides a Research Brief or Reconciliation output and wants to create executable campaigns.
---

# GTM Alpha: Play Design & PVP Architecture

## What This Skill Does

Takes intelligence input (Research Brief or Reconciliation output) and produces 3 executable GTM plays, each with:
- Strategic thesis — why this play wins
- Trigger conditions — what signals fire this play
- How to find accounts — plain-language detection approach + volume estimate
- Target accounts and persona — who to reach and why now
- Messaging architecture + email templates with A/B variants
- Objection handling with response approaches
- Inline PVP spec — what the PVP Generator will produce as a deliverable
- Portfolio summary — deployment matrix, confidence assessment, open questions

**Input:** Research Brief (Fast Path) or Reconciliation output (Full Path, preferred)
**Output:** 3 plays + 3 inline PVP specs + portfolio summary — client-facing document

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Intelligence input** (required) — Either Research Brief or Reconciliation output
2. **Target domain** (required) — Which vendor's plays to design

**Input resolution (in priority order):**
1. If the user already provided intelligence input in their message, use it and proceed.
2. If the user provides just a domain, check for existing runs:
   - Look for `runs/{domain}/reconciliation-*.md` first (Full Path, preferred — use most recent by date in filename)
   - If not found, look for `runs/{domain}/brief.md` (Fast Path)
   - If found:
     - Check the file's last-modified date. If older than 30 days, warn: *"The brief for {domain} was last updated on [date]. Signals may be stale. Want to re-run upstream first, or proceed with the existing one?"*
     - For `brief.md`: check the first 20 lines for the "Unvalidated" marker. If found, warn: *"The existing brief was generated without search validation. Recommend re-running before using it as input."*
     - If multiple dated files exist, use the most recent. Tell the user: *"Found [filename] (last updated [date]) — using this. Say 'use the earlier one' if that's wrong."*
     - Read the file and use it as input. Tell the user: *"Using [filename] (last updated [date])."*
3. If neither is provided nor found, ask the user to provide the intelligence input.

### Step 2: Analyze Intelligence

Before designing plays, extract the key inputs:

1. **Signal priority:** If Full Path input, use the Re-Scored Signal List ranking. If Fast Path, use composite scores from the Signal Scorecard.
2. **Buyer language:** If Full Path input, note the Language Calibration Map for messaging. If Fast Path, use buyer topology language.
3. **Objection landscape:** If Full Path input, use the Objection Reality Check. If Fast Path, use hypothesized objections from buyer personas.
4. **Detection specs:** Pull from Research Brief signal detection specs to inform "How to Find These Accounts."

### Step 3: Design Plays

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the design framework.

Follow the template exactly. Each play includes:
1. Strategic Thesis
2. Trigger Conditions (signal descriptions in plain English, no composite scores)
3. How to Find These Accounts (one sentence + volume estimate — no query syntax or tool details)
4. Target Accounts (simplified table)
5. Target Persona
6. Messaging Architecture
7. Email Template (subject line A/B table + two body variants + personalization variables)
8. Objection Handling (3-column table, one-word evidence tags)
9. PVP spec inline (The Deliverable, Why It's Permissionless, Data Required, Delivery Format)

Then produce the Portfolio Summary: Deployment Matrix, Portfolio Assessment, Open Questions.

### Step 4: Quality Gates

Before finalizing, verify:

**Play quality:**
- [ ] Each play has a clear strategic thesis — not just "target people who show these signals"
- [ ] Each play has a "How to Find These Accounts" section with a volume estimate
- [ ] Each play has a Target Accounts table
- [ ] Each play has 2 subject line variants and 2 opening line variants — no exceptions
- [ ] Email templates include specific personalization variables
- [ ] No play could be sent by a competitor without modification
- [ ] Each play includes objection handling for 2-3 likely objections

**PVP quality:**
- [ ] Each play has a PVP spec inline (not a separate section or just a reference)
- [ ] Each PVP spec is detailed enough for the PVP Generator to produce the deliverable without asking questions

**Client-facing compliance (CLAUDE.md rules):**
- [ ] No module numbers or system notation anywhere in the output
- [ ] No composite scores — signal confidence described in plain English
- [ ] No unsourced KPI targets (no "target: 55% open rate")
- [ ] No operator logistics (no Clay queries, enrichment steps, tool costs, campaign config)
- [ ] No `[H]` markers — unverified claims use plain language ("estimated based on...") or are omitted
- [ ] Plays connect directly to signals from the Intelligence Input

**Additional checks if using Full Path input:**
- [ ] Plays are built around the highest-scoring signals from the Re-Scored Signal List
- [ ] Email copy incorporates specific phrases from the Language Calibration Map
- [ ] Objection handling references proven responses from calls where available

### Step 5: Deliver

Save the play design as `runs/{domain}/2-play-design-{date}.md` (create the directory if it doesn't exist) and present to the user.

Tell the user:
- PVP specs can be run through the PVP Generator to produce actual deliverables for specific prospects
- The operator checklist (separate document) covers signal detection queries, enrichment steps, A/B test execution, and campaign configuration

## Anti-Patterns

- **Plays that are just "signal + generic pitch."** Every play needs a strategic thesis.
- **Emails starting with flattery or congratulations.** Pattern interrupt, not flattery.
- **PVP specs that are marketing collateral in disguise.** PVPs must be valuable independent of the sale.
- **Including operator logistics in the output.** Clay queries, enrichment waterfalls, tool costs, A/B test execution details, and campaign configuration belong in the operator checklist — not the play design.
- **Using module numbers or system notation.** This is a client-facing document. No "Module 3B", no `[H]`, no composite scores.
- **Unsourced KPI targets.** No "target: 55% open rate" or "expect 8% reply rate." State what you'll track, not what you'll hit.
- **Ignoring the Language Calibration Map.** When Full Path input is available, buyer language always beats your language.
- **Building plays on unvalidated signals when validated alternatives exist.**
