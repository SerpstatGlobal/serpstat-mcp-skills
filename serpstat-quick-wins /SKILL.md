---
name: serpstat-quick-wins
description: >
  Find "low hanging fruit" SEO quick wins using Serpstat MCP. Identifies keywords
  where the site already ranks at positions 4-20 (almost in top-3) with high
  search volume (500+), calculates potential traffic gain from reaching top-3,
  finds missing keywords for each URL via content gap analysis, pulls competitor
  benchmarks, and delivers a prioritized action list with concrete per-URL
  recommendations — rendered as an interactive HTML dashboard in Serpstat brand
  style.

  Trigger on: quick wins, low hanging fruit, easy wins, fast SEO wins, almost
  top keywords, positions 4-20, positions 5-10, what can I improve quickly,
  быстрые победы, низко висящие фрукты, почти в топе, позиции 5-10, что можно
  быстро улучшить, low effort high impact SEO, easy ranking improvements,
  keywords close to top, near top-3 keywords. Use this skill even for
  "what's the fastest way to get more traffic" or "show me easy SEO
  improvements" or "which pages are almost ranking". Category: Content / SEO.
  SEO Impact: CRITICAL.
---

# Quick Wins Finder

Find "low hanging fruit" in 2 minutes. This skill discovers keywords where
the site already ranks at positions 4-20 with high search volume, calculates
the traffic potential from reaching top-3, identifies missing keywords to add
to each page's content, benchmarks against top-3 competitors, and produces a
prioritized list of concrete micro-tasks (add word X to title, add internal
link from /page-A to /page-B) with ROI estimates per URL.

## What the User Gets

- A list of 20-50 keywords at positions 4-20 with volume > 500
- For each keyword: URL, current position, potential traffic if promoted to top-3
- Missing keywords for each URL (what to add to the content)
- Concrete micro-tasks: "add keyword X to title", "add a link from /page-A
  to /page-B"
- A prioritized action list with estimated ROI per task

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

The analysis has 5 phases. Execute them in order.

---

### Phase 1: Collect Inputs

**Step 1a — Get the domain.**

If the user has not provided a domain, ask for it. Extract the bare domain
(no protocol, no trailing slash).

**Step 1b — Detect region and confirm.**

**IMPORTANT: ALWAYS confirm the region with the user before proceeding to
Phase 2 — even if the user did not explicitly mention a region. Never start
the analysis without explicit user confirmation of the region.**

If the user has NOT specified a region in their message:

1. Try to infer the most likely region from context clues: the user's
   language, the domain's TLD (e.g. `.de` → Germany, `.co.uk` → UK),
   the site's content language, or the user's known location.
2. Use `get_domain_regions_count` to verify which Google databases have
   the most keywords for this domain:
   ```
   Method: get_domain_regions_count
   Params: { domain: "<domain>", size: 5 }
   ```
3. Based on the context clues AND the API data, suggest the best-fitting
   region and ask the user to confirm using `ask_user_input_v0`:
   ```
   ask_user_input_v0({
     questions: [
       {
         question: "I detected that the best region for analysis is likely {detected_region}. Which market should we analyze?",
         type: "single_select",
         options: ["{detected_region}", <2nd and 3rd top regions>, "Other (I'll type it)"]
       }
     ]
   })
   ```
4. **Wait for the user's response. Do NOT proceed to Phase 2 until the
   user confirms or selects a region.**

If the user HAS explicitly specified a region in their message (e.g.
"analyze example.com in the US market"), use that region directly and
proceed to Phase 2 — no confirmation needed.

**If the user provided domain + region explicitly, skip interactive steps
and proceed to Phase 2.**

---

### Phase 2: Pull Data from Serpstat

Execute these API calls in sequence. All calls use the Serpstat MCP tools.

#### 2a. Get "almost top" keywords (positions 4-20, high volume)

Pull the domain's keywords filtered to the "quick win zone" — positions 4-20
with search volume above 500:

```
Method: get_domain_keywords
Params: {
  domain: "<domain>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 100,
  withIntents: true,
  include_all_fields: true,
  filters: {
    position_from: 4,
    position_to: 20,
    region_queries_count_from: 500
  }
}
```

This returns: keyword, position, search volume, traffic, CPC, difficulty,
URL, and intent data. These are the "almost top" keywords — the quick win
candidates.

From the results, select the top 50 keywords sorted by search volume. These
are the primary quick win candidates.

#### 2b. Get exact volume and KD for each keyword

For the top keywords from step 2a, get precise metrics:

```
Method: get_keywords_info
Params: {
  keywords: [<array of top keywords from 2a>],
  se: "<se>",
  withIntents: true,
  include_all_fields: true
}
```

This gives accurate volume, keyword difficulty (KD), CPC, and competition
for each keyword. Merge this data with the position data from step 2a.

#### 2c. Get missing keywords for top URLs

From the results in step 2a, identify the top 15 unique URLs (by traffic
potential). For each URL, pull missing keywords — content gaps that
competitors rank for but this URL does not:

```
Method: get_url_missing_keywords
Params: {
  url: "<url>",
  se: "<se>",
  sort: { "weight": "desc" },
  size: 20,
  filters: { region_queries_count_from: 100 }
}
```

Run this for each of the top 15 URLs. These missing keywords tell the user
exactly what to add to each page's content to improve rankings.

#### 2d. Get top-3 competitors for each URL (benchmark)

For the same top 15 URLs, find who currently ranks in top-3 for the same
keywords — these are the benchmark competitors:

```
Method: get_url_competitors
Params: {
  url: "<url>",
  se: "<se>",
  sort: { "cnt": "desc" },
  size: 3
}
```

This shows who the user needs to outperform for each URL. The competitor
URLs serve as content benchmarks.

---

### Phase 3: Analyze, Score, and Generate Recommendations

This phase is performed entirely by Claude — no additional API calls needed.

#### 3a. Calculate Traffic Potential

For each keyword at position 4-20, estimate the traffic gain from reaching
top-3. Use these approximate CTR benchmarks:

| Position | Estimated CTR |
|---|---|
| 1 | 28% |
| 2 | 15% |
| 3 | 11% |
| 4-5 | 6% |
| 6-10 | 2.5% |
| 11-20 | 1% |

**Traffic potential** = volume × (target_CTR - current_CTR)

Where target_CTR is position 3 (11%) and current_CTR is based on current
position. This gives the monthly traffic gain if the keyword reaches top-3.

#### 3b. Score and Prioritize

For each keyword/URL pair, calculate a **quick win score** (0-100):

```
score = (volume_norm × 0.3) + (proximity_score × 0.3) +
        (low_difficulty × 0.2) + (missing_kw_count × 0.2)
```

Where:
- **volume_norm**: normalized volume (0-100, where 100 = highest volume keyword)
- **proximity_score**: how close to top-3 (position 4 = 100, position 20 = 0)
- **low_difficulty**: inverse difficulty (100 - KD), so easier keywords score higher
- **missing_kw_count**: normalized count of missing keywords for the URL
  (more missing = more room to improve = higher score)

#### 3c. Generate Concrete Micro-Tasks

For each of the top 20-30 quick win URLs, generate actionable tasks:

**Title optimization tasks:**
- If the primary keyword is NOT in the page's title tag (inferred from URL
  and keyword data), recommend: "Add '{keyword}' to the title tag of {url}"

**Content enrichment tasks:**
- For each URL's top 5 missing keywords (from step 2c), recommend:
  "Add a paragraph about '{missing_keyword}' to {url}"
- Group related missing keywords into single content blocks

**Internal linking tasks:**
- Cross-reference URLs from the quick win list: if URL-A has missing keyword X
  and URL-B ranks for keyword X, recommend:
  "Add internal link from {url-B} to {url-A} with anchor text '{keyword X}'"

**Priority assignment:**
- **HIGH**: Position 4-6, Volume > 2000, KD < 50 — closest to top-3, high impact
- **MEDIUM**: Position 7-10, Volume > 1000, KD < 60 — solid opportunity
- **LOW**: Position 11-20, Volume > 500, KD < 70 — longer push needed

**Effort estimation:**
- Title change = QUICK WIN (5 min)
- Add paragraph = LOW EFFORT (30 min)
- Internal link = QUICK WIN (5 min)
- New section / rewrite = MEDIUM EFFORT (2-4 hrs)

#### 3d. Estimate ROI

For each task, estimate the ROI:
- **Potential traffic** = volume × (target_CTR - current_CTR) per month
- **Time to see results** = 2-4 weeks for title/internal link changes,
  4-8 weeks for content additions
- **Effort** = as estimated above

Sort all tasks by: potential_traffic / effort (highest ROI first).

---

### Phase 4: Build the Dashboard

Create a standalone `.html` file and save it to `/mnt/user-data/outputs/`.
The file must be fully self-contained — all CSS, JS, and data inline.

Read `references/serpstat-brand.md` for the complete brand style reference
including colors, fonts, widget catalog, and spacing rules.

#### Dashboard Structure

**Hero Section:**
- Pill: "QUICK WINS FINDER · Serpstat"
- Title: "{domain} — Quick Wins Report"
- Subtitle: "Low-hanging fruit keywords at positions 4-20 · {region} · {month} {year}"
- Description paragraph (below subtitle, in muted/secondary text, max 2-3 sentences):
  A brief explanation of what this report contains and how to use it, e.g.:
  "This report identifies keywords where {domain} already ranks at positions 4-20
  with significant search volume — the easiest opportunities to push into top-3.
  Below you'll find a prioritized action plan with concrete tasks, missing keywords
  to add to each page, and competitor benchmarks. Start with the HIGH priority
  tasks in the Action Plan tab for the fastest results."
  Adapt the description to reflect the actual data found (number of keywords,
  number of URLs, total potential traffic). Keep it concise and actionable.
- Stats row: Total Quick Wins, Total Potential Traffic/mo, Avg Position,
  URLs Analyzed, Missing Keywords Found

**Tab 1 — Overview**
- Metric cards row (Widget 3): Quick Wins by priority (HIGH, MEDIUM, LOW),
  Total Potential Traffic, Avg KD
- Bar distribution (Widget 4): Keywords by position bracket (4-6, 7-10, 11-20)
  showing count and total volume per bracket
- Bar distribution: Keywords by intent type
  (Informational, Commercial, Transactional, Navigational)
- Data table (Widget 5) — Top 15 Quick Wins summary:
  Columns: Keyword, Position, Volume, KD, Traffic Potential, URL (truncated),
  Priority (badge)
  Sorted by traffic potential descending

**Tab 2 — All Keywords**
- Full data table (Widget 5) with ALL quick win keywords (up to 50)
- Columns: Keyword, Current Position, Volume, KD, CPC, Traffic Potential,
  URL, Intent (badge), Priority (badge)
- Sortable by any column
- Color-coded position cells: green (4-6), yellow (7-10), orange (11-20)

**Tab 3 — Action Plan**
- Action cards (Widget 6) for the top 20-30 tasks
- Each card shows:
  - Priority badge (HIGH/MEDIUM/LOW) + effort badge (QUICK WIN / LOW EFFORT /
    MEDIUM EFFORT)
  - Left border color: red = HIGH, amber = MEDIUM, blue = LOW
  - Task title (e.g., "Add 'keyword planner' to title of /blog/seo-tools")
  - Metrics row: Volume, Current Position, Traffic Potential
  - Expandable detail:
    - Target URL
    - Target keywords (primary + missing keywords to add)
    - Benchmark competitor URL (from step 2d)
    - Estimated time to results
- Cards sorted by ROI score descending (traffic potential / effort)
- Two-column grid on desktop, single column on mobile

**Tab 4 — Missing Keywords by URL**
- For each of the top 15 URLs, a collapsible section showing:
  - URL as header
  - Current quick win keywords for this URL (with position, volume)
  - Missing keywords table: Keyword, Volume, Weight (connection strength),
    Suggested Action
  - Competitor benchmark URL (from step 2d)
- This tab helps the user see exactly what to add to each page

**Tab 5 — Competitor Benchmarks**
- For each top 15 URL, show the top-3 competitor URLs (from step 2d)
- Comparison bars (Widget 8) showing keyword overlap
- Data table: User URL, Competitor URL, Shared Keywords count,
  Competitor's advantage keywords

#### UX Requirements

- Every section needs a title + subtitle explaining what it shows
- All numbers in Roboto Mono
- All badges pill-shaped (border-radius: 20px)
- 48px gap between sections
- Sortable tables with click handlers
- Action cards with colored left border matching priority
- Position cells color-coded: green (4-6), yellow (7-10), orange (11-20)
- Dark footer: "Data: Serpstat API · get_domain_keywords, get_keywords_info,
  get_url_missing_keywords, get_url_competitors · DB: {se}" + Serpstat logo SVG

#### File Naming

```
quick-wins_{domain}_{se}_{YYYY-MM-DD}.html
```

---

### Phase 5: Present and Summarize

After creating the dashboard, use `present_files` to share it with the user.

Provide a brief text summary:
1. How many quick win keywords were found, total potential traffic
2. Top 3 highest-ROI tasks (task description, potential traffic, effort)
3. How many URLs have missing keywords to add
4. Estimated total traffic gain if all quick wins are executed
5. Wow-effect line: "You have {N} keywords at positions 5-10 with a combined
   potential of +{X}K visits/month. Here are 10 tasks for this week, each
   takes about 30 minutes. This is the fastest way to show SEO ROI — results
   visible in 2-4 weeks."

Keep it concise — the dashboard is the deliverable.

---

## Credit Budget

Estimated API credit usage per run:

| Step | Method | Credits |
|---|---|---|
| Credit check | get_credits_stats | free |
| Region check | get_domain_regions_count | ~5 |
| Domain keywords (filtered) | get_domain_keywords | ~100 |
| Keyword info | get_keywords_info | ~50 |
| Missing keywords (×15 URLs) | get_url_missing_keywords | ~300 |
| URL competitors (×15 URLs) | get_url_competitors | ~45 |
| **Total** | | **~500** |

To stay under 400 credits when budget is tight:
- Reduce URLs analyzed from 15 to 10
- Reduce missing keywords size from 20 to 10
- Reduce domain keywords size from 100 to 50

---

## Error Handling

- If Serpstat MCP is not connected: use `suggest_connectors`
- If domain returns 0 keywords at positions 4-20: widen to positions 4-30 or
  lower volume threshold to 200. If still empty, inform: "No quick win
  opportunities found — the domain may have too few rankings or all keywords
  are already in top-3."
- If credits are insufficient: warn and stop (see Pre-flight Check)
- If get_url_missing_keywords returns empty for a URL: skip it, note
  "No content gaps found for {url} — the page covers its topic well."
- If get_url_competitors returns "Data not found": the URL has fewer than
  10 keywords in top-10. Skip competitor benchmark for that URL.

---

## Why This Skill Needs Serpstat

Filtering by positions + volume + enriching with missing keywords at the
URL level — this is a data pipeline that's impossible to assemble manually
in reasonable time. Without the API, you'd need to manually check each URL
for missing keywords, cross-reference competitors, and calculate traffic
potential. Serpstat provides the position, volume, and content gap data at
the URL level that makes this actionable.

---

## Example Invocations

**Simple:**
> "Find quick wins for serpstat.com"

**With context:**
> "I manage ahrefs.com. Show me keywords at positions 5-10 that I can
> push to top-3 with small content changes."

**Casual:**
> "What's the fastest way to get more traffic to mybrand.com?"

**Direct:**
> "Low hanging fruit for example.com in the US market"

**Localized:**
> "Быстрые победы для сайта example.com, регион — Украина"
> "Найди низко висящие фрукты для mysite.com"
