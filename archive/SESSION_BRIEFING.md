# GTM Alpha — Session Briefing

**Created:** 2026-02-19
**Source:** Deep brainstorming session in YouTube Brain project. Read this entire file before starting any work.

---

## What Happened

Captured a video (Eugene Vyborov — "The Sovereign AI Setup That Changed Everything") about deploying Claude Code agents autonomously in production. That triggered a 45-minute brainstorming session comparing Vyborov's architecture to GTM Alpha, which surfaced major insights about system simplification, a retro feedback loop, and Version B's role.

---

## Key Decisions Made

### 1. Version B Is Reframed

**Original thesis:** Can GTM Alpha output deployable infrastructure (PredictLeads → Prospeo → Claude API → Instantly) instead of strategy documents?

**New reframe:** Feasibility is already proven. The Vibe GTM campaign (55 sends, 75% positive reply rate, built entirely in Claude Code) showed the terminal-to-send pipeline works. The APIs exist. The wiring is doable.

**What to focus on instead:** Tightening play design specs so a machine can follow them literally. Every time a human has to "tweak" an email output, that's a spec gap. Version B's real value isn't the automated pipeline — it's what the failures teach us about Version A's specs.

**Bottom line:** Stop trying to prove automation is possible. Start making the input specs machine-readable.

### 2. Retro Loop for GTM Alpha (New Capability to Build)

The user runs GTM Alpha, reviews the email output, and has scattered feedback that evaporates before it gets captured. The compound loop breaks at the most basic step — writing down what was noticed.

**The pattern:**
1. GTM Alpha finishes a run (multiple outputs generated)
2. User does a 60-second brain dump right in the terminal — raw, unstructured reactions
3. Claude reads the generated outputs + the user's feedback
4. Claude classifies each piece of feedback by which upstream module caused it:
   - "Hook doesn't connect to trigger event" → **play design fix** (Layer 3A)
   - "Signal isn't relevant to this buyer" → **signal selection fix** (Layer 1)
   - "Right signal, right person, email reads generic" → **email generation fix** (template/prompt)
   - "Wrong person entirely" → **enrichment/persona fix** (Layer 1)
5. Two types of output:
   - **Quick fixes** → applied immediately (edit a template, add a rule)
   - **Heavy fixes** → written as a **diagnosis note** that persists across sessions

**Why diagnosis notes matter:** Heavy structural fixes require plan mode, which clears chat history. The original context (the specific email that failed, WHY it failed, which layers are affected) disappears. The diagnosis note captures all of that BEFORE entering plan mode so the build session starts with full context.

**This is `/retro` for GTM Alpha** — same pattern as the YouTube Brain retro loop, but applied to email output instead of vault notes.

### 3. Layer 4 Teardown

Layer 4 (Learning Engine) as currently designed is dead on arrival. Module 4A requires manually logging each outbound touch in a JSON template. Nobody will do this.

**What should happen instead:** The learning engine should PULL data from the sequencer (Instantly API — opens, replies, bounces, conversions), not require the user to PUSH data into a log file. The data already exists in the tools.

**Status:** Layer 4 hasn't been used yet. User plans to reach it in the next couple weeks. Redesign it with API integration before building, not after.

### 4. System Simplification — Apply Second Brain Principles

The YouTube Brain (second brain vault) works because of four properties:
1. **User understands every folder** — Inbox, Reference, Library, Archive. Four words, four purposes.
2. **Taste invariants are simple** — 5 mechanical checks. Pass/fail, no judgment.
3. **Files are modular** — each note is self-contained.
4. **Rules live in one place** — CLAUDE.md governs everything.

GTM Alpha has drifted from all four:
- User doesn't understand module numbering (1A, 2B, 3C mean nothing to them)
- No simple quality checks on outputs
- Modules are tightly coupled (3A needs 1A's exact output format)
- Rules scattered across CANONICAL.md, 6 contracts, SYSTEM_BUILD_PLAN.md, and each skill's internal logic

**Direction:** Simplify toward these principles. Make skills modular. Collapse contracts. Make naming human-readable.

---

## Layer-by-Layer User Feedback

### Layer 1: Research — "Tell me about this company"
- **Signal validation (Module 1B) is the most important piece.** User was worried it was missing — it's already there, baked into Layer 1. Each signal hypothesis gets validated against real-world evidence.
- User is generally happy with Layer 1 output quality. This is the strongest part of the system.
- Persona matching works well — usually correct (e.g., taxwire.com → controller, VP of finance). Not a pain point.

### Layer 2: Validation — "What do actual buyers say?"
- **Should be tool-agnostic.** Currently Clay-biased because user built it while working at Clay. Don't lock to Clay.
- Don't worry about Clay token limits — system hasn't been run in Clay yet. Premature optimization.
- **Want a path for CRM / first-party data input.** Can be an afterthought but worth noting in the architecture.
- The Tier 1/Tier 2 extraction split is a Clay optimization detail the user doesn't need to think about yet.

### Layer 3: Execution — "What do I actually send?"
- **PVP = Permissionless Value Prop** (not Personalized Value Proposition). User has additional resources to improve Module 3B.
- **Remove expected response rate predictions** (e.g., "expect X% reply rate") unless backed by concrete evidence. Not valuable without actual data.
- **Email output is where most feedback lives.** It's downstream of everything — if the email is bad, the diagnosis tells you which upstream step caused it.
- User finds email output always needs tweaks — this is the spec gap the retro loop targets.

### Layer 4: Learning Engine — "What worked?"
- **Tear down and redesign.** Manual JSON logging (4A) is DOA.
- Connect to sequencer APIs (Instantly) to pull outcome data automatically.
- 4B (Signal Performance Updater) and 4C (Pattern Library) are good ideas but depend on 4A, which needs to be automated first.
- User will reach Layer 4 in the next couple weeks.

### Contracts
- **Six separate contract files feels like too many.** Should be collapsed to 1-2 reference docs until the system stabilizes.
- The concept is sound (define what data looks like between modules) but the implementation is over-engineered for the current stage.

### CANONICAL.md
- User understands the 4-layer flow but NOT: module numbering, version numbers, specific contract contents.
- User uses it as a "hey Claude, check your homework" tool, not as a reference they personally consult.
- **Needs human-readable naming.** "Module 3B PVP Generator v2.0" should probably just be "PVP Generator."

### Skills vs Prompts
- User doesn't remember why some modules became skills and others stayed as prompts.
- **Answer:** Skills are Claude Code's packaging for prompts — they get a name, trigger, references, and can run as workflows. Module 2A stays as a prompt because it was designed for Clay. Others got converted to skills during the build phases.
- Going forward: skills that include the prompt should be portable to Clay when needed.

---

## Live Run Feedback

_This section gets updated after the user runs GTM Alpha on a real domain and reports back. Not yet populated._

### Evaluation Criteria (what to look for in the feedback)
1. Which outputs does the user stop and read? → earning their place
2. Where does the user want to edit something? → spec is loose, retro loop target
3. Where does the user say "yeah, this is solid"? → protect this
4. Where is the user confused? → naming problem or unnecessary complexity
5. Which modules does the user skip entirely? → candidate for removal

## Build Direction

_Gets populated after live run feedback is processed. Will contain: simplification plan, retro loop design, Layer 4 redesign, and prioritized build order._

---

## User Preferences (For This Session)

- **Not an engineer.** Explain what things do, not how they work. Avoid jargon.
- **System-design thinker.** Preconditions, failure modes, detection, recovery.
- **Wants explicit artifacts.** Ledgers, not "verify it."
- **Aggressive pseudo-code planning** over document-style plans.
- **"Ship it" = proceed through all steps without pausing.**
- **Tends toward analysis paralysis.** Flag when overthinking and push to ship.
- **Direct communication.** No filler phrases. Be concise.

---

## Relevant Files

| File | What It Is |
|------|-----------|
| `CANONICAL.md` | File manifest — if it's not listed here, it doesn't exist |
| `SYSTEM_BUILD_PLAN.md` | Master blueprint with all 6 build phases |
| `README.md` | System overview with module reference and quick start |
| `contracts/` | 6 data format contracts between modules (user thinks this should be collapsed) |
| `skills/` | Claude Code skills for each module |
| `runs/` | Output folder — one subfolder per vendor domain |

## Relevant Context From Other Projects

- **YouTube Brain vault** at `/Users/lucas/Documents/projects/YouTube Brain/` — the second brain system whose architectural principles should inform GTM Alpha simplification
- **Vyborov capture note:** `Inbox/The Sovereign AI Setup That Changed Everything.md` — the video that triggered this conversation. Contains playbook evolution pattern (Stage 1-4) and sovereign agent infrastructure.
- **Version B spec:** `Library/Version B Experiment — Bobyard Automated Signal-to-Email Pipeline.md` — the experiment spec being reframed
- **Vibe GTM:** `Library/Vibe GTM - Full Outbound Campaign Built and Launched from Claude Code.md` — proof that terminal-to-send pipeline works

---

## Harness Engineering Analysis — Added Insights

**Source:** Deep brainstorming session in YouTube Brain project, cross-referencing Harness Engineering frameworks + 16 vault notes against GTM Alpha's current architecture.

**Core finding:** The session briefing's four improvement areas (retro loop, Layer 4 teardown, simplification, Version B reframe) aren't separate fixes. They're one harness — the same pattern OpenAI used to ship 1M lines of agent-generated code. When an agent struggles, fix the environment, not the agent.

---

### Deployment Audit Score: 11/15

Scored GTM Alpha against the Claude Code Deployment Audit (5 maturity dimensions, 0-3 each). Below 12 = not production-ready.

| Dimension | Score | Why |
|---|---|---|
| Persistent Memory | 2 | CLAUDE.md + CANONICAL exist, but no cross-run memory — each vendor run starts cold |
| Skills Architecture | 3 | All modules are proper skills with SKILL.md + references/ |
| Feedback Loop | 1 | Module 4B exists on paper but hasn't run. No compound loop feeding outcomes back into 1A |
| Output Verifiability | 2 | Claim verification in 3B, but 1A signal scores and 3A play designs have no verification mechanism |
| Context Architecture | 3 | Clean folder hierarchy, contracts, CANONICAL.md |

**Weakest dimension:** Feedback Loop (1/3). This is the Layer 4 problem, but also the retro loop problem — no mechanism captures what went wrong and feeds it back upstream.

**Target:** Get to 12+ before considering GTM Alpha production-ready. Re-score after each build session.

---

### The Four-Component Harness for GTM Alpha

```yaml
GTM_ALPHA_HARNESS:

  1_LEGIBILITY:
    what: "Rename everything human-readable. Collapse contracts."
    specifics:
      - Replace module numbers (1A, 2B, 3C) with plain names (Research Brief, Call Synthesis, Play Design)
      - Collapse 6 contracts to 2-3 reference docs
      - CANONICAL.md should read like the vault's folder names — four words, four purposes
    principle: "Documentation as Navigation — short maps pointing to deeper sources, not encyclopedias"

  2_TASTE_INVARIANTS:
    what: "3-5 mechanical quality checks on module outputs. Pass/fail, no judgment."
    candidates:
      - "play_strength = value_score × unique_score; below 25 = auto-kill (from Feature Mapping framework)"
      - "every claim cites a source (from Deployment Audit dimension 4)"
      - "signal has detection method defined (already in Layer 1)"
      - "email anchors to specific signal event, not generic pain point"
      - "PVP contains zero unverified claims (already enforced in 3B)"
    principle: "Vault works on 5 taste invariants. GTM Alpha needs the same — small number of deterministic checks."
    build_before: "Layer 4. Every future run generates cleaner data if taste invariants exist first."

  3_RETRO_LOOP:
    what: "60-second brain dump → classify by upstream module → quick fix or diagnosis note"
    enhancement: "Also build self-scoring INTO modules (recursive self-improvement pattern) so fewer failures reach the human"
    mechanism:
      - "generate output → score against criteria checklist → diagnose failures → rewrite → repeat until passing"
      - "Module 3B already does this (claim verification + STOP_OUTPUT). Extend to 1A, 3A, 3C."
    principle: "Compound Engineering — every run teaches the next one. Cognitive Debt Cascade is the failure mode if this doesn't exist."
    build_before: "Layer 4. The retro loop IS the feedback mechanism Layer 4 depends on."

  4_GARBAGE_COLLECTION:
    what: "Automated freshness checks. Layer 4 pulls from sequencer APIs, not manual logs."
    specifics:
      - "Instantly API provides opens, replies, bounces, conversions — no manual logging needed"
      - "Signal scores should have expiration dates — stale signals get flagged automatically"
      - "Schedule maintenance BEFORE entropy is visible, not quarterly"
    principle: "Harness Engineering — fight entropy before it compounds."
    build_when: "Layer 4 redesign. But the ARCHITECTURE decision (pull, don't push) should be locked now."
```

---

### Build Order Recommendation

Components 2 (Taste Invariants) and 3 (Retro Loop) are pre-work for Layer 4, not separate improvements. Building them first means every future run generates cleaner data for the learning engine.

```yaml
RECOMMENDED_SEQUENCE:
  1. Legibility pass (rename modules, collapse contracts) — 1 session
  2. Define 3-5 taste invariants for module outputs — 1 session
  3. Build retro loop skill — 1 session
  4. Run GTM Alpha on a real domain with taste invariants + retro loop active
  5. THEN redesign Layer 4 with API integration (informed by retro loop data)
```

---

### Key Frameworks to Apply (Not Yet in GTM Alpha)

**Cognitive Debt Cascade** — The systemic risk when output exceeds review capacity. Every GTM Alpha run that doesn't capture what went wrong accumulates cognitive debt. Signal scores drift. Play designs reference stale intel. PVPs cite dead data. It compounds. The retro loop is the antidote.

**Recursive Self-Improvement Loop** — The mechanism for building quality gates INTO modules. Generate → score against criteria → diagnose → rewrite → repeat. Module 3B already does this with claim verification. Extend the pattern to other modules.

**Agent Handoff Chain** — Each module should reduce the context surface for the next module. Instead of 3A consuming 1A's raw output directly, an intermediate context reduction step would make modules more independent and less tightly coupled.

**Feature Mapping as Taste Invariant** — Crawford's `play_strength = value × demonstrability; < 25 = kill` is a mechanical check that belongs in play design, not in the operator's head.

**"The Harness IS the Deployable Infrastructure"** — Version B's reframe ("make specs machine-readable") combined with Harness Engineering means: the contracts, taste invariants, and quality gates you build for GTM Alpha ARE the product you eventually deploy for clients. You're not selling documents or operator time — you're selling the harness.

---

### Vault Notes to Reference During Revamp

**For Taste Invariants + Quality Gates:**
- `Reference/Vault Taste Invariants.md` — The pattern to copy. 5 mechanical checks, pass/fail, YAML spec.
- `Library/Recursive Self-Improvement Loops for Claude Marketing Skills.md` — The generate → score → diagnose → improve loop. Blueprint for self-scoring modules.
- `Library/B2B Sellers - How to develop targeting and messaging that no one can compete with BY feature.md` — Feature Mapping scoring logic (value × demonstrability) as a concrete taste invariant for play design.

**For the Retro Loop:**
- `Library/Compound Engineering - How to Make Claude Code Get Better Every Time You Use It.md` — The 4-step compound loop (plan → work → assess → compound). The retro loop is the "assess + compound" steps applied to GTM output.
- `Library/Cognitive Debt Compounds Faster Than AI Ships Code.md` — The risk model for why the retro loop matters. Cognitive debt cascade = what happens without it.
- `Library/I Used AI Agents for Every Go-To-Market Role (Sales Marketing CS RevOps).md` — The Compounding SOP Loop: good output → write SOP → save → feed to future agents. Same pattern as retro loop feeding fixes back upstream.

**For Layer 4 Redesign:**
- `Reference/Claude Code Deployment Audit - Five Maturity Dimensions.md` — Dimension 3 (Feedback Loop) is the scoring rubric for whether Layer 4 is working. Re-score after redesign.
- `Library/Version B Experiment — Bobyard Automated Signal-to-Email Pipeline.md` — The pipeline architecture (PredictLeads → Prospeo → Claude API → Instantly) that Layer 4 should pull outcome data FROM.

**For System Simplification:**
- `Reference/Context_OS_Architecture.md` — The vault constitution pattern. Four architectural decisions, each with what/why/where-enforced/failure-mode. Model for consolidating GTM Alpha's scattered rules.
- `Reference/Context Engineering Framework - Theory Architecture and Case Study.md` — The three-layer pyramid (Taste → Architecture → Prompts). Prompts are 5% of the problem — if GTM Alpha's output is bad, the fix is almost always upstream in taste or architecture, not in the prompt.
- `Library/Harness Engineering - Leveraging Codex in an Agent-First World.md` — The source framework. Context engineering + architectural constraints + garbage collection. "When an agent struggles, fix the harness, not the agent."

**For Module Decoupling:**
- `Library/How I Built an AI Agent Operating System in 90 Days.md` — LeanScale's 4-repo architecture where each agent reduces context surface for the next. Blueprint for making GTM Alpha modules more independent.
