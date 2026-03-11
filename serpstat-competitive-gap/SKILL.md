---
name: serpstat-competitive-gap
description: >
  Run a full competitive content gap analysis using the Serpstat MCP connector.
  Compares a user's domain against 1-2 competitors by pulling top pages sorted
  by organic traffic, classifying URLs into content categories, identifying
  missing topics and underperforming areas, enriching gaps with domain
  intersection data, and rendering everything as a polished interactive HTML
  dashboard file (downloadable, opens in any browser) in Serpstat brand style.

  Trigger whenever the user asks to: compare domains for SEO, find content
  gaps, analyze competitor pages, see what competitors rank for, do a
  competitive audit, find missing content opportunities, or run any
  Serpstat-based domain comparison. Also trigger on "top pages",
  "content gap", "competitor analysis", "domain comparison", or Serpstat
  MCP domain comparisons. Even "compare my site with competitors" or
  "what content am I missing vs adidas" — use this skill.
---

# Serpstat Competitive Content Gap Analysis

This skill orchestrates a multi-step competitive analysis workflow using the
Serpstat MCP connector, then builds an interactive dashboard from the results.

## Prerequisites

The Serpstat MCP connector must be enabled in the chat. If it's not connected,
use `suggest_connectors` to prompt the user to connect it before proceeding.

## Workflow Overview

The analysis has 5 phases. Execute them in order.

### Phase 1: Collect Inputs

**Step 1a — Get the main domain.**

If the user has not yet provided their domain, ask for it. If they have, extract it.

**Step 1b — Auto-detect competitors and region (if not explicitly provided).**

If the user did NOT explicitly specify competitors and/or region in their
message, you MUST still figure them out — but you must get user confirmation
before running the analysis.

1. **Competitors**: Call Serpstat to fetch competitor suggestions:

```
Method: get_domain_competitors
Params: { domain: "<userDomain>", se: "g_us", size: 5 }
```

Pick the top 2 competitors from the results based on relevance (traffic overlap,
same industry). If only region is missing but competitors were provided, skip
this step for competitors.

2. **Region**: Infer the most likely region from the domain's TLD and content:
   - `.com` with English content → `g_us` (USA)
   - `.co.uk` → `g_uk` (UK)
   - `.de` → `g_de` (Germany)
   - `.ua` or Ukrainian content → `g_ua` (Ukraine)
   - For ambiguous cases, default to `g_us` and note this in the confirmation.

**Step 1c — Confirm with the user before proceeding.**

This step is MANDATORY whenever competitors or region were auto-detected (i.e.,
not explicitly stated by the user). Do NOT start data collection (Phase 2)
without explicit user confirmation.

Present your selections using `ask_user_input_v0` for the user to confirm or
adjust. For example:

```
"Based on Serpstat data, I've selected these parameters for your analysis:

• **Domain**: mybrand.com
• **Competitors**: competitor1.com, competitor2.com
• **Region**: 🇺🇸 USA (g_us)

Please confirm or adjust below."

ask_user_input_v0({
  questions: [
    {
      question: "Are these competitors correct? (pick up to 2, or type your own after)",
      type: "multi_select",
      options: [<top 5 from Serpstat>]
    },
    {
      question: "Is this the right region for the analysis?",
      type: "single_select",
      options: ["🇺🇸 USA (g_us)", "🇬🇧 UK (g_uk)", "🇩🇪 Germany (g_de)", "🇺🇦 Ukraine (g_ua)"]
    }
  ]
})
```

If the user confirms — proceed to Phase 2.
If the user adjusts competitors or region — use the updated values.
If the user's preferred competitor wasn't in the list, accept it as free-text
input after the widget.

**IMPORTANT**: Never auto-launch the full analysis without confirmation.
Even if you are confident in the auto-detected values, always present them
to the user and wait for their explicit "go ahead" before proceeding.

**Step 1d — Resolve region.**

Map the selected region label to the Serpstat `se` code:
- 🇺🇸 USA → `g_us`
- 🇬🇧 UK → `g_uk`
- 🇩🇪 Germany → `g_de`
- 🇺🇦 Ukraine → `g_ua`

If the user types a custom region not in the list, try to map it to the correct `se` code using your knowledge of Serpstat search engines.

**If the user already provided all three (domain + competitors + region) in their opening message, skip steps 1b and 1c and proceed directly to Phase 2.**

The final report will include data for all specified domains (up to 3 total: the user's main domain + 1-2 competitors).

### Phase 2: Pull Data from Serpstat

Make these API calls in sequence. All calls use the Serpstat MCP tools.

#### 2a. Domain overview (context metrics)

```
Method: get_domains_info
Params: { domains: [userDomain, ...competitors], se: "<se>" }
```
This gives total traffic, keywords, visibility for each domain — used in the
dashboard header cards for context.

#### 2b. Top pages by traffic for EACH domain

```
Method: get_domain_top_urls
Params: {
  domain: "<domain>",
  se: "<se>",
  sort: { "potencial_traff": "desc" },
  size: 100,
  include_all_fields: true
}
```
Run this once per domain (user + all competitors). The response contains:
- `url` — the page URL
- `organic_keywords` — number of keywords the page ranks for
- `potencial_traff` — estimated organic traffic (this is the PRIMARY metric)
- `facebook_shares` — social signals

Important: sort by `potencial_traff` descending to get the highest-traffic
pages first. Use `size: 100` to get a meaningful sample.

#### 2c. Domain intersection (where user loses to competitors)

```
Method: get_domains_intersection
Params: {
  se: "<se>",
  domains: ["<userDomain>", "<competitor>"],
  size: 50,
  filters: { region_queries_count_from: 1000, position_from: 4 },
  include_all_fields: true
}
```
Run this for user+competitor1, then user+competitor2 (if applicable). Filter
to keywords with 1000+ search volume where the user ranks 4th or lower — these
are "losing battles" where the user is present but underperforming.

### Phase 3: Classify and Analyze

#### URL Classification

Classify every URL into a content category based on its path. Use these
categories (extend if the data suggests additional ones):

| Category | URL signals |
|---|---|
| Blog / Content | `/blog`, `/articles/`, `/magazine` |
| Iconic Lines / Products | Named product lines (`/samba`, `/ultraboost`, `/suede`, `/club-c`, etc.) |
| Athletes / Teams / Collabs | Team names, athlete names, `/collaborations` |
| Sport Categories | `/soccer`, `/basketball`, `/running`, `/training`, `/golf`, etc. |
| Sale / Promotions | `/sale`, `/promo`, `/discount`, `/offers` |
| Size Guides / Help | `/size`, `/help`, `/return`, `/contact`, `/faq` |
| Store Locator | `/stores`, `/store-locator` |
| About / Careers | `/about`, `/careers`, `/history`, `/sustainability` |
| Clothing / Apparel | `/clothing`, `/pants`, `/hoodies`, `/jackets`, `/jerseys`, etc. |
| Accessories / Bags | `/bags`, `/backpacks`, `/hats`, `/accessories` |
| Slides / Sandals | `/slides`, `/sandals` |
| Walking Shoes | `/walking` |
| Style / Color Collections | `/white-sneakers`, `/colorful`, `/platform`, `/high-top` |
| New Arrivals / Best Sellers | `/new-arrivals`, `/best-sellers` |
| Main Category Pages | Generic `/shoes`, `/men`, `/women`, `/kids` |
| Homepage | Root path or near-root |

The classification function should be adaptive — scan the actual URLs first and
create categories that make sense for the specific industry. The table above is
a starting template for sportswear/footwear brands.

#### Gap Scoring

For each category, compute:
- **User traffic** — sum of `potencial_traff` for all user URLs in this category
- **Best competitor traffic** — max(sum per competitor) in this category
- **Gap ratio** — best_competitor / user (Infinity if user = 0)
- **Severity**:
  - `MISSING` — user has zero pages in this category
  - `HUGE GAP` — ratio > 10×
  - `UNDERPERFORMING` — ratio 3-10×
  - `MINOR GAP` — ratio 1.5-3×
  - `COMPETITIVE` — ratio < 1.5×

#### Priority Score

Calculate an actionable priority score for each gap:

```
priority = gap_traffic_potential × ease_factor
```

Where:
- `gap_traffic_potential` = competitor traffic − user traffic in the category
- `ease_factor`:
  - 1.5 if user already has some pages (can optimize existing)
  - 1.0 if category is MISSING (need to create from scratch)
  - 0.7 if category requires specialized content (blog, size guides)

Sort gaps by priority score descending for the recommendations.

#### Actionable To-Do List Generation

After scoring and sorting the gaps, generate a concrete to-do list. Each to-do
item represents a single, actionable task that the SEO team can assign, execute,
and mark complete. The list should be derived directly from the gap and keyword
data — never generic filler.

For each gap category (sorted by priority score), create 1-3 to-do items using
this logic:

| Severity | To-Do Type | Example |
|---|---|---|
| `MISSING` | "Create new [content type] targeting [top keywords]" | "Create a /running landing page targeting 'best running shoes' (vol: 12K), 'running shoes for men' (vol: 8K)" |
| `HUGE GAP` | "Expand and optimize existing [pages] — add sections for [missing subtopics]" | "Expand /shoes/sneakers to cover 'white sneakers' (vol: 22K) — competitor ranks #2, we're absent" |
| `UNDERPERFORMING` | "Improve on-page SEO for [URL] — target [specific keywords where competitor outranks]" | "Optimize /blog/best-running-shoes — we rank #14, competitor #3 for 'best running shoes 2025' (vol: 9K)" |
| `MINOR GAP` | "Refresh content on [URL] and add internal links from [related pages]" | "Update /sale page with seasonal keywords — competitor gets 2× traffic from promo-related terms" |
| `COMPETITIVE` | Skip — no to-do needed for categories where the user is competitive |

Each to-do item must include:
- **Task title**: A short imperative sentence (verb-first, e.g., "Create…", "Optimize…", "Expand…")
- **Category**: The gap category it belongs to
- **Severity**: Inherited from the gap
- **Priority**: `HIGH` (MISSING/HUGE GAP), `MEDIUM` (UNDERPERFORMING), `LOW` (MINOR GAP)
- **Traffic opportunity**: Estimated monthly traffic gain (= competitor traffic − user traffic for that category)
- **Target keywords**: 1-3 specific keywords with search volume (from `get_domains_intersection` data when available, otherwise inferred from URL path patterns and category context)
- **Competitor reference**: Which competitor page(s) to study as a benchmark
- **Effort estimate**: `Quick Win` (< 1 week, e.g., on-page tweaks), `Medium` (1-4 weeks, e.g., new page creation), `Large` (1+ month, e.g., new content hub)

Cap the list at 15-20 items maximum. Focus on the highest-priority gaps first.
If more than 20 items would be generated, keep only the top 20 by priority and
note in the dashboard that additional lower-priority items were omitted.

### Phase 4: Build the Dashboard

Create a standalone `.html` file and save it to `/mnt/user-data/outputs/`. The
file must be fully self-contained — all CSS, JS, and data embedded inline — so
the user can open it directly in any browser without a server.

Use the `create_file` tool (or bash) to write the file. Then use `present_files`
to share it with the user.

#### Branding (Serpstat Style)

Read `references/serpstat-brand.md` for the complete brand token reference.
Key points:
- Light theme: background `#F7F9FD`, text `#24223C`
- Primary accent: `#1B6EBE`, buttons: `#2C61ED`
- Font: Roboto (import from Google Fonts via `<link>` in `<head>`)
- Border radius: small (3-6px), cards with subtle shadows
- Professional, clean aesthetic — no dark mode unless user requests it

#### Report Description in Hero Header

The hero section MUST include a brief human-readable description (2-3 sentences)
right below the H1 title, rendered as the `.hero-subtitle` element. This
description should explain what the report contains and how to use it, so a
reader landing on the page immediately understands its purpose. Example:

> "This report compares **mybrand.com** against **competitor1.com** and
> **competitor2.com** in the US market (g_us). It identifies content categories
> where competitors outperform you, highlights keyword battles you're losing,
> and provides a prioritized action plan to close the gaps."

The description must be dynamic — generated from the actual analysis inputs
(domains, region, number of gaps found, total traffic opportunity). Do NOT use a
generic/static subtitle. Include at minimum: which domains are compared, which
region, and a one-line summary of the key finding (e.g., "We found 12 content
gaps with a total traffic opportunity of ~45K visits/month.").

#### Dashboard Structure

The dashboard MUST have these tabs/sections, implemented as a single-page app
with vanilla JS tab switching (no frameworks needed):

**Tab 1 — Overview**
- Domain comparison cards (traffic, keywords, visibility)
- Traffic distribution by category for each domain (horizontal bar charts via
  inline SVG or CSS bars — no external charting library needed)
- Tooltip on every metric explaining what it means

**Tab 2 — Category Battle**
- Side-by-side horizontal bar chart comparing all domains per category
- Color-coded by domain
- Sortable by different domains

**Tab 3 — Gap Analysis** (the core deliverable)
- List of all gaps sorted by priority score
- Each gap card shows:
  - Severity badge (color-coded: red=MISSING, orange=HUGE GAP, yellow=UNDERPERFORMING)
  - Category name
  - Traffic comparison (user vs each competitor)
  - Priority score with explanation tooltip
  - Expandable section with:
    - Top competitor URLs in this category with their traffic
    - User's URLs in this category (if any) with their traffic
    - Keywords from domain intersection data (if available for this category)
    - Actionable recommendation text

**Tab 4 — Keyword Battles**
- Data from `get_domains_intersection`
- Table showing keywords where the user ranks but loses to competitors
- Columns: keyword, search volume, user position, competitor position, gap
- Filter by position gap size
- This is the "low-hanging fruit" section — explain this to the user

**Tab 5 — Top Pages Explorer**
- Full sortable table of top pages per domain
- Columns: rank, URL path, traffic, keywords, category badge
- Toggleable between domains

**Tab 6 — Action Plan**
This is the actionable to-do list — the most operationally useful part of the
report. It turns analysis into execution.

- Render each to-do item as a card with:
  - A checkbox (visual only, toggled via JS — helps the user mentally
    track progress while reviewing)
  - Task title in bold
  - Severity badge (same color scheme as Gap Analysis tab)
  - Priority tag: `HIGH` (red), `MEDIUM` (amber), `LOW` (blue)
  - Effort estimate badge: `Quick Win` (green), `Medium` (yellow), `Large` (orange)
  - Traffic opportunity number (formatted, e.g., "+12.4K/mo")
  - Expandable detail section showing:
    - Target keywords with search volume
    - Competitor benchmark URLs
    - Brief rationale (1-2 sentences explaining why this task matters)
- Filters at the top:
  - By priority (HIGH / MEDIUM / LOW)
  - By effort (Quick Win / Medium / Large)
  - By severity (MISSING / HUGE GAP / UNDERPERFORMING / MINOR GAP)
- Default sort: by priority score descending (same as Gap Analysis)
- Include a summary bar at the top showing:
  - Total to-do items
  - Breakdown by priority (e.g., "5 HIGH · 8 MEDIUM · 4 LOW")
  - Total estimated traffic opportunity (sum of all items)
- Add a subtle section header/subtitle: "Your prioritized SEO action plan based
  on the gap analysis. Start from the top for maximum impact."

#### UX Requirements — Tooltips and Explanations

This is critical. The dashboard must be intuitive for SEO professionals who
may not be familiar with this specific tool. Every section needs context:

- **Section headers** should have a subtitle explaining what the section shows
  and why it matters
- **Metric labels** should have `(?)` info icons or hover tooltips explaining
  the metric (e.g., "Potential Traffic — Serpstat's estimate of monthly organic
  visits this page receives based on its keyword rankings and search volumes")
- **Severity badges** should have tooltips explaining the threshold
  (e.g., "HUGE GAP means competitors get 10× or more traffic in this category")
- **Priority Score** needs a clear explanation: "Higher = bigger opportunity.
  Calculated from traffic potential × ease of execution"
- **Recommendations** should be specific and actionable, not generic.
  Reference the actual competitor URLs and keywords when available.
- **Empty states** — if a section has no data, show a helpful message
  explaining why (e.g., "No keyword intersection data found — this usually
  means the domains target very different keyword sets")

#### Technical Implementation Notes

- **No external JS frameworks or charting libraries** — use vanilla JS, CSS
  bars/flexbox, and inline SVG for charts. This keeps the file lightweight and
  avoids CDN dependencies that could break offline.
- Import Roboto from Google Fonts via `<link>` tag (gracefully degrades to
  sans-serif if offline)
- All data should be embedded as a `<script>` block with a `const DATA = {...}`
  at the top of the file — this makes it easy to inspect and reuse the raw data
- Add a footer crediting the data source: "Data: Serpstat API · get_domain_top_urls · Database: {se}"
- Make the dashboard responsive — it should work on both desktop and tablet
- Tab switching: use a simple class-toggle pattern (`.tab-content { display: none }`,
  `.tab-content.active { display: block }`)
- Tables should be sortable via vanilla JS click handlers on column headers
- File name format: `gap-report_{userDomain}_vs_{competitors}_{date}.html`

### Phase 5: Present and Summarize

After rendering the dashboard, provide a brief text summary highlighting:
1. The 3 biggest content gaps (with estimated traffic opportunity)
2. The #1 quick win from keyword intersections
3. The top 3 to-do items from the Action Plan tab — state the task title,
   priority, and estimated traffic gain so the user can start acting immediately
4. One strategic recommendation

Keep it concise — the dashboard is the deliverable, the summary is the hook.
Point the user to the Action Plan tab as their starting point for execution.

## Error Handling

- If Serpstat MCP is not connected: use `suggest_connectors` to prompt connection
- If a domain returns no data: note this in the dashboard with a message
- If `get_domains_intersection` returns no results: show "No keyword intersection
  data found" in the Keyword Battles tab with a helpful explanation
- If credits are low: warn the user before making expensive calls. Use `get_credits_stats` to check.

## Example Invocations

**Simple:**
> "Compare puma.com with adidas.com and reebok.com"

**Detailed:**
> "I manage SEO for mybrand.com. Can you analyze our content gaps vs
> competitor1.com and competitor2.com in the US market? I want to know
> what topics they cover that we don't."

**Continuation:**
> "Now do the same analysis but for the UK market (g_uk)"
