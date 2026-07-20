# Google Search Results API: How to Scrape Google SERPs Without Getting Blocked — ScraperAPI vs SerpAPI vs Serper vs DataForSEO Full Comparison (Which Plan Is Actually Worth It, and What Nobody Tells You About Credits)

If you've ever tried to pull Google search results programmatically, you've probably hit the same wall everyone hits: a few hundred requests in, your IP gets rate-limited, the responses come back garbled, or you just start seeing CAPTCHAs instead of results. And then you go down the rabbit hole of "google search results api" — only to find out that Google doesn't actually offer one.

That part trips up a lot of people. So let's start there, then work through which third-party solution actually makes sense for your situation — including a close look at ScraperAPI's SERP endpoint, which tends to get lumped together with "proxy tools" when it's actually something more specific.

---

## Why There's No Official Google Search Results API (And What That Actually Means for You)

Google shut down its Web Search API back in 2011. What exists today — the Custom Search JSON API — only searches within custom-configured indexes or domains you define. It doesn't return general Google.com results. Free tier: 100 queries a day. After that, you're paying, and you're still not getting real SERP data.

So what are people actually doing when they need Google search results at scale?

Three approaches:

1. **Official alternative search engine APIs** — Brave Search API, Yandex Search API, DuckDuckGo's unofficial zero-click API. These give you real API access, but to their results, not Google's. Good for some use cases; irrelevant if you specifically need Google data.

2. **Third-party SERP scraping APIs** — Services that handle the proxy rotation, CAPTCHA solving, browser fingerprinting, and parsing on your behalf, and return structured JSON. This is what SerpAPI, Serper, ScraperAPI, and DataForSEO all do.

3. **DIY scraping** — Running your own rotating proxy pool and headless browser setup. Works, but maintaining it as Google's detection systems evolve is a continuous engineering investment.

For most teams, option 2 is where the real decision lives. And that's where things get complicated, because the pricing models between providers are structurally different in ways that make apples-to-apples comparison genuinely tricky.

---

## What a Google Search Results API Actually Returns

Before comparing tools, it's worth being precise about what you actually get back. A modern SERP response from a quality provider isn't just a list of blue links — it typically includes:

- **Organic results**: position, title, URL, snippet, displayed link
- **People Also Ask (PAA)**: the expandable question clusters that now appear mid-page on most queries
- **Knowledge Graph**: entity cards on the right side
- **Featured snippets**: the zero-position boxed answer
- **Shopping results**: PLAs with pricing
- **News results**: freshness-timestamped article links
- **Related searches**: the suggestion cluster at the bottom
- **AI Overviews**: Google's AI-generated summary now appearing on a growing share of queries
- **Pagination data**: current page, next page URL

How much of this you actually get back depends heavily on which provider you're using and at what tier.

---

## The Provider Landscape: Who's Actually Worth Looking At

### SerpAPI — The Oldest, the Priciest, the Most Comprehensive

SerpAPI has been around the longest and covers 80+ search engines — Google, Bing, Baidu, Yahoo, Yandex, YouTube, and more. Its documentation is excellent. SDKs exist for Python, Ruby, Node.js, Go, and PHP. It even operates under a "Legal US Shield" program, citing the hiQ Labs v. LinkedIn precedent as a basis for arguing its data collection practices are legally defensible.

The problem is cost. The entry Developer tier is $75/month for 5,000 searches — $15 per 1,000 requests. At the Production tier ($150/mo for 15,000 searches), you're at $10 per 1K. Even at scale, you're paying 3–10× what competitors charge per search.

If you need Bing, Baidu, or Yahoo in addition to Google, and legal compliance is a board-level concern, SerpAPI makes sense. If you just need Google results, it's hard to justify on pure cost.

### Serper — The Budget Option That Actually Works

Serper launched more recently and went straight at SerpAPI's pricing with what amounts to a 10–30× lower cost per search. The free tier is 2,500 searches per month — genuinely useful for testing. Paid plans start at $50/month for 50,000 searches ($1 per 1K), dropping to $0.30 per 1K at the highest volume tier.

Response times benchmark at 1–2 seconds, which is faster than most competitors. The tradeoff: Google only, no AI Overviews, no multi-engine support. If your entire workflow lives on Google organic results and you're cost-sensitive, Serper is a logical starting point.

### DataForSEO — Best for Bulk Async Pipelines

DataForSEO uses a pay-as-you-go task-based model with no monthly minimums. Standard queue (async): ~$0.60 per 1,000 tasks. Priority queue: ~$1.20/1K. Live mode: ~$2.00/1K. No subscription required.

The catch is the setup complexity. Authentication uses HTTP Basic with base64-encoded credentials. Requests are POST-based JSON arrays. Async workflows require a separate polling call to retrieve results. For bulk SEO pipelines where you can tolerate 5-minute processing windows, the cost efficiency is unmatched. For real-time applications or quick prototyping, the overhead is frustrating.

### ScraperAPI — The Proxy-First Platform With a Solid SERP Endpoint

ScraperAPI is often described as a "proxy tool," but that framing undersells what it actually is. The platform was built around a massive proxy network (40M+ IPs across 50+ countries), automatic CAPTCHA solving, JavaScript rendering, and automatic retry logic — and on top of that infrastructure, it offers structured data endpoints that return parsed JSON for a specific set of high-demand domains.

For Google search results specifically, ScraperAPI provides a dedicated SERP endpoint:


GET https://api.scraperapi.com/structured/google/search
    ?api_key=YOUR_API_KEY
    &query=YOUR_SEARCH_QUERY
    &country_code=us
    &tld=com


The response includes organic results, knowledge graph, related questions (PAA), video results, and pagination — structured JSON ready to use without HTML parsing. Parameters let you specify the Google domain (`tld`), country for geo-targeting (`country_code`), language settings (`hl`, `gl`), time range filtering (`tbs`), result pagination (`start`), and UULE location encoding for hyper-local targeting.

One thing that matters here: a Google SERP request on ScraperAPI costs **25 credits** per call. If you're on the Hobby plan (100,000 credits/month at $49/mo), that works out to roughly 4,000 SERP queries per month, not 100,000. This credit multiplier system is the most important thing to understand before signing up — more on that below.

The platforms where ScraperAPI genuinely shines based on independent Scrapeway benchmarks (April 2026): Amazon (98% success rate), Zillow (100%), Etsy (99%), Walmart (93%). On Google SERP specifically, it's functional but sits around 82% success rate in some benchmarks — not the highest in the category, but competitive.

👉 [Try ScraperAPI's Google SERP API free](https://www.scraperapi.com/?fp_ref=coupons)

---

## ScraperAPI's Credit Multiplier System: The Math Nobody Explains Upfront

This is where a lot of people get confused, and it's worth being direct about it.

ScraperAPI charges credits based on both the type of request and the features you enable. Base costs by domain type:

| Domain Category | Credits per Request | Examples |
|---|---|---|
| Standard websites | 1 | Blogs, news, simple HTML |
| E-commerce sites | 5 | Amazon, eBay, Walmart |
| Search engines (SERP) | **25** | Google, Bing |
| Social media | 30 | LinkedIn |

And then feature flags stack on top of the base cost:

| Feature Flag | Extra Credits |
|---|---|
| `render=true` (JavaScript rendering) | +10 |
| `screenshot=true` | +10 |
| `premium=true` (residential proxies) | +10 |
| `ultra_premium=true` | +30 |
| Premium + JS rendering combined | **+25** (not +20) |
| Ultra-premium + JS rendering combined | **+75** (not +40) |

Note that combining features costs more than the sum of individual additions — that non-linear stacking catches people off guard.

For the structured Google SERP endpoint specifically, the 25-credit base is fixed. Standard SERP queries without additional feature flags stay at 25 credits each.

---

## ScraperAPI Plans: All Tiers, Full Breakdown

Here's every plan currently available on ScraperAPI, with what the credits actually translate to for SERP queries:

| Plan | Monthly Price | Annual (per mo) | API Credits | Threads | Geo Targeting | SERP Queries @ 25cr | Purchase |
|---|---|---|---|---|---|---|---|
| **Free** | $0 | — | 1,000 | 5 | None | ~40 |  [Start Free](https://www.scraperapi.com/?fp_ref=coupons) |
| **Hobby** | $49 | ~$44 | 100,000 | 20 | US & EU | ~4,000 |  [Get Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| **Startup** | $149 | ~$134 | 1,000,000 | 50 | US & EU | ~40,000 |  [Get Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| **Business** | $299 | ~$269 | 3,000,000 | 100 | 50+ countries | ~120,000 |  [Get Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| **Scaling** | $475 | ~$427 | 5,000,000 | 200 | Country-level | ~200,000 |  [Get Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| **Professional** | $975 | — | 10,500,000 | 300 | Country-level + PAYG | ~420,000 |  [Get Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| **Enterprise** | Custom | Custom | 5M+ custom | 200+ | Full | Custom |  [Contact Sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

A few things worth flagging:

- **Credits don't roll over.** Unused credits expire at the end of each billing cycle.
- **Pay-As-You-Go is only available on Scaling ($475/mo) and above.** On Hobby, Startup, and Business, if you exhaust credits before the month is up, you're cut off — the only option is upgrading.
- **Geotargeting beyond US & EU requires the Business plan.** If you need to target search results from, say, Germany or Japan, the Startup plan won't do it.
- **Annual billing saves roughly 10–15%** across plans where it's offered.
- **7-day free trial** gives you 5,000 credits to test before committing.

---

## Head-to-Head: ScraperAPI vs SerpAPI vs Serper vs DataForSEO for Google Search Results

| Factor | ScraperAPI | SerpAPI | Serper | DataForSEO |
|---|---|---|---|---|
| **Cost per 1K SERP searches** | ~$2.49–$12.25 | $10–$25 | $0.30–$1.00 | $0.60–$2.00 |
| **Monthly 100K searches cost** | ~$299–$475 | ~$900 | ~$90 | ~$60 |
| **Free tier** | 1,000 credits (~40 SERP) | 100 searches | 2,500 searches | $1 credit |
| **Search engines** | Google (+ others via raw) | 80+ engines | Google only | Google + others |
| **Geo targeting** | Yes (Business+) | Yes | Yes (`gl`,`hl`) | Yes |
| **AI Overviews** | No | Limited | No | No |
| **PAA / Knowledge Graph** | Yes | Yes | Yes | Yes |
| **Response format** | Structured JSON | Structured JSON | Structured JSON | Structured JSON |
| **Avg response time** | ~5–7s | ~2.5s | ~1.8s | ~4–6s (live) |
| **API simplicity** | Simple GET | Simple GET | Simple POST | Complex POST array |
| **Beyond SERP** | Yes (Amazon, Maps, etc.) | Yes | No | Yes |
| **PAYG overage** | Scaling+ only | No | Yes | Always (no sub needed) |

The honest read: **Serper wins on pure price-per-search for Google-only needs**. **DataForSEO wins on pure cost at very high async volume**. **SerpAPI wins if you need multi-engine coverage and legal compliance is non-negotiable**. **ScraperAPI wins if you want a single platform that handles both SERP and other structured data scraping** (Amazon, Google Maps, Google News, Google Jobs, Walmart, eBay, Redfin) without managing multiple accounts.

---

## What ScraperAPI's Google SERP Endpoint Actually Gives You

Let's be concrete about what you get in the response. Here's a simplified version of what the API returns for a standard query:

python
import requests

payload = {
    "api_key": "YOUR_API_KEY",
    "query": "best project management software",
    "country_code": "us",
    "tld": "com"
}

r = requests.get(
    'https://api.scraperapi.com/structured/google/search',
    params=payload
)
print(r.json())


The JSON response includes:
- `search_information` — the displayed query string
- `knowledge_graph` — entity card if present (title, description, image)
- `organic_results` — array of results with position, title, snippet, link, displayed_link
- `related_questions` — People Also Ask entries with question text and position
- `videos` — video results with title, source, channel, publish date, thumbnail, duration
- `pagination` — pages count, current page, next page URL, all page URLs

Supported Google domains via `tld`: `.com`, `.co.uk`, `.ca`, `.de`, `.es`, `.fr`, `.it`, `.co.jp`, `.in`, `.cn`, `.com.sg`, `.com.mx`, `.ae`, `.com.br`, `.nl`, `.com.au`, `.com.tr`, `.sa`, `.se`, `.pl` — 20 regional Google domains.

The `tbs` parameter lets you filter results by freshness: past hour (`tbs=h`), day (`tbs=d`), week (`tbs=w`), month (`tbs=m`), year (`tbs=y`). For news monitoring or tracking trending queries, this is genuinely useful.

---

## Five Actual Use Cases Where a Google Search Results API Earns Its Cost

**1. SEO rank tracking at scale.** Checking keyword positions manually is a spreadsheet nightmare past a few dozen keywords. A google search results api call per keyword per day, logged to a database, gives you historical position data for every URL you care about — without paying for an all-in-one SEO platform.

**2. Competitive intelligence.** Who's ranking for your product category's core terms? What new competitors appeared in the top 10 last week? What ads are running alongside organic results? Structured SERP data lets you build dashboards that answer these questions automatically.

**3. Content gap analysis.** Scrape the top 10 results for 500 target keywords, extract their titles and snippets, run them through a language model, and surface what angles your site is missing. This kind of workflow is why AI-focused teams are increasingly building on top of google search results APIs — it's not just SEO anymore.

**4. Local SERP monitoring.** ScraperAPI's `country_code` and UULE parameters let you simulate searches from specific geographic locations. A multi-location business can track local pack rankings per city without deploying VPNs in each location.

**5. Trend detection and news monitoring.** Combine the `tbs=h` time-filter with keyword queries to monitor breaking topics in near-real-time. Feed results into a Slack webhook or internal dashboard. Journalists, PR teams, and market researchers all run this kind of workflow.

---

## Who ScraperAPI's Google SERP API Is (and Isn't) For

**Good fit:**
- Developer teams that already use ScraperAPI for other scraping (Amazon, Zillow, etc.) and want SERP data on the same account, same credits, same billing
- Teams that need moderate SERP volume (up to ~120,000 Google queries/month on the Business plan) alongside other structured data endpoints
- Projects where geo-targeting across multiple countries matters (Business plan and above)
- Teams that value a simple GET-based API without complex auth or async polling

**Not the best fit:**
- Pure SERP-at-scale use cases where cost-per-query is the only metric — Serper or DataForSEO will be cheaper
- Teams needing Bing, Baidu, or Yahoo alongside Google — SerpAPI covers more engines
- Non-technical users who need data in a spreadsheet without writing any code — ScraperAPI requires API integration
- Projects specifically needing AI Overviews parsed as structured data — that's still a gap for ScraperAPI (and most competitors)

👉 [Start with ScraperAPI's free trial — 5,000 credits, no commitment](https://www.scraperapi.com/?fp_ref=coupons)

---

## How to Pick the Right ScraperAPI Plan for SERP Use Cases

Run this math before you commit:

**Monthly SERP query volume × 25 credits = credits needed per month**

- 1,000 SERP queries/month → 25,000 credits → **Free plan works** (with 1K free credits, you get 40 queries; for 1,000 you'd need the Hobby plan)
- 4,000 SERP queries/month → 100,000 credits → **Hobby plan ($49/mo)**
- 40,000 SERP queries/month → 1,000,000 credits → **Startup plan ($149/mo)**
- 120,000 SERP queries/month → 3,000,000 credits → **Business plan ($299/mo)**
- 200,000 SERP queries/month → 5,000,000 credits → **Scaling plan ($475/mo)**

If you also need geo-targeting outside the US and EU, you're on the Business plan minimum regardless of credit volume.

If you're mixing SERP with Amazon scraping (5 credits/request) or other standard page scraping (1 credit/request), your effective query capacity per plan will be lower — budget accordingly.

---

## A Few Practical Tips Before You Start

**Test on the free tier first, against your actual targets.** The 7-day trial with 5,000 credits exists for exactly this reason. Run a sample of 100–200 queries representative of your real workload and check success rates and response completeness before picking a paid plan.

**Don't mix feature flags unless you understand the cost stacking.** Standard SERP queries at 25 credits are predictable. Adding `premium=true` + `render=true` to a SERP request bumps you from 25 to 50 credits per call (25 base + 25 for the combined flag). That cuts your effective query volume in half.

**Use the `tbs` and `country_code` parameters strategically.** For rank tracking, you typically don't need freshness filters — just consistent geo settings. For news monitoring, `tbs=d` or `tbs=h` is worth the slight additional specificity.

**Keep an eye on your dashboard manually.** ScraperAPI doesn't send proactive alerts when you're approaching your credit limit. Check it regularly, especially in the first few weeks.

---

## The Bottom Line

The Google search results API landscape in 2026 is genuinely competitive. There's no one "best" tool — it depends on your volume, your budget, whether you need multiple search engines, and what else you're scraping alongside SERP data.

ScraperAPI's Google SERP endpoint isn't the cheapest per query, and it's not the fastest. What it is: a solid, well-documented structured data endpoint within a broader platform that handles Amazon, Walmart, Google Maps, Google News, Google Jobs, eBay, and Redfin under the same API key and credit pool. If your data stack already lives inside ScraperAPI, or if you're building a multi-source pipeline that includes SERP data, it's a natural fit.

If you're exclusively doing high-volume Google SERP scraping and nothing else, run Serper's numbers first — the 2,500 free queries per month and $0.30/1K at scale are hard to beat on cost alone.

Either way, the free tier and 7-day trial are there. Run the actual numbers on your workload, then decide.

👉 [Create a free ScraperAPI account and test the SERP endpoint](https://www.scraperapi.com/?fp_ref=coupons)

---

*Plans and pricing sourced from ScraperAPI documentation and independent benchmarks (Scrapeway April 2026, OpenWebNinja June 2026, Thunderbit review). Always verify current pricing on the provider's site before committing.*
