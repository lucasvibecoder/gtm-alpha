---
name: execution-blueprint
description: >
  Generate an operational execution blueprint from play design output. Use this skill when the user wants to
  operationalize plays, build Clay tables, set up sending infrastructure, or translate play designs into
  step-by-step execution plans. Trigger when the user says "build an execution blueprint", "how do I execute
  these plays", "set up Clay for [domain]", "operationalize plays for [domain]", "run execution blueprint",
  or has Module 3A output and wants to know exactly what to build.
---

# GTM Alpha: Execution Blueprint

## What This Skill Does

Takes play design output (Module 3A) and intelligence brief (Module 1A) and produces a per-vendor operational blueprint covering:
- How to find prospects (sourcing strategy matched to ICP type)
- What to build in Clay (exact table structure, columns, enrichment waterfall)
- How to send (tool config, warm-up, sequence setup, cold email hygiene)
- How to produce PVPs at scale (batch vs. template vs. custom classification)
- How to track outcomes (Google Sheet mapped to outcome-log.md)
- How to spend your time (weekly operating rhythm within stated budget)

The key differentiator: **ICP type determines everything.** A blue collar contractor ICP needs Google Maps + Apify, not LinkedIn Sales Nav. An enterprise ICP needs Blitz API, not Google Maps. This skill reads the ICP Sourcing Matrix to match the right tools to the right ICP.

This is Module 3C in the GTM Alpha system. It sits in Layer 3 (Execution) alongside Modules 3A and 3B.

**Input:** Module 3A play design + Module 1A brief + operator's tool stack
**Output:** Operational blueprint ready to build in one work session

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Module 3A play design** (required) — The play design output for the target vendor
2. **Module 1A brief** (required) — The intelligence brief for the target vendor
3. **Target domain** (required) — Which vendor's blueprint to build
4. **Operator's tool stack** (required) — What tools they already have. Ask specifically:
   - Sourcing: Clay? LinkedIn Sales Nav? Apollo? ZoomInfo?
   - Sending: Smartlead? Instantly? Lemlist? Apollo?
   - Tracking: Google Sheets? CRM? What?
   - Budget: What's the monthly budget for tools?
5. **Weekly time budget** (optional, default: 3 hours/week) — How many hours per week the operator can spend on execution

If the user already provided input in their message, skip the questions and proceed. If Module 3A or 1A output exists in `runs/{domain}/`, read it directly.

### Step 2: Classify the ICP

Read the ICP Sourcing Matrix at `references/ICP_SOURCING_MATRIX.md`.

For each play in the Module 3A output:
1. Look at the Target Account List Spec and Target Persona
2. Classify the ICP into one of the four profiles: Blue Collar/SMB, Mid-Market, Enterprise, Niche Vertical
3. Determine the sourcing pipeline and enrichment waterfall from the matrix

**If plays target different ICP profiles** (e.g., Play 1 targets SMB owners, Play 2 targets enterprise VPs), document separate sourcing pipelines. Do not force a single pipeline.

### Step 3: Map Tool Stack to Needs

Compare the operator's current tools against what the ICP Sourcing Matrix recommends:
- **Have it:** Tool is available — use it
- **Need it:** Tool is required but not available — flag it with cost and setup time
- **Alternative:** If a cheaper/simpler alternative exists for the operator's volume, suggest it

Calculate total monthly cost for the recommended stack.

### Step 4: Build the Blueprint

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the output framework.

Build all 7 sections (0-6) with specifics pulled from:
- **Module 3A** for: play names, signal detection methods, email templates, personalization variables, A/B variants, sequence timing, PVP specs, outcome tracking targets
- **Module 1A** for: ICP details, buyer personas, signal hypotheses, detection specs
- **ICP Sourcing Matrix** for: sourcing tools, enrichment waterfalls, expected hit rates, costs
- **Operator's tool stack** for: which tools to configure, which integrations to set up

### Step 5: Classify PVPs

For each PVP from Module 3A, classify into a scalability tier:

| Tier | Criteria | Approach |
|------|----------|----------|
| **Batch** | Same deliverable works for all prospects in a segment (geography, industry). Research once, send many. | Generate once per segment per month. |
| **Template + Enrich** | 80% templatized, 20% per-prospect. Clay can auto-populate the variables. | Build Clay template + short Claude pass per prospect. |
| **Custom** | Deep per-prospect research required. Only valuable for high-value accounts. | Run Module 3B per prospect. Reserve for top accounts. |

**Classification signals:**
- If the PVP spec says "metro-level" or "industry-level" data → Batch
- If the PVP spec has 3+ personalization variables that Clay can fill → Template + Enrich
- If the PVP spec requires "prospect-specific research" or "competitive audit" → Custom

### Step 5b: Include Pre-Send QA Protocol

Add Section 4b (Pre-Send QA Protocol) to the blueprint output. This section defines what the operator checks before any PVP goes out, by scalability tier (Batch, Template+Enrich, Custom). Pull the QA framework from the prompt template.

For each PVP, flag the 3 highest-risk claims to spot-check — prioritize dates, entity attributions, and dollar amounts. Don't leave claim selection to operator judgment.

Include QA time in the Tuesday/Wednesday time block in Section 6 (Weekly Operating Rhythm).

### Step 6: Quality Gates

Before finalizing, verify:
- [ ] Every play has been classified into an ICP profile
- [ ] Sourcing tools match the ICP profile (no LinkedIn for blue collar, no Google Maps for enterprise)
- [ ] Clay table columns include every personalization variable from Module 3A email templates
- [ ] Enrichment waterfall specifies order (which tool runs first, what feeds what)
- [ ] Sending configuration includes warm-up plan and all cold email hygiene rules
- [ ] Every PVP has a scalability tier with a concrete production workflow
- [ ] Section 4b Pre-Send QA protocol is included with tier-specific checklists
- [ ] QA time is included in the Tuesday/Wednesday time block in Section 6
- [ ] The 3 highest-risk claims per PVP type are flagged (dates, entity attributions, dollar amounts)
- [ ] Tracking sheet columns map 1:1 to `contracts/outcome-log.md` schema
- [ ] Weekly rhythm fits within the stated time budget
- [ ] Blueprint is specific enough to build in one work session — no "determine the best approach" steps
- [ ] All paid tools include monthly cost estimates

### Step 7: Deliver

Save the blueprint as `runs/{domain}/execution-blueprint-{date}.md` (create the directory if it doesn't exist) and present to the user.

Tell the user:
- This blueprint is ready to build — start with Section 3 (sending setup) since warm-up takes 2 weeks
- While warming up, build Clay tables (Section 2) and start sourcing (Section 1)
- PVP production (Section 4) can start immediately for Batch-tier PVPs
- Pre-Send QA (Section 4b) runs before every PVP send — read the tier-specific checklist
- Outcome tracking (Section 5) should be set up before the first send
- After 50+ logged touches, run Module 4B (Signal Performance Updater) to close the learning loop

## Anti-Patterns

- **Generic tool recommendations.** "Use a data enrichment tool" is not a blueprint. Name the specific tool, plan, and cost.
- **Wrong tools for the ICP.** LinkedIn Sales Nav for landscapers is a waste. Google Maps for enterprise is a waste. Match tools to ICP.
- **Missing enrichment order.** "Enrich with Clay" is not actionable. Specify: step 1 = company enrichment via X, step 2 = contact find via Y, step 3 = email verify via Z.
- **5+ step email sequences.** Start with 2 steps. Add more only when data shows it helps. Most replies come from steps 1-2.
- **Skipping the warm-up plan.** New sending domains without warm-up go straight to spam. This is non-negotiable.
- **Tracking that takes more than 30 seconds per entry.** If the operator won't use it, it doesn't exist.
- **Blueprints that read like strategy docs.** This is an operations document. Every line should map to something the operator builds or configures.
- **Ignoring the operator's existing tools.** Don't recommend a $600/mo tool when they already have a $49/mo tool that covers 80% of the need.
