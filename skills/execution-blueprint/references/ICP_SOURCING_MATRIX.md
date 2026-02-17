# ICP Sourcing Matrix

**Version:** v1.0
**Role:** Maps ICP types to sourcing strategies, enrichment waterfalls, tools, costs, and expected hit rates
**Status:** Active

This matrix grows over time — every vendor engagement adds data about what works for which ICP type.

---

## How to Use This Matrix

1. Classify your target ICP into one of the four profiles below
2. Use the **Primary Sourcing** column to identify how to find prospect companies
3. Use the **Contact Enrichment** column to identify how to find the right person + email
4. Use the **Cost & Volume** column to estimate monthly spend
5. If a prospect doesn't fit cleanly into one profile, use the closest match and note the deviation

---

## Profile 1: Blue Collar / SMB

**Who:** Trades contractors, landscapers, HVAC, plumbers, roofers, concrete, excavation. Owner-operated businesses with 5-200 employees. Revenue $1M-$30M. Thin digital footprint — these companies often have no LinkedIn presence, minimal website, and owners don't use email daily.

**Example Vendors Targeting This ICP:** Bobyard, ServiceTitan, Jobber, CompanyCam

### Sourcing Pipeline

| Step | Tool | What It Does | Expected Hit Rate |
|------|------|-------------|-------------------|
| 1. Find companies | Google Maps + Apify scraper | Search by service category + geography. Scrape business name, address, phone, website, reviews, rating. | 90%+ of local businesses found |
| 2. Find owner name | Website scrape (Apify or Clay) + AI extraction | Scrape About/Team page, extract owner name. Fall back to Google search "[company name] owner" | 60-75% owner found |
| 3. Find email | Prospeo (primary) → website scrape → Hunter.io (fallback) | Email waterfall: try Prospeo first (best for SMB), then scrape website contact pages, then Hunter.io pattern match | 50-65% verified email found |
| 4. Verify email | NeverBounce or ZeroBounce | Verify deliverability before sending | 85-90% of found emails verify |

### Key Details

- **Primary data source:** Google Maps (not LinkedIn, not ZoomInfo — these people aren't there)
- **Apify cost:** ~$1 per 1,000 emails scraped (Google Maps Actor + Contact Info Extractor)
- **Prospeo cost:** ~$39/mo for 1,000 credits
- **Best signal sources:** Google Maps reviews (volume, recency, rating), job postings on Indeed/ZipRecruiter (not LinkedIn), permit filings, government bid databases
- **Contact strategy:** Email owner directly. No SDR-to-AE handoff — the owner IS the buyer.
- **Warm-up note:** These owners get very little cold email. Lower volume = higher response rates. 30-50 sends/week is plenty.

### Anti-Patterns

- Do NOT use LinkedIn Sales Nav as primary sourcing — most blue collar owners don't have profiles
- Do NOT use ZoomInfo/Apollo for company data — coverage is <20% for this segment
- Do NOT send high-volume sequences — these are real people with small teams, not enterprise inboxes

---

## Profile 2: Mid-Market

**Who:** Companies with 200-2,000 employees, $50M-$500M revenue. Regional or national players with established digital presence. Buyers have LinkedIn profiles, companies have websites with leadership pages, and standard B2B data providers have decent coverage.

**Example Vendors Targeting This ICP:** Ramp, Gong, Lattice, Brex

### Sourcing Pipeline

| Step | Tool | What It Does | Expected Hit Rate |
|------|------|-------------|-------------------|
| 1. Find companies | LinkedIn Sales Nav + Clay enrichment | Sales Nav account search with industry/size/geo filters. Export to Clay for enrichment. | 80-90% of target companies found |
| 2. Find contacts | LinkedIn Sales Nav (people search) | Filter by title + company from step 1. Pull decision makers. | 70-85% of target personas found |
| 3. Find email | Apollo (primary) → Prospeo (fallback) → Hunter.io (last resort) | Email waterfall: Apollo has best mid-market coverage, Prospeo catches what Apollo misses | 65-80% verified email found |
| 4. Verify email | NeverBounce or ZeroBounce | Verify deliverability | 90%+ of found emails verify |

### Key Details

- **Primary data source:** LinkedIn Sales Nav ($99/mo) + Clay ($149/mo for enrichment credits)
- **Apollo cost:** $49-99/mo depending on plan
- **Best signal sources:** LinkedIn (job postings, headcount growth, funding), Clay signal monitoring, G2 intent data, company blog/PR, 10-K/quarterly filings (for public)
- **Contact strategy:** Target VP/Director level. May need multi-threading (2-3 contacts per account).
- **Volume:** 100-200 sends/week is sustainable with proper warm-up

### Anti-Patterns

- Do NOT rely solely on Apollo for company discovery — it's better for contacts than accounts
- Do NOT skip LinkedIn Sales Nav — it's the best account-level filter for mid-market
- Do NOT send to C-suite only — VP/Director level has higher reply rates in mid-market

---

## Profile 3: Enterprise

**Who:** Companies with 2,000+ employees, $500M+ revenue. Public companies, PE-backed platforms, Fortune 1000. Complex org structures, multiple decision makers, long sales cycles. Data providers have excellent coverage.

**Example Vendors Targeting This ICP:** Snowflake, Databricks, Workday, Salesforce

### Sourcing Pipeline

| Step | Tool | What It Does | Expected Hit Rate |
|------|------|-------------|-------------------|
| 1. Build full TAM | Blitz API (domain → full enrichment) | Feed a list of domains, get back full company + contact data. Unlimited enrichment model. | 95%+ company data coverage |
| 2. Find contacts | Blitz API → LinkedIn Sales Nav validation | Blitz returns contacts by title. Validate on LinkedIn to confirm current role. | 85-95% of target personas found |
| 3. Find email | Blitz API (primary) → ZoomInfo (fallback) → Lusha (direct dials) | Enterprise emails are well-covered. Blitz or ZoomInfo for email, Lusha for direct phone. | 80-90% verified email found |
| 4. Verify email | NeverBounce or ZeroBounce | Verify deliverability | 90%+ verify |

### Key Details

- **Primary data source:** Blitz API (~$600/mo unlimited enrichment) OR ZoomInfo ($15K+/yr)
- **Blitz advantage:** Flat-rate unlimited enrichment vs. per-credit pricing. Better for full-TAM plays.
- **Best signal sources:** Earnings calls, 10-K filings, board appointments, M&A activity, tech stack changes (BuiltWith/Wappalyzer), Glassdoor reviews (org dysfunction signals)
- **Contact strategy:** Multi-thread 3-5 contacts per account. Map the buying committee. Executive sponsors + technical evaluators + procurement.
- **Volume:** 50-100 sends/week. Quality over quantity. Every email should feel bespoke.

### Anti-Patterns

- Do NOT use Google Maps or Apify — enterprise data is clean in standard providers
- Do NOT single-thread to one contact — enterprise deals require multi-threading
- Do NOT send PVPs that could be generated for any company — enterprise buyers expect depth

---

## Profile 4: Niche Vertical

**Who:** Specialized segments where prospects are found through domain-specific databases, not general B2B tools. Examples: government RFP responders, PE portfolio companies, franchise operators, healthcare systems, higher education institutions.

**Example Vendors Targeting This ICP:** BidPrime (gov contractors), PitchBook (PE-backed), Definitive Healthcare (health systems)

### Sourcing Pipeline

| Step | Tool | What It Does | Expected Hit Rate |
|------|------|-------------|-------------------|
| 1. Find companies | Domain-specific database (see sub-profiles below) | Query the vertical-specific database to build initial company list | Varies by database — 70-95% |
| 2. Classify company profile | Manual or AI classification | Determine which of the 4 ICP profiles the company matches for enrichment | N/A |
| 3. Find contacts | Follow the enrichment path matching the company's size profile | Use Profile 1/2/3 enrichment based on company size | Varies by profile |
| 4. Find email | Follow the email path matching the company's size profile | Use Profile 1/2/3 email waterfall | Varies by profile |

### Sub-Profiles and Domain-Specific Sources

| Niche | Primary Database | What It Provides | Cost |
|-------|-----------------|-----------------|------|
| Government contractors | BidPrime, SAM.gov, state procurement portals | Companies bidding on specific RFP categories | BidPrime: ~$499/mo. SAM.gov: free |
| PE portfolio companies | PitchBook, Crunchbase Pro | Companies acquired by PE firms, with deal details | PitchBook: ~$20K/yr. Crunchbase: $49/mo |
| Franchise operators | FDD filings (public), franchise disclosure databases | Franchise locations, unit economics, operator info | Varies |
| Healthcare systems | Definitive Healthcare, CMS data | Hospital systems, clinic networks, provider data | Definitive: ~$12K/yr |
| Higher education | IPEDS (free), College Navigator | Institution size, budgets, program offerings | Free |

### Key Details

- **The pattern:** Niche vertical sourcing always has two steps: (1) find companies through the vertical-specific source, (2) enrich contacts using whichever standard profile matches the company's size
- **Snowflake MCP pattern:** For advanced setups, raw public data (government filings, permit databases) can be loaded into Snowflake and queried via MCP for stakeholder mapping and intelligence packages. This turns messy public data into structured prospect lists.
- **Best signal sources:** The domain-specific database IS the signal source. RFP posted = signal. PE acquisition = signal. New franchise filing = signal.

### Anti-Patterns

- Do NOT try to source niche verticals through LinkedIn Sales Nav alone — you'll miss 50%+ of the market
- Do NOT skip the company classification step — a government contractor could be 5 people or 5,000 people, and the enrichment path is completely different
- Do NOT assume one enrichment path works for all companies in the niche — size determines the path

---

## Choosing Cheap AI Processing

When you need AI at scale for any profile (owner identification from website scraping, email copy generation, data classification):

| Option | Cost | Best For |
|--------|------|----------|
| Claude via API | ~$3-15 per 1M input tokens (varies by model) | PVP generation, email personalization, data classification |
| Vast.ai GPU rental | ~$0.69/hr | High-volume processing (1000+ prospects) where you need cheap compute |
| Clay AI enrichment | Included in Clay credits | Simple extraction/classification within Clay workflows |

---

## Matrix Version History

| Date | Change |
|------|--------|
| 2026-02-16 | v1.0 — Initial matrix with 4 profiles sourced from YouTube Brain research |
