---
name: serpstat-content-strategy
description: >
  Build a complete content strategy in 2 minutes using Serpstat MCP. Takes a
  domain, pulls current rankings, expands with related keywords and long-tail
  suggestions, finds competitor-only keywords the domain misses, clusters by
  intent, assigns content formats, scores traffic potential, and outputs a
  prioritized content plan as an interactive HTML dashboard in Serpstat style.

  Trigger on: content plan, content strategy, blog topics, editorial calendar,
  topic ideas, what to write about, publication plan, content keyword research.
  Also: "контент-план", "content strategy", "темы для блога",
  "контент-стратегия", "план публикаций", "what should I write about",
  "content calendar", "blog strategy", "give me topics for my blog",
  "what content is my site missing". Use this skill for any Serpstat content
  planning request.
---

# Content Strategy Architect

Full content strategy in 2 minutes. This skill takes a domain, collects
semantics via phrase matching, related keywords, and search questions,
clusters by intent, finds missed competitor topics, and delivers a
ready-to-use content plan for 3-6 months with priorities, traffic
estimates, and format recommendations (article, FAQ, category, landing).

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
     Please top up at serpstat.com/users/profile"
3. If `left_lines >= 600` → proceed with execution

**Never skip this check.**

## Workflow Overview

The analysis has 5 phases. Execute them in order.

---

### Phase 1: Collect Inputs

**Step 1a — Get the domain.**

If the user has not provided a domain, ask for it. Extract the bare domain
(no protocol, no trailing slash).

**Step 1b — Auto-detect region and competitors, then confirm.**

Even if the user did NOT explicitly mention a region or competitors,
**do NOT ask them to choose from scratch**. Instead, auto-detect both
and present them for confirmation. This reduces friction — the user just
says "yes" or corrects.

1. Call `get_domain_regions_count` to find the top region:

```
Method: get_domain_regions_count
Params: { domain: "<domain>", size: 5 }
```

Pick the region with the highest keyword count as the recommended one.

2. Call `get_domain_competitors` using that top region:

```
Method: get_domain_competitors
Params: { domain: "<domain>", se: "<top_region_se>", size: 6 }
```

Pick the top competitor (skip the domain itself in results).

3. **Present the auto-detected settings for confirmation** via
`ask_user_input_v0`. Show a summary of what was detected and ask
the user to confirm or adjust:

```
ask_user_input_v0({
  questions: [
    {
      question: "I detected these settings for your analysis:\n\n📍 Region: <detected region, e.g. 🇺🇸 USA (g_us)>\n🏆 Competitor: <detected competitor domain>\n\nShall we proceed?",
      type: "single_select",
      options: [
        "Yes, proceed",
        "Change region",
        "Change competitor",
        "Skip competitor analysis",
        "Change both"
      ]
    }
  ]
})
```

4. **CRITICAL: Do NOT proceed to Phase 2 until the user confirms.**
   Wait for the user's response. If they want changes, ask for the
   new values and re-confirm. Only after explicit approval, move on.

**If the user already provided domain + region + competitor explicitly
in their opening message, skip auto-detection — use what they gave,
confirm briefly, and proceed to Phase 2.**

**If the user provided only region OR only competitor, auto-detect
the missing one and confirm both together.**

---

### Phase 2: Pull Data from Serpstat

Execute these API calls in sequence. All calls use the Serpstat MCP tools.

#### 2a. Current keyword semantics

Pull the domain's existing keyword rankings to understand what content
already exists:

```
Method: get_domain_keywords
Params: {
  domain: "<domain>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 100,
  withIntents: true,
  include_all_fields: true
}
```

This returns: keyword, position, search volume, traffic, CPC, difficulty,
URL, and intent data. These are the domain's current content topics.

#### 2b. Keyword expansion — related keywords

For the top 3-5 highest-volume keywords from step 2a, expand semantics:

```
Method: get_keywords
Params: {
  keyword: "<top_keyword>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 50,
  withIntents: true,
  include_all_fields: true,
  filters: { region_queries_count_from: 100 }
}
```

Run this for each of the top 3-5 seed keywords. This finds keywords that
share ranking domains — related by competition overlap.

#### 2c. Semantic expansion — related by meaning

For the same top 3-5 keywords, also pull semantically related keywords:

```
Method: get_related_keywords
Params: {
  keyword: "<top_keyword>",
  se: "<se>",
  sort: { "region_queries_count": "desc" },
  size: 50,
  withIntents: true,
  include_all_fields: true,
  filters: { region_queries_count_from: 100 }
}
```

These are related by meaning (not just ranking overlap) — better for
topic clustering and discovering adjacent content opportunities.

#### 2d. Long-tail expansion — autocomplete suggestions

For the same seed keywords, pull autocomplete-style suggestions:

```
Method: get_keyword_suggestions
Params: {
  keyword: "<top_keyword>",
  se: "<se>",
  size: 50
}
```

These capture question-style and long-tail queries — perfect for FAQ
sections and "People Also Ask" opportunities.

#### 2e. Competitor missed keywords (if competitor selected)

If the user selected a competitor in Phase 1, pull keywords the competitor
ranks for that the user's domain does NOT:

```
Method: get_domain_uniq_keywords
Params: {
  se: "<se>",
  domains: ["<competitor>"],
  minusDomain: "<userDomain>",
  size: 100,
  include_all_fields: true,
  filters: { region_queries_count_from: 100 }
}
```

These are "missed topics" — content the competitor has that the user lacks.
This is the highest-value data for the content plan.

---

### Phase 3: Cluster, Classify, and Prioritize

This phase is performed entirely by Claude — no additional API calls needed.

#### 3a. Deduplicate

Merge all keywords from steps 2a-2e. Remove exact duplicates. For near-
duplicates (e.g., "best running shoes" vs "best running shoe"), keep the
higher-volume variant.

#### 3b. Cluster into Topics

Group keywords into content topics (clusters). A topic is a group of
keywords that can be targeted by a single piece of content. Rules:

- Each topic gets a **topic name** (a short descriptive label, e.g.,
  "Best Running Shoes 2025", "How to Start a Blog", "CRM Comparison")
- Group by semantic similarity and shared intent
- Aim for 30-100 topics total
- Each topic should have 3-15 keywords
- One keyword per topic is the **primary keyword** (highest volume)
- Remaining keywords are **supporting keywords**

#### 3c. Classify Intent

For each topic, assign a search intent based on the keywords and any
intent data from Serpstat:

| Intent | Signals |
|---|---|
| Informational | how, what, why, guide, tutorial, tips, examples |
| Commercial | best, top, review, comparison, vs, alternative |
| Transactional | buy, price, discount, coupon, order, cheap |
| Navigational | brand name, product name, login, official |

If Serpstat returned `withIntents` data, use it. Otherwise infer from
keyword text.

#### 3d. Assign Content Format

Based on intent and keyword patterns, assign a recommended content format:

| Format | When to use |
|---|---|
| Long-form Article | Informational, high volume, broad topic |
| Listicle / Roundup | "best X", "top N", comparison-style |
| How-to Guide | "how to", step-by-step, tutorial |
| FAQ Page | Question keywords, "what is", multiple related questions |
| Category / Hub Page | Head terms with many subtopics |
| Landing Page | Transactional, product-focused |
| Glossary / Definition | "what is X", single-concept terms |
| Comparison Post | "X vs Y", "X alternative" |

#### 3e. Score and Prioritize

For each topic, calculate a **priority score** (0-100):

```
priority = (volume_score × 0.4) + (difficulty_score × 0.3) + (gap_score × 0.3)
```

Where:
- **volume_score**: normalized total monthly volume of all keywords in topic
  (0-100, where 100 = highest volume topic)
- **difficulty_score**: inverse of average keyword difficulty
  (100 - avg_difficulty), so easier topics score higher
- **gap_score**: 100 if topic comes from competitor gap data (step 2e),
  50 if it's a new topic not in current rankings (step 2a), 0 if the
  domain already ranks well for it

Classify each topic:
- **Quick Win** (priority ≥ 70, difficulty < 40): easy to rank, high potential
- **Strategic** (priority ≥ 50, difficulty 40-70): medium effort, good payoff
- **Long-term** (priority < 50 OR difficulty > 70): requires sustained effort
- **Existing — Optimize** (already ranking but position > 10): improve existing

#### 3f. Build Content Calendar

Distribute topics across a 3-6 month timeline:
- Month 1-2: Quick Wins + Existing Optimizations
- Month 2-4: Strategic topics
- Month 4-6: Long-term topics

Assign 4-8 topics per month (realistic publishing cadence).

---

### Phase 4: Build the Dashboard

Create a standalone `.html` file and save it to `/mnt/user-data/outputs/`.
The file must be fully self-contained — all CSS, JS, and data inline.

Read `references/serpstat-brand.md` for the complete brand style reference
including colors, fonts, widget catalog, and spacing rules.

#### Dashboard Structure

**Hero Section:**
- Pill: "CONTENT STRATEGY · Serpstat"
- Title: "{domain} — Content Strategy"
- Subtitle: "Powered by Serpstat keyword data · {region} · {month} {year}"
- **Report description** (1-3 sentences below the subtitle, in normal body
  text style, explaining what this report contains and how to use it).
  Example: "This report analyzes {domain}'s current keyword footprint,
  expands it with related and long-tail queries, identifies content gaps
  vs {competitor}, and organizes everything into a prioritized content
  plan for the next 3-6 months. Use the tabs below to explore topics
  by priority, format, and timeline." Adapt the text to the actual
  analysis performed (e.g., omit competitor mention if none was used).
- Stats row: Total Topics, Total Keywords, Total Monthly Volume,
  Quick Wins count, Missed Competitor Topics count

**Tab 1 — Overview**
- Metric cards row: Topics by priority type (Quick Win, Strategic, Long-term, Optimize)
- Bar distribution: Topics by content format (Article, FAQ, Landing, etc.)
- Bar distribution: Topics by intent (Informational, Commercial, Transactional, Navigational)
- Coverage grid: Showing which topic clusters the domain already covers
  vs gaps

**Tab 2 — Content Plan**
- The core deliverable: a data table with ALL topics
- Columns: Priority (badge), Topic Name, Primary Keyword, Volume,
  KD (difficulty), Intent (badge), Format, Timeline (Month), Source
  (Current / Competitor Gap / Expansion)
- Sortable by any column
- Filterable by intent, format, priority type
- Each row expandable to show all supporting keywords with their
  individual volume and difficulty

**Tab 3 — Quick Wins**
- Action cards (Widget 6 from style guide) for the top 15 Quick Win topics
- Each card shows:
  - Priority badge (HIGH/MEDIUM) + type badge (QUICK WIN)
  - Topic title
  - Metrics: primary keyword volume, avg KD, keyword count
  - Expandable detail: all keywords in cluster, suggested format,
    competitor reference URL (if from gap data)
- Cards sorted by priority score descending

**Tab 4 — Competitor Gaps** (only if competitor was analyzed)
- Topics that came from competitor gap analysis (step 2e)
- Data table: Topic, Primary Keyword, Volume, Competitor Position,
  User Status (Missing / Weak), Suggested Format
- If no competitor was selected, show a helpful message: "No competitor
  was selected for this analysis. Re-run with a competitor to discover
  missed content opportunities."

**Tab 5 — FAQ & Questions**
- All question-style keywords extracted from step 2d
- Grouped by topic cluster
- These make excellent FAQ sections, "People Also Ask" targets,
  and featured snippet opportunities
- Data table: Question Keyword, Volume, Parent Topic, Difficulty

#### UX Requirements

- Every section needs a title + subtitle explaining what it shows
- All numbers in Roboto Mono
- All badges pill-shaped (border-radius: 20px)
- 48px gap between sections
- Sortable tables with click handlers
- Action cards with colored left border matching priority
- Dark footer: "Data: Serpstat API · get_domain_keywords, get_keywords,
  get_related_keywords, get_keyword_suggestions, get_domain_uniq_keywords
  · DB: {se}" + Serpstat logo SVG

#### File Naming

```
content-strategy_{domain}_{se}_{YYYY-MM-DD}.html
```

---

### Phase 5: Present and Summarize

After creating the dashboard, use `present_files` to share it with the user.

Provide a brief text summary:
1. Total topics found and how they break down by priority
2. Top 3 Quick Win topics (name, volume, difficulty)
3. Biggest competitor gap (if applicable)
4. Recommended first month focus
5. One strategic insight about the domain's content landscape

Keep it concise — the dashboard is the deliverable.

---

## Credit Budget

Estimated API credit usage per run:

| Step | Method | Credits |
|---|---|---|
| Region check | get_domain_regions_count | ~5 |
| Competitors | get_domain_competitors | ~6 |
| Domain keywords | get_domain_keywords | 100 |
| Keyword expansion (×5) | get_keywords | 250 |
| Related keywords (×5) | get_related_keywords | 250 |
| Long-tail (×5) | get_keyword_suggestions | 250 |
| Competitor gaps | get_domain_uniq_keywords | 100 |
| **Total (with competitor)** | | **~960** |
| **Total (without competitor)** | | **~860** |

To stay under 600 credits when budget is tight:
- Reduce seed keywords from 5 to 3
- Reduce size from 100/50 to 50/30
- Skip get_keyword_suggestions (least critical)

---

## Error Handling

- If Serpstat MCP is not connected: use `suggest_connectors`
- If domain returns 0 keywords: suggest checking the domain spelling or
  trying a different region
- If credits are insufficient: warn and stop (see Pre-flight Check)
- If a keyword expansion returns no results: skip it, note in summary
- If competitor gap returns empty: note "No unique competitor keywords found —
  domains likely target very different keyword sets"

---

## Example Invocations

**Simple:**
> "Build me a content plan for serpstat.com"

**With context:**
> "I run ahrefs.com. Create a content strategy for the US market,
> and show me what topics semrush.com covers that we don't."

**Casual:**
> "What should I write about on my blog? My site is mybrand.com"

**Localized:**
> "Контент-план для сайта example.com на украинском рынке"
