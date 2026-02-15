# GTM Alpha v3 — Canonical File Manifest

**Rule:** If a file isn't listed here, it doesn't exist in the system.

Last updated: 2026-02-15 (Phase 1)

---

## System Files

| File | Version | Role | Location |
|------|---------|------|----------|
| System Build Plan | v1.0 | Master blueprint for all build phases | `SYSTEM_BUILD_PLAN.md` |
| Canonical Manifest | v1.0 | This file. Lists every active file and its role. | `CANONICAL.md` |

---

## Layer 1: Research

| File | Version | Role | Location |
|------|---------|------|----------|
| Vendor Intelligence Brief (Skill) | v3.0 | Module 1A + 1B — Generates scored intelligence brief with signal validation, scoring, and detection specs | `skills/vendor-intelligence-brief/SKILL.md` |
| Brief Output Template | v3.0 | Output structure for Module 1A (includes signal scoring + detection spec tables, no Strategic Recommendations section) | `skills/vendor-intelligence-brief/references/PROMPT_TEMPLATE.md` |
| Example Brief (Bobyard) | v1.0 | Reference example of a completed brief | `skills/vendor-intelligence-brief/references/example-brief-bobyard.md` |
| Example Signal Validation | v1.0 | Reference example of deep signal validation | `skills/vendor-intelligence-brief/references/example-signal-validation.md` |

---

## Layer 2: Validation

| File | Version | Role | Location |
|------|---------|------|----------|
| Call Intelligence Extractor | v2.0 | Module 2A — Clay prompt for extracting buyer intelligence from call transcripts | `prompts/call-intelligence-extractor/PROMPT.md` |
| Call Intelligence Synthesis | v2.0 | Module 2B — Synthesizes extracted call data across transcripts | `prompts/call-intelligence-synthesis/PROMPT.md` |
| Signal & Messaging Reconciliation | v1.0 | Module 2C — Reconciles research hypotheses against call evidence | `prompts/reconciliation/PROMPT.md` |

---

## Layer 3: Execution

| File | Version | Role | Location |
|------|---------|------|----------|
| Play Design & PVP Architecture | v2.1 | Module 3A — Designs executable outbound plays | `prompts/play-design/PROMPT.md` |

---

## Layer 4: Learning Engine

No modules built yet. Templates and prompts will be added in Phase 5.

| File | Version | Role | Location |
|------|---------|------|----------|
| Outcome Log Template | — | Module 4A placeholder | `templates/` (TBD — Phase 5) |
| Pattern Library | — | Module 4C placeholder | `templates/` (TBD — Phase 5) |

---

## Contracts

| File | Version | Role | Location |
|------|---------|------|----------|
| Call Extraction Schema | v1.0 | Structured output format for Module 2A | `contracts/call-extraction-schema.md` |
| Signal Scorecard | v1.0 | Scored signal output format for Module 1A — Active | `contracts/signal-scorecard.md` |
| Detection Spec | v1.0 | Signal detection specification format — Active | `contracts/detection-spec.md` |
| Outcome Log | v1.0 | Outbound outcome tracking format for Module 4A | `contracts/outcome-log.md` |
| Performance Update | v1.0 | Signal score update format for Module 4B | `contracts/performance-update.md` |

---

## Archive

| Directory | Contents |
|-----------|----------|
| `archive/v2/` | Dead v1.0 prompt versions (archived for reference, not for use) |

---

## File Status Key

- **Active:** File is in use by the current system
- **TBD:** Placeholder — contract or prompt structure exists, content not yet built
- **Archived:** Superseded version, kept for reference only
