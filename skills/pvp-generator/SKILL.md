---
name: pvp-generator
description: >
  Generate a Permissionless Value Prop (PVP) deliverable for a specific prospect. Use this skill when the user
  wants to create a PVP asset — a competitive audit, signal analysis, benchmark report, or any insight deliverable
  that provides value independent of a sale. Trigger when the user says "generate a PVP", "build a PVP for [company]",
  "create a [deliverable type] for [prospect]", or provides a PVP spec from Module 3A play design output.
---

# GTM Alpha: PVP Generator

## What This Skill Does

Takes a PVP spec (from Module 3A Play Design output) and a target prospect, then produces the actual deliverable — a research-backed asset ready to email. The output is a file (markdown or PDF) that the prospect would find valuable even if they never buy.

This is Module 3B in the GTM Alpha system. It turns PVP concepts into real assets.

**Input:** A PVP spec + prospect company domain
**Output:** A finished deliverable file ready to send

## Workflow

### Step 1: Gather Input

Ask the user for:
1. **PVP spec** (required) — Either:
   - Paste the PVP spec from Module 3A output (preferred — contains deliverable definition, data required, research steps)
   - Or describe the deliverable: what type of analysis, who it's for, what it should contain
2. **Vendor domain** (required) — The vendor this PVP belongs to (e.g., `bobyard.com`) — determines which `runs/` folder to save into
3. **Prospect domain** (required) — The company this PVP is being created for (e.g., `acme-landscaping.com`)
4. **Prospect context** (optional) — Any additional context: prospect name, role, recent signals detected, notes from CRM
5. **Output format** (optional, default: markdown) — markdown or PDF

If the user already provided these in their message, skip the questions and proceed.

### Step 2: Understand the Deliverable

Before researching, confirm what you're building. Parse the PVP spec to identify:

- **Deliverable type:** What kind of asset (competitive audit, signal analysis, benchmark, gap analysis, etc.)
- **Target persona:** Who will read this and what do they care about
- **Research scope:** What data sources to hit
- **Value proposition:** Why this is worth their time to read
- **Proof alignment:** What trust barriers this addresses (if specified in the PVP spec)

### Step 3: Research Phase

**Before researching, check the PVP spec's `Data Source Type` field:**

- **IF Structured:** The PVP's core data comes from APIs, databases, or procurement portals named in the spec. Query those sources directly. AI web search MUST NOT be used as a substitute for the named sources.
- **IF Web Search:** Use the standard 10-15+ web search approach below.
- **IF Hybrid:** Use structured sources for the claims flagged in the spec, web search for the rest.
- **IF not specified:** Default to Web Search.

This is a research-heavy skill. Use **10-15+ web searches** tailored to the deliverable type (or structured source queries if Data Source Type = Structured).

**Research sequence (adapt based on PVP type):**

**For Competitive Audits:**
1. Prospect's website — positioning, messaging, key claims (2-3 searches)
2. Prospect's top 3 competitors — same dimensions (3-5 searches)
3. Industry benchmarks for the comparison dimensions (1-2 searches)
4. G2/review sites for competitive intelligence (1-2 searches)
5. Recent news/changes for prospect and competitors (1-2 searches)

**For Signal/Market Analysis:**
1. Prospect's market segment — trends, shifts, pressures (2-3 searches)
2. Specific signals relevant to the prospect (3-5 searches)
3. Comparable companies and what happened to them (2-3 searches)
4. Data sources for the analysis (1-2 searches)
5. Industry reports or benchmarks to reference (1-2 searches)

**For Benchmark Reports:**
1. Prospect's current metrics/performance (if publicly available) (2-3 searches)
2. Industry benchmark data (3-5 searches)
3. Best-in-class examples (2-3 searches)
4. Methodology sources to cite (1-2 searches)

**For Gap Analyses:**
1. Prospect's current approach (website, job postings, tech stack signals) (2-3 searches)
2. Best practices in the relevant area (2-3 searches)
3. Specific gaps with supporting evidence (3-5 searches)
4. Cost of the gaps / value of closing them (1-2 searches)

Do not move to output until you have specific, cited data points. Generic observations are worthless.

### Step 3b: Claim Verification Pass

**This step is mandatory.** Run it on every data point collected in Step 3, before any of it enters the deliverable. Follow the rules in `contracts/claim-verification.md`.

```
FOR EACH data point collected in Step 3:

  1. CLASSIFY: Is this Static, Dynamic, or Attributed?
     - Contains a date, deadline, "expiring," "currently," "active," "open" → Dynamic
     - Says "[Entity] did/has/posted/issued [thing]" → Attributed
     - Everything else → Static
     - A single claim can be BOTH (e.g., "Dallas Parks contract expiring Feb 28"
       is Dynamic + Attributed)

  2. VERIFY based on tier:

     IF Static:
       - Does the cited source URL contain this specific data point?
       - PASS: Source contains the claim → include in deliverable
       - FAIL: Source doesn't mention it → find a source that does, or exclude

     IF Dynamic:
       - Visit the source URL directly (not via search snippet — the actual page)
       - Is the data current? Is the listing active? Is the deadline in the future?
       - PASS: Data is live and current → include with "as of [date]" qualifier
       - FAIL: Page shows "no results," listing removed, deadline passed → EXCLUDE
       - FAIL: Cannot access source (paywall, 404, login) → EXCLUDE.
         Mark as INACCESSIBLE in ledger.

     IF Attributed:
       - Does the source name the SPECIFIC entity being cited?
       - PASS: Source says "City of Dallas Parks issued RFP..." → confirmed
       - FAIL: Source says "Northside ISD issued RFP..." but claim says
         "Dallas Parks" → KILL. Do not attempt to repair attribution.
       - FAIL: Source is an aggregator that doesn't name the issuing entity
         clearly → visit the original procurement portal to confirm. Mark as
         AGGREGATOR_NEEDS_ORIGIN until resolved.

  3. RECORD in Verification Ledger:
     - Add a row per claim: claim_text, tier, discovered_via_url, verified_url,
       entity_on_page, entity_in_claim, as_of_timestamp, status
     - verified_url = the original source (NOT the aggregator)
     - as_of_timestamp = ISO 8601 of the actual verification moment
     - Claims without a ledger row CANNOT enter the deliverable

  4. ENFORCE Claim Budget:
     - Cover note: max 1-2 high-specificity claims (Dynamic or Attributed).
       Prefer Static. Demote or remove excess.
     - Body: cap Dynamic claims at ~3. Remove lowest-confidence if over.

  5. CHECK STOP_OUTPUT:
     - IF cover note hook claim status ≠ PASS → STOP
     - IF >50% of Dynamic claims FAIL → STOP
     - IF primary Attributed claim FAIL → STOP
       (Primary Attributed = cover note hook if Attributed, else first
        Attributed in thesis section, else N/A)
     - HARD INVARIANT: every cover note claim must exist as a ledger row
       with status=PASS. If ANY is missing or ≠ PASS → STOP
     - IF STOP triggered: output STATUS: NEEDS_MORE_RESEARCH at top,
       include ledger + verified content, do NOT ship as final
```

The Verification Ledger is appended to the deliverable file as an internal appendix (after the Sources section, marked as internal — not shown to prospect).

### Step 4: Produce the Deliverable

Read the output template at `references/PVP_OUTPUT_TEMPLATE.md` and adapt it to the specific deliverable type.

**Core rules for the deliverable:**

1. **Prospect-specific.** Every insight must reference the prospect by name with specific data. "Companies in your industry" is a failure. "[Prospect name]'s current [metric] compared to [benchmark]" is correct.

2. **Cited and Verified.** Every claim must have a source, and the claim must pass verification per `contracts/claim-verification.md`. A cited URL that doesn't contain the claim is worse than no source — it signals fabrication. No unsourced assertions. If you can't find data, say "we were unable to find public data on [X]" — don't fabricate.

3. **Actionable.** End with 2-3 specific recommendations the prospect can act on regardless of whether they buy. This is what makes it permissionless.

4. **Not a pitch.** The vendor ({TARGET_DOMAIN}) should appear zero times in the deliverable body. The PVP builds trust by being useful, not by selling. The only mention of the vendor is in the cover note (Step 5).

5. **Concise.** Target 800-1200 words for the deliverable body. Executives don't read 10-page reports from people they don't know.

### Step 5: Write the Cover Note

Write a 3-4 sentence cover note that:
- References the signal or context that prompted this outreach (from the paired play)
- Introduces the deliverable in one sentence
- Asks a lightweight question (not "can we book a call?" but "does this match what you're seeing?")

The cover note is NOT part of the deliverable file — it's the email body that accompanies the attachment.

### Step 6: Quality Gates

Before finalizing, verify:
- [ ] Every data point in the deliverable is prospect-specific (not generic industry observations)
- [ ] Every claim has a cited source
- [ ] The deliverable contains zero mentions of {TARGET_DOMAIN} or the vendor
- [ ] The deliverable ends with 2-3 actionable recommendations
- [ ] Total length is 800-1200 words (excluding tables and charts)
- [ ] The cover note references the triggering signal from the paired play
- [ ] A prospect would find this useful even if they never buy — the "permissionless" test passes
- [ ] Verification Ledger exists and contains a row for EVERY claim in body + cover note
- [ ] Every data point classified as Static, Dynamic, or Attributed
- [ ] Every Dynamic claim visited at source URL and confirmed current
- [ ] Every Attributed claim confirmed at source to name the correct entity
- [ ] No claim cites a URL that does not contain the claimed data
- [ ] Cover note contains max 1-2 high-specificity claims (claim budget enforced)
- [ ] Body contains ≤ 3 Dynamic claims (claim budget enforced)
- [ ] Cover note contains ONLY claims that passed verification (strict subset of ledger PASS rows)
- [ ] STOP_OUTPUT conditions checked — if triggered, deliverable marked NEEDS_MORE_RESEARCH
- [ ] No claim in the deliverable has status ≠ PASS in the ledger

### Step 7: Deliver

Save the deliverable based on the user's chosen format:
- **Markdown** (default): Save as `runs/{vendor-domain}/pvps/{prospect-domain}-{pvp-type}.md` (create the directory if it doesn't exist)
- **PDF**: Read `/mnt/skills/public/pdf/SKILL.md`, then generate a formatted PDF

Present both the cover note and the deliverable file to the user.

## Anti-Patterns

- **Vendor mentions in the deliverable.** The PVP is not a sales pitch. Zero mentions of {TARGET_DOMAIN} in the body.
- **Generic insights.** "Companies in your industry are adopting AI" is useless. "[Prospect] has 3 job postings mentioning manual data entry while [Competitor A] and [Competitor B] have none — suggesting an automation gap" is useful.
- **Unsourced claims.** Every data point needs a source. No source = don't include it.
- **Too long.** This is a cold outreach asset. 800-1200 words. Not a consulting report.
- **Buried value.** The most interesting finding goes in the first paragraph, not the conclusion. If they stop reading after 30 seconds, they should still get the key insight.
