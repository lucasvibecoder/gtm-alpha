# Performance Update Output Template

**Version:** v1.0
**Role:** Output structure for Module 4B (Signal Performance Updater) reports

---

## Usage

Follow this structure for every Performance Update report. All 5 sections are required. See `contracts/performance-update.md` for the full specification of rules, thresholds, and quality standards.

---

## Template

```markdown
# Signal Performance Update: {TARGET_DOMAIN}
## Analysis Date: {DATE}

---

### Dataset Summary

| Metric | Value |
|--------|-------|
| **Total Entries Analyzed** | [N] |
| **Date Range** | [Earliest entry] — [Latest entry] |
| **Plays Represented** | [List play names with entry counts] |
| **Signals Represented** | [List signal names with entry counts] |
| **Data Sufficiency** | [Sufficient (50+) / Preliminary (20-49) / Insufficient (<20)] |

---

### 1. Signal Performance Scorecard

| Signal Name | Entries | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommended Composite | Change | Action |
|-------------|---------|------------|---------------------|--------------|-------------------|-----------------------|--------|--------|
| [Signal] | [N] | [N/total = %] | [N/total = %] | [N/total = %] | [n.nn] | [n.nn] | [+/- n.nn] | [Keep / Boost / Demote / Kill / Insufficient Data] |

**Score Update Detail:**

For each signal where the composite score changes:

**[Signal Name]:** [Action]
- Current composite: [n.nn]
- Updated composite: [n.nn]
- Change: [+/- n.nn]
- Rationale: [Which dimensions changed and why, citing specific performance data]
- Recalculation: (Vol×0.20) + (Det×0.25) + (Spec×0.25) + (Tim×0.15) + (Act×0.15) = [n.nn]

---

### 2. Messaging Variant Performance

| Play | Test Element | Variant A | Variant B | A Rate | B Rate | Winner | Confidence |
|------|-------------|-----------|-----------|--------|--------|--------|------------|
| [Play] | Subject Line | [Text] | [Text] | [N/total = %] | [N/total = %] | [A / B / No Winner] | [High / Low / Insufficient Data] |
| [Play] | Opening Line | [Text] | [Text] | [N/total = %] | [N/total = %] | | |

**Recommendations:**
- [For each High-confidence winner: promote to default, create new challenger]
- [For Low-confidence or Insufficient Data: continue testing, estimate sends needed to reach confidence]

---

### 3. Signals to Kill

| Signal Name | Total Sends | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommendation |
|-------------|-------------|------------|---------------------|--------------|-------------------|----------------|
| [Signal] | [N] | [%] | [%] | [%] | [n.nn] | Kill — [reason] |

[If no signals meet kill criteria: "No signals meet kill criteria in this cycle."]

---

### 4. Signals to Promote

| Signal Name | Total Sends | Reply Rate | Positive Reply Rate | Meeting Rate | Current Composite | Recommended Composite | Recommendation |
|-------------|-------------|------------|---------------------|--------------|-------------------|-----------------------|----------------|
| [Signal] | [N] | [%] | [%] | [%] | [n.nn] | [n.nn] | Boost — [reason] |

[If no signals meet promote criteria: "No signals meet promote criteria in this cycle. Continue logging data."]

---

### 5. Emerging Patterns

**Emerging Signal Candidates:**
[Patterns from notes field. Format: "[N] entries mention [pattern] — consider adding as signal hypothesis."]

**Persona Insights:**
[Response rate differences by industry, company size, or role. Report as co-occurrences with counts.]

**Timing Insights:**
[Day-of-week or seasonal observations, if visible in the data.]

**PVP Performance:**
| PVP Name | Sends with PVP | Reply Rate (with PVP) | Reply Rate (without PVP) | Deployment Timing | Notes |
|----------|----------------|----------------------|-------------------------|-------------------|-------|
| [PVP] | [N] | [%] | [%] | [Cold Opener / Follow-Up / Re-Engagement] | |

**Recommendations for Next Cycle:**
1. [Specific action citing data]
2. [Specific action citing data]
3. [Specific action citing data]

---

### Updated Signal Rankings

The re-ranked signal list for the next cycle:

| Rank | Signal Name | Updated Composite | Change from Previous | Action Taken | Status |
|------|-------------|-------------------|---------------------|-------------|--------|
| 1 | [Signal] | [n.nn] | [+/- n.nn] | [Keep / Boost / New] | Active |
| 2 | | | | | |
| ... | | | | | |
| — | [Killed signal] | 0.00 | [-n.nn] | Kill | Removed |
```
