# Serpstat Report Style Guide

All reports are **standalone .html files** — CSS, JS, and data inline. No external libs except Google Fonts. Must work offline.

---

## Logo (inline SVG)

```html
<svg xmlns="http://www.w3.org/2000/svg" width="231px" height="26px" viewBox="0 0 231 26"><g fill="none" fill-rule="evenodd"><path fill="#004683" d="M51.1995 1.825c-1.4228.552-2.5232 1.3214-3.3025 2.31-.778.9908-1.1684 2.134-1.1684 3.4266 0 2.5984 1.5632 4.6413 4.685 6.1286.9612.4576 2.195.9363 3.7077 1.426 1.5114.4944 2.5692.9687 3.169 1.4296.603.4598.9013 1.1.9013 1.9248 0 .727-.2786 1.2914-.8357 1.6922-.558.4008-1.315.6023-2.277.6023-1.499 0-2.585-.297-3.252-.89-.669-.593-1.002-1.516-1.002-2.766H46c0 1.54.4006 2.9 1.1994 4.083.8 1.181 1.9926 2.112 3.5777 2.79 1.582.68 3.351 1.02 5.304 1.02 2.77 0 4.95-.581 6.538-1.747C64.208 22.086 65 20.481 65 18.439c0-2.555-1.3-4.562-3.905-6.015-1.0693-.599-2.437-1.159-4.105-1.6766-1.6632-.52-2.828-1.0207-3.4912-1.5084-.663-.4865-.9922-1.0297-.9922-1.6298 0-.6835.3005-1.248.9014-1.691.602-.4443 1.4204-.667 2.4586-.667 1.0258 0 1.8408.2628 2.4416.787.5998.5245.9026 1.2615.9026 2.2134H65c0-1.4208-.3787-2.6888-1.1407-3.794-.761-1.107-1.829-1.9596-3.2024-2.5586C59.281 1.2994 57.729 1 55.997 1c-1.775 0-3.375.275-4.7978.825M72 1v24h15.969v-4.4518H77.5884V14.813h8.7895v-4.2848h-8.79V5.4667H88V1m7 0v24h5.6817v-8.4884h3.0726L107.9153 25H114v-.2482l-4.9185-9.6906c1.4125-.6806 2.449-1.5682 3.1132-2.6663.663-1.099.995-2.483.995-4.15 0-2.306-.788-4.09-2.3626-5.352C109.252 1.631 107.068 1 104.275 1H95zm5.6817 4.4507h3.5926c1.067 0 1.8763.2928 2.4267.8842.5515.589.826 1.402.826 2.437s-.2712 1.841-.8182 2.419c-.5447.58-1.3562.869-2.4345.869h-3.5926V5.45zM120 1v24h5.818v-8.144h3.7172c2.9366 0 5.2495-.7002 6.9363-2.1002C138.156 13.3558 139 11.4833 139 9.143c0-1.583-.3886-2.998-1.1703-4.2448-.7794-1.248-1.8782-2.2088-3.2975-2.8836C133.1106 1.3374 131.4768 1 129.632 1H120zm5.818 4.4667h3.881c1.049.0114 1.878.3523 2.4857 1.0214.6076.672.911 1.566.911 2.686 0 1.024-.294 1.815-.8856 2.376-.592.561-1.451.841-2.578.841h-3.814V5.467z"/><path fill="#006EC2" d="M148.1957 1.825c-1.4226.552-2.522 1.3214-3.3 2.31-.7804.9908-1.1683 2.134-1.1683 3.4266 0 2.5984 1.5608 4.6413 4.6836 6.1286.96.4576 2.196.9363 3.7074 1.426 1.5113.4944 2.5668.9687 3.1688 1.4296.6008.4598.9024 1.1.9024 1.9248 0 .727-.2785 1.2914-.8356 1.6922-.5594.4008-1.3157.6023-2.2756.6023-1.501 0-2.583-.297-3.2528-.89-.6665-.593-1.0003-1.516-1.0003-2.766H143c0 1.54.4006 2.9 1.1994 4.083.801 1.181 1.9924 2.112 3.5762 2.79 1.5814.68 3.3494 1.02 5.3028 1.02 2.7694 0 4.9483-.581 6.5378-1.747C161.2058 22.086 162 20.481 162 18.439c0-2.555-1.303-4.5623-3.9054-6.015-1.0716-.599-2.4402-1.159-4.1046-1.6767-1.6667-.52-2.8315-1.0207-3.4922-1.5084-.662-.487-.9933-1.03-.9933-1.63 0-.684.2992-1.248.9012-1.691.6008-.445 1.4204-.667 2.4586-.667 1.0256 0 1.8382.263 2.4425.787.5985.524.9 1.261.9 2.213h5.791c0-1.421-.38-2.689-1.1408-3.794-.7608-1.107-1.829-1.96-3.2033-2.559S154.7267 1 152.9977 1c-1.7782 0-3.3793.275-4.802.825M166 1v4.4667h7.051V25h5.7676V5.4667H186V1m9.7594 0L187 25h6.1522l1.3783-4.4838h7.89L203.8158 25H210l-8.8267-24h-5.414zm2.7075 6.7246l2.576 8.325h-5.135l2.558-8.325zM211 1v4.4667h7.051V25h5.7676V5.4667H231V1"/><path fill="#004683" d="M36.25 0H12.8828l.0012.0056c-.3986.0157-.7187.34-.7187.742 0 .213.0907.4028.234.5386l-.0034.0034 5.058 5.0248c.294.293.294.77.001 1.063l-8.323 8.275c-.214.2144-.162.5758.104.7195l1.035.561c.19.103.488.082.674-.029.257-.154.556-.244.877-.244.22 0 .428.045.621.122.108.044.232.03.319-.049l1.975-1.79c-.002-.037-.011-.073-.011-.111 0-.954.771-1.728 1.722-1.728.801 0 1.469.55 1.661 1.293l1.915.815c.059.024.124.014.178-.022.276-.193.61-.309.971-.309.171 0 .337.028.493.076.0583.019.121.005.1657-.037l3.308-3.231c.048-.046.0725-.115.0714-.181-.029-1.697 1.2213-1.8937 1.7127-1.91.746-.028 1.373.487 1.6046 1.162.0238.064.072.117.137.14l2.0955.718c.0795.028.1635.0057.226-.0493.3047-.276.7065-.4454 1.1487-.4454.157 0 .43.099.589-.074l4.158-4.248c.0723-.073.113-.172.113-.2766V.752C37 .3367 36.664 0 36.25 0"/><path fill="#006EC2" d="M36.9876 10.2534c0-.2262-.2743-.3393-.4323-.1764l-2.6816 2.7377c-.0587.0588-.0813.1437-.0677.2263.0688.4502-.0226.9536-.6197 1.508-.3148.293-.7155.4944-1.1455.4842-.701-.0192-1.2912-.457-1.5485-1.0714-.027-.0644-.078-.1176-.1456-.1414l-2.2155-.7613c-.0768-.026-.159-.006-.2235.046-.298.242-.676.389-1.089.389-.1875 0-.3647-.037-.534-.093 0 0-2.528 2.452-3.4265 3.338-.1095.11-.1106.118-.0858.339 0 .962-.7765 1.741-1.737 1.741-.8114 0-1.4886-.559-1.6793-1.312-.02-.077-.07-.142-.143-.174l-1.645-.699c-.083-.037-.177-.019-.25.037-.291.223-.652.359-1.045.359-.187 0-.368-.03-.537-.087-.1-.033-.21-.016-.289.054l-2.002 1.814c-.068.061-.104.146-.114.237-.088.878-.824 1.564-1.724 1.564-.697 0-1.292-.414-1.568-1.009-.072-.156-.205-.274-.354-.356l-2.254-1.22c-.177-.096-.395-.063-.537.078L.223 24.71l-.0024.0023-.009.009.0024.001c-.131.136-.2133.319-.2133.523C0 25.6617.3363 26 .754 26H22.63c.4188 0 1.0045-.241 1.3025-.5362l.429-.4265.0123-.0125 5.134-5.0987c.297-.294.781-.294 1.078 0l5.1195 5.088c.1377.142.3273.231.5406.231.415 0 .754-.337.754-.753l-.013-14.237z"/></g></svg>
```

For dark backgrounds override fills: `.logo-white svg path { fill: #FFFFFF; }`

---

## Template Architecture

Every skill report is a **template .html file** stored in `references/`. Claude fills in only the `DATA` object — never rewrites CSS or JS.

```
references/
  template.html    ← full HTML with CSS + JS + widget renderers
  style-guide.md   ← this file
```

The template has this structure:

```html
<!DOCTYPE html>
<html><head>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&family=Roboto+Mono:wght@400;500;700&display=swap" rel="stylesheet">
  <style>/* all CSS here */</style>
</head>
<body>
  <script>
    // ===== Claude fills ONLY this object =====
    const DATA = {
      hero: { pill: "CONTENT STRATEGY REPORT", title: "...", subtitle: "...",
              stats: [{value: "42", label: "Topics"}, ...] },
      tabs: ["Overview", "Content Plan", "Quick Wins"],
      sections: [
        { tab: 0, type: "metric-row", items: [...] },
        { tab: 0, type: "bar-distribution", title: "...", items: [...] },
        { tab: 1, type: "data-table", columns: [...], rows: [...] },
        { tab: 2, type: "action-cards", items: [...] },
      ]
    };
  </script>
  <script>/* renderer JS — reads DATA, builds DOM */</script>
</body></html>
```

**Claude's job when using a template:** Copy the template, replace `DATA = {...}` with real data. Done.

**Claude's job when creating a new template for a skill:** Use the widget catalog below. Pick the widgets the skill needs, assemble the template, include the renderer JS for those widgets.

---

## Page Structure

```
┌──────────────────────────────────────────┐
│  HERO  (dark gradient)                   │
│  pill · H1 · subtitle · stat row         │
├──────────────────────────────────────────┤
│  TAB BAR  (sticky)                       │
├──────────────────────────────────────────┤
│  SECTIONS  (rendered per active tab)     │
│  48px gap between sections               │
│  Each section: title + subtitle + widget │
├──────────────────────────────────────────┤
│  FOOTER  (dark, logo + data source)      │
└──────────────────────────────────────────┘
```

---

## Fonts

```html
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&family=Roboto+Mono:wght@400;500;700&display=swap" rel="stylesheet">
```

| Role | Font | Weight | Size |
|---|---|---|---|
| Hero H1 | Roboto | 700 | 38px |
| Hero subtitle | Roboto | 400 | 15px |
| Hero stat number | Roboto Mono | 700 | 34px |
| Hero stat label | Roboto | 500 | 12px uppercase |
| Section title | Roboto | 700 | 20px |
| Section subtitle | Roboto | 400 | 13px |
| Card title | Roboto | 600 | 15px |
| Body text | Roboto | 400 | 14px |
| Table header | Roboto | 600 | 11px uppercase |
| Table cell | Roboto | 400 | 13px |
| Any number/metric | Roboto Mono | 600 | inherit size |
| Badge | Roboto | 600 | 11px uppercase |

---

## Color Palette

### Hero & Footer (dark zone)

```css
.hero { background: linear-gradient(135deg, #1A1B3A 0%, #0F172A 100%); color: #fff; }
/* subtitle: rgba(255,255,255,0.65) */
/* no divider line between subtitle and stats — use spacing only */
/* pill bg: rgba(44,97,237,0.25), text: #93C5FD, border: rgba(44,97,237,0.4) */
```

### Content body (light zone)

| Token | Value | Usage |
|---|---|---|
| Page bg | `#F7F9FD` | Body background |
| Card bg | `#FFFFFF` | All cards |
| Card border | `#E5E7EB` | Default border |
| Card shadow | `0 1px 4px rgba(0,0,0,0.05)` | Subtle lift |
| Hover bg | `#EEF2FA` | Table row hover |
| Primary text | `#24223C` | Headings |
| Secondary text | `#6B7280` | Descriptions, labels |
| Muted text | `#9CA3AF` | Footnotes |
| Accent | `#2C61ED` | Links, active states |
| Border | `#E5E7EB` | Dividers |

### Semantic colors

| Status | Background | Text | Use for |
|---|---|---|---|
| Success | `#D1FAE5` | `#065F46` | Competitive, strong, done |
| Warning | `#FEF3C7` | `#B45309` | Underperforming, medium |
| Danger | `#FEE2E2` | `#DC2626` | Missing, critical, high priority |
| Orange | `#FED7AA` | `#C2410C` | Huge gap |
| Info | `#DBEAFE` | `#1D4ED8` | Minor gap, info, low priority |
| Purple | `#EDE9FE` | `#6D28D9` | Quick win |

### Domain comparison colors

| Role | Color | Usage |
|---|---|---|
| User domain | `#2C61ED` | Bars, lines, labels |
| Competitor 1 | `#8B5CF6` | Purple |
| Competitor 2 | `#EC4899` | Pink |
| Competitor 3 | `#F59E0B` | Amber |
| Competitor 4 | `#10B981` | Green |

---

## Spacing & Sizing

| Element | Value |
|---|---|
| Max content width | `1200px` centered |
| Section gap (between sections) | `48px` — this is critical for visual breathing room |
| Section title → content gap | `24px` |
| Card padding | `24px` |
| Card border-radius | `10px` |
| Card gap (in grids) | `16px` |
| Badge border-radius | `20px` (always pill, never square) |
| Hero padding | `56px 48px 64px` |
| Tab bar height | ~`48px` |

---

## Widget Catalog

Below are all reusable widget types. Each skill template picks the ones it needs.

### Widget 1: Hero

Always present. Dark gradient, centered.

```html
<div class="hero">
  <div class="hero-pill">REPORT TYPE · Serpstat</div>
  <h1>domain.com — Report Title</h1>
  <p class="hero-subtitle">Powered by Serpstat keyword data · USA · March 2026</p>
  <div class="hero-stats">
    <div class="stat"><span class="stat-number">42</span><span class="stat-label">Topics</span></div>
    <div class="stat"><span class="stat-number">430+</span><span class="stat-label">Keywords</span></div>
    <!-- 3-5 stats -->
  </div>
</div>
```

```css
.hero { background: linear-gradient(135deg, #1A1B3A 0%, #0F172A 100%); padding: 56px 48px 64px; text-align: center; color: #fff; }
.hero-pill { display: inline-block; background: rgba(44,97,237,0.25); color: #93C5FD; border: 1px solid rgba(44,97,237,0.4); border-radius: 20px; padding: 6px 16px; font-size: 13px; font-weight: 500; margin-bottom: 20px; letter-spacing: 0.3px; }
.hero h1 { font-size: 38px; font-weight: 700; margin: 0 auto 12px; line-height: 1.2; text-align: center; max-width: 800px; }
.hero-subtitle { font-size: 15px; color: rgba(255,255,255,0.65); max-width: 600px; margin: 0 auto 40px; text-align: center; }
.hero-stats { display: flex; justify-content: center; gap: 48px; flex-wrap: wrap; margin-top: 40px; }
.stat { text-align: center; }
.stat-number { display: block; font-size: 34px; font-weight: 700; font-family: 'Roboto Mono', monospace; }
.stat-label { display: block; font-size: 12px; color: rgba(255,255,255,0.6); text-transform: uppercase; letter-spacing: 0.6px; margin-top: 4px; }
```

### Widget 2: Tab Bar

Sticky, horizontal scroll on mobile (scrollbar hidden).

```html
<div class="tabs-bar">
  <div class="tabs-bar-inner">
    <button class="tab-btn active" onclick="switchTab(0)">Overview</button>
    <button class="tab-btn" onclick="switchTab(1)">Content Plan</button>
    <button class="tab-btn" onclick="switchTab(2)">Quick Wins</button>
  </div>
</div>
```

```css
/* Outer bar: full-width sticky background */
.tabs-bar { position: sticky; top: 0; z-index: 100; background: #fff; border-bottom: 1px solid #E5E7EB; box-shadow: 0 2px 8px rgba(0,0,0,0.05); }
/* Inner wrapper: same max-width + padding as .section, so tabs align with content */
.tabs-bar-inner { max-width: 1200px; margin: 0 auto; padding: 0 32px; display: flex; overflow-x: auto;
  scrollbar-width: none;
  -ms-overflow-style: none; scrollbar-width: none; -ms-overflow-style: none; }
.tabs-bar-inner::-webkit-scrollbar { display: none; }
.tab-btn { padding: 14px 20px; font-size: 14px; font-weight: 500; color: #6B7280; border: none; background: none; cursor: pointer; border-bottom: 2px solid transparent; white-space: nowrap; font-family: 'Roboto', sans-serif; transition: color 0.15s; }
.tab-btn:hover { color: #24223C; }
.tab-btn.active { color: #2C61ED; border-bottom-color: #2C61ED; font-weight: 600; }
```

```js
function switchTab(i) {
  document.querySelectorAll('.tab-btn').forEach((b,idx) => b.classList.toggle('active', idx===i));
  document.querySelectorAll('.tab-panel').forEach((p,idx) => p.style.display = idx===i ? 'block' : 'none');
}
```

### Widget 3: Metric Cards Row

A row of 3-5 KPI cards. Used at top of overview tab.

**Key design rules:**
- Each card has a colored left border (4px) matching its sentiment
- Number is large and monospace
- Label above number (small, uppercase, muted)
- Description below number (small, secondary color)
- Cards are equal-width in a flex row

```html
<div class="metric-row">
  <div class="metric-card" style="border-left: 4px solid #2C61ED">
    <span class="metric-label">NEW TOPICS</span>
    <span class="metric-value">28</span>
    <span class="metric-desc">Content from scratch</span>
  </div>
  <!-- more cards -->
</div>
```

```css
.metric-row { display: flex; gap: 16px; flex-wrap: wrap; }
.metric-card { flex: 1; min-width: 160px; background: #fff; border: 1px solid #E5E7EB; border-radius: 10px; padding: 20px 24px; box-shadow: 0 1px 4px rgba(0,0,0,0.05); }
.metric-label { display: block; font-size: 11px; font-weight: 600; color: #6B7280; text-transform: uppercase; letter-spacing: 0.6px; margin-bottom: 8px; }
.metric-value { display: block; font-size: 32px; font-weight: 700; font-family: 'Roboto Mono', monospace; color: #24223C; line-height: 1; }
.metric-desc { display: block; font-size: 12px; color: #9CA3AF; margin-top: 6px; }
```

### Widget 4: Bar Distribution

Horizontal labeled bars showing distribution (e.g., topics by content type). Each bar shows: label on left, count badge, colored bar with percentage, value on right.

**Key design rules:**
- Bar track has `height: 12px`, rounded `6px`
- Bar fill color varies by row (cycle through domain colors or use a fixed palette)
- Count badge sits between label and bar
- Right-aligned stats: "18 topics · 43%"
- Rows are spaced `16px` apart

```html
<div class="bar-dist">
  <div class="bar-row">
    <span class="bar-label">Long-form articles</span>
    <span class="badge badge-info">18 topics</span>
    <div class="bar-track"><div class="bar-fill" style="width:43%; background:#2C61ED"></div></div>
    <span class="bar-stat">18 topics · 43%</span>
  </div>
  <!-- more rows -->
</div>
```

```css
.bar-dist { display: flex; flex-direction: column; gap: 16px; }
.bar-row { display: grid; grid-template-columns: 180px auto 1fr 140px; align-items: center; gap: 12px; }
.bar-label { font-size: 13px; font-weight: 500; color: #24223C; }
.bar-track { height: 12px; background: #E5E7EB; border-radius: 6px; overflow: hidden; min-width: 100px; }
.bar-fill { height: 100%; border-radius: 6px; transition: width 0.4s ease; }
.bar-stat { font-size: 12px; color: #6B7280; text-align: right; font-family: 'Roboto Mono', monospace; }
@media (max-width: 768px) { .bar-row { grid-template-columns: 1fr; } }
```

**Color cycle for bars:**
```js
const BAR_COLORS = ['#2C61ED', '#8B5CF6', '#EC4899', '#F59E0B', '#10B981', '#6366F1'];
```

### Widget 5: Data Table

Sortable table with hover. For keyword lists, page lists, competitor data.

**Key design rules:**
- Header row has light background `#F7F9FD`
- Columns with numbers right-aligned, monospace
- Sortable columns show `▲`/`▼` indicator
- Max 30 rows in the template; add "Show all" toggle if more
- Badge cells for status/severity

```html
<div class="table-wrapper">
  <table class="data-table">
    <thead><tr>
      <th onclick="sortTable(0)">Keyword ▼</th>
      <th onclick="sortTable(1)" class="num">Volume</th>
      <th onclick="sortTable(2)" class="num">KD</th>
      <th>Intent</th>
    </tr></thead>
    <tbody><!-- rendered by JS --></tbody>
  </table>
</div>
```

```css
.table-wrapper { overflow-x: auto; }
.data-table { width: 100%; border-collapse: collapse; font-size: 13px; }
.data-table th { font-size: 11px; font-weight: 600; color: #6B7280; text-transform: uppercase; letter-spacing: 0.6px; padding: 12px 16px; text-align: left; border-bottom: 2px solid #E5E7EB; cursor: pointer; user-select: none; background: #F7F9FD; }
.data-table th.num, .data-table td.num { text-align: right; font-family: 'Roboto Mono', monospace; }
.data-table td { padding: 12px 16px; border-bottom: 1px solid #F3F4F6; color: #24223C; }
.data-table tbody tr:hover { background: #EEF2FA; }
.data-table td a { color: #2C61ED; text-decoration: none; }
.data-table td a:hover { text-decoration: underline; }
```

### Widget 6: Action Cards (Quick Wins / To-Do)

Cards with priority badge, title, metrics, expandable detail. Used for action plans, quick wins, recommendations.

**Key design rules:**
- Left color bar (4px) matches priority: red=HIGH, amber=MEDIUM, blue=LOW
- Top-right: priority badge + effort badge side by side
- Title is bold, 15px
- Below title: 2-3 inline metrics in muted monospace (volume, position, traffic)
- Expandable detail section with target keywords and competitor reference
- Cards in a 2-column grid on desktop, 1-column on mobile

```html
<div class="action-grid">
  <div class="action-card" style="border-left: 4px solid #EF4444">
    <div class="action-header">
      <div>
        <span class="badge badge-danger">HIGH</span>
        <span class="badge badge-purple">QUICK WIN</span>
      </div>
    </div>
    <h3 class="action-title">Keyword Planner — tool comparison article</h3>
    <div class="action-metrics">
      <span>Vol <strong>12,100</strong></span>
      <span>KD <strong>59</strong></span>
      <span>Competitors: <strong>17</strong></span>
    </div>
    <div class="action-detail">
      <p>Target keywords: keyword planner, keyword research tool, free keyword tool</p>
      <p>Benchmark: competitor.com/blog/keyword-planner-guide</p>
    </div>
  </div>
  <!-- more cards -->
</div>
```

```css
.action-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
@media (max-width: 768px) { .action-grid { grid-template-columns: 1fr; } }
.action-card { background: #fff; border: 1px solid #E5E7EB; border-radius: 10px; padding: 20px 24px; box-shadow: 0 1px 4px rgba(0,0,0,0.05); }
.action-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 12px; }
.action-header .badge { margin-right: 6px; }
.action-title { font-size: 15px; font-weight: 600; color: #24223C; margin: 0 0 10px; line-height: 1.4; }
.action-metrics { display: flex; gap: 16px; font-size: 12px; color: #6B7280; margin-bottom: 12px; }
.action-metrics strong { font-family: 'Roboto Mono', monospace; color: #24223C; }
.action-detail { font-size: 13px; color: #6B7280; line-height: 1.6; padding-top: 12px; border-top: 1px solid #F3F4F6; }
```

### Widget 7: Coverage / Progress Grid

Shows coverage of categories with labeled progress bars and right-side annotation. Used for cluster coverage, audit progress, feature comparison.

**Key design rules:**
- Each row: category label (left, fixed width) → status badge → progress bar → annotation (right)
- Progress bar colors match status: green (Strong), blue (Partial), amber (Weak), red (Gap)
- Annotation is a short text in muted italic
- Rows spaced `14px`

```html
<div class="coverage-grid">
  <div class="coverage-row">
    <span class="coverage-label">Keyword Research</span>
    <span class="badge badge-warning">Partial</span>
    <div class="bar-track"><div class="bar-fill" style="width:65%; background:#F59E0B"></div></div>
    <span class="coverage-note">Has KD 60-70 cluster</span>
  </div>
  <!-- more rows -->
</div>
```

```css
.coverage-grid { display: flex; flex-direction: column; gap: 14px; }
.coverage-row { display: grid; grid-template-columns: 160px 90px 1fr 180px; align-items: center; gap: 12px; }
.coverage-label { font-size: 13px; font-weight: 500; color: #24223C; }
.coverage-note { font-size: 12px; color: #9CA3AF; font-style: italic; text-align: right; }
```

**Status → color mapping:**
| Status | Badge class | Bar color |
|---|---|---|
| Strong | `badge-competitive` | `#10B981` |
| Partial | `badge-minor-gap` | `#3B82F6` |
| Weak | `badge-underperform` | `#F59E0B` |
| Gap / Missing | `badge-missing` | `#EF4444` |

### Widget 8: Comparison Bars (Multi-domain)

Side-by-side bars comparing domains per category. Used in competitive analysis.

```html
<div class="compare-row">
  <span class="compare-label">Blog</span>
  <div class="compare-bars">
    <div class="compare-bar">
      <div class="bar-track"><div class="bar-fill" style="width:80%; background:#2C61ED"></div></div>
      <span class="compare-val">45K</span>
    </div>
    <div class="compare-bar">
      <div class="bar-track"><div class="bar-fill" style="width:55%; background:#8B5CF6"></div></div>
      <span class="compare-val">31K</span>
    </div>
  </div>
</div>
```

```css
.compare-row { display: grid; grid-template-columns: 140px 1fr; gap: 16px; align-items: center; margin-bottom: 20px; }
.compare-label { font-size: 13px; font-weight: 600; color: #24223C; }
.compare-bars { display: flex; flex-direction: column; gap: 6px; }
.compare-bar { display: flex; align-items: center; gap: 10px; }
.compare-val { font-size: 12px; font-family: 'Roboto Mono', monospace; color: #6B7280; min-width: 50px; }
```

---

## Badges

Always pill-shaped. Never square.

```css
.badge { display: inline-flex; align-items: center; padding: 3px 10px; border-radius: 20px; font-size: 11px; font-weight: 600; letter-spacing: 0.3px; text-transform: uppercase; }
.badge-missing      { background: #FEE2E2; color: #DC2626; }
.badge-huge-gap     { background: #FED7AA; color: #C2410C; }
.badge-underperform { background: #FEF3C7; color: #B45309; }
.badge-minor-gap    { background: #DBEAFE; color: #1D4ED8; }
.badge-competitive  { background: #D1FAE5; color: #065F46; }
.badge-danger       { background: #FEE2E2; color: #B91C1C; }
.badge-warning      { background: #FEF3C7; color: #92400E; }
.badge-info         { background: #DBEAFE; color: #1E40AF; }
.badge-purple       { background: #EDE9FE; color: #6D28D9; }
```

---

## Tooltips

```css
.tip { position: relative; cursor: help; }
.tip::after { content: attr(data-tip); position: absolute; bottom: calc(100% + 6px); left: 50%; transform: translateX(-50%); background: #1A1B3A; color: #fff; font-size: 12px; padding: 8px 12px; border-radius: 6px; max-width: 260px; line-height: 1.5; z-index: 200; box-shadow: 0 4px 12px rgba(0,0,0,0.2); display: none; white-space: normal; min-width: 180px; }
.tip:hover::after { display: block; }
```

---

## Section Wrapper

Every content section uses this wrapper. The `48px` padding between sections is critical for visual breathing room.

```css
.section { padding: 48px 32px 0; max-width: 1200px; margin: 0 auto; }
.section:last-child { padding-bottom: 48px; }
.section-title { font-size: 20px; font-weight: 700; color: #24223C; margin: 0 0 4px; }
.section-subtitle { font-size: 13px; color: #6B7280; margin: 0 0 24px; }
```

---

## Footer

```html
<footer class="report-footer">
  <span>Data: Serpstat API · get_domain_keywords, get_keywords · DB: g_us</span>
  <span class="logo-white"><!-- Serpstat SVG logo here --></span>
</footer>
```

```css
.report-footer { background: #1A1B3A; color: rgba(255,255,255,0.5); font-size: 12px; padding: 20px 32px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 8px; }
.logo-white svg path { fill: rgba(255,255,255,0.5); }
```

---

## General Rules

- **Charts:** CSS bars + inline SVG only. No chart libraries.
- **JS:** Vanilla only. Tab switching, table sorting, tooltip — all covered above.
- **Data:** `<script>const DATA = {...}</script>` at top. All rendering reads from DATA.
- **Max rows in tables:** 30 default. Add "Show all (N)" toggle for more.
- **Responsive:** Grid columns collapse at 768px. Tables get horizontal scroll.
- **No animation overkill:** Only `transition` on bar fills and hover states. No keyframe animations.

---

## Checklist

Before finalizing any report template:

- [ ] Dark gradient hero with pill + H1 + subtitle + 3-5 stat blocks
- [ ] Sticky tab bar (if multi-tab)
- [ ] 48px gap between sections (not cramped)
- [ ] All badges are pills (border-radius: 20px)
- [ ] All numbers/metrics in Roboto Mono
- [ ] Every section has title + subtitle explaining what it shows
- [ ] Metric cards have colored left border
- [ ] Action cards have priority + effort badges
- [ ] Bar fills have distinct colors (not all the same)
- [ ] Table headers are sortable with click handlers
- [ ] Dark footer with API methods + Serpstat logo
- [ ] DATA object at top, all rendering from JS
- [ ] File works offline, responsive at 768px+