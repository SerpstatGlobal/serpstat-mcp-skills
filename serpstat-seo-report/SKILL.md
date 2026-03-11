---
name: serpstat-seo-report
description: >
  Generate a comprehensive One-Page SEO Report for any domain using Serpstat MCP.
  Covers organic visibility, keyword rankings, position distribution, keyword
  difficulty breakdown, top pages, top keywords with traffic, opportunity finder
  (bubble chart + action cards), full backlink profile (SDR, dofollow/nofollow
  thermometer, anchor analysis), and a prioritized action plan — all rendered as
  a polished interactive HTML dashboard in Serpstat brand style.

  Trigger on: SEO report, SEO audit, domain analysis, site analysis, domain
  overview, SEO health check, one-page SEO report, full SEO report, SEO
  dashboard, SEO scorecard, website audit, organic analysis, backlink audit,
  keyword audit, "analyze mysite.com", "run SEO report", "how is my site
  doing in SEO", "check SEO health", "SEO snapshot". Use this skill even
  for "tell me everything about domain.com's SEO" or "full SEO analysis".
---

# One-Page SEO Report

Generate a complete SEO health dashboard in 2 minutes. This skill pulls domain
metrics, keyword rankings, position distribution, backlink profile, and
opportunities from Serpstat, computes an SEO Score (0-100), identifies quick
wins and growth potential, and renders everything as a single interactive HTML
dashboard the user can open in any browser.

## What the User Gets

- SEO Score (0-100) with grade and gauge visualization
- Organic visibility metrics (keywords, traffic, visibility, dynamics)
- Position distribution (Top 1 / 2-3 / 4-10 / 11-30 / 31-100)
- Keyword difficulty breakdown (Low / Medium / Hard)
- Top 10 pages by keyword count
- Top 20 keywords with position, traffic, volume, and KD
- Opportunity finder: hot opportunities, quick wins, growth potential
- Full backlink profile: SDR, ref domains, dofollow/nofollow ratio, anchors
- Alert banners for critical issues
- Prioritized action plan with concrete next steps

## Prerequisites

The Serpstat MCP connector must be enabled in the chat. If it's not connected,
use `suggest_connectors` to prompt the user to connect it before proceeding.

## Pre-flight Check

Before executing any API calls, verify the account has sufficient credits.

1. Call `get_credits_stats`
2. Check `data.left_lines`:
   - If `left_lines < 600` → **stop immediately**, do not proceed
   - Inform the user: "Insufficient API credits to run this analysis.
     Current balance: {left_lines}. Required: 600.
     Please purchase more credits: serpstat.com/users/profile"
3. If `left_lines >= 600` → proceed with execution

**Never skip this check.** Running without sufficient credits will result
in partial or failed results mid-execution.

## Workflow Overview

The analysis has 6 phases. Execute them in order.

---

### Phase 1: Collect Inputs

**Step 1a — Get the domain.**

If the user has not provided a domain, ask for it. Extract the bare domain
(strip `https://`, `www.`, trailing slashes).

**Step 1b — Auto-detect region (if not explicitly provided).**

If the user did NOT specify a region/search engine in their message, auto-detect
the best region:

1. Call Serpstat to find the domain's strongest region:

```
Method: get_domain_regions_count
Params: { domain: "<userDomain>" }
```

Pick the region with the maximum keyword count (top-1). Record its `se` code.

2. Also infer a fallback from the domain's TLD:
   - `.com` with English content → `g_us`
   - `.co.uk` → `g_uk`
   - `.de` → `g_de`
   - `.fr` → `g_fr`
   - `.ua` or Ukrainian content → `g_ua`
   - For ambiguous cases, default to `g_us`

Use the Serpstat-detected region as primary, TLD-inferred as secondary context.

**Step 1c — Confirm with the user before proceeding.**

This step is MANDATORY whenever the region was auto-detected (not explicitly
stated by the user). Do NOT start data collection (Phase 2) without explicit
user confirmation.

Present your selections using `ask_user_input_v0`:

```
"Based on Serpstat data, I've identified the optimal parameters for your SEO report:

• **Domain**: example.com
• **Primary region**: 🇺🇸 USA (g_us) — 45K keywords
• **Secondary region (for comparison)**: Always g_us for US market context

Please confirm or adjust below."

ask_user_input_v0({
  questions: [
    {
      question: "Is this the right primary region for the analysis?",
      type: "single_select",
      options: ["🇺🇸 USA (g_us)", "🇬🇧 UK (g_uk)", "🇩🇪 Germany (g_de)", "🇫🇷 France (g_fr)", "🇺🇦 Ukraine (g_ua)"]
    }
  ]
})
```

If the user confirms — proceed to Phase 2.
If the user adjusts — use the updated region.
If the user types a custom region, map it to the correct Serpstat `se` code.

**If the user already provided both domain AND region in their opening message,
skip steps 1b and 1c entirely and proceed directly to Phase 2.**

**Step 1d — Resolve region.**

Map the selected region label to the Serpstat `se` code:
- 🇺🇸 USA → `g_us`
- 🇬🇧 UK → `g_uk`
- 🇩🇪 Germany → `g_de`
- 🇫🇷 France → `g_fr`
- 🇺🇦 Ukraine → `g_ua`

---

### Phase 2: Pull Data from Serpstat

Make these API calls. All calls use Serpstat MCP tools.

#### 2a. Domain info — primary region

```
Method: get_domains_info
Params: { domains: ["<userDomain>"], se: "<primary_se>" }
```

Extract: visibility, keywords, traff, traff_dynamic, new_keywords, out_keywords.

#### 2b. Domain info — US region (for comparison, if primary is not g_us)

```
Method: get_domains_info
Params: { domains: ["<userDomain>"], se: "g_us" }
```

Extract: keywords_us, traff_us, traff_dynamic_us, visibility_us.
If primary region IS g_us, skip this call and reuse data from 2a.

#### 2c. Backlink summary

```
Method: get_backlinks_summary
Params: { query: "<userDomain>", searchType: "domain" }
```

Extract: SDR, ref_domains, backlinks, dofollow_backlinks, nofollow_backlinks,
text_backlinks, image_backlinks, malicious_domains, malicious_change, ips, subnets.

#### 2d. Top pages

```
Method: get_domain_urls
Params: { domain: "<userDomain>", se: "<primary_se>", size: 20 }
```

Extract: url + keywords count for each page.

#### 2e. Top keywords

```
Method: get_domain_keywords
Params: { domain: "<userDomain>", se: "<primary_se>", size: 100 }
```

Extract for each keyword: keyword, position, traff, region_queries_count
(search volume), difficulty (KD).

#### 2f. Top backlink anchors

```
Method: get_top10_anchors
Params: { query: "<userDomain>", searchType: "domain" }
```

Extract: anchor text, ref_domains count, backlinks count for each.

---

### Phase 3: Analyze the Data

From the collected data, compute and derive the following:

#### Domain Metrics

- **Primary region**: keywords, traff, traff_dynamic, visibility, new_keywords, out_keywords
- **US region** (if different): keywords_us, traff_us, traff_dynamic_us, visibility_us
- **Backlinks**: SDR, ref_domains, backlinks, dofollow_backlinks, nofollow_backlinks
- **Derived percentages**:
  - `dofollow_pct = round(dofollow_backlinks / backlinks * 100, 1)`
  - `nofollow_pct = round(100 - dofollow_pct, 1)`
  - `text_pct = round(text_backlinks / backlinks * 100, 1)`
  - `image_pct = round(image_backlinks / backlinks * 100, 1)`
  - `redirect_pct = round(100 - text_pct - image_pct, 1)` (approximate)
- **Malicious**: malicious_domains, malicious_change

#### SEO Score (0-100)

Compute a composite SEO health score:

```
visibility_score = min(visibility / 20 * 100, 100)
sdr_score = SDR  (already 0-100)
traffic_score = min(traff / 100000 * 100, 100)
growth_score = traff_dynamic > 0
    ? 70 + min(traff_dynamic / 1000 * 30, 30)
    : max(0, 50 + traff_dynamic / 500 * 50)
keyword_balance = new_keywords / (new_keywords + abs(out_keywords) + 1) * 100

score = round(
  visibility_score * 0.25 +
  sdr_score * 0.20 +
  traffic_score * 0.20 +
  growth_score * 0.20 +
  keyword_balance * 0.15
)
```

Grade thresholds:
- 80-100 → "Excellent 🚀"
- 60-79 → "Good 👍"
- 40-59 → "Average ⚡"
- 0-39 → "Needs Work 🔧"

#### Position Distribution

From the 100-keyword sample, count how many keywords fall in each range:
- Top 1 (position = 1)
- Top 2-3
- Top 4-10
- Top 11-30
- Top 31-100

Scale proportionally to the domain's total keyword count.

#### Keyword Difficulty Distribution

From the sample, count:
- Low KD (0-30)
- Medium KD (31-60)
- Hard KD (61-100)

Scale proportionally to total keywords.

#### Top Pages

Use `get_domain_urls` data. For each URL, shorten the path to a readable label
(strip the domain, truncate long paths). Show top 10 by keyword count.

#### Top Keywords

Use `get_domain_keywords` data. For each of the top 20 keywords, include:
keyword, position, traffic, search volume, KD.

#### Opportunities

From the keyword data, identify three types:

1. **Hot Opportunities**: position > 3 AND search volume > 5,000 — high-volume
   keywords where small ranking improvements yield large traffic gains.
2. **Quick Wins**: KD < 20 AND position > 5 — low-competition keywords that
   are easy to push higher with minimal effort.
3. **Growth Potential**: high traffic estimate AND position > 10 — keywords
   already driving some traffic but with major room for improvement.

Select the top 5 opportunities across all types.

#### Alerts

Generate contextual alert banners:
- **Red alert**: if out_keywords significantly exceeds new_keywords (balance < -500),
  or if malicious_domains_change > 5
- **Green alert**: if traff_dynamic > 0 (traffic is growing)
- **Yellow alert**: if visibility_us < 1.0 (weak US presence), or if SDR < 30

---

### Phase 4: Build the Dashboard

Create a standalone `.html` file and save it to `/mnt/user-data/outputs/`.
The file must be fully self-contained — all CSS, JS, and data inline — so the
user can open it directly in any browser.

**Read the style guide** at `references/serpstat-report-style-guide.md` for the
complete design system: colors, fonts, spacing, widget catalog, and CSS. Follow
it exactly.

File name format: `seo_report_{domain_clean}_{date}.html`
(where `domain_clean` replaces dots with underscores).

#### Report Description in Hero Header

The hero section MUST include a dynamic, context-aware description in the
`.hero-subtitle` element. This description explains what the report contains,
generated from the actual analysis data. Example:

> "Comprehensive SEO analysis of **example.com** in the US market (g_us).
> SEO Score: 72/100 (Good). Tracking 45K keywords with 128K monthly organic
> visits. This report covers rankings, backlink health, and 5 actionable
> growth opportunities."

Include at minimum: domain name, region, SEO Score, total keywords, total
traffic, and a one-line finding summary. Do NOT use a generic/static subtitle.

#### Dashboard Sections

The dashboard uses the Serpstat brand style (light theme with dark hero). It
has a tab bar for navigation. Structure the following tabs and sections:

**Tab 1 — Overview**

Sections in order:

1. **SEO Score Gauge** — A doughnut chart (CSS-based or Chart.js from CDN) showing
   the score out of 100 with the grade label. Use the primary accent color for
   the filled arc and a muted track for the remainder.

2. **Alert Banners** — Contextual alerts based on the data (red/green/yellow).
   Each alert has an icon, short title, and explanation. Use semantic badge colors.

3. **Key Metrics Row** — 6 metric cards in a row:
   - Keywords (primary region)
   - Traffic/month
   - Visibility
   - SDR (Serpstat Domain Rank)
   - Ref. Domains
   - Keyword Balance (new − lost)

   Each card uses the Metric Card widget from the style guide: colored left
   border, large monospace number, label above, description below.

4. **Organic Visibility Panel** — 8 KPI cards (4+4 grid):
   - Row 1: Keywords (primary), Traffic (primary), Visibility (primary), Lost Keywords
   - Row 2: Keywords (US), Traffic (US), Visibility (US), Regions covered
   - Each card shows the value with a delta arrow (▲/▼) colored green/red.

**Tab 2 — Rankings**

1. **Position Distribution** — Donut chart showing Top 1 / 2-3 / 4-10 / 11-30 / 31-100
   with a legend showing counts and progress bars. Use CSS donut or Chart.js.

2. **Keyword Difficulty Distribution** — Donut chart showing Low / Medium / Hard
   with legend and progress bars.

3. **Geographic Coverage** — Horizontal bar chart showing top 8 regions from
   `get_domain_regions_count` data (already fetched in Phase 1).

**Tab 3 — Top Pages & Keywords**

1. **Top Pages** — Horizontal bar chart: top 10 pages (y-axis = readable URL label,
   x-axis = keyword count). Use CSS bars from the style guide.

2. **Top Keywords Table** — Data table (style guide Widget 5) with 20 rows:
   Columns: #, Keyword, Position (color badge), Traffic, Volume, KD (color-coded),
   mini traffic bar. Make it sortable.

3. **Traffic by Position Range** — Grouped bar showing aggregate traffic for each
   position bucket (Top 1, 2-3, 4-10, 11-30, 31-100).

4. **Insight Card** — Highlight the most surprising keyword: high traffic but
   poor position. Explain why this is an opportunity.

**Tab 4 — Opportunities**

1. **Opportunity Scatter/Bubble** — Plot opportunities with X = position,
   Y = search volume, bubble size = traffic, color = KD level. Can be built
   with CSS positioned dots or Chart.js bubble chart.

2. **Opportunity Cards** — Action cards (style guide Widget 6) for the top 5
   opportunities. Each card shows:
   - Type badge: 🔥 HOT (red border) / ⚡ QUICK WIN (yellow) / 📈 GROWTH (green)
   - Keyword, position, volume, KD badge
   - Action description: what to do to capture the opportunity
   - Priority based on potential impact

**Tab 5 — Backlink Profile**

1. **Backlink KPI Row** — 5 metric cards:
   - SDR (color: ≥50 green, 30-50 yellow, <30 red)
   - Ref. Domains
   - Total Backlinks
   - % Dofollow (color bar: ≥80% green, 50-80% yellow, <50% red)
   - IPs / Subnets

2. **Link Type Thermometer** — Horizontal stacked bar (28px height, rounded):
   - Follow segment: green gradient, width = dofollow_pct%
   - Nofollow segment: amber, width = nofollow_pct%
   - UGC segment: blue, width = 0% (no API data — show "no data")
   - Sponsored segment: orange, width = 0% (no API data — show "no data")
   - Header shows total follow / nofollow counts

3. **Breakdown Cards** — 4 small cards for Follow, Nofollow, UGC, Sponsored.
   UGC and Sponsored show "No data available" at reduced opacity since the
   Serpstat API doesn't provide this breakdown.

4. **Content & Anchors Panel**:
   - Progress bars: Text links (text_pct%), Image links (image_pct%), Redirects (redirect_pct%)
   - Bar chart: top 6 anchors by ref_domains and backlinks (from `get_top10_anchors`)
   - Info card: links from homepages, malicious domains (with warning if increased),
     any spam anchor patterns

**Tab 6 — Action Plan**

1. **Summary Bar** — Total issues found, breakdown by severity (Critical / Quick Win / Strength)

2. **Three Columns Layout**:
   - Column 1: "Critical Issues" (red) — real problems found in the data
     (e.g., losing keywords faster than gaining, malicious backlink growth,
     weak US visibility)
   - Column 2: "Quick Wins" (yellow/amber) — easy improvements
     (e.g., keywords at positions 4-5 with high volume, content to optimize)
   - Column 3: "Strengths" (green) — what's working well
     (e.g., growing traffic, strong SDR, healthy dofollow ratio)

3. **Priority Plan Card** — Numbered list of 5 prioritized actions with colored
   priority indicators. Each action is specific and references actual data
   (keyword names, URLs, metrics).

#### Technical Implementation

- Use Chart.js 4.x from CDN (`https://cdn.jsdelivr.net/npm/chart.js`) for donut
  and bubble charts. Initialize each chart in a separate IIFE.
- All data in a `const DATA = {...}` block at the top of the `<script>` section.
- All numbers formatted: ≥1M → "1.5M", ≥1K → "92K", otherwise locale format.
- Chart axes and grid lines use muted colors matching the style guide.
- Hover effects on table rows.
- Chart animations with duration 1200ms.
- Responsive: grid collapses from 4-column to 2-column at 768px.
- Tables get horizontal scroll on mobile.
- Follow all spacing, typography, colors, and widget patterns from the style guide.

---

### Phase 5: Present and Summarize

After creating the file, use `present_files` to share it, then provide a brief
text summary:

```
✅ SEO Report created

📊 Quick Summary:
- SEO Score: {score}/100 ({grade})
- Primary region: {country} ({se}), {keywords}K keywords
- Traffic: {traff}/mo, trend {▲/▼ delta}
- Keyword balance: {new} new / {out} lost = {net balance}
- Backlinks: SDR {sdr}, {refDomains} domains, {backlinks} links

🎯 Top 3 Opportunities:
1. "{keyword}" — position {pos}, volume {vol}, KD {kd}
2. "{keyword}" — position {pos}, volume {vol}, KD {kd}
3. "{keyword}" — position {pos}, volume {vol}, KD {kd}

⚠️ Key issue: {description if any}
```

Keep it concise — the dashboard is the deliverable.

---

### Phase 6: Error Handling

- If Serpstat MCP is not connected: use `suggest_connectors` to prompt connection.
- If a domain returns no data: inform the user clearly with a suggestion to
  check the domain spelling or try a different region.
- If backlink API returns errors: render the backlink section with "Data
  unavailable" placeholders and note this in the summary.
- If credits are low mid-execution: stop gracefully, present whatever data was
  already collected, and warn the user.

## Example Invocations

**Simple:**
> "Run an SEO report for serpstat.com"

**With region:**
> "SEO audit of ahrefs.com in the US market"

**Casual:**
> "How is moz.com doing in SEO?"

**Detailed:**
> "Give me a full SEO analysis of semrush.com — rankings, backlinks, opportunities, everything"
