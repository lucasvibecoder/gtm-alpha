# GTM Alpha v3 — Canonical File Manifest

**Rule:** If a file isn't listed here, it doesn't exist in the system.

Last updated: 2026-02-22 (Phase 4 — data source registry, detection spec v1.1, Step 3d validation)

---

## System Files

| File | Version | Role | Location |
|------|---------|------|----------|
| Canonical Manifest | v1.0 | This file. Lists every active file and its role. | `CANONICAL.md` |
| README | v1.0 | System overview, module reference, quick start guide | `README.md` |
| CLAUDE.md | v1.0 | System-wide output rules and audience model for Claude Code | `CLAUDE.md` |
| Phase 4 Build Plan | v1.0 | Validation layer design + active backlog tracker | `PHASE4_BUILD_PLAN.md` |

---

## Layer 1: Research

| File | Version | Role | Location |
|------|---------|------|----------|
| Vendor Intelligence Brief (Skill) | v3.0 | Module 1A + 1B — Generates scored intelligence brief with signal validation, scoring, detection specs, and `[H]` markers | `skills/vendor-intelligence-brief/SKILL.md` |
| Brief Output Template | v3.0 | Output structure for Module 1A (includes signal scoring + detection spec tables) | `skills/vendor-intelligence-brief/references/PROMPT_TEMPLATE.md` |
| Example Brief (Bobyard) | v1.0 | Reference example of a completed brief | `skills/vendor-intelligence-brief/references/example-brief-bobyard.md` |
| Example Signal Validation | v1.0 | Reference example of deep signal validation | `skills/vendor-intelligence-brief/references/example-signal-validation.md` |

---

## Layer 2: Validation

| File | Version | Role | Location |
|------|---------|------|----------|
| Call Intelligence Extractor (Prompt) | v2.0 | Module 2A — Clay/Sonnet prompt with Tier 1/Tier 2 extraction split — Active | `prompts/call-intelligence-extractor/PROMPT.md` |
| Call Intelligence Synthesis (Skill) | v2.0 | Module 2B — 7-section compressed synthesis, Tier 2-aware — Active | `skills/call-intelligence-synthesis/SKILL.md` |
| Call Intelligence Synthesis Template | v2.0 | Prompt template for Module 2B | `skills/call-intelligence-synthesis/references/PROMPT_TEMPLATE.md` |
| Signal & Messaging Reconciliation (Skill) | v2.0 | Module 2C — 5-section net-new intelligence, signal score updates, re-scored signal list — Active | `skills/reconciliation/SKILL.md` |
| Reconciliation Template | v2.0 | Prompt template for Module 2C | `skills/reconciliation/references/PROMPT_TEMPLATE.md` |

---

## Layer 3: Execution

| File | Version | Role | Location |
|------|---------|------|----------|
| Play Design & PVP Architecture (Skill) | v4.0 | Module 3A — Client-facing plays with signal detection, A/B variants, inline PVP specs. Operator detail stripped to 3C. | `skills/play-design/SKILL.md` |
| Play Design Template | v4.0 | Prompt template for Module 3A (client-facing, no operator logistics) | `skills/play-design/references/PROMPT_TEMPLATE.md` |
| PVP Generator (Skill) | v2.1 | Module 3B — PVP deliverables with claim verification, Verification Ledger (now with source_tier), claim budget, STOP_OUTPUT — Active | `skills/pvp-generator/SKILL.md` |
| PVP Output Template | v1.0 | Output structure for Module 3B deliverables | `skills/pvp-generator/references/PVP_OUTPUT_TEMPLATE.md` |
| Operator Checklist (Skill) | v2.0 | Module 3C — Operator checklist absorbing detection queries, enrichment steps, PVP production detail from play design. Replaces Execution Blueprint. | `skills/execution-blueprint/SKILL.md` |
| Operator Checklist Template | v2.0 | Output structure for Module 3C (7 sections: ICP, signals, enrichment, sending, PVP production, QA, tool stack) | `skills/execution-blueprint/references/PROMPT_TEMPLATE.md` |
| ICP Sourcing Matrix | v1.0 | Reusable reference — maps ICP type to sourcing strategy, tools, costs | `skills/execution-blueprint/references/ICP_SOURCING_MATRIX.md` |

---

## Layer 4: Learning Engine

| File | Version | Role | Location |
|------|---------|------|----------|
| Outcome Log Template | v1.0 | Module 4A — Empty JSON array for per-touch outcome tracking — Active | `templates/outcome-log.json` |
| Signal Performance Updater (Skill) | v1.0 | Module 4B — Analyzes outcome data, updates signal scores, identifies variant winners, kill/promote signals — Active | `skills/signal-performance-updater/SKILL.md` |
| Performance Update Template | v1.0 | Output structure for Module 4B reports | `skills/signal-performance-updater/references/PERFORMANCE_UPDATE_TEMPLATE.md` |
| Pattern Library | v1.0 | Module 4C — Cross-client pattern library (signal archetypes, PVP formats, objection frameworks) — Active | `templates/pattern-library.md` |

---

## Contracts

| File | Version | Role | Location |
|------|---------|------|----------|
| Call Extraction Schema | v2.0 | Tier 1/Tier 2 schema, full enum registry, data flow — Active | `contracts/call-extraction-schema.md` |
| Signal Scorecard | v1.0 | Scored signal output format for Module 1A — Active | `contracts/signal-scorecard.md` |
| Detection Spec | v1.1 | Signal detection specification format with Claim Type and Structured Params for Step 3d routing — Active | `contracts/detection-spec.md` |
| Data Source Registry | v1.0 | Machine-readable catalog of validated API sources for signal validation (Step 3d) — Active | `contracts/data-source-registry.md` |
| Outcome Log | v2.0 | Per-touch outcome tracking schema with enums — Active | `contracts/outcome-log.md` |
| Performance Update | v2.0 | 5-section performance update format with score update rules — Active | `contracts/performance-update.md` |
| Claim Verification | v1.1 | Claim tiers, source tiers (V1/V2/H), Verification Ledger spec, claim budget, send rules, STOP_OUTPUT policy for Module 3B — Active | `contracts/claim-verification.md` |

---

## Prompts (Non-Skill Modules)

| File | Version | Role | Location |
|------|---------|------|----------|
| Call Intelligence Extractor | v2.0 | Module 2A — Runs in Clay/Sonnet, not Claude Code | `prompts/call-intelligence-extractor/PROMPT.md` |

Note: Modules 2B, 2C, and 3A also retain their original PROMPT.md files at `prompts/*/PROMPT.md` for reference. The canonical versions are the skill files listed above.

---

## Output (runs/)

All skill output is saved to `runs/{vendor-domain}/`. One folder per vendor, gitignored except `runs/README.md`.

| File Pattern | Module | Location |
|---|---|---|
| `brief.md` | 1A | `runs/{domain}/brief.md` |
| `call-synthesis-{date}.md` | 2B | `runs/{domain}/call-synthesis-{date}.md` |
| `reconciliation-{date}.md` | 2C | `runs/{domain}/reconciliation-{date}.md` |
| `2-play-design-{date}.md` | 3A | `runs/{domain}/2-play-design-{date}.md` |
| `{prospect}-{type}.md` | 3B | `runs/{domain}/pvps/{prospect}-{type}.md` |
| `3-operator-checklist-{date}.md` | 3C | `runs/{domain}/3-operator-checklist-{date}.md` |
| `performance-update-{date}.md` | 4B | `runs/{domain}/performance-update-{date}.md` |
| `bid-radar-pipeline.md` | 3B (pipeline) | `runs/{domain}/bid-radar-pipeline.md` — Structured data pipeline spec for Batch PVPs (replaces web search with APIs) |

See `runs/README.md` for the full convention.

---

## Archive

| Directory / File | Contents |
|-----------|----------|
| `archive/v2/` | Dead v1.0 prompt versions (archived for reference, not for use) |
| `archive/SYSTEM_BUILD_PLAN.md` | Original master blueprint (Phases 0-6). Superseded by PHASE4_BUILD_PLAN.md for active planning. |
| `archive/REVAMP_BRIEF.md` | Revamp planning doc (Sessions 1-3, all completed 2026-02-21). Decisions captured in memory. |
| `archive/SESSION_BRIEFING.md` | Feb 19 brainstorm notes that seeded the revamp. Historical. |

---

## File Status Key

- **Active:** File is in use by the current system
- **Archived:** Superseded version, kept for reference only
