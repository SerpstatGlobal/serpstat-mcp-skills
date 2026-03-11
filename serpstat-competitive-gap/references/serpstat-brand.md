# Serpstat Brand Tokens

Reference this file when building the gap analysis dashboard. The visual style
should match the design of serpstat.com/page/* — specifically the MCP and LLM
Brand Monitor product pages. These pages use a **dark hero + light content body**
layout with clean metric blocks and feature cards.

---

## Page Structure Pattern

The Serpstat product pages follow this layout pattern:

```
┌─────────────────────────────────────────────────────┐
│  HERO SECTION (dark navy gradient)                  │
│  • Tag pill ("Gap Analysis Report · by Serpstat")   │
│  • H1 headline (white, bold)                        │
│  • Subtitle (muted white)                           │
│  • Stat blocks row: 4 key numbers with labels       │
├─────────────────────────────────────────────────────┤
│  TAB BAR (sticky, white background, blue underline) │
├─────────────────────────────────────────────────────┤
│  CONTENT BODY (light background #F7F9FD)            │
│  • Section blocks with cards, tables, charts        │
├─────────────────────────────────────────────────────┤
│  FOOTER (dark navy, matches hero)                   │
└─────────────────────────────────────────────────────┘
```

---

## Color Palette

### Dark Hero Zone
| Token | Value | Usage |
|---|---|---|
| `--hero-bg-start` | `#1A1B3A` | Hero gradient start (deep navy) |
| `--hero-bg-end` | `#0F172A` | Hero gradient end (darker) |
| `--hero-text` | `#FFFFFF` | Primary text in hero |
| `--hero-text-muted` | `rgba(255,255,255,0.65)` | Subtitle, captions in hero |
| `--hero-divider` | `rgba(255,255,255,0.12)` | NOT used between subtitle and stats (use spacing only) |
| `--hero-pill-bg` | `rgba(44,97,237,0.25)` | Tag pill background |
| `--hero-pill-text` | `#93C5FD` | Tag pill text |
| `--hero-pill-border` | `rgba(44,97,237,0.4)` | Tag pill border |

### Light Content Zone
| Token | Value | Usage |
|---|---|---|
| `--bg-primary` | `#F7F9FD` | Page / section background |
| `--bg-card` | `#FFFFFF` | Card background |
| `--bg-hover` | `#EEF2FA` | Hover, selected rows |
| `--text-primary` | `#24223C` | Headings, primary text |
| `--text-secondary` | `#6B7280` | Descriptions |
| `--text-muted` | `#9CA3AF` | Footnotes |

### Brand Accent
| Token | Value | Usage |
|---|---|---|
| `--accent` | `#2C61ED` | CTA buttons, active tabs, links |
| `--accent-hover` | `#2351CC` | Hover state |
| `--accent-light` | `#DBEAFE` | Tinted backgrounds |
| `--border` | `#E5E7EB` | Card borders, dividers |

### Semantic Colors
| Token | Value | Usage |
|---|---|---|
| `--success` | `#10B981` | Competitive |
| `--success-light` | `#D1FAE5` | Success badge bg |
| `--warning` | `#F59E0B` | Underperforming |
| `--warning-light` | `#FEF3C7` | Warning badge bg |
| `--danger` | `#EF4444` | Missing / critical |
| `--danger-light` | `#FEE2E2` | Danger badge bg |
| `--orange` | `#EA580C` | Huge gap |
| `--orange-light` | `#FED7AA` | Huge gap badge bg |
| `--info` | `#3B82F6` | Minor gap / info |
| `--info-light` | `#DBEAFE` | Info badge bg |

---

## Typography

- **Font**: `'Roboto', sans-serif`
- **Import URL**: `https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&family=Roboto+Mono:wght@400;500;700&display=swap`
- **Hero H1**: 38–44px, weight 700, white, line-height 1.2
- **Section H2**: 22–26px, weight 700, `#24223C`
- **Card title H3**: 15px, weight 600, `#24223C`
- **Body text**: 14px, weight 400, `#24223C`
- **Caption**: 12–13px, weight 400, `#6B7280`
- **Hero stat number**: 32–36px, weight 700, `'Roboto Mono'`, white
- **Hero stat label**: 12px, `rgba(255,255,255,0.6)`, uppercase, letter-spacing 0.6px
- **Metric numbers in content**: `'Roboto Mono'`, weight 600

---

## Spacing

- Card padding: 20–24px
- Card gap: 16px
- Section vertical padding: 40–48px
- Border radius (cards): 10px
- Border radius (badges): 20px ← fully rounded pills, NOT square
- Border radius (buttons): 8px
- Max content width: 1200px, centered

---

## Hero Section CSS

```css
.hero {
  background: linear-gradient(135deg, #1A1B3A 0%, #0F172A 100%);
  padding: 56px 48px 64px;
  text-align: center;
  color: #fff;
}
.hero-pill {
  display: inline-block;
  background: rgba(44,97,237,0.25);
  color: #93C5FD;
  border: 1px solid rgba(44,97,237,0.4);
  border-radius: 20px;
  padding: 6px 16px;
  font-size: 13px;
  font-weight: 500;
  margin-bottom: 20px;
  letter-spacing: 0.3px;
}
.hero h1 {
  font-size: 38px;
  font-weight: 700;
  margin: 0 auto 12px; text-align: center; max-width: 800px;
  line-height: 1.2;
}
.hero-subtitle {
  font-size: 15px;
  color: rgba(255,255,255,0.65);
  margin: 0 0 40px;
}
.hero-stats {
  display: flex;
  justify-content: center;
  gap: 48px;
  flex-wrap: wrap;
  /* no border separator — use margin for spacing */
  margin-top: 40px;
  margin-top: 8px;
}
.stat-block { display: flex; flex-direction: column; align-items: center; gap: 4px; }
.stat-number {
  font-size: 34px;
  font-weight: 700;
  font-family: 'Roboto Mono', monospace;
  color: #FFFFFF;
}
.stat-label {
  font-size: 12px;
  color: rgba(255,255,255,0.60);
  text-transform: uppercase;
  letter-spacing: 0.6px;
}
```

---

## Sticky Tab Bar CSS

```css
.tabs-bar {
  position: sticky;
  top: 0;
  z-index: 100;
  background: #FFFFFF;
  border-bottom: 1px solid #E5E7EB;
  display: flex;
  padding: 0 32px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
  overflow-x: auto;
  scrollbar-width: none;
  -ms-overflow-style: none;
}
.tabs-bar-inner::-webkit-scrollbar { display: none; }
.tab-btn {
  padding: 14px 18px;
  font-size: 14px;
  font-weight: 500;
  color: #6B7280;
  border: none;
  background: none;
  cursor: pointer;
  border-bottom: 2px solid transparent;
  white-space: nowrap;
  transition: color 0.15s;
}
.tab-btn:hover { color: #24223C; }
.tab-btn.active {
  color: #2C61ED;
  border-bottom-color: #2C61ED;
  font-weight: 600;
}
```

---

## Cards CSS

```css
.card {
  background: #FFFFFF;
  border: 1px solid #E5E7EB;
  border-radius: 10px;
  padding: 20px 24px;
  box-shadow: 0 1px 4px rgba(0,0,0,0.05);
}
```

---

## Severity Badges (fully rounded pills)

```css
/* Always use border-radius: 20px for ALL badges */
.badge {
  display: inline-flex;
  align-items: center;
  padding: 3px 10px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 0.3px;
  border: 1px solid transparent;
  text-transform: uppercase;
}
.badge-missing      { background:#FEE2E2; color:#DC2626; border-color:#FECACA; }
.badge-huge-gap     { background:#FED7AA; color:#C2410C; border-color:#FDBA74; }
.badge-underperform { background:#FEF3C7; color:#B45309; border-color:#FDE68A; }
.badge-minor-gap    { background:#DBEAFE; color:#1D4ED8; border-color:#BFDBFE; }
.badge-competitive  { background:#D1FAE5; color:#065F46; border-color:#6EE7B7; }
.badge-high         { background:#FEE2E2; color:#B91C1C; }
.badge-medium       { background:#FEF3C7; color:#92400E; }
.badge-low          { background:#DBEAFE; color:#1E40AF; }
.badge-quick-win    { background:#D1FAE5; color:#065F46; }
.badge-large        { background:#FFE4E6; color:#9F1239; }
```

---

## Domain Colors

| Role | Color |
|---|---|
| User domain | `#2C61ED` (Serpstat blue) |
| Competitor 1 | `#8B5CF6` (purple) |
| Competitor 2 | `#EC4899` (pink) |

---

## Data Tables CSS

```css
.data-table { width: 100%; border-collapse: collapse; font-size: 13px; }
.data-table thead tr { background: #F7F9FD; }
.data-table th {
  font-size: 11px; font-weight: 600; color: #6B7280;
  text-transform: uppercase; letter-spacing: 0.6px;
  padding: 10px 14px; text-align: left;
  border-bottom: 2px solid #E5E7EB;
  cursor: pointer; user-select: none;
}
.data-table td { padding: 10px 14px; border-bottom: 1px solid #F3F4F6; }
.data-table tbody tr:hover { background: #F7F9FD; }
```

---

## Tooltip CSS

```css
.info-icon {
  display: inline-flex; align-items: center; justify-content: center;
  width: 15px; height: 15px; border-radius: 50%;
  background: #E5E7EB; color: #6B7280;
  font-size: 9px; font-weight: 700;
  cursor: help; margin-left: 4px; position: relative;
}
.info-icon:hover::after {
  content: attr(data-tip);
  position: absolute; bottom: calc(100% + 6px); left: 50%;
  transform: translateX(-50%);
  background: #1A1B3A; color: #fff;
  font-size: 12px; font-weight: 400;
  padding: 8px 12px; border-radius: 6px;
  white-space: normal; max-width: 260px; line-height: 1.5;
  z-index: 200; box-shadow: 0 4px 12px rgba(0,0,0,0.2);
  pointer-events: none; text-align: left; min-width: 180px;
}
```

---

## Content Section Layout

```css
.content-section { padding: 40px 32px; max-width: 1200px; margin: 0 auto; }
.section-header { margin-bottom: 24px; }
.section-title { font-size: 20px; font-weight: 700; color: #24223C; margin: 0 0 6px; }
.section-subtitle { font-size: 13px; color: #6B7280; margin: 0; }
```

---

## Dark Footer CSS

```css
.report-footer {
  background: #1A1B3A;
  color: rgba(255,255,255,0.50);
  font-size: 12px;
  text-align: center;
  padding: 20px 32px;
  border-top: 1px solid rgba(255,255,255,0.08);
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 8px;
}
```

Footer content:
- Left: `Data: Serpstat API · {method} · Database: {se_code}`
- Right: `serpstat.com`

---

## Overall Aesthetic Checklist

When building the report, verify:
- [ ] Hero has dark navy gradient background (not white/light)
- [ ] Hero shows 4 stat numbers (large, monospace, white)
- [ ] Hero has a pill/tag label at the top
- [ ] Tab bar is sticky and white with blue active underline
- [ ] All badges are fully rounded pills (border-radius: 20px), NOT square
- [ ] Section headers have a subtitle line
- [ ] Footer is dark navy, matches the hero
- [ ] Font is Roboto throughout
- [ ] Cards have subtle shadow and 10px radius
