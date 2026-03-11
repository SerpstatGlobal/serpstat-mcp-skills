---
name: serpstat-competitor-reverse-engineer
description: >
  Deep reverse-engineering of ONE competitor's SEO strategy via Serpstat MCP. Pulls 7 data types (metrics, top pages, keywords, backlinks by page, anchors, backlink summary, history) to build a full dossier with SWOT and action plan as an interactive HTML dashboard.
  Trigger on: reverse engineer competitor, competitor deep dive, competitor SEO dossier, break down competitor strategy, full competitor analysis, analyze competitor, competitor audit, competitor breakdown, competitor traffic sources, competitor backlink strategy, deep dive into competitor, досье на конкурента, разбери стратегию конкурента, как работает SEO у. Use even for "tell me everything about X's SEO" or "how is X getting traffic".
---

# Competitor Strategy Reverse-Engineer

Deep reverse-engineering of a single competitor's SEO strategy in 2 minutes.
This skill produces a complete dossier on ONE competitor domain: not a
comparison, but a full breakdown of how they built their organic presence —
which clusters drive traffic, which pages lead, where links come from, what
anchors they use, how the site architecture works, and how visibility evolved
over the past year.

## What the User Gets

- Top-20 competitor pages with traffic and keywords
- Traffic distribution: commercial vs informational vs branded
- Backlink strategy: where links point, with which anchors, what donor quality
- Strengths and weaknesses: "blog is strong, but product categories are weak"
- Historical dynamics: growth trends over the past year
- SWOT analysis with concrete recommendations: what to copy, where to outflank

## Prerequisites

The Serpstat MCP connector must be enabled in the chat. If it's not connected,
use `suggest_connectors` to prompt the user to connect it before proceeding.

## Pre-flight Check

Before executing any API calls, verify the account has sufficient credits.

1. Call `get_credits_stats`
2. Check `data.left_lines`:
   - If `left_lines < 600` → **stop immediately**, do not proceed
   - Inform the user: "Insufficient API credits to run this analysis.
     Current balance: {left_lines}. Required: ~600.
     Please purchase more credits: serpstat.com/users/profile"
3. If `left_lines >= 600` → proceed with execution

**Never skip this check.** Running without sufficient credits will result
in partial or failed results mid-execution.

## Workflow Overview

The analysis has 5 phases. Execute them in order.

### Phase 1: Collect Inputs

**Step 1a — Get the competitor domain.**

If the user has not yet provided a competitor domain, ask for it. If they have,
extract it. This skill analyzes ONE competitor domain deeply.

**Step 1b — Auto-detect region (if not explicitly provided).**

If the user did NOT specify a region in their message, auto-detect it:

1. **Region**: Infer the most likely region from the domain's TLD and content:
   - `.com` with English content → `g_us` (USA)
   - `.co.uk` → `g_uk` (UK)
   - `.de` → `g_de` (Germany)
   - `.ua` or Ukrainian content → `g_ua` (Ukraine)
   - `.fr` → `g_fr` (France)
   - `.es` → `g_es` (Spain)
   - For ambiguous cases, call `get_domain_regions_count` with the competitor
     domain to determine the primary market, then pick the top region.

**Step 1c — Confirm with the user before proceeding.**

This step is MANDATORY whenever the region was auto-detected (i.e., not
explicitly stated by the user). Do NOT start data collection (Phase 2)
without explicit user confirmation.

Present your selections using `ask_user_input_v0`:

```
"I'll do a deep reverse-engineering of **competitor.com**'s SEO strategy.
Here are the parameters I've detected:

• **Competitor**: competitor.com
• **Region**: 🇺🇸 USA (g_us)

Please confirm or adjust below."

ask_user_input_v0({
  questions: [
    {
      question: "Is this the right region for the analysis?",
      type: "single_select",
      options: ["🇺🇸 USA (g_us)", "🇬🇧 UK (g_uk)", "🇩🇪 Germany (g_de)", "🇺🇦 Ukraine (g_ua)", "Other (I'll specify)"]
    }
  ]
})
```

If the user confirms — proceed to Phase 2.
If the user adjusts region — use the updated value.
If the user selects "Other" — ask them for the specific region code.

**IMPORTANT**: Never auto-launch the full analysis without confirmation.
Even if you are confident in the auto-detected values, always present them
to the user and wait for their explicit "go ahead" before proceeding.

**If the user already provided both the competitor domain AND region in their
opening message, skip steps 1b and 1c and proceed directly to Phase 2.**

### Phase 2: Pull Data from Serpstat

Make these 7 API calls in sequence. All calls use the Serpstat MCP tools.

#### 2a. Domain overview metrics

```
Method: get_domains_info
Params: { domains: ["<competitor>"], se: "<se>" }
```
Returns: total traffic, keywords, visibility, ads keywords — used in the
dashboard hero section and overview metrics.

#### 2b. Top pages (leader pages)

```
Method: get_domain_top_urls
Params: {
  domain: "<competitor>",
  se: "<se>",
  sort: { "potencial_traff": "desc" },
  size: 100,
  include_all_fields: true
}
```
Returns the top-100 pages by estimated organic traffic. These are the traffic
leaders — the pages that matter most.

#### 2c. Keyword core

```
Method: get_domain_keywords
Params: {
  domain: "<competitor>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 100,
  withSubdomains: false,
  withIntents: true,
  include_all_fields: true
}
```

Then make a SECOND call to get the next page:

```
Method: get_domain_keywords
Params: {
  domain: "<competitor>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 100,
  page: 2,
  withSubdomains: false,
  withIntents: true,
  include_all_fields: true
}
```

This gives 200 keywords total, sorted by search volume — the competitor's
keyword core. Intent data (informational, commercial, transactional,
navigational) is critical for the traffic distribution analysis.

#### 2d. Top pages by backlinks

```
Method: get_top_pages_by_backlinks
Params: {
  query: "<competitor>",
  size: 50
}
```
Returns pages that attract the most backlinks — reveals the link-building
strategy: which content earns links, where the link equity flows.

#### 2e. Anchor profile

```
Method: get_anchors
Params: {
  query: "<competitor>",
  size: 50,
  sort: { "backlinks": "desc" }
}
```
Returns anchor text distribution — reveals branded vs commercial vs
informational anchor balance, potential over-optimization signals.

#### 2f. Backlink summary

```
Method: get_backlinks_summary
Params: { query: "<competitor>" }
```
Returns overall backlink profile: total backlinks, referring domains,
dofollow/nofollow ratio, link types, quality distribution — the big picture
of the competitor's link authority.

#### 2g. Domain history (12 months)

```
Method: get_domain_history
Params: {
  domain: "<competitor>",
  se: "<se>",
  withSubdomains: false
}
```
Returns bi-weekly snapshots of keywords, traffic, visibility, and
new/lost/rising/falling keyword dynamics — shows the trajectory and
helps identify algorithm update impacts or growth phases.

### Phase 3: Analyze and Classify

This is where raw data becomes insight. Claude performs ALL of the following
analysis steps before building the report.

#### 3a. URL Classification

Classify every URL from steps 2b and 2d into content categories based on
URL path patterns. Adapt categories to the specific domain. Common categories:

| Category | URL signals |
|---|---|
| Blog / Content Hub | `/blog`, `/articles/`, `/magazine`, `/resources` |
| Product / Category Pages | `/products/`, `/shop/`, `/category/`, `/collections/` |
| Landing Pages | `/solutions/`, `/features/`, `/pricing`, `/demo` |
| Tools / Interactive | `/tools/`, `/calculator/`, `/generator/`, `/checker/` |
| Help / Support | `/help/`, `/support/`, `/faq/`, `/docs/`, `/knowledge-base` |
| About / Brand | `/about/`, `/team/`, `/careers/`, `/press/` |
| Geographic / Local | `/locations/`, city names, `/store-locator` |
| User Content | `/community/`, `/forum/`, `/reviews/` |

Add or modify categories as the actual URL patterns suggest. The goal is to
map the competitor's entire site architecture into meaningful groups.

#### 3b. Traffic Distribution Analysis

Using keyword intent data from step 2c, calculate traffic distribution:

- **Commercial**: keywords with commercial or transactional intent
- **Informational**: keywords with informational intent
- **Branded**: keywords containing the competitor's brand name or obvious brand terms
- **Navigational**: keywords with navigational intent

Calculate the percentage split and total traffic per intent group.

#### 3c. Backlink Strategy Analysis

From steps 2d, 2e, and 2f, determine:

- **Link targets**: Which page types attract links? (blog vs homepage vs tools)
- **Anchor distribution**: What % is branded, commercial, URL-based, generic?
- **Donor quality**: Based on referring domains count and backlink summary metrics
- **Link velocity**: Are they actively building or coasting on existing links?

Classify each anchor from step 2e into one of these types:
- **Branded**: contains the domain name or brand name
- **Commercial**: contains product/service keywords (buy, best, cheap, tool name)
- **URL / Naked**: is a URL or partial URL
- **Generic**: "click here", "read more", "this site", "learn more"
- **Other**: everything else

#### 3d. Historical Dynamics

From step 2g, identify:

- **Overall trend**: Growing, stable, or declining?
- **Growth rate**: % change in traffic/keywords over past 12 months
- **Inflection points**: Sudden jumps or drops (potential algorithm impacts or
  major content launches)
- **Keyword dynamics**: Net new vs lost keywords — are they expanding or
  losing ground?

#### 3e. SWOT Analysis

Based on all data, produce a SWOT matrix:

- **Strengths**: What are they doing well? (e.g., "Strong blog with 60% of
  traffic from informational keywords", "Diverse anchor profile")
- **Weaknesses**: Where are the gaps? (e.g., "Product categories have thin
  content and few backlinks", "Over-reliance on branded traffic")
- **Opportunities**: What can you exploit? (e.g., "Their tool pages get links
  but have low traffic — create better tools", "They have no content in
  category X")
- **Threats**: What makes them dangerous? (e.g., "Growing 15% QoQ with
  aggressive link building", "Their blog covers topics you haven't touched")

Each SWOT bullet MUST reference specific data from the analysis (numbers,
URLs, traffic values). No generic statements.

#### 3f. Actionable Recommendations

Generate 5-10 specific, prioritized recommendations. Each recommendation must:

- Reference specific data from the analysis (page URLs, keyword volumes,
  traffic numbers)
- Be actionable: "Create a tools section similar to competitor.com/tools/
  which attracts 234 referring domains" — not "improve your content"
- Have a priority: HIGH / MEDIUM / LOW
- Have an effort estimate: Quick Win / Medium / Large
- Identify the expected impact (traffic potential, link opportunity)

### Phase 4: Build the Dashboard

Read `references/serpstat-brand.md` for the complete design system — colors,
fonts, spacing, and the widget catalog. Build the report by assembling widgets
from that catalog.

Create a standalone `.html` file and save it to `/mnt/user-data/outputs/`.
The file must be fully self-contained — all CSS, JS, and data embedded
inline — so the user can open it directly in any browser without a server.

Structure the file as described in the style guide:
- `const DATA = { ... }` at the top with all analysis data
- Renderer JS below that reads DATA and builds the DOM
- All CSS inline in `<style>`

#### Report Description in Hero Header

The hero section MUST include a dynamic, context-aware description (2-3
sentences) as the `.hero-subtitle` element. This description should explain
what the report contains and what was found, so a reader landing on the page
immediately understands its purpose. Example:

> "A complete reverse-engineering of **competitor.com**'s SEO strategy in the
> US market (g_us). The site ranks for 12,400 keywords driving an estimated
> 89K monthly visits, with 60% of traffic coming from informational content.
> Their blog is the main growth engine with 234 referring domains, while
> product categories show weak backlink coverage."

The description must be dynamic — generated from the actual analysis data.
Include at minimum: which domain was analyzed, which region, total keywords
and traffic, and a one-line summary of the key finding.

#### Dashboard Tabs and Widgets

Use the following tabs. Pick widgets from the style guide's widget catalog
for each tab.

**Tab 1 — Overview**
- Widget: Metric Cards Row — 5 KPI cards: keywords, traffic, visibility,
  referring domains, total backlinks
- Widget: Bar Distribution — traffic by intent (informational, commercial,
  transactional, branded, navigational)
- Widget: Bar Distribution — site architecture: categories with page count
  and traffic share
- Section: 12-month trend summary (key finding from dynamics data)

**Tab 2 — Top Pages**
- Widget: Data Table — top-100 pages sorted by traffic
- Columns: #, URL (truncated, full path in tooltip), Traffic, Keywords, Category
- Add category filter buttons above the table

**Tab 3 — Keyword Core**
- Widget: Data Table — top-200 keywords sorted by volume
- Columns: Keyword, Position, Volume, Traffic, KD, CPC, Intent (badge), URL
- Add intent filter buttons above the table

**Tab 4 — Backlink Profile**
- Widget: Metric Cards Row — backlink summary: total backlinks, referring
  domains, dofollow %, link type split
- Widget: Data Table — top pages by backlinks: URL, ref domains, ref pages
- Widget: Bar Distribution — anchor type breakdown (branded, commercial,
  URL, generic, other)
- Widget: Data Table — top-30 anchors with backlink counts

**Tab 5 — Historical Dynamics**
- CSS bar chart showing traffic and keywords over time (bi-weekly snapshots)
- Widget: Metric Cards Row — growth rate, net new keywords, trend direction
- Key findings list with inflection point annotations

**Tab 6 — SWOT & Recommendations**
- SWOT grid: 2×2 card layout (Strengths green left border, Weaknesses red,
  Opportunities blue, Threats amber) — each with 3-5 data-backed bullet points
- Widget: Action Cards — 5-10 prioritized recommendations with priority badge,
  effort badge, title, description, specific data reference, expected impact

#### UX Requirements

- Every section has a title + subtitle explaining what it shows
- All numbers/metrics use Roboto Mono font
- Sortable table headers with click handlers and sort arrows
- Badge-based intent/category labels in tables
- Tooltips on metric labels explaining what the metric means
- 48px gap between sections (critical for visual breathing room)
- Responsive at 768px (grids collapse to single column, tables get horizontal scroll)
- Dark footer with Serpstat logo and API methods used

#### File name format

`competitor-dossier_{domain}_{date}.html`

### Phase 5: Present and Summarize

After rendering the dashboard, provide a brief text summary highlighting:

1. The competitor's key strength (e.g., "Their blog drives 60% of traffic
   with 234 referring domains")
2. Their biggest weakness (e.g., "Product categories have 3x fewer keywords
   than their blog")
3. The #1 opportunity for the user (e.g., "They have no content in the
   [topic] cluster — 8,400 monthly searches you could capture")
4. The top 3 action items from the SWOT tab — with priority and expected impact

Keep it concise — the dashboard is the deliverable, the summary is the hook.
Point the user to the SWOT & Recommendations tab for execution.

## Error Handling

- If Serpstat MCP is not connected: use `suggest_connectors` to prompt connection
- If a domain returns no data: inform the user and suggest checking the domain
  spelling or trying a different region
- If `get_domain_history` returns sparse data: note "Limited historical data
  available" in the dashboard
- If credits are low: warn the user before making calls. Always check credits first.
- If `withIntents` returns no intent data (non-US/UA region): skip intent
  distribution and note "Intent data not available for this region" in the report

## Example Invocations

**Simple:**
> "Analyze competitor.com"

**Explicit:**
> "Do a full SEO reverse-engineering of semrush.com in the US market"

**Trigger phrases:**
> "Break down competitor.com's SEO strategy"
> "How is competitor.com getting their traffic?"
> "Tell me everything about competitor.com's SEO"
> "Competitor deep dive on ahrefs.com"
> "Reverse engineer moz.com's content strategy"

## API Methods Used

| Method | Purpose | Cost |
|---|---|---|
| `get_credits_stats` | Pre-flight credit check | Free |
| `get_domains_info` | Domain overview metrics | 5/domain |
| `get_domain_top_urls` | Top-100 pages by traffic | 1/row (100) |
| `get_domain_keywords` | Keyword core (2 pages × 100) | 1/row (200) |
| `get_top_pages_by_backlinks` | Pages attracting links | 1/row (50) |
| `get_anchors` | Anchor text distribution | 1/row (50) |
| `get_backlinks_summary` | Backlink profile overview | 5/request |
| `get_domain_history` | 12-month visibility dynamics | 1/row (~24) |
| **Total estimated** | | **~435 credits** |
