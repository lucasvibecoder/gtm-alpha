# GTM Alpha v3

Signal-based intelligence operating system for B2B outbound. Turns research into executable plays, tracks outcomes, and compounds over time.

---

## How It Works

GTM Alpha v3 is a 4-layer system. Each layer feeds the next.

```
Layer 1: RESEARCH        → Intelligence brief with scored signals and detection specs
Layer 2: VALIDATION      → Call data validates/calibrates the research (optional)
Layer 3: EXECUTION       → Executable plays + PVP deliverables + operational blueprint
Layer 4: LEARNING ENGINE → Outcome tracking updates signal scores for next cycle
```

---

## Two Paths

### Fast Path (no call data)

```
Module 1A (Vendor Intelligence Brief)
    ↓
Module 3A (Play Design)  →  Module 3B (PVP Generator)
                         \→  Module 3C (Execution Blueprint)
    ↓
Module 4A (Outcome Logger)  →  Module 4B (Performance Updater)
```

Use this when starting with a new vendor and no first-party call data exists. Go straight from research to plays.

### Full Path (with call data)

```
Module 1A (Vendor Intelligence Brief)
    ↓
Module 2A (Call Extractor, runs in Clay)  →  Module 2B (Call Synthesis)
    ↓
Module 2C (Reconciliation) ← compares 1A vs 2B
    ↓
Module 3A (Play Design)  →  Module 3B (PVP Generator)
                         \→  Module 3C (Execution Blueprint)
    ↓
Module 4A (Outcome Logger)  →  Module 4B (Performance Updater)
```

Use this when 15+ discovery call transcripts are available. Call data validates research hypotheses and calibrates messaging with buyer language.

---

## Module Reference

### Layer 1: Research

| Module | Type | What It Does | Location |
|--------|------|-------------|----------|
| **1A+1B** Vendor Intelligence Brief | Skill | Generates scored intelligence brief with signal validation, scoring, and detection specs | `skills/vendor-intelligence-brief/` |

### Layer 2: Validation

| Module | Type | What It Does | Location |
|--------|------|-------------|----------|
| **2A** Call Intelligence Extractor | Prompt (Clay) | Per-call JSON extraction with Tier 1/Tier 2 split | `prompts/call-intelligence-extractor/` |
| **2B** Call Intelligence Synthesis | Skill | 7-section pattern analysis across 15+ call extractions | `skills/call-intelligence-synthesis/` |
| **2C** Signal & Messaging Reconciliation | Skill | 5-section reconciliation with signal score updates | `skills/reconciliation/` |

### Layer 3: Execution

| Module | Type | What It Does | Location |
|--------|------|-------------|----------|
| **3A** Play Design & PVP Architecture | Skill | 3 executable plays with signal detection, A/B variants, outcome tracking | `skills/play-design/` |
| **3B** PVP Generator | Skill | Produces actual PVP deliverables from play design specs | `skills/pvp-generator/` |
| **3C** Execution Blueprint | Skill | Operational blueprint: sourcing, Clay tables, sending config, PVP batching, tracking, weekly rhythm | `skills/execution-blueprint/` |

### Layer 4: Learning Engine

| Module | Type | What It Does | Location |
|--------|------|-------------|----------|
| **4A** Outcome Logger | Template | Per-touch outcome tracking (JSON schema) | `templates/outcome-log.json` |
| **4B** Signal Performance Updater | Skill | Analyzes outcomes, updates signal scores, identifies winners | `skills/signal-performance-updater/` |
| **4C** Pattern Library | Template | Cross-client patterns that transfer across engagements | `templates/pattern-library.md` |

### Contracts

| Contract | What It Defines | Location |
|----------|----------------|----------|
| Call Extraction Schema | Tier 1/Tier 2 field spec + enum registry for Module 2A | `contracts/call-extraction-schema.md` |
| Signal Scorecard | 5-dimension weighted scoring for signals | `contracts/signal-scorecard.md` |
| Detection Spec | Per-signal monitoring specification | `contracts/detection-spec.md` |
| Outcome Log | Per-touch tracking schema | `contracts/outcome-log.md` |
| Performance Update | Module 4B output format with score update rules | `contracts/performance-update.md` |

---

## Quick Start

### 1. Run your first brief

Trigger Module 1A with a target domain:

> "Run GTM Alpha for ramp.com"

The skill researches the company, produces a scored intelligence brief with signal hypotheses, and validates each signal against real-world data.

### 2. Design plays

Take the Module 1A output and run Module 3A:

> "Design plays for ramp.com" (paste the brief)

You get 3 executable plays with signal detection filters, A/B email variants, and outcome tracking targets.

### 3. Generate PVPs

Take a PVP spec from Module 3A and run Module 3B:

> "Generate a PVP for acme-corp.com" (paste the PVP spec)

You get an email-ready deliverable — competitive audit, benchmark, or analysis — with zero vendor mentions.

### 4. Build the execution blueprint

Take the Module 3A play design + Module 1A brief and run Module 3C:

> "Build an execution blueprint for bobyard.com" (reference the play design and brief)

You get a step-by-step operations plan: which tools to use for this ICP type, exact Clay table columns, sending tool warm-up plan, PVP production workflow, tracking sheet setup, and a weekly schedule.

### 5. Track outcomes

Log each outbound touch using the outcome log schema (`templates/outcome-log.json`). After 50+ entries, run Module 4B:

> "Update signal scores" (paste or reference the outcome log)

You get updated signal scores, variant winners, and kill/promote recommendations.

### 6. Close the loop

Updated signal scores feed back into Module 1A for the next brief refresh. Winning messaging feeds into Module 3A for the next play design. The system gets smarter every cycle.

---

## File Structure

```
gtm-alpha/
├── CANONICAL.md                          # File manifest (source of truth)
├── SYSTEM_BUILD_PLAN.md                  # Architecture blueprint
├── README.md                             # This file
├── .gitignore                            # Ignores runs/* (except README)
├── contracts/
│   ├── call-extraction-schema.md         # Module 2A field spec + enums
│   ├── signal-scorecard.md               # Signal scoring dimensions
│   ├── detection-spec.md                 # Signal detection format
│   ├── outcome-log.md                    # Outcome tracking schema
│   └── performance-update.md             # Module 4B output format
├── skills/
│   ├── vendor-intelligence-brief/        # Module 1A+1B
│   │   ├── SKILL.md
│   │   └── references/
│   ├── call-intelligence-synthesis/      # Module 2B
│   │   ├── SKILL.md
│   │   └── references/
│   ├── reconciliation/                   # Module 2C
│   │   ├── SKILL.md
│   │   └── references/
│   ├── play-design/                      # Module 3A
│   │   ├── SKILL.md
│   │   └── references/
│   ├── pvp-generator/                    # Module 3B
│   │   ├── SKILL.md
│   │   └── references/
│   ├── execution-blueprint/              # Module 3C
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── PROMPT_TEMPLATE.md
│   │       └── ICP_SOURCING_MATRIX.md
│   └── signal-performance-updater/       # Module 4B
│       ├── SKILL.md
│       └── references/
├── prompts/
│   └── call-intelligence-extractor/      # Module 2A (Clay prompt, not a skill)
│       └── PROMPT.md
├── templates/
│   ├── outcome-log.json                  # Module 4A empty template
│   └── pattern-library.md               # Module 4C cross-client patterns
├── runs/                                 # Output — one folder per vendor (gitignored)
│   ├── README.md                         # Convention docs (public)
│   └── {domain}/                         # e.g., bobyard.com/
│       ├── brief.md                      # Module 1A output
│       ├── play-design-{date}.md         # Module 3A output
│       ├── execution-blueprint-{date}.md # Module 3C output
│       └── pvps/                         # Module 3B deliverables
│           └── {prospect}-{type}.md
└── archive/
    └── v2/                               # Archived v1.0 versions
```
