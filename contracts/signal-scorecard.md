# Signal Scorecard Contract

**Version:** v1.0
**Role:** Defines the format for scored signal output from Module 1A (Vendor Intelligence Brief)
**Status:** Active

---

## Purpose

Every signal hypothesis in Section 5 of the brief must be scored. Scoring replaces subjective "High / Medium / Low" conviction labels with a weighted composite that enables ranking, cut decisions, and downstream comparison against outcome data (Phase 5).

---

## Scoring Dimensions

| Dimension | Weight | Scale | What It Measures |
|-----------|--------|-------|------------------|
| **Volume** | 20% | 1–5 | How often does this signal fire across the target market annually? 1 = <25/yr, 2 = 25–100, 3 = 100–500, 4 = 500–2,000, 5 = 2,000+ |
| **Detectability** | 25% | 1–5 | How reliably can this signal be observed in public data? 1 = mostly internal/unobservable, 2 = occasionally surfaces, 3 = available but requires manual effort, 4 = available via structured data source, 5 = fully automatable via API/feed |
| **Specificity** | 25% | 1–5 | How tightly does this signal connect to {TARGET_DOMAIN}'s specific value prop? 1 = generic business health indicator, 2 = industry-relevant but not pain-specific, 3 = pain-adjacent, 4 = directly indicates the specific pain, 5 = only explainable by the exact problem {TARGET_DOMAIN} solves |
| **Timing Precision** | 15% | 1–5 | How clearly does the signal define a window for outreach? 1 = no timing signal (chronic condition), 2 = vague timing (within a year), 3 = seasonal pattern, 4 = event-triggered with ~30-day window, 5 = event-triggered with <14-day window |
| **Actionability** | 15% | 1–5 | How directly can this signal be converted into an outbound play? 1 = awareness only, 2 = requires significant additional research, 3 = usable with moderate personalization, 4 = ready for templatized outreach, 5 = ready for automated trigger + template |

---

## Composite Score Calculation

```
Composite = (Volume × 0.20) + (Detectability × 0.25) + (Specificity × 0.25) + (Timing Precision × 0.15) + (Actionability × 0.15)
```

Score range: 1.00–5.00

---

## Cut Threshold

**Composite score below 2.5 → signal is killed from the final brief output.**

Killed signals are listed in Section 8 (Research Confidence Assessment) under "Signals killed by scoring" with their composite score and the reason for the lowest-scoring dimension. They are not included in Sections 4 or 5.

---

## Output Format (in PROMPT_TEMPLATE.md)

Each signal in Section 5 must include this table immediately after the Differentiation Assessment:

```markdown
**Signal Score:**

| Dimension | Score (1–5) | Rationale |
|-----------|-------------|-----------|
| Volume | [n] | [one-sentence justification] |
| Detectability | [n] | [one-sentence justification] |
| Specificity | [n] | [one-sentence justification] |
| Timing Precision | [n] | [one-sentence justification] |
| Actionability | [n] | [one-sentence justification] |
| **Composite** | **[n.nn]** | |
```

---

## Downstream Consumers

- **Module 2C (Reconciliation):** Updates composite scores based on call evidence
- **Module 3A (Play Design):** Uses composite scores to prioritize which signals become plays
- **Module 4B (Performance Updater):** Compares predicted scores against actual outcome data
