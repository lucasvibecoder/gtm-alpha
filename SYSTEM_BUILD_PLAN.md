# GTM Alpha v3: System Build Plan

**Owner:** Lucas
**Status:** Not started
**Last updated:** 2026-02-13

This is the master blueprint. Every build session starts here.

---

## What We're Building

GTM Alpha v2 is a document generation chain: input → prompts → documents.

GTM Alpha v3 is an intelligence operating system: signals in → plays out → results back → system improves.

The difference: v2 produces a brief. v3 produces a compounding advantage.

---

## Fully Built System Vision

When v3 is complete, a weekly operating rhythm looks like this:

- **Monday (45 min):** Check signal monitors. Log new accounts where signals are firing. Update outcome log from last week's outbound.
- **Tue-Wed (60 min):** Execute plays against signal-active accounts. Send PVPs. Log each touch.
- **Thursday (30 min):** Review reply/no-reply patterns. Flag underperforming messaging.
- **Monthly (2 hrs):** Update signal scores from outcome data. Kill weak signals. Refresh plays.
- **Quarterly (half day):** Full system refresh. Re-run research. Reconcile against new call data. Redesign plays from 3 months of learning.

Total weekly time: 2-3 hours. The system does the thinking. The operator does the executing.

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        GTM ALPHA v3 SYSTEM MAP                          │
│                                                                         │
│  LAYER 1: RESEARCH                                                      │
│  ┌───────────────────────────┐  ┌────────────────────────────────┐     │
│  │ Module 1A                 │  │ Module 1B                      │     │
│  │ Vendor Intelligence Brief │─▶│ Signal Validation Loop         │     │
│  │                           │  │ (Perplexity / Web Search)      │     │
│  │ • Exec context            │  │                                │     │
│  │ • Value prop              │  │ Per signal:                    │     │
│  │ • Buyer topology          │  │ • Real example found?          │     │
│  │ • Signal stacks           │  │ • Volume confirmed?            │     │
│  │ • Scored signal hypotheses│  │ • Detection method defined?    │     │
│  │ • Competitive intel       │  │ • Signal score assigned        │     │
│  │ • Signal detection specs  │  │                                │     │
│  └───────────────────────────┘  └──────────────┬─────────────────┘     │
│                                                 │                       │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─   │
│  LAYER 2: VALIDATION (only if call data exists) │                       │
│  ┌───────────────────────────┐                  │                       │
│  │ Module 2A                 │                  │                       │
│  │ Call Intelligence         │                  │                       │
│  │ Extractor (Clay)          │                  │                       │
│  │                           │                  │                       │
│  │ • Tier 1 fields (always)  │                  │                       │
│  │ • Tier 2 fields (if rich) │                  │                       │
│  └──────────┬────────────────┘                  │                       │
│             │                                   │                       │
│             ▼                                   │                       │
│  ┌───────────────────────────┐                  │                       │
│  │ Module 2B                 │                  │                       │
│  │ Call Intelligence         │                  │                       │
│  │ Synthesis                 │                  │                       │
│  │                           │                  │                       │
│  │ • 7 sections (not 12)     │                  │                       │
│  │ • Weighted by call quality│                  │                       │
│  └──────────┬────────────────┘                  │                       │
│             │                                   │                       │
│             ▼                                   │                       │
│  ┌───────────────────────────┐                  │                       │
│  │ Module 2C                 │◀─────────────────┘                       │
│  │ Reconciliation            │                                          │
│  │                           │                                          │
│  │ • 5 sections (not 11)     │                                          │
│  │ • Only net-new insight    │                                          │
│  │ • Updated signal scores   │                                          │
│  └──────────┬────────────────┘                                          │
│             │                                                           │
│  ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─   │
│  LAYER 3: EXECUTION                                                     │
│             │                                                           │
│             ▼                                                           │
│  ┌───────────────────────────┐  ┌────────────────────────────────┐     │
│  │ Module 3A                 │  │ Module 3B                      │     │
│  │ Play Design               │  │ PVP Generator                  │     │
│  │                           │  │                                │     │
│  │ • 3 plays w/ A/B variants │  │ • Takes PVP spec from 3A      │     │
│  │ • Signal detection method │  │ • Produces actual deliverable  │     │
│  │ • Target account list spec│  │ • Ready to email               │     │
│  │ • Outcome tracking fields │  │                                │     │
│  └──────────┬────────────────┘  └────────────────────────────────┘     │
│             │                                                           │
│  ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─   │
│  LAYER 4: LEARNING ENGINE                                               │
│             │                                                           │
│             ▼                                                           │
│  ┌───────────────────────────┐  ┌────────────────────────────────┐     │
│  │ Module 4A                 │  │ Module 4B                      │     │
│  │ Outcome Logger            │──▶ Signal Performance Updater     │     │
│  │                           │  │                                │     │
│  │ • Per-touch tracking      │  │ • Updates signal scores        │     │
│  │ • Play + signal + result  │  │ • Flags winning messaging      │     │
│  │ • 30 sec per entry        │  │ • Kills underperformers        │     │
│  └───────────────────────────┘  └──────────────┬─────────────────┘     │
│                                                 │                       │
│  ┌──────────────────────────────────────────────┘                       │
│  │                                                                      │
│  ▼  FEEDS BACK INTO ALL LAYERS                                          │
│  • Updated signal scores → Layer 1 (better hypotheses)                  │
│  • Winning language → Layer 3 (better plays)                            │
│  • Pattern library → new client briefs (faster, higher quality)         │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

**Two paths through the system:**

```
FAST PATH (no call data):     Layer 1 → Layer 3 → Layer 4
FULL PATH (with call data):   Layer 1 → Layer 2 → Layer 3 → Layer 4
```

**Cross-cutting layers (not in the flow, but enforce consistency):**

```
contracts/          → Define every structured object that passes between modules
CANONICAL.md        → Manifest of every active file and its role
SYSTEM_BUILD_PLAN   → This file. The blueprint.
```

---

## Build Order

### Phase 0.5: Repo Canonicalization

**Objective:** Lock the repo structure, archive dead versions, and establish the contracts layer before building anything new. 30-minute timebox.

**Why it matters:** You've already hit version confusion (v1.0 vs v2.0 of Prompts 2 and 3). Building v3 modules on top of an unorganized repo guarantees drift. Fix the foundation first.

**Components:**

1. **Archive v1.0 prompt files**
   - Move `prompt_2_buyer_intelligence_extractor_v1.md` → `archive/v2/`
   - Move `prompt_3_call_intelligence_synthesis_v1.md` → `archive/v2/`
   - These are dead. They stay for reference, not for use.

2. **Lock canonical filenames**
   - Create `CANONICAL.md` at repo root — a one-page manifest listing every active file, its version, and its role in the system:

   | File | Version | Role | Location |
   |------|---------|------|----------|
   | Vendor Intelligence Brief (Skill) | v2.0 | Module 1A + 1B | `skills/vendor-intelligence-brief/` |
   | Buyer Intelligence Extractor | v2.0 | Module 2A (Clay) | `prompts/call-intelligence-extractor/` |
   | Call Intelligence Synthesis | v2.0 | Module 2B | `prompts/call-intelligence-synthesis/` |
   | Signal & Messaging Reconciliation | v1.0 | Module 2C | `prompts/reconciliation/` |
   | Play Design & PVP Architecture | v2.1 | Module 3A | `prompts/play-design/` |
   | Schema Contract | v1.0 | Reference | `contracts/` |
   | System Build Plan | v1.0 | Blueprint | repo root |

3. **Create `contracts/` folder**
   - Move existing schema contract into `contracts/call-extraction-schema.md`
   - Create placeholder files for new v3 contracts (empty with header + "TBD"):
     - `contracts/signal-scorecard.md` — format for scored signal output (built in Phase 1)
     - `contracts/detection-spec.md` — format for signal detection specs (built in Phase 1)
     - `contracts/outcome-log.md` — format for outbound outcome tracking (built in Phase 5)
     - `contracts/performance-update.md` — format for signal score updates (built in Phase 5)
   - Rule: any structured object that passes between modules gets a contract BEFORE the module is built

4. **Set up repo folder structure**
   ```
   gtm-alpha-v3/
   ├── SYSTEM_BUILD_PLAN.md
   ├── CANONICAL.md
   ├── README.md
   ├── contracts/
   │   ├── call-extraction-schema.md     # Existing schema contract (renamed)
   │   ├── signal-scorecard.md           # TBD — Phase 1
   │   ├── detection-spec.md             # TBD — Phase 1
   │   ├── outcome-log.md               # TBD — Phase 5
   │   └── performance-update.md        # TBD — Phase 5
   ├── skills/
   │   └── vendor-intelligence-brief/    # Module 1A + 1B (exists)
   │       ├── SKILL.md
   │       └── references/
   │           ├── PROMPT_TEMPLATE.md
   │           ├── example-brief-bobyard.md
   │           └── example-signal-validation.md
   ├── prompts/
   │   ├── call-intelligence-extractor/  # Module 2A (Clay prompt)
   │   │   └── PROMPT.md
   │   ├── call-intelligence-synthesis/  # Module 2B (becomes skill in Phase 2)
   │   │   └── PROMPT.md
   │   ├── reconciliation/               # Module 2C (becomes skill in Phase 3)
   │   │   └── PROMPT.md
   │   └── play-design/                  # Module 3A (becomes skill in Phase 4)
   │       └── PROMPT.md
   ├── templates/
   │   ├── outcome-log.json              # Module 4A (Phase 5)
   │   └── pattern-library.md            # Module 4C (Phase 5)
   └── archive/
       └── v2/                           # Dead versions
   ```

   Note: Prompts start as standalone files in `prompts/`. As each phase converts them to skills, they move to `skills/` with a proper SKILL.md + references/ structure. This happens incrementally, not all at once.

**Done looks like:**
- `git log` shows a clean commit: "v3 repo canonicalization"
- Every active file has one canonical location
- Dead versions are archived, not deleted
- `contracts/` folder exists with placeholder files for every structured object in the system
- `CANONICAL.md` is the manifest — if a file isn't listed there, it doesn't exist
- An operator opening this repo for the first time can understand the structure in 2 minutes

**Compounding effect:** Every subsequent phase builds into a clean structure instead of fighting version chaos. Contracts are defined before modules are built, preventing the "Prompt 4 can't parse Prompt 2 output" problem.

---

### Phase 1: Harden the Core

**Objective:** Upgrade Prompt 1 (the skill that already exists) from a brief generator into a scored intelligence engine with detection specs.

**Why it matters:** Everything downstream depends on Prompt 1 output quality. If signals aren't scored and detection methods aren't defined, the rest of the system produces unactionable insight.

**Components:**

1. **Define contracts first (in `contracts/`)**
   - `contracts/signal-scorecard.md` — define the exact format for signal scoring output:
     - Scoring dimensions: Volume (20%), Detectability (25%), Specificity (25%), Timing Precision (15%), Actionability (15%)
     - Score range: 1-5 per dimension, weighted composite
     - Cut threshold: below 2.5 gets killed
   - `contracts/detection-spec.md` — define the exact format for detection specs:
     - Fields: data source, query/filter, refresh cadence, estimated monitoring cost, automation feasibility

2. **Add to PROMPT_TEMPLATE.md (output structure)**
   - Signal Scoring table added to each signal in Section 5
   - Signal Detection Spec table added to each signal in Section 5
   - Kill Section 8 (Strategic Recommendations) — recommendations are premature before validation, they belong in Prompt 4 or Prompt 5

3. **Add to SKILL.md (enforcement + workflow)**
   - Quality gate: all signals must have composite scores populated
   - Quality gate: signals scoring below 2.5 are cut from final output
   - Step 3b (Validation Loop) now also populates detection specs per signal
   - Step 3c (Quality Gates) updated to verify scoring + detection specs present

**Done looks like:**
- Running `gtm-alpha` on a domain produces a brief where every signal has a numeric score and a concrete detection method
- The bottom 30% of signals are already killed before output
- An operator can read the brief and immediately know WHERE to monitor each signal and HOW OFTEN

**Compounding effect:** Scored signals feed directly into Play Design (Phase 4). Detection specs feed directly into the monitoring setup. Nothing is theoretical anymore.

---

### Phase 2: Streamline the Call Intelligence Chain

**Objective:** Slim down Prompts 2 and 3 so they actually work reliably in Clay and produce cleaner input for reconciliation.

**Why it matters:** The current Prompt 2 schema is too heavy for Clay. Prompt 3 has 12 sections where 7 would do. These prompts are the bridge between "what we think" and "what buyers say" — if the bridge is shaky, the reconciliation (Prompt 4) produces garbage.

**Components:**

1. **Split Prompt 2 schema into Tier 1 + Tier 2**
   - Tier 1 (always extract): `buyer_problem_framing`, `triggering_context`, `alternative_attempts`, `indigenous_terminology`, `vocabulary_gap`, `pain_vocabulary`, `engagement_moments`
   - Tier 2 (extract if rich): `decision_ecosystem`, `trust_and_risk`, `call_quality_score`, `messaging_goldmine`, `recommended_actions`
   - Add logic: if `buyer_talk_ratio_estimate` = Low, only extract Tier 1

2. **Compress Prompt 3 from 12 sections to 7**
   - Keep: §1 Dataset Overview, §2 Signal Validation Patterns, §3 Buyer Language Map, §4 Problem Framing, §7 Engagement Moments, §10 Key Insights
   - Merge: §5 Alternative Attempts + §6 Objection Patterns → one section
   - Kill: §8 Decision Ecosystem, §9 Buyer Sophistication, §11 Hypotheses, §12 Recommendations (these either repeat or belong later)

3. **Update Schema Contract**
   - Reflect Tier 1/Tier 2 split
   - Update field references for compressed Prompt 3
   - Clear all "NEEDS UPDATE" items from breaking changes log

**Done looks like:**
- Prompt 2 runs in Clay on Sonnet without dropping critical fields
- Prompt 3 produces a tighter synthesis that Prompt 4 can actually consume in full
- Schema contract has zero unresolved breaking changes

**Compounding effect:** Cleaner call intelligence → sharper reconciliation in Phase 3 → better-calibrated plays in Phase 4.

---

### Phase 3: Sharpen Reconciliation

**Objective:** Cut Prompt 4 from 11 sections to 5. Make it produce only net-new intelligence from comparing Prompt 1 to Prompt 3.

**Why it matters:** Current Prompt 4 repeats huge chunks of Prompt 3 (buyer psychology, alternative attempts, decision ecosystem). The model runs out of gas producing duplicated content and the unique insights — signal validation, language calibration, missed signals — get shortchanged.

**Components:**

1. **Rebuild Prompt 4 as 5 sections:**
   - §1 Signal Scorecard (validated / partially validated / unvalidated / contradicted)
   - §2 Language Calibration Map (our words → their words, with exact quotes)
   - §3 Missed Signals (patterns from calls NOT in our hypotheses — this is where the gold is)
   - §4 Objection Reality Check (hypothesized vs actual, compressed)
   - §5 Calibrated Recommendations (the ONLY place recommendations live in the system)

2. **Add signal score updates**
   - For each validated signal, update the composite score from Phase 1
   - For each contradicted signal, zero the score
   - Output a ranked, re-scored signal list that feeds directly into Play Design

3. **Kill sections 6-8 (Buyer Psychology, Alternative Attempts, Decision Ecosystem)**
   - These are pass-through from Prompt 3
   - If the operator needs them, they read Prompt 3 output directly

**Done looks like:**
- Prompt 4 output is half the length but twice the density
- Every section contains net-new intelligence, not repeated upstream content
- Signal scores are updated based on call evidence
- The output is directly consumable by Prompt 5 without the operator having to mentally filter noise

**Compounding effect:** A tighter reconciliation means Prompt 5 gets cleaner input → better plays. Updated signal scores start building the scoring history that Layer 4 will eventually automate.

---

### Phase 4: Upgrade Play Design + Build PVP Generator

**Objective:** Make Prompt 5 output executable, not just strategic. Add a PVP generation module that produces actual deliverables.

**Why it matters:** Current Prompt 5 produces beautiful strategy documents. But a play isn't real until it has: a way to find accounts, a testable message, and a trackable outcome. And a PVP isn't real until it's a file you can attach to an email.

**Components:**

1. **Add to each play in Prompt 5:**
   - **Signal Detection Method:** The exact Clay/Sales Nav/BidPrime filter to find accounts where this signal is firing NOW
   - **Target Account List Spec:** ICP filters + signal filters + disqualifiers = a runnable query
   - **A/B Variants:** 2 subject lines, 2 opening lines per play. Not optional. The system needs variation to learn.
   - **Outcome Tracking Fields:** What counts as success? Reply rate target, meeting rate target.

2. **Simplify PVP format:**
   - Kill verbose deployment timing prose
   - Replace with a Play-PVP pairing matrix (cold opener / follow-up / re-engagement)

3. **Build Module 3B: PVP Generator (new skill)**
   - Input: PVP spec from Prompt 5
   - Output: The actual deliverable (competitive messaging audit, signal analysis, benchmark report)
   - Uses Claude Code + web search to research and produce the asset
   - Output is a file (markdown, PDF, or HTML) ready to email

4. **Kill Play Risk Assessment section**
   - Replace with the A/B test structure, which actually addresses risk through testing

**Done looks like:**
- Each play output includes a runnable filter to find target accounts today
- Each play has built-in A/B variants ready for testing
- PVPs are actual files, not concepts — an operator can attach them to an email immediately after generation

**Compounding effect:** Executable plays + real PVPs → operator starts sending outbound → generates outcome data → feeds Layer 4.

---

### Phase 5: Build the Learning Engine

**Objective:** Close the loop. Build the outcome tracking and signal performance updating that makes the system compound over time.

**Why it matters:** Without this, every run starts from zero. With this, every run makes the next run better. This is the difference between a prompt chain and an intelligence system.

**Components:**

1. **Module 4A: Outcome Logger**
   - A structured template (JSON or spreadsheet) for logging per-touch results
   - Fields: account, play used, signal that triggered, signal score, PVP sent, outcome, days to response, reply sentiment, notes
   - Must take <30 seconds per entry or it won't get used
   - Build as a simple schema + logging prompt that takes natural language input and structures it

2. **Module 4B: Signal Performance Updater**
   - A prompt that takes the outcome log (after 50+ entries) and produces:
     - Updated signal scores based on actual conversion data
     - Messaging variant performance (which subject lines / openings win)
     - Emerging signals from the "notes" field
     - Signals to kill (consistently zero response)
     - Signals to promote (consistently positive response)
   - Output feeds back into Module 1A signal scores for next brief refresh

3. **Module 4C: Cross-Client Pattern Library** (if running for multiple vendors)
   - A growing reference doc of:
     - Signal archetypes that work across verticals
     - PVP formats that consistently get responses
     - Objection handling frameworks that transfer
     - Industry-specific signal patterns
   - Updated quarterly
   - Becomes the consulting moat over time

**Done looks like:**
- After 50+ outbound touches, the operator runs Module 4B and gets updated signal scores
- Underperforming signals are flagged for removal
- Winning messaging variants are flagged for amplification
- The system has a memory — it knows what worked and what didn't

**Compounding effect:** This is the compounding effect. Every cycle through the learning engine makes the research sharper, the signals more predictive, and the plays more effective. After 6 months, the system is operating on proprietary data that no competitor has access to.

---

### Phase 6: Final Skill Conversion + System Polish

**Objective:** Convert remaining prompts into Claude Code skills and finalize the system for sharing.

**Why it matters:** By this point, every module works. This phase packages it for durability — so the system is runnable by anyone, not just the person who built it.

**Components:**

1. **Convert remaining prompts to skills**
   - Modules that should be skills by now (if not already converted during their build phase):
     - `call-intelligence-synthesis/` (Module 2B)
     - `reconciliation/` (Module 2C)
     - `play-design/` (Module 3A)
     - `pvp-generator/` (Module 3B)
     - `signal-performance-updater/` (Module 4B)
   - Each gets: SKILL.md (triggers, workflow, quality gates) + references/ (templates, examples)
   - Module 2A (Call Intelligence Extractor) stays as a prompt template — it runs in Clay, not Claude Code

2. **Update README.md**
   - v3 system map
   - Quick start for both paths (fast / full)
   - Weekly operating rhythm
   - Module reference table with links to each skill/prompt
   - Contracts reference

3. **Update CANONICAL.md**
   - Reflect final file locations after all conversions
   - Confirm all contracts are populated (no more TBD placeholders)

4. **Verify contract compliance**
   - Every module's output matches its contract
   - Every module's input references match upstream contracts
   - Zero unresolved items in any contract file

**Done looks like:**
- Every module is either a runnable skill or a documented prompt template
- Repo is the single source of truth for the entire system
- README gives a new user the full picture in 5 minutes
- All contracts are populated and enforced

**Compounding effect:** Clean, shareable system = open source credibility, consulting deliverable, or product foundation.

---

## Build Tracker

| Phase | Module(s) | Status | Date Started | Date Complete |
|-------|-----------|--------|-------------|---------------|
| 0.5 | Repo canonicalization, contracts/ folder, CANONICAL.md | Not started | | |
| 1 | 1A Signal Scoring + Detection Specs, contracts defined | Not started | | |
| 2 | 2A Tier Split, 2B Compression, Schema Update | Not started | | |
| 3 | 2C Reconciliation Rebuild | Not started | | |
| 4 | 3A Play Upgrade, 3B PVP Generator | Not started | | |
| 5 | 4A Outcome Logger, 4B Performance Updater, 4C Pattern Library | Not started | | |
| 6 | Final skill conversions, README, CANONICAL update | Not started | | |

---

## Rules for Building

1. **Each phase must be tested on a real domain before moving to the next.** Bobyard is the test case.
2. **Ship > perfect.** If a module works at 80%, move on. Come back and polish later.
3. **Contracts are defined BEFORE modules are built.** Any structured object that passes between modules gets a contract first.
4. **The schema contract is updated FIRST when changing any field or enum.** Then propagate to prompts.
5. **If a section doesn't change what an operator DOES, kill it.**
6. **Every build session starts by reading this file.** If the plan needs updating, update it before building.
7. **CANONICAL.md is the manifest.** If a file isn't listed there, it doesn't exist in the system.
