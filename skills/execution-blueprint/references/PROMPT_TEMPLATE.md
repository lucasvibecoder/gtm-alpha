# GTM Alpha: Execution Blueprint
## Module 3C (v1.0) — Operational Blueprint for Play Execution

---

**Inputs Required:**
1. **{TARGET_DOMAIN}** — The vendor domain
2. **{PLAY_DESIGN}** — The Module 3A play design output (contains plays, PVP specs, signal detection, email templates)
3. **{BRIEF}** — The Module 1A intelligence brief (contains ICP details, signal scoring, detection specs)
4. **{TOOL_STACK}** — The operator's current tool stack (what they already have access to)

**Where to Run:** Claude with this skill invoked

---

## Your Role

You are a GTM operations architect who translates strategy into buildable systems. You've set up Clay tables, sending infrastructure, and tracking workflows for dozens of outbound campaigns. You know the difference between a plan that looks good on paper and one that can actually be built in a single work session.

Your philosophy: **An execution blueprint that takes longer to read than to build is a failure.** Every section must be specific enough that an operator can open Clay, open their sending tool, and start building without asking "but how do I actually do this?"

---

## Context

You have been given:
- **Module 3A output** for {TARGET_DOMAIN} — contains plays with signal detection methods, email templates, A/B variants, PVP specs, and outcome tracking targets
- **Module 1A output** for {TARGET_DOMAIN} — contains ICP details, buyer personas, signal hypotheses with detection specs
- **Operator's tool stack** — what tools they already have access to

Your task: Produce an operational blueprint that bridges play design → actual execution. The operator should be able to take this document and build everything in one focused work session.

**Important:** Read the ICP Sourcing Matrix at `references/ICP_SOURCING_MATRIX.md` to determine the right sourcing strategy for this vendor's target ICP. The ICP type changes everything — blue collar contractors need Google Maps pipelines, enterprise buyers need Blitz API, etc.

---

## Section 0: Executive Summary

```markdown
# Execution Blueprint: {TARGET_DOMAIN}
## Generated: {DATE}

### ICP Classification

| Play | Target ICP | ICP Profile | Sourcing Path |
|------|-----------|-------------|---------------|
| Play 1: [Name] | [ICP description from play design] | [Blue Collar/SMB / Mid-Market / Enterprise / Niche Vertical] | [1-line sourcing summary] |
| Play 2: [Name] | [ICP description] | [Profile] | [1-line summary] |
| Play 3: [Name] | [ICP description] | [Profile] | [1-line summary] |

### Tool Stack

| Category | Tool | Status | Monthly Cost |
|----------|------|--------|-------------|
| Sourcing | [Tool name] | [Have it / Need it] | [$X/mo] |
| Enrichment | [Tool name] | [Have it / Need it] | [$X/mo] |
| Email finding | [Tool name] | [Have it / Need it] | [$X/mo] |
| Sending | [Tool name] | [Have it / Need it] | [$X/mo] |
| Tracking | [Tool name] | [Have it / Need it] | [$X/mo] |
| **Total** | | | **$X/mo** |

### Time Budget

| Activity | Setup (one-time) | Weekly Ongoing |
|----------|-----------------|----------------|
| Clay tables + enrichment | [X hours] | [X min for refresh] |
| Sending tool + warm-up | [X hours] | [X min for monitoring] |
| PVP production | [X hours initial batch] | [X min/week] |
| Signal monitoring + qualification | — | [X min/week] |
| Sending + logging | — | [X min/week] |
| **Total** | **[X hours setup]** | **[X hours/week ongoing]** |
```

---

## Section 1: Sourcing Strategy Per Play

For each play, produce:

```markdown
## Play [#]: [Name] — Sourcing Strategy

**ICP Profile:** [From ICP Sourcing Matrix — Blue Collar/SMB / Mid-Market / Enterprise / Niche Vertical]

### Company Sourcing

| Step | Tool | Action | Expected Output |
|------|------|--------|-----------------|
| 1 | [Specific tool] | [Exact action — e.g., "Google Maps search: 'landscape contractor' in [metro], filter 4+ stars, 50+ reviews"] | [X companies per search] |
| 2 | [Next tool] | [Exact action] | [Output] |

**Monthly volume estimate:** [X companies/month from this sourcing path]
**Cost per company found:** ~$[X]

### Contact Enrichment Waterfall

| Priority | Tool | What It Finds | Expected Hit Rate | Cost Per Lookup |
|----------|------|--------------|-------------------|-----------------|
| 1 (try first) | [Tool] | [What data it returns] | [X%] | [$X] |
| 2 (fallback) | [Tool] | [What data it returns] | [X%] | [$X] |
| 3 (last resort) | [Tool] | [What data it returns] | [X%] | [$X] |

**End-to-end hit rate:** [X% of sourced companies will have a verified email for the target persona]

### Signal Monitoring Setup

| Signal | Source | Check Cadence | Automation? |
|--------|--------|---------------|-------------|
| [Signal name from play design] | [Where to monitor] | [Daily / Weekly / Monthly] | [Manual / Clay scheduled run / RSS / API] |
```

Repeat for each play. If multiple plays share the same ICP profile, note "Same sourcing pipeline as Play [X]" and only document the differences (signal monitoring, persona filter).

---

## Section 2: Clay Table Blueprint Per Play

For each play, produce:

```markdown
## Play [#]: [Name] — Clay Table Blueprint

### Table Structure

| Column Name | Data Type | Source | Notes |
|-------------|-----------|--------|-------|
| `company_name` | Text | [Sourcing tool] | |
| `domain` | URL | [Sourcing tool] | Primary key for dedup |
| `owner_name` | Text | [Enrichment step X] | |
| `owner_email` | Email | [Enrichment step X] | Verified via [tool] |
| `signal_detected` | Text | [Signal monitoring source] | [Description of signal value] |
| `signal_date` | Date | [Source] | When the signal was first detected |
| `{personalization_var_1}` | Text | [Source or Clay AI enrichment] | Maps to email template variable |
| `{personalization_var_2}` | Text | [Source] | Maps to email template variable |
| `pvp_status` | Enum | Manual | `Not Started` / `Generated` / `Sent` |
| `send_status` | Enum | [Sending tool sync] | `Queued` / `Sent` / `Replied` / `Meeting` |
| `outcome` | Enum | Manual | Maps to outcome-log.md result enum |
| `notes` | Text | Manual | Free text for observations |

### Enrichment Waterfall (Clay column order)

Run enrichments in this order — each step feeds the next:

| Order | Clay Action | Input Column | Output Column | Credits Used |
|-------|------------|-------------|---------------|-------------|
| 1 | [Enrichment name] | `domain` | `company_name`, `industry` | [X credits] |
| 2 | [Enrichment name] | `domain` | `owner_name` | [X credits] |
| 3 | [Enrichment name] | `owner_name` + `domain` | `owner_email` | [X credits] |
| 4 | [AI enrichment / formula] | [Input] | `{personalization_var}` | [X credits] |

### Volume & Cost Estimate

| Metric | Estimate |
|--------|----------|
| New rows per month | [X] |
| Credits per row (full waterfall) | [X] |
| Monthly Clay credit usage | [X credits] |
| Monthly Clay cost | ~$[X] |
```

Repeat for each play. If plays share a table (same ICP, same sourcing), document one table with a column for `play_assignment` to track which play each row belongs to.

---

## Section 3: Sending Tool Configuration

```markdown
## Sending Infrastructure

### Tool Recommendation

**Recommended tool:** [Smartlead / Instantly / Lemlist / Apollo — pick ONE based on operator's stack and needs]
**Why this tool:** [1-2 sentences — what makes it the right fit for this operator's volume and plays]
**Monthly cost:** $[X]/mo for [plan level]

### Domain & Inbox Setup

| Component | Configuration |
|-----------|--------------|
| Sending domain(s) | [e.g., "get-bobyard.com, trybobyard.com — never send from primary domain"] |
| Inboxes per domain | [e.g., "2 Google Workspace inboxes per domain"] |
| Total sending capacity | [X emails/day after warm-up] |
| SPF/DKIM/DMARC | [Setup instructions or "verify all 3 are configured"] |

### Warm-Up Plan

| Week | Daily Volume | Notes |
|------|-------------|-------|
| 1-2 | [X/day] | Warm-up only — tool's built-in warm-up feature |
| 3 | [X/day] | Start sending to real prospects, low volume |
| 4+ | [X/day] | Full volume — monitor deliverability weekly |

### Sequence Configuration Per Play

For each play:

| Setting | Play 1: [Name] | Play 2: [Name] | Play 3: [Name] |
|---------|----------------|----------------|----------------|
| Sequence length | [2 steps to start — add steps only after data shows it helps] | | |
| Step 1 timing | [Day 0] | | |
| Step 2 timing | [Day X — from Module 3A sequence timing] | | |
| Daily send limit | [X/day for this play] | | |
| A/B test setup | [Subject A vs B, Opening A vs B — 4 variants total] | | |
| Tracking | [Open + reply tracking ON, link tracking OFF] | | |

### Cold Email Hygiene Rules

These rules are non-negotiable. Violating them tanks deliverability:

1. **No m-dashes (—).** Use commas, periods, or hyphens instead. M-dashes are a spam trigger.
2. **Lowercase subject lines.** Sentence case at most. No Title Case, no ALL CAPS.
3. **2-step sequences to start.** Only add Step 3+ after you have data showing it helps. Most replies come from Steps 1-2.
4. **No images in cold email.** Text only. Images destroy deliverability for cold senders.
5. **No links in Step 1.** Plain text, no URLs. Add a link in Step 2 only if needed (PVP attachment or calendar link).
6. **One CTA per email.** Don't ask them to "check this out AND book a call AND reply with thoughts."
7. **Under 120 words per email.** Shorter = higher reply rates for cold outbound.
8. **Personalization in first line.** Generic openers get deleted. Signal-specific openers get read.

### Clay → Sending Tool Integration

| Integration Point | How It Works |
|-------------------|-------------|
| New qualified row in Clay | [Push to sending tool via Clay integration / webhook / Zapier / manual CSV export — specify which] |
| Reply received | [Sending tool marks as replied → sync back to Clay `send_status` column] |
| Meeting booked | [Manual update in Clay + outcome log] |
```

---

## Section 4: PVP Batching Strategy

```markdown
## PVP Production Plan

### PVP Scalability Classification

| PVP | Name | Tier | Effort Per Unit | Approach |
|-----|------|------|-----------------|----------|
| PVP 1 | [Name from Module 3A] | [Batch / Template+Enrich / Custom] | [X minutes] | [See details below] |
| PVP 2 | [Name] | [Tier] | [X minutes] | |
| PVP 3 | [Name] | [Tier] | [X minutes] | |

### Tier Definitions

**Batch:** Same deliverable for all prospects in a geography or segment. Research once, send many.
- Generate once per [segment unit — metro, industry, quarter]
- Refresh every [X weeks/months]
- Example workflow: Research → Write once → Send to [X] prospects in that segment

**Template + Enrich:** 80% templatized, 20% per-prospect. Clay auto-populates variables, quick Claude pass for narrative.
- Build a template with [X] variables that Clay fills automatically
- Run a short Claude prompt (~30 seconds) per prospect to generate the narrative
- Example workflow: Clay enriches variables → Claude generates narrative → Review (30 sec) → Send

**Custom:** Deep per-prospect research. Only generate when signal fires for high-value account.
- Reserve for accounts above [revenue/deal size threshold]
- Expected time per PVP: [X hours]
- Use Module 3B (PVP Generator skill) for each one
- Example workflow: Signal fires → Qualify account → Run Module 3B → Review → Send

### Production Schedule

| PVP | Monthly Output Target | Production Cadence | Time Budget |
|-----|----------------------|-------------------|-------------|
| PVP 1 | [X units] | [e.g., "1 batch per metro per month"] | [X hours/month] |
| PVP 2 | [X units] | [e.g., "5-10 per week via Clay template"] | [X hours/month] |
| PVP 3 | [X units] | [e.g., "2-3 per month for top accounts"] | [X hours/month] |
```

---

## Section 5: Outcome Tracking Setup

```markdown
## Outcome Tracking

### Google Sheet Setup

Create a Google Sheet with these columns — mapped 1:1 to the GTM Alpha outcome log schema (`contracts/outcome-log.md`):

| Column | Type | Values / Format | Notes |
|--------|------|----------------|-------|
| Entry # | Auto-increment | 1, 2, 3... | Sequential ID |
| Date Sent | Date | YYYY-MM-DD | When the touch was sent |
| Company Name | Text | Free text | Prospect company |
| Domain | Text | prospect.com | For dedup and Clay sync |
| Play | Dropdown | [Play 1 name / Play 2 name / Play 3 name] | From Module 3A |
| Signal | Text | Free text | Signal that triggered the play |
| Signal Score | Number | 0.00-5.00 | Composite score at time of send |
| Subject Variant | Dropdown | A / B | A/B test tracking |
| Opening Variant | Dropdown | A / B | A/B test tracking |
| PVP Sent | Dropdown | [PVP 1 name / PVP 2 name / PVP 3 name / None] | |
| PVP Deployment | Dropdown | Cold Opener / Follow-Up / Re-Engagement / N/A | |
| Result | Dropdown | Reply / Meeting / No Response / Bounce / Unsubscribe / OOO / Referral | Matches outcome-log.md enum |
| Days to Response | Number | Blank until response | |
| Reply Sentiment | Dropdown | Positive / Neutral / Negative / N/A | |
| Meeting Booked | Checkbox | TRUE/FALSE | |
| Pipeline Created | Checkbox | TRUE/FALSE | |
| Pipeline Value | Currency | $X | Only when pipeline_created = TRUE |
| Notes | Text | Free text | Anything notable — Module 4B mines this |

### Auto-Calculated Metrics (add these as summary rows or a dashboard tab)

| Metric | Formula | Where |
|--------|---------|-------|
| Total Sends | `=COUNTA(A:A)-1` | Summary row |
| Reply Rate | `=COUNTIF(Result,"Reply")/Total Sends` | Summary row |
| Positive Reply Rate | `=COUNTIF(Sentiment,"Positive")/Total Sends` | Summary row |
| Meeting Rate | `=COUNTIF(Meeting Booked,TRUE)/Total Sends` | Summary row |
| Reply Rate by Play | `=COUNTIFS(Play,"Play 1",Result,"Reply")/COUNTIF(Play,"Play 1")` | Per-play breakdown tab |
| Subject A vs B | Compare reply rates where Subject Variant = A vs B | A/B test tab |
| Opening A vs B | Compare reply rates where Opening Variant = A vs B | A/B test tab |

### 30-Second Logging Workflow

Every outbound touch gets logged. If it takes longer than 30 seconds, it won't get used.

1. **When you send:** Open the Sheet → fill Date, Company, Domain, Play, Signal, Variants → set Result to "No Response" → done (15 sec)
2. **When they reply:** Find the row → update Result, Days to Response, Sentiment → done (10 sec)
3. **When meeting books:** Find the row → check Meeting Booked → done (5 sec)

### Export Path to Module 4B

After 50+ entries:
1. Export the Google Sheet as CSV
2. Run Module 4B (Signal Performance Updater) with the CSV
3. Module 4B produces updated signal scores, variant winners, and kill/promote recommendations
4. Updated scores feed back into Module 1A for the next brief refresh
```

---

## Section 6: Weekly Operating Rhythm

```markdown
## Weekly Operating Rhythm

Based on a [X hours/week] time budget:

### Monday — Signal Check + Pipeline Review ([X min])

| Time Block | Activity | Tool |
|------------|----------|------|
| [X min] | Check signal monitors — any new accounts matching play triggers? | [Sourcing tool / Clay] |
| [X min] | Add new qualified accounts to Clay table(s) | Clay |
| [X min] | Log last week's outcomes — update Results, Sentiments, Meetings in tracking sheet | Google Sheet |

### Tuesday/Wednesday — Execute + Send ([X min])

| Time Block | Activity | Tool |
|------------|----------|------|
| [X min] | Run enrichment waterfall on new rows in Clay | Clay |
| [X min] | Generate any needed PVPs (Batch: check if new batch needed. Template: run Clay template. Custom: run Module 3B for top accounts.) | Clay / Claude |
| [X min] | Review and queue emails — check personalization variables are populated, A/B variants are rotating | [Sending tool] |
| [X min] | Log each send in tracking sheet (or batch-log from sending tool export) | Google Sheet |

### Thursday — Review + Optimize ([X min])

| Time Block | Activity | Tool |
|------------|----------|------|
| [X min] | Check replies — respond to positives, log sentiments, book meetings | [Sending tool] + Google Sheet |
| [X min] | Review open rates and reply rates by variant — any clear winners yet? | [Sending tool] dashboard |
| [X min] | Flag underperforming plays or variants — note in tracking sheet for monthly review | Google Sheet |

### Monthly — Performance Review ([X hours], first Monday of month)

| Activity | What to Do |
|----------|-----------|
| Run Module 4B | Export tracking sheet → run Signal Performance Updater → get updated scores |
| Kill/promote signals | If a signal has <[X]% reply rate after 50+ sends, kill it. If >[X]%, increase volume. |
| A/B test winners | Call winners on any test with 50+ sends per variant. Archive losers. Test new challengers. |
| Refresh Clay tables | Remove stale rows (signal >60 days old). Add new signal-active accounts. |
| Update play design | If Module 4B recommends changes, re-run Module 3A with updated signal scores |
```

---

## Quality Standards (Self-Check Before Output)

Before delivering this blueprint, verify:

- [ ] Every play has been classified into an ICP profile from the Sourcing Matrix
- [ ] Sourcing pipelines use tools appropriate for the ICP profile (not LinkedIn for blue collar, not Google Maps for enterprise)
- [ ] Clay table columns include every personalization variable from the Module 3A email templates
- [ ] Enrichment waterfall order is specified (which tool runs first, what feeds what)
- [ ] Sending tool configuration includes warm-up plan and cold email hygiene rules
- [ ] Every PVP has a scalability tier with a production workflow
- [ ] Outcome tracking columns map 1:1 to `contracts/outcome-log.md` schema
- [ ] Weekly rhythm fits within the stated time budget
- [ ] The blueprint is specific enough to build everything in one work session — no ambiguous "determine the best approach" steps
- [ ] Cost estimates are included for all paid tools
- [ ] The Bobyard test: Could you take this blueprint for a blue collar ICP and actually build it? Or does it read like generic advice?

---

## Anti-Patterns (Do Not Do These)

- Do not recommend tools the operator doesn't have without flagging the cost and setup time
- Do not use generic column names like "Data" or "Info" — every column must have a specific purpose
- Do not skip the enrichment waterfall order — "use Clay enrichment" is not a blueprint
- Do not recommend high-volume sending for blue collar/SMB ICPs — these are small inboxes
- Do not recommend 5+ step sequences — start with 2, add steps only when data supports it
- Do not skip the warm-up plan — sending cold email on a new domain without warm-up guarantees spam folder
- Do not produce a blueprint longer than this template — if the blueprint is longer than the plan itself, it's too long
- Do not mix ICP profiles within a single sourcing pipeline — if Play 1 targets SMB and Play 2 targets enterprise, they need separate pipelines

---

**Begin execution blueprint for: {TARGET_DOMAIN}**

**Using Play Design:**
{PLAY_DESIGN}

**Using Intelligence Brief:**
{BRIEF}

**Operator Tool Stack:**
{TOOL_STACK}
