---
name: execution-blueprint
description: >
  Generate an operator checklist from play design output. Use this skill when the user wants to
  operationalize plays, build campaign infrastructure, set up sending, or translate play designs into
  step-by-step execution setup. Trigger when the user says "build an operator checklist", "how do I execute
  these plays", "set up Clay for [domain]", "operationalize plays for [domain]", "run operator checklist",
  or has play design output and wants to know exactly what to build.
---

# GTM Alpha: Operator Checklist

## What This Skill Does

Takes play design output and intelligence brief and produces a per-vendor operator checklist covering:
- ICP classification and sourcing approach per play
- Signal detection setup with exact queries and filters (absorbed from play design)
- Enrichment waterfall and contact finding sequence
- Sending infrastructure: domains, warm-up, sequence config, cold email hygiene
- PVP production: scalability tier, data source type, production workflow, research steps
- Pre-Send QA protocol by PVP tier
- Tool stack and cost summary

The key differentiator: **ICP type determines everything.** A blue collar contractor ICP needs Google Maps + Apify, not LinkedIn Sales Nav. An enterprise ICP needs Blitz API, not Google Maps. This skill reads the ICP Sourcing Matrix to match the right tools to the right ICP.

This is Module 3C in the GTM Alpha system. It sits in Layer 3 (Execution) alongside Modules 3A and 3B.

**Input:** Play design output + intelligence brief + operator's tool stack
**Output:** Operator checklist ready to build in one work session

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **Play design output** (required) — The play design for the target vendor
2. **Intelligence brief** (required) — The brief for the target vendor
3. **Target domain** (required) — Which vendor's checklist to build
4. **Operator's tool stack** (required) — What tools they already have. Ask specifically:
   - Sourcing: Clay? LinkedIn Sales Nav? Apollo? ZoomInfo?
   - Sending: Smartlead? Instantly? Lemlist? Apollo?
   - Budget: What's the monthly budget for tools?

If the user already provided input in their message, skip the questions and proceed. If play design or brief output exists in `runs/{domain}/`, read it directly.

### Step 2: Classify the ICP

Read the ICP Sourcing Matrix at `references/ICP_SOURCING_MATRIX.md`.

For each play in the play design output:
1. Look at the Target Account section and Target Persona
2. Classify the ICP into one of the four profiles: Blue Collar/SMB, Mid-Market, Enterprise, Niche Vertical
3. Determine the sourcing pipeline and enrichment waterfall from the matrix

**If plays target different ICP profiles** (e.g., Play 1 targets SMB owners, Play 2 targets enterprise VPs), document separate sourcing pipelines. Do not force a single pipeline.

**If plays share an ICP profile**, note "shared pipeline" and only document differences (signal monitoring, persona filter).

### Step 3: Map Tool Stack to Needs

Compare the operator's current tools against what the ICP Sourcing Matrix recommends:
- **Have it:** Tool is available — use it
- **Need it:** Tool is required but not available — flag it with cost
- **Alternative:** If a cheaper/simpler alternative exists for the operator's volume, suggest it

Calculate total monthly cost for the recommended stack.

### Step 4: Build the Checklist

Read the prompt template at `references/PROMPT_TEMPLATE.md` and use it as the output framework.

Build all 7 sections with specifics pulled from:
- **Play design** for: play names, PVP specs (data source type, scalability tier from inline spec), messaging architecture, A/B variants, signal descriptions
- **Intelligence brief** for: ICP details, buyer personas, signal hypotheses, detection specs (query syntax, filter logic, data sources)
- **ICP Sourcing Matrix** for: sourcing tools, enrichment waterfalls, expected hit rates, costs
- **Operator's tool stack** for: which tools to configure, which integrations to set up

**Key absorption from play design:** The play design is a client-facing document and intentionally omits operator detail. This checklist fills in:
- Signal detection: exact query syntax, filter parameters, tool-specific settings (from brief detection specs)
- Enrichment: Clay column order, enrichment names, credit estimates
- A/B testing: when to call winner, what to do with loser variant
- PVP production: scalability tier, data source type, research steps for PVP Generator, effort level

### Step 5: Quality Gates

Before finalizing, verify:
- [ ] Every play has been classified into an ICP profile
- [ ] Sourcing tools match the ICP profile (no LinkedIn for blue collar, no Google Maps for enterprise)
- [ ] Enrichment waterfall specifies order (which tool runs first, what feeds what)
- [ ] Signal detection table includes exact query/filter logic for every signal
- [ ] Sending configuration includes warm-up plan and all cold email hygiene rules
- [ ] Every PVP has a scalability tier, data source type, and production workflow
- [ ] Pre-Send QA protocol is included with tier-specific checklists
- [ ] Tool stack table includes every tool with status and cost
- [ ] The checklist is specific enough to build in one work session — no "determine the best approach" steps
- [ ] All paid tools include monthly cost estimates
- [ ] If plays share an ICP profile, shared pipeline is noted and only differences documented

### Step 6: Deliver

Save the checklist as `runs/{domain}/3-operator-checklist-{date}.md` (create the directory if it doesn't exist) and present to the user.

Tell the user:
- This checklist is ready to build — start with Section 4 (sending setup) since warm-up takes 2 weeks
- While warming up, set up enrichment (Section 3) and start sourcing (Section 1)
- PVP production (Section 5) can start immediately for Batch-tier PVPs
- Pre-Send QA (Section 6) runs before every PVP send — read the tier-specific checklist
- PVP specs from the play design can be run through the PVP Generator to produce actual deliverables for specific prospects

## Anti-Patterns

- **Wrong tools for the ICP.** LinkedIn Sales Nav for landscapers is a waste. Google Maps for enterprise is a waste. Match tools to ICP.
- **Missing enrichment order.** "Enrich with Clay" is not actionable. Specify: step 1 = company enrichment via X, step 2 = contact find via Y, step 3 = email verify via Z.
- **5+ step email sequences.** Start with 2 steps. Add more only when data shows it helps. Most replies come from steps 1-2.
- **Skipping the warm-up plan.** New sending domains without warm-up go straight to spam. This is non-negotiable.
- **Checklists that read like strategy docs.** This is an operations document. Every line should map to something the operator builds or configures.
- **Ignoring the operator's existing tools.** Don't recommend a $600/mo tool when they already have a $49/mo tool that covers 80% of the need.
- **Including outcome tracking or weekly schedules.** The operator manages their own time and tracking. This checklist covers what to build, not how to spend your week.
