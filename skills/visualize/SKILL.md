---
name: visualize
description: "Generate self-contained HTML visualizations from documents, data, flows, system architectures, dashboards, timelines, and analysis content. Keywords: visualize, visualization, visual, diagram, flow, dashboard, timeline, infographic, slides, deck, html, report, chart, topology, network diagram, service mesh, microservices, infrastructure, kubernetes, nodes, edges."
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
| `topology` | Node-edge graphs — service mesh, infra, microservice dependencies, network | hero → SVG canvas (nodes + edges + clusters) → legend → notes |

**Selection heuristics**:
- Long text with explanation → `report`
- Core is steps or state changes → `flow` or `timeline`
- Core is difference or tradeoff → `comparison`
- Core is numbers or status → `dashboard`
- Core is narrative or presentation → `deck`
- Core is actual data with charts, distributions, or statistical patterns → `analysis`
- Core is nodes and edges (services, databases, network components, infra, K8s, microservices) → `topology`
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
- Horizontal timeline bar — stage breakdown per observation (e.g., user interaction vs system processing); label each segment centered below its midpoint; for segments too narrow to label inside, omit inner text and show the value below only; use a vertical dashed marker for reference baselines (e.g., p50)

**Marker convention**: observed values (specific transactions, incidents) are shown as vertical dashed lines with labeled callouts — never buried in prose.

**Horizontal timeline bar — label rules**:
- Each segment gets one centered sub-label below the bar: `{time}s · {pct}% {name}`
- Sub-label `left` = `(segmentStart + segmentWidth / 2) / MAX_S * 100%`, `transform: translateX(-50%)`
- Segments under ~5% of total width: omit inner text; sub-label below still shown
- Right column shows total time only (`~Xs`)

**Interpretation placement**: one short paragraph directly below each chart. Do not save all interpretation for the end.

## Topology Type — Additional Rules

Use `topology` when the subject is **nodes and their connections**: services, servers, databases, queues, load balancers, external APIs, or any network/infra components.

### SVG-only vs D3.js

| Condition | Approach |
|-----------|----------|
| ≤ 15 nodes, positions known or derivable | Vanilla SVG + JS |
| 15+ nodes, or no natural layout | D3.js force-directed |
| Interactive drill-down needed | D3.js |

### Data Structure

Define `topo` before rendering:

```js
const topo = {
  nodes: [
    // required: id, label, type, x, y
    // optional: sub (subtitle), status, state
    // state: 'planned' | 'disabled' | 'deprecated'  → dimmed + badge + hatching
    { id: 'api', label: 'API Gateway', sub: ':443', type: 'gateway', status: 'healthy', x: 400, y: 200 },
    { id: 'db',  label: 'PostgreSQL',  sub: 'v15',  type: 'database', status: 'healthy', x: 700, y: 200 },
    { id: 'ml',  label: 'ML Service',  sub: 'TBD',  type: 'service',  status: 'unknown', state: 'planned', x: 400, y: 400 },
  ],
  edges: [
    // required: from, to, type
    // optional: label
    // edges to/from planned nodes are automatically dimmed
    { from: 'api', to: 'db', type: 'sync', label: 'SQL' },
    { from: 'api', to: 'ml', type: 'async', label: 'infer' },
  ],
  clusters: [
    // bounds: { x, y, w, h } — explicit positioning (REQUIRED when nesting zones)
    // Without bounds, bbox is auto-computed from nodes — causes header overlap in nested zones
    // meta: short strings shown as chips in the zone header
    // state: 'planned' — dims the entire zone with hatching overlay
    { id: 'vpc', label: 'Production VPC', type: 'vpc', icon: '⬡',
      bounds: { x: 60, y: 80, w: 800, h: 520 },
      nodes: ['api', 'db', 'ml'], meta: ['10.0.0.0/16'] },
    { id: 'ns', label: 'ns: backend', type: 'namespace', icon: '⎈', parent: 'vpc',
      bounds: { x: 320, y: 140, w: 240, h: 360 },
      nodes: ['api', 'ml'], meta: ['k8s v1.29'] },
  ]
};
```

### Node Types → Shapes

| type | Shape | Border color |
|------|-------|--------------|
| `service` | rounded rect (rx 10) | `#68b6ff` (accent) |
| `gateway` | diamond | `#70e1cb` (accent-2) |
| `database` | SVG cylinder (ellipse + rect) | `#a87dff` |
| `cache` | rounded rect, dashed stroke | `#ffd072` |
| `queue` | rect + deco | `#ff9940` |
| `external` | hexagon | `#98abc9` |

### Edge Types → Styles

Default `stroke-width`: **`2`** (1.5 is too thin to read; hover highlight uses `2.5`)

| type | Stroke | Dash | Arrowhead |
|------|--------|------|-----------|
| `http` | `rgba(104,182,255,0.55)` | solid (no dash) | single end |
| `sync` | `rgba(104,182,255,0.45)` | `3,3` | single end |
| `async` | `rgba(112,225,203,0.55)` | `6,3` | single end |
| `bidirectional` | `rgba(104,182,255,0.65)` | solid (no dash) | both ends |

> **`http` / `bidirectional`** have no dasharray → `edge-flow` animation class is present but produces no visible movement (solid lines don't march). This is intentional — solid = synchronous call.

### Status Colors

`healthy` → `#52d988` · `degraded` → `#ffd072` · `down` → `#ff8c9f` · `unknown` → `#98abc9`

Render as a 4px filled circle at the top-right corner of each node.

### SVG Canvas Setup

```html
<style>
  /* edge flow animation — applied by default to all edges; each edge type's dasharray drives the visual */
  /* NEVER add stroke-dasharray here — it overrides http(solid)/sync/async type distinction */
  @keyframes flow { to { stroke-dashoffset: -20; } }
  .edge-flow { animation: flow 0.65s linear infinite; }
  /* smooth opacity transitions on all node groups */
  #topoG > g { transition: opacity 0.15s; }
</style>

<svg id="topoSvg" style="display:block;width:100%;height:560px;cursor:grab;"
     viewBox="0 0 1200 600" preserveAspectRatio="xMidYMid meet">
  <defs>
    <!-- normal arrowheads (one per edge type) -->
    <marker id="arr-http"  markerWidth="7" markerHeight="7" refX="6" refY="3.5" orient="auto"><path d="M0,0 L0,7 L7,3.5z" fill="rgba(104,182,255,0.75)"/></marker>
    <marker id="arr-async" markerWidth="7" markerHeight="7" refX="6" refY="3.5" orient="auto"><path d="M0,0 L0,7 L7,3.5z" fill="rgba(112,225,203,0.75)"/></marker>
    <!-- bright arrowheads shown only during hover highlight -->
    <marker id="arr-hi-out" markerWidth="8" markerHeight="8" refX="6" refY="4" orient="auto"><path d="M0,0 L0,8 L8,4z" fill="#68b6ff"/></marker>
    <marker id="arr-hi-in"  markerWidth="8" markerHeight="8" refX="6" refY="4" orient="auto"><path d="M0,0 L0,8 L8,4z" fill="#70e1cb"/></marker>
    <!-- node glow filter -->
    <filter id="node-glow" x="-40%" y="-40%" width="180%" height="180%">
      <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur"/>
      <feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge>
    </filter>
    <!-- planned/disabled hatching patterns -->
    <pattern id="hatch-planned"  width="10" height="10" patternUnits="userSpaceOnUse" patternTransform="rotate(45)">
      <line x1="0" y1="0" x2="0" y2="10" stroke="rgba(104,182,255,0.07)" stroke-width="4"/>
    </pattern>
    <pattern id="hatch-disabled" width="10" height="10" patternUnits="userSpaceOnUse" patternTransform="rotate(45)">
      <line x1="0" y1="0" x2="0" y2="10" stroke="rgba(152,171,201,0.06)" stroke-width="4"/>
    </pattern>
  </defs>
  <g id="topoG"></g>
</svg>
```

### Zoom / Pan (vanilla SVG)

```js
let vb = { x:0, y:0, w:1200, h:600 };
const svg = document.getElementById('topoSvg');
const upd = () => svg.setAttribute('viewBox', `${vb.x} ${vb.y} ${vb.w} ${vb.h}`);

svg.addEventListener('wheel', e => {
  e.preventDefault();
  const f = e.deltaY > 0 ? 1.12 : 0.89;
  const r = svg.getBoundingClientRect();
  const mx = (e.clientX - r.left) / r.width * vb.w + vb.x;
  const my = (e.clientY - r.top)  / r.height * vb.h + vb.y;
  vb.w = Math.min(2400, Math.max(400, vb.w * f));
  vb.h = Math.min(1400, Math.max(200, vb.h * f));
  vb.x = mx - (e.clientX - r.left) / r.width  * vb.w;
  vb.y = my - (e.clientY - r.top)  / r.height * vb.h;
  upd();
});

let drag = false, ds, vs;
svg.addEventListener('mousedown', e => { drag=true; ds={x:e.clientX,y:e.clientY}; vs={...vb}; });
window.addEventListener('mousemove', e => {
  if (!drag) return;
  const r = svg.getBoundingClientRect();
  vb.x = vs.x - (e.clientX - ds.x) / r.width  * vb.w;
  vb.y = vs.y - (e.clientY - ds.y) / r.height * vb.h;
  upd();
});
window.addEventListener('mouseup', () => { drag = false; });
```

### Auto-layout (when x/y not provided)

1. BFS from nodes with no incoming edges to assign layer depth
2. `x = layer * 240 + 120`, `y = (index_in_layer) * 160 + 80 + (layer % 2) * 80`
3. Adjust `viewBox` width/height to bounding box of all nodes + 80px padding

### Edge Path

Use cubic bezier for smooth curves:
```js
function edgePath(a, b) {
  const dx = b.x - a.x;
  return `M${a.x},${a.y} C${a.x + dx*0.45},${a.y} ${b.x - dx*0.45},${b.y} ${b.x},${b.y}`;
}
```
Shorten endpoints by ~NODE_W/2 so arrows don't overlap node bodies.

### Zone Header Strip

Each zone has a header band at the top of its bounding box:
- Height: `headerH` (26–30px depending on zone type)
- Top-rounded rect (same radius as zone body) clipped to header height
- Left: `icon` + `label` + meta chips (clipped to zone width — never overflow)
- Right: aggregate status dot (green/yellow/red based on member node statuses)
- Use `<clipPath>` to prevent label and chips from overflowing zone bounds

### Node / Zone State

| state | Rendering |
|-------|-----------|
| `planned` | opacity 0.42, dashed border, diagonal hatch fill, `PLANNED` pill badge above node |
| `disabled` | opacity 0.28, grey stroke, grey hatch fill, `INACTIVE` pill badge |
| `deprecated` | opacity 0.35, red-grey, `DEPRECATED` pill badge |

- Edges where either endpoint has `state` → auto-dimmed (opacity 0.35, stroke-dasharray `5,4`)
- Zone with `state: 'planned'` → entire `<g>` opacity 0.45 + hatching overlay + `PLANNED` chip in header

### Zone Nesting — Avoiding Header Overlap

**Always use explicit `bounds: { x, y, w, h }` when zones are nested** (any zone with `parent`).

Without explicit bounds, auto-computed bbox from node positions causes nested zone headers to stack within ~20px of each other — they overlap.

Header clearance rule:
```
depth 0 (region): bounds.y = 28
depth 1 (vpc):    bounds.y ≥ region.bounds.y + region.headerH + 30
depth 2 (ns):     bounds.y ≥ vpc.bounds.y    + vpc.headerH    + 30
```

### Zone Type System

| type | border dash | fill | header | typical use |
|------|-------------|------|--------|-------------|
| `region` | `16,6` | transparent | muted | AWS/GCP region |
| `vpc` | `8,4` | blue tint | blue | VPC / private network |
| `az` | `6,3` | yellow tint | yellow | Availability Zone |
| `subnet` | `4,2` | teal tint | teal | Public/private subnet |
| `namespace` | `4,4` | purple tint | purple | K8s namespace, logical group |
| `security-group` | `3,2` | red tint | red | Firewall / security boundary |

### Cluster Rendering

Render clusters **before** nodes (outermost depth first, so nodes appear on top):
1. Sort by depth: no parent = 0, each level +1
2. For each zone: body rect → hatch overlay (if planned) → header strip → label → chips → status dot
3. Use `<clipPath>` per zone for label + chips — never let text overflow zone bounds

### Legend

Place below the SVG canvas. Show: node type color swatches + edge line samples + status dots + zone type borders.

### Hover Interaction (Focus + Dim)

When a node is hovered, highlight its connections and dim everything else.

**Required setup — store refs during rendering:**

```js
const nodeElems = {};  // nodeId → SVG <g>
const edgeElems = [];  // { from, to, path, labelEl, origMarker, origDash }

// After building each node <g>:
nodeElems[n.id] = g;

// After building each edge <path>:
edgeElems.push({ from: e.from, to: e.to, path, labelEl,
  origMarker: `url(#arr-${e.type})`, origDash: dash || null, origStroke: s.stroke });
// origStroke is required — resetHighlight must restore the attribute, not clear it to ''
```

**Highlight function:**

```js
function highlightNode(nodeId) {
  const conns  = topo.edges.filter(e => e.from === nodeId || e.to === nodeId);
  const connIds = new Set(conns.flatMap(e => [e.from, e.to]));

  Object.entries(nodeElems).forEach(([id, el]) => {
    if      (id === nodeId)    { el.style.opacity = '1';    el.style.filter = 'url(#node-glow)'; }
    else if (connIds.has(id))  { el.style.opacity = '0.85'; el.style.filter = ''; }
    else                       { el.style.opacity = '0.1';  el.style.filter = ''; }
  });

  edgeElems.forEach(ee => {
    const conn = conns.find(e => e.from === ee.from && e.to === ee.to);
    if (conn) {
      const isOut = ee.from === nodeId;
      ee.path.style.stroke      = isOut ? 'rgba(104,182,255,0.95)' : 'rgba(112,225,203,0.95)';
      ee.path.style.strokeWidth = '2.5';
      ee.path.style.opacity     = '1';
      ee.path.setAttribute('marker-end', `url(#arr-hi-${isOut ? 'out' : 'in'})`);
      ee.path.classList.add('edge-flow');                          // flowing dash animation
      if (ee.labelEl) { ee.labelEl.style.opacity = '1';
                        ee.labelEl.style.fill = isOut ? '#68b6ff' : '#70e1cb'; }
    } else {
      ee.path.style.opacity = '0.05';
      ee.path.classList.remove('edge-flow');
      if (ee.labelEl) ee.labelEl.style.opacity = '0';
    }
  });
}

function resetHighlight() {
  Object.values(nodeElems).forEach(el => { el.style.opacity = ''; el.style.filter = ''; });
  edgeElems.forEach(ee => {
    // restore origStroke via setAttribute — never use setAttribute('stroke','') which makes edges invisible
    ee.path.setAttribute('stroke', ee.origStroke);
    ee.path.style.strokeWidth = ''; ee.path.style.opacity = '';
    ee.path.setAttribute('marker-end', ee.origMarker);
    ee.path.classList.add('edge-flow');   // keep animation — do NOT remove on reset
    if (ee.origDash) ee.path.setAttribute('stroke-dasharray', ee.origDash);
    else ee.path.removeAttribute('stroke-dasharray');
    if (ee.labelEl) { ee.labelEl.style.opacity = ''; ee.labelEl.style.fill = ''; }
  });
}

// Reset on canvas background click
svg.addEventListener('click', e => { if (e.target === svg || e.target.id === 'topoG') resetHighlight(); });

// Apply edge-flow to all edges by default after render — sync/async edges animate, http(solid) edges stay solid
// (flow animation only has a visible effect when the edge has a stroke-dasharray)
renderTopo();
edgeElems.forEach(ee => ee.path.classList.add('edge-flow'));
```

**Hover behaviour summary:**

| Element | Default state | On hover (connected) | Not connected |
|---------|--------------|----------------------|---------------|
| Hovered node | — | opacity 1 + glow filter | — |
| Connected nodes | — | opacity 0.85 | opacity 0.1 |
| Outgoing edge | origStroke + flow anim (if dashed) | blue `#68b6ff`, `arr-hi-out`, flow anim | opacity 0.05 |
| Incoming edge | origStroke + flow anim (if dashed) | teal `#70e1cb`, `arr-hi-in`, flow anim | opacity 0.05 |
| Edge labels | opacity 0.6 | opacity 1, colored | opacity 0 |

**Directional tooltip content:**

```js
const out = topo.edges.filter(e => e.from === n.id)
  .map(e => `→ ${target.label} (${e.label})`);   // blue arrow = outgoing
const inc = topo.edges.filter(e => e.to   === n.id)
  .map(e => `← ${source.label} (${e.label})`);   // teal arrow = incoming
```

Skip `state` nodes in connection lists (planned edges are not interactive).

### D3.js Force-Directed (15+ nodes)

```js
// Load: <script src="https://cdnjs.cloudflare.com/ajax/libs/d3/7.9.0/d3.min.js"></script>
const sim = d3.forceSimulation(topo.nodes)
  .force('link', d3.forceLink(topo.edges).id(d => d.id).distance(160))
  .force('charge', d3.forceManyBody().strength(-400))
  .force('center', d3.forceCenter(width / 2, height / 2))
  .force('collision', d3.forceCollide(60));

sim.on('tick', () => {
  // update SVG element positions each tick
  edgePaths.attr('d', d => edgePath(d.source, d.target));
  nodeGroups.attr('transform', d => `translate(${d.x},${d.y})`);
});
```

Use `simulation.stop()` + `simulation.tick(300)` for static render (no animation).

### Reference Implementation

See `topology-skeleton.html` in this skill directory for a complete working example with all node types, edge styles, zoom/pan, legend, and tooltip.

## Libraries

Vanilla HTML/CSS/JS by default. Add libraries only when genuinely needed:
- `Chart.js` — default for `analysis` type; optional for other types
- `Mermaid` — only for quick flowcharts or sequences
- `Reveal.js` — only for deck type with explicit slide navigation
- `D3.js` — for `topology` type when 15+ nodes or positions are unknown (force-directed layout); load via CDN `d3.v7.min.js`

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
