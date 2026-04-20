---
name: visualize
description: "Generate self-contained HTML visualizations from documents, data, flows, system architectures, dashboards, timelines, and analysis content. Keywords: visualize, visualization, visual, diagram, flow, dashboard, timeline, infographic, slides, deck, html, report, chart."
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

# Visualize

Convert any document or structured data into a readable, self-contained single HTML file that people can understand at a glance.

## Use When

- A document needs to be restructured as a flow, comparison view, timeline, or presentation
- Step documents or analysis notes need to be embedded as standalone HTML
- Text is correct but hard to read — information structure needs redesign
- KPIs, summaries, before/after, system layouts, or process flows need visual treatment

## Output Rules

1. Default output is a single `*.html` file.
2. Place the file in the current working directory or a user-specified location.
3. Always include the absolute path of the created/modified HTML in the response.
4. Do not auto-open in a browser unless the user requests it or it is safe to do so.

## Workflow

1. Read the input source and summarize the visualization purpose in one sentence.
2. Select one type from **Type Selection** below.
3. Decide: new file or improve an existing HTML.
4. Start from the **HTML Skeleton** below; strip unnecessary sections.
5. Apply the **Design Rules** below — hierarchy, density, color, responsive.
6. Review for readability, mobile width (375px), and scannability before writing.

## Type Selection

| Type | Use When | Structure |
|------|----------|-----------|
| `report` | Long analysis, tech review, step documents (default) | hero → summary → body sections → risks/actions |
| `flow` | Process, state transitions, system architecture | hero → steps/nodes → connections → notes |
| `comparison` | Before/after, option tradeoffs, old vs new | hero → comparison axis → side-by-side or stacked → recommendation |
| `timeline` | Sequential steps, rollout, incident progression | hero → sequence → milestone details → risk/decision |
| `dashboard` | KPIs, operational status, snapshot summary | hero → KPI row → supporting cards → notes |
| `deck` | Presentation, storytelling, section-driven narrative | title slide → section slides → closing |
| `analysis` | Data-driven investigation — distribution, density, statistical patterns | hero → data context → chart sections → interpretation → synthesis |

**Selection heuristics**:
- Long text with explanation → `report`
- Core is steps or state changes → `flow` or `timeline`
- Core is difference or tradeoff → `comparison`
- Core is numbers or status → `dashboard`
- Core is narrative or presentation → `deck`
- Core is actual data with charts, distributions, or statistical patterns → `analysis`
- When unsure, use `report` and mix in a `flow` or `comparison` section.

Don't mix three or more types. Pick one primary type and add secondary patterns sparingly.

## Design Rules

**Typography**: Inter primary, Noto Sans KR for CJK. `h1` starts at `clamp(2.25rem, 5vw, 3.75rem)`. Body is `15px` or `16px` — never default to `14px` for descriptive text.

**Layout density**: Max 2-col for long text cards, 3-col max overall. 4-col only for short KPIs, status pills, or number cards.

**Information hierarchy**: One question per section. Section head = title + 1-sentence description. Show the most important line before cards, not inside them.

**Color**: Dark background with sufficient text contrast first. Use 1–2 accent colors. `danger` and `warning` only when meaningful. No decorative gradient text, neon glow, or rainbow borders.

**Responsive**: No horizontal scroll at 375px. Collapse 2+ col grids to 1-col on mobile. Don't force a high-density desktop layout onto small screens.

**Interaction**: Only add filter/tab/toggle if it aids understanding. Core information must be visible in the default state.

## Analysis Type — Additional Rules

Use `analysis` when the core deliverable is data-driven charts with statistical interpretation, not prose or process flows.

**Structure**:
1. `hero` — data source, period, and the question being answered
2. Data context section — observation cards (key values, what was measured)
3. Chart sections — one question per section, chart first then interpretation below
4. Interpretation section — what the data shows, anomalies, notable patterns
5. Synthesis section — 2–4 cards summarizing the conclusion per axis

**Chart patterns** (use Chart.js for this type by default):
- Distribution bar — frequency counts per bucket
- Line + dual-axis — compare two scales (e.g., count vs percentage)
- Density curve — log-normal or KDE fit with observed-value vertical markers
- Stacked bar — composition across categories
- Data table — raw values alongside charts when precision matters

**Marker convention**: observed values (specific transactions, incidents) are shown as vertical dashed lines with labeled callouts — never buried in prose.

**Interpretation placement**: one short paragraph directly below each chart. Do not save all interpretation for the end.

## Libraries

Vanilla HTML/CSS/JS by default. Add libraries only when genuinely needed:
- `Chart.js` — default for `analysis` type; optional for other types
- `Mermaid` — only for quick flowcharts or sequences
- `Reveal.js` — only for deck type with explicit slide navigation

Do not preload libraries when the task can be accomplished without them.

## HTML Skeleton

Start from this and strip sections that aren't needed.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Document Title</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Noto+Sans+KR:wght@400;500;700&display=swap" rel="stylesheet" />
  <style>
    :root {
      color-scheme: dark;
      --bg: #08111f;
      --surface: rgba(12, 21, 36, 0.92);
      --surface-strong: rgba(16, 28, 46, 0.98);
      --border: rgba(153, 186, 255, 0.14);
      --border-strong: rgba(153, 186, 255, 0.28);
      --text: #edf3ff;
      --text-muted: #98abc9;
      --accent: #68b6ff;
      --accent-2: #70e1cb;
      --danger: #ff8c9f;
      --warning: #ffd072;
      --radius-xl: 28px;
      --radius-lg: 20px;
      --radius-md: 14px;
      --shadow: 0 24px 72px rgba(0, 0, 0, 0.34);
      --font-sans: "Inter", "Noto Sans KR", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      --font-mono: "SFMono-Regular", "JetBrains Mono", "Consolas", monospace;
      --max: 1240px;
    }

    * { box-sizing: border-box; }

    html, body {
      margin: 0;
      min-height: 100%;
      background: #07101b;
    }

    body {
      font-family: var(--font-sans);
      color: var(--text);
      background:
        radial-gradient(circle at top left, rgba(104, 182, 255, 0.18), transparent 28%),
        linear-gradient(180deg, #091320 0%, #07101b 42%, #050a12 100%);
      line-height: 1.65;
      letter-spacing: -0.014em;
      -webkit-font-smoothing: antialiased;
    }

    .page {
      width: min(var(--max), calc(100% - 32px));
      margin: 0 auto;
      padding: 28px 0 56px;
    }

    main { display: grid; gap: 18px; }

    .hero, .section, .card {
      border: 1px solid var(--border);
      background: var(--surface);
      box-shadow: var(--shadow);
    }

    .hero, .section { border-radius: var(--radius-xl); }

    .hero {
      padding: 32px;
      margin-bottom: 4px;
      background: linear-gradient(180deg, rgba(15, 27, 44, 0.98), rgba(8, 15, 28, 0.94));
    }

    .eyebrow {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      padding: 8px 12px;
      border-radius: 999px;
      border: 1px solid rgba(104, 182, 255, 0.2);
      background: rgba(104, 182, 255, 0.08);
      color: #c2ddff;
      font-size: 12px;
      font-weight: 700;
      letter-spacing: 0.08em;
      text-transform: uppercase;
    }

    h1, h2, h3 { margin: 0; line-height: 1.08; letter-spacing: -0.03em; text-wrap: balance; }
    h1 { margin-top: 16px; font-size: clamp(2.25rem, 5vw, 3.75rem); }
    h2 { font-size: clamp(1.6rem, 3vw, 2.3rem); }
    h3 { font-size: clamp(1.15rem, 2vw, 1.45rem); }

    p { margin: 0; color: var(--text-muted); max-width: 74ch; font-size: 15px; }

    .section { padding: 24px; }

    .section-head {
      display: flex;
      justify-content: space-between;
      align-items: end;
      gap: 16px;
      margin-bottom: 18px;
    }

    .section-head p { margin-top: 10px; }

    .grid { display: grid; gap: 16px; }
    .grid.two { grid-template-columns: repeat(2, minmax(0, 1fr)); }
    .grid.three { grid-template-columns: repeat(3, minmax(0, 1fr)); }

    .card {
      border-radius: var(--radius-lg);
      padding: 20px;
      background: rgba(255, 255, 255, 0.03);
    }

    .kpi {
      display: block;
      font-size: clamp(1.6rem, 3vw, 2.4rem);
      font-weight: 800;
      letter-spacing: -0.04em;
      margin-bottom: 8px;
    }

    .chip-row { display: flex; flex-wrap: wrap; gap: 8px; }

    .chip {
      display: inline-flex;
      align-items: center;
      padding: 6px 10px;
      border-radius: 999px;
      border: 1px solid var(--border);
      background: rgba(255, 255, 255, 0.04);
      font-size: 12px;
    }

    @media (max-width: 900px) {
      .grid.two, .grid.three { grid-template-columns: 1fr; }
      .section-head { display: block; }
    }

    @media (max-width: 375px) {
      body { overflow-x: hidden; }
      .page { width: calc(100% - 20px); }
      .hero, .section { padding: 18px; }
      p { font-size: 14px; }
    }

    @media print {
      body { background: #fff; color: #111; }
      .hero, .section, .card { box-shadow: none; background: #fff; border-color: #ddd; }
    }
  </style>
</head>
<body>
  <main class="page">
    <header class="hero">
      <span class="eyebrow">Category</span>
      <h1>Page Title</h1>
      <p>One sentence describing what this visualization shows.</p>
    </header>

    <section class="section">
      <div class="section-head">
        <div>
          <h2>Section Title</h2>
          <p>Brief description of what this section shows.</p>
        </div>
      </div>
      <div class="grid two">
        <article class="card">
          <span class="kpi">01</span>
          <p>Key point</p>
        </article>
        <article class="card">
          <span class="kpi">02</span>
          <p>Key point</p>
        </article>
      </div>
    </section>
  </main>
</body>
</html>
```

**Skeleton notes**:
- This is a starting point only. Add menus, theme toggles, or download buttons only when needed.
- For descriptive documents, keep hero + report sections and reduce card count.
- Long explanatory text reads better as paragraphs and lists than as cards.

## Review Checklist

Before finalizing, verify:
- [ ] First screen immediately shows what this document is about
- [ ] Heading, description, and detail text have clear size contrast
- [ ] Items of the same importance have the same visual weight
- [ ] Reducing the card count would not improve readability
- [ ] No horizontal scrolling required to read a paragraph on mobile
