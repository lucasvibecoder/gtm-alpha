# GTM Alpha — System Rules

## Output Rules (enforced on every module)

1. **No numbers without a source.** Every data point traces to a specific source (URL, database, API). If it can't be sourced, mark it `[H]` (hypothesis) in internal docs, use plain language ("estimated based on...") in client-facing docs, or omit it entirely. Never present estimates as facts.

2. **Operator logistics stay out of client-facing output.** Tool costs, time budgets, enrichment hit rates, Clay column orders, and weekly operating rhythms are operator concerns. Client-facing output (play design, PVPs) describes what and why — not how the operator builds it.

3. **No unsourced KPI targets.** Do not output "target: 55% open rate" or "8% reply rate" unless tagged with a cited source. Campaign performance depends on too many variables to predict. State what you'll track, not what you'll hit.

4. **Internal docs use `[H]` markers. Client-facing docs use plain language.** The brief (1A) is an internal research doc — mark unverified claims inline with `[H]`. Play designs and PVPs are client-facing — no system notation. Either source the claim, describe it as an estimate in plain English, or omit it.

5. **Lead with the insight, not the methodology.** First paragraph = the most important finding. Action items before reference details. If a section doesn't change the reader's next decision, compress or cut it.

## Audience Model

- **Internal docs** (brief, operator checklist): system notation OK, `[H]` markers, operator detail, tool-specific queries
- **Client-facing docs** (play design, PVPs): no system notation, no module numbers, no contract references, plain language only
