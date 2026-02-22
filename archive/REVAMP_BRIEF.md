# GTM Alpha — Revamp Brief

**Created:** 2026-02-20
**Last updated:** 2026-02-21 (status tracker added after Sessions 1-3 completed and merged to main)
**Purpose:** Handoff document for Claude Code. Read this entire file before starting any work. Stay in think mode after reading — propose a plan, don't execute.
**Source:** Synthesis of live run feedback (helpside.com), session briefing analysis, and cross-conversation brainstorming.

---

## Revamp Status Tracker

Everything below this section is the **original revamp brief** — unchanged. This tracker summarizes what shipped and what's still open.

### Completed (Sessions 1-3, merged to main 2026-02-21)

| Phase | What Shipped | Commit |
|-------|-------------|--------|
| Phase 1 (CLAUDE.md) | 5 output rules + audience model in `CLAUDE.md`. `[H]` markers added to 1A brief template. | `d8d6265` |
| Phase 2 (Output Template Redesign) | 3A rewritten as client-facing doc — plays + PVPs inline, operator detail stripped. 3C gutted from 40-section ops manual → 7-section operator checklist. Output filenames prefixed: `2-play-design-`, `3-operator-checklist-`. | `f98c675`, `8d504f8` |
| Phase 3 (Unified Claim Classification) | `source_tier` (V1/V2/H) added to verification ledger in `contracts/claim-verification.md` (v1.1). Send rules added. 3B PVP Generator updated with source_tier classification + quality gates. | `8d504f8` |

### Not Started

| Phase | What's Left | Notes |
|-------|------------|-------|
| Phase 4 (Validation Layer Architecture) | Design the interface for self-validating claims against real data sources (LinkedIn Sales Nav API, job board APIs, BLS, KFF, state DOL, vertical databases). Lock architecture decisions — don't build yet. | Original brief says "design only." The `[H]` → `[V1]` upgrade path is partially in place via source_tier, but no automated validation exists. |
| Phase 5 (Legibility Pass) | Rename modules to plain names in CANONICAL.md. Collapse 6 contracts to 2-3. README.md still references old "Execution Blueprint" naming. | Low priority. System works without this. |
| Phase 6 (Retro Loop) | 60-second brain dump → classify → quick fix pattern. Self-scoring extended beyond 3B. | Depends on more live runs generating feedback. |
| Phase 7 (Layer 4 Redesign) | Nuke manual JSON logging. Pull from Instantly API instead. Build after enough usage data exists. | DOA as designed — needs API integration, not manual logs. |

### Post-Revamp Feedback (Helpside v2 Run — 2026-02-21)

Issues surfaced during the first run on the revamped templates:

1. **[H] markers still missing on volume estimates.** Numbers like "estimated X companies/month" still generated as facts. System needs stricter enforcement — any number not sourced from a specific URL, API, or operator input gets `[H]`.
2. **3C: Credit estimates and effort-per-run unsourced.** "X credits per lookup" and "X minutes per PVP" are guesses. These should be **operator inputs** (ask the operator) or omitted. Don't fabricate operational numbers.
3. **3C: Tool recommendations too prescriptive.** Describe *what* to monitor/detect, not *which tool* to use. The operator knows their stack.
4. **System gap: No first-party data integration.** No mechanism to combine research data (web search, structured APIs) with first-party data the client could provide (CRM exports, internal metrics, customer lists). PVPs would be significantly stronger with both. This connects to Phase 4 (Validation Layer) — first-party data is a V2 source that the system can't currently ingest.

---

## Original Revamp Brief (unchanged below this line)

---

## The Reframe

GTM Alpha's moat is **intelligence quality**, not execution infrastructure.

The helpside.com live run proved the system works end-to-end. It also proved where the value actually lives:

- **Module 1A (Research Brief)** — signal design is genuinely strong. Buyer topology, value prop analysis, pain point stacks, competitive insights — all solid. This is the engine.
- **Module 3B (PVP Generator)** — the actual deliverable a prospect receives. The artifact that makes someone stop scrolling. This is the product.
- **Module 3A (Play Design)** — the bridge between signals and PVPs. Stays, but gets leaner.
- **Module 3C (Execution Blueprint)** — currently a 40-section ops manual that tries to teach the operator how to build Clay tables, configure Instantly, and set up Google Sheets. **This is not where the value is.** The operator (Lucas) already knows how to do this. Anyone with Claude Code can do it in an afternoon. 3C drops to an internal operator checklist — not a deliverable.

The execution layer (Clay builds, Instantly config, enrichment waterfalls) is commodity work. The intelligence layer (validated signals for vertical markets where standard tools fail, PVPs backed by real data) is the moat.

---

## Why This Matters

Standard data providers (ZoomInfo, Apollo, LinkedIn) have terrible coverage in vertical/niche markets — HOA companies, architecture firms, HR services businesses, school districts, municipal buildings. The data literally doesn't exist in traditional tools.

GTM Alpha's opportunity is building proprietary, vertical-specific intelligence that standard tools can't produce. That means:

1. Identifying signals specific to a vertical (GTM Alpha already does this well)
2. **Validating those signals against real data sources** (GTM Alpha doesn't do this yet — this is the big unlock)
3. Producing PVPs backed by verified data, not third-party SEO blog summaries
4. Delivering all of this at the moment it matters (timing signals)

The validation layer isn't a nice-to-have. It's the core value proposition. "My system doesn't just hypothesize signals — it validates them against real data sources" is the sentence that differentiates this from someone using ChatGPT to brainstorm outbound plays.

---

## Two Problems to Solve

The live run feedback revealed two distinct problems. Both need to happen. They're not in tension.

### Problem 1: Output Trust

The system generates authoritative-looking numbers with no verification mechanism.

Examples from the helpside.com run:
- "30-60 accounts/month" — volume estimate with no source or methodology
- "80-90% enrichment coverage" — never been tested
- "Conviction level: high" — no methodology for how conviction was determined
- "2-3 hours/week" — speculative time estimate
- "Volume confirmed" — confirmed by what?
- "$334/mo" tool cost — prices change constantly, some are gated

**Pattern:** The system generates confident-looking output with nothing behind it. This is the #1 issue across all modules.

**Fix:** Taste invariants (CLAUDE.md rules) + inline hypothesis markers + unified claim classification.

### Problem 2: Output Usability

Even if the numbers were all accurate, the docs are exhausting to read.

Examples from the helpside.com run:
- Execution blueprint tries to be a reference manual, getting-started guide, and operating playbook simultaneously
- Play design separates plays from PVPs — forces scrolling up and down to connect them
- ROI context (the most exciting part) is buried at the very bottom
- "Getting Started" checklist is at the end instead of the beginning
- Google Sheets outcome tracking with Excel formulas — manual infrastructure for something that should be automated
- By Section 5, the reader wants to stop

**Fix:** Output template redesign + 3C demotion to internal checklist.

---

## Revised Build Order

The original session briefing proposed: Legibility → Taste Invariants → Retro Loop → Run → Layer 4.

The live run showed the biggest pain is in output trust and usability, not system naming. Revised:

### Phase 1: CLAUDE.md (1 session)

Create a system-level behavioral layer. 5 rules maximum. Start small — let live correction add rules as failure modes surface.

**Rules to start with:**

1. **No numbers without a source.** If the data point can't be traced to a specific source (URL, database, API result), label it as `[UNVERIFIED]` inline or omit it entirely. Do not present estimates as facts.
2. **No speculative operator metrics.** Do not generate time-per-week estimates, enrichment hit rates, or tool costs unless the operator provides them as inputs. The system researches the market, not the operator's workflow.
3. **No generic KPI targets.** Do not output "target: 55% open rate" or "8% reply rate" unless tagged as `[INDUSTRY BENCHMARK]` with a cited source. Campaign targets depend on too many variables to guess.
4. **Fast Path = Hypothesis Mode.** When running without call data (no Layer 2), all outputs must use inline `[H]` markers on any claim that has not been validated against real-world data. Header disclaimer alone is insufficient — markers must appear next to each unvalidated claim.
5. **Optimize for skimmability.** Lead with what matters most (ROI, key insight, first action). Put reference details after action items. If a section doesn't change the reader's next decision, compress or cut it.

### Phase 2: Output Template Redesign (1-2 sessions)

Redesign output templates for 1A, 3A, and 3C simultaneously. The bloat dies in the redesign — don't plan stripping and redesigning as separate steps.

**Module 1A (Research Brief) — mostly protect, minor fixes:**
- Keep: buyer topology, value prop analysis, signal design, confidence assessment, scoring
- Fix: inline `[H]` markers on volume estimates and any unvalidated detection claims
- Fix: if signal count exceeds quality threshold (e.g., all 9 passed 2.5 but downstream context cost is high), consider raising threshold or adding a "top 5 signals" executive summary
- Fix: prefix output filename with sequence number: `1-research-brief.md`

**Module 3A (Play Design) — restructure:**
- Merge plays with their paired PVPs: Play 1 + PVP 1 together → Play 2 + PVP 2 together → Play 3 + PVP 3 together
- If play-PVP pairing is always 1:1, collapse the pairing matrix (it's redundant)
- Strip: outcome tracking section (belongs in sequencer tool, not a static doc)
- Strip: generic KPI targets without cited sources
- Compress: A/B test plan to variants + hypothesis only (testing execution lives in Instantly)
- Add: first-party data prompt — "What internal data could enhance this PVP?" (even if empty, plants the seed)
- Fix: inline `[H]` markers on volume estimates, "conviction level," "volume confirmed"
- Fix: prefix output filename: `2-play-design-YYYY-MM-DD.md`

**Module 3C (Execution Blueprint) — gut and demote:**
- 3C is no longer a deliverable. It's an internal operator checklist.
- Keep: ROI context (move to top of 3A or make it a standalone summary), domain/inbox setup checks (SPF/DKIM/DMARC), cold email hygiene rules
- Strip entirely: time budget tables, weekly operating rhythm, Google Sheets outcome tracking, Excel formulas, 30-second logging workflow, export to Module 4B, production schedule with monthly targets, quarterly refresh cadence, detailed Clay column order / enrichment waterfall specs, Clay-to-Instantly integration code snippets, volume and cost estimates without sources, PVP batching strategy with jargon tiers ("Tier + Enrich"), contact enrichment waterfall (Clay handles this natively)
- What remains should fit on one page: "Here's the ICP profile → Here's the tool checklist → Here are the hygiene rules → Go build it"
- Fix: prefix output filename: `3-operator-checklist-YYYY-MM-DD.md`

### Phase 3: Unified Claim Classification (1 session)

Merge two complementary dimensions into one system applied across ALL modules (not just 3B):

**Source dimension** (where did this come from?):
- `V1` — Verified Public: found on company site, regulatory site, or reputable dataset
- `V2` — Verified Internal: confirmed by first-party/internal data
- `H` — Hypothesis: plausible but unverified

**Freshness dimension** (is this still true?):
- `Static` — stable facts unlikely to change (e.g., federal compliance thresholds)
- `Dynamic` — volatile facts with expiration dates (e.g., job postings, headcount, pricing)
- `Attributed` — entity-specific facts requiring verification against the specific company

Module 3B already has the freshness dimension (Static/Dynamic/Attributed) with verification ledger, claim budgets, and STOP_OUTPUT. Extend this framework to 1A and 3A by adding the source dimension.

**Inline format:** `[V1-Static]` or `[H-Dynamic]` next to claims. This replaces both the header-only "Fast Path" disclaimer AND the unsourced confidence problem.

**Send rules (for 3B deliverables):**
- Step 1 email: V1 claims only
- Step 2 with PVP: V1 + V2 allowed if PVP substantiates
- Any H claim must be reframed as "often / typically / many companies in this space" or removed

### Phase 4: Validation Layer Architecture (design only — don't build yet)

Lock the architecture decision: **the system validates its own claims using real data sources.**

This is the future differentiator. Don't build it in this revamp cycle, but design the interface so modules are ready for it.

**What it would do:**
- After 1A generates signals → validation pass checks signal volume against real databases (LinkedIn Sales Nav API, job board APIs, vertical-specific directories)
- After 3A designs plays → reality check gates verify detection methods actually work (e.g., HR posting has 2+ scope keywords AND is still open, headcount filter uses source that can detect at the resolution needed)
- For 3B data sourcing → pull real data from BLS, KFF, state DOL sites, Glassdoor, vertical-specific databases instead of guessing

**Tools that could power this (for future reference):**
- Perplexity / web search MCP → validate compliance claims, penalty amounts, salary benchmarks
- LinkedIn Sales Nav API / Sherpa → validate signal volume
- Firecrawl / Playwright → scrape job boards, state DOL sites, vertical databases
- RSS feeds → real-time signal monitoring
- Docker / datagen.dev → structured data extraction

**Architecture decision to lock now:** Each module should have a `validation_status` field in its output that starts as `[H]` and can be upgraded to `[V1]` or `[V2]` when the validation layer eventually runs. This way outputs are honest from day one and improve as validation capabilities are added.

### Phase 5: Legibility Pass (1 session)

Now that outputs are clean, make the system itself readable:
- Rename modules from numbers to plain names in CANONICAL.md (1A → Research Brief, 3A → Play Design, etc.)
- Update SYSTEM_BUILD_PLAN.md build tracker (currently says "Not started" for all phases — everything has been built and run)
- Prefix output filenames with sequence numbers (already done in Phase 2)
- Collapse 6 contracts to 2-3 reference docs (or defer until contracts are actually referenced during a run)

### Phase 6: Retro Loop (1 session)

Once the system is being run on real domains with clean outputs:
- 60-second brain dump → classify by upstream module → quick fix or diagnosis note
- Diagnosis notes persist across sessions for heavy fixes that require plan mode
- Self-scoring pattern from 3B (generate → score → diagnose → rewrite) extended to other modules

### Phase 7: Layer 4 Redesign (when there's usage data)

- Architecture decision already locked: pull from Instantly API, don't push via manual logs
- Nuke manual JSON logging (4A as designed is DOA)
- Build only after retro loop has generated enough data to inform the design

---

## Architectural Decisions to Lock Now

These are decisions that should be made before building starts, because changing them later creates rework:

1. **Modules read upstream outputs from disk, not conversation history.** The helpside.com run consumed 65% of the context window by Module 3C. If 3A reads the brief from disk instead of from chat history, context is preserved for the work that matters. Each module reads from `runs/{domain}/` — no passing outputs through conversation.

2. **Fast Path outputs use inline hypothesis markers.** Not just a header disclaimer. `[H]` appears next to every unvalidated claim. This is enforced in CLAUDE.md, not optional per module.

3. **The system validates its own claims.** The architecture should assume a validation pass will eventually exist. Outputs include `validation_status` fields from day one, even if they all start as `[H]`.

4. **Execution layer is operator work, not system output.** Module 3C produces an internal checklist, not a deliverable. The system doesn't teach the operator how to use Clay — it tells the operator what to build.

5. **One unified claim classification system.** Source (V1/V2/H) × Freshness (Static/Dynamic/Attributed). Applied across all modules. Not two parallel systems.

---

## Key Feedback Patterns (from helpside.com live run)

52 feedback items were logged during the live run. These are the patterns:

### Pattern 1: Unsourced Confidence (Items #3, #8, #13, #17, #22, #24, #28, #29, #34)
The system generates authoritative numbers with no methodology. Volume estimates, enrichment hit rates, time budgets, conviction levels, KPI targets — all presented as facts with nothing behind them. **This is the single biggest problem.** Fix: CLAUDE.md rules + inline [H] markers.

### Pattern 2: Doc Structure Forces Scrolling (Items #19, #20, #47, #49, #50)
Plays separated from PVPs. ROI buried at the bottom. Getting Started at the end. Three audiences in one doc. Fix: template restructure.

### Pattern 3: Static Doc Does Sequencer's Job (Items #18, #44, #45, #46)
A/B testing, outcome tracking, weekly operating rhythm, Google Sheets with formulas — all belong in the sequencer tool or don't belong anywhere yet. Fix: strip from templates.

### Pattern 4: Tool Specificity Trap (Items #26, #30, #33, #36, #39, #40)
Clay column orders, Instantly config, enrichment waterfall details, integration code snippets — change constantly, will be wrong within months. Fix: specify logic, not implementation.

### Pattern 5: Rep Adoption Problem (Item #15)
Email copy will always need taste adjustments. Three layers of opinion (operator, team, client). System assumes one voice. Fix: not solvable in this revamp — park for later. The retro loop may help.

### What to Protect (Items #1, #4, #6, #9, #37)
- System runs 15 min autonomously with no interaction needed
- Buyer topology, value prop analysis always solid
- Signal validation, timing/decay, scoring logic all sound
- Research confidence assessment already self-aware about LinkedIn gaps
- Domain/inbox setup, SPF/DKIM/DMARC, cold email hygiene rules — good mechanical checks

### What to Park (Items #5, #10, #23, #25, #35, #52)
- LinkedIn detection unreliable below 50 employees (known limitation, not solvable now)
- Layer 4 redesign (after usage data exists)
- ABM nurture campaigns parallel to cold outreach (new capability, not a fix)
- API-based volume validation (future validation layer)
- Mid-market definition is client-configurable

---

## What the Operator Needs vs. What Exists

| What Exists Today | What Should Exist |
|---|---|
| 3 output files with no sequence indicators | `1-research-brief.md`, `2-play-design.md`, `3-operator-checklist.md` |
| Execution blueprint as a 40-section ops manual | Short internal checklist (1 page) |
| Plays and PVPs in separate sections | Play + PVP together, inline |
| ROI buried at bottom of 3C | ROI at top of play design or standalone |
| Unsourced volume estimates everywhere | `[H]` inline markers or omitted |
| No CLAUDE.md | 5-rule system behavioral layer |
| 6 separate contracts | Defer — collapse when contracts are actually referenced |
| Manual JSON logging for Layer 4 | Nothing yet — redesign later with API integration |
| Generic KPI targets | Stripped or labeled `[INDUSTRY BENCHMARK]` with source |
| Clay-specific implementation details | Logic only ("find companies → enrich → find contacts → verify → send") |

---

## Reference: Files That Need Changes

**Create:**
- `CLAUDE.md` — system-level behavioral rules (5 rules, 20 lines max)

**Redesign:**
- `skills/play-design/SKILL.md` — template restructure (plays+PVPs together, strip outcome tracking, add first-party data prompt)
- `references/PROMPT_TEMPLATE.md` — update template to match new structure
- `skills/execution-blueprint/SKILL.md` — gut to internal checklist format

**Update:**
- `skills/vendor-intelligence-brief/SKILL.md` — add inline [H] marker instructions, consider signal count threshold
- `CANONICAL.md` — reflect new module roles and naming
- `SYSTEM_BUILD_PLAN.md` — update build tracker (currently all "Not started")

**No changes needed yet:**
- `skills/pvp-generator/SKILL.md` — already has the strongest quality gates. Extend its claim system to other modules, don't change 3B itself.
- `skills/call-intelligence-synthesis/SKILL.md` — Layer 2 modules untouched until call data is available
- `skills/reconciliation/SKILL.md` — same
- `contracts/` — defer collapse until contracts are actively referenced during a run

---

## Instructions for Claude Code

**Mode:** Think mode. Read this brief, read SESSION_BRIEFING.md, and propose a build plan back to the operator. Do not start editing files.

**What to propose:**
1. Exact CLAUDE.md content (5 rules, 20 lines)
2. Revised template structure for 3A (play design) — show the new section order
3. What 3C becomes (the short internal checklist)
4. How inline [H] markers work in practice — show an example from the helpside.com brief
5. How the unified claim classification (Source × Freshness) integrates with 3B's existing system
6. Which files change, in what order, and estimated session count

**What NOT to do:**
- Don't start editing files
- Don't build the validation layer
- Don't build the retro loop
- Don't redesign Layer 4
- Don't over-engineer the CLAUDE.md — 5 rules, not 50
