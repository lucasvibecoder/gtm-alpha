# GTM Alpha v3 — Canonical File Manifest

**Rule:** If a file isn't listed here, it doesn't exist in the system.

Last updated: 2026-02-15 (Phase 4)

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
| Call Intelligence Extractor | v2.0 | Module 2A — Clay/Sonnet prompt with Tier 1/Tier 2 extraction split — Active | `prompts/call-intelligence-extractor/PROMPT.md` |
| Call Intelligence Synthesis | v2.0 | Module 2B — 7-section compressed synthesis, Tier 2-aware — Active | `prompts/call-intelligence-synthesis/PROMPT.md` |
| Signal & Messaging Reconciliation | v2.0 | Module 2C — 5-section net-new intelligence, signal score updates, re-scored signal list — Active | `prompts/reconciliation/PROMPT.md` |

---

## Layer 3: Execution

| File | Version | Role | Location |
|------|---------|------|----------|
| Play Design & PVP Architecture | v3.0 | Module 3A — Executable plays with signal detection, A/B variants, outcome tracking, PVP specs — Active | `prompts/play-design/PROMPT.md` |
| PVP Generator (Skill) | v1.0 | Module 3B — Produces actual PVP deliverables (competitive audits, signal analyses, benchmarks) from PVP specs — Active | `skills/pvp-generator/SKILL.md` |
| PVP Output Template | v1.0 | Output structure for Module 3B deliverables | `skills/pvp-generator/references/PVP_OUTPUT_TEMPLATE.md` |

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
| Call Extraction Schema | v2.0 | Tier 1/Tier 2 schema, full enum registry, data flow, zero unresolved breaking changes — Active | `contracts/call-extraction-schema.md` |
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
