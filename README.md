# claude-visualize

A Claude Code plugin that converts documents and data into readable, self-contained HTML visualizations.

[한국어](README.ko.md) · [Live Demo](https://seokrae.github.io/claude-visualize/examples/)

## What It Does

Give it any document, data set, or analysis — it produces a single `.html` file with a polished dark-theme layout. No external dependencies required in the output; the HTML is fully self-contained.

## Supported Types

| Type | Best For |
|------|----------|
| `report` | Long analysis, technical review, step documents |
| `flow` | Process flows, state transitions, system architecture |
| `comparison` | Before/after, option tradeoffs, old vs new structure |
| `timeline` | Sequential steps, rollout schedules, incident progression |
| `dashboard` | KPIs, operational status, snapshot summaries |
| `deck` | Presentation slides, storytelling |

## Installation

### Add as a marketplace

```
/plugins marketplace add SeokRae/claude-visualize
```

### Install the plugin

```
/plugins install claude-visualize@claude-visualize
```

## Examples

Live samples for each visualization type — open in any browser.

| File | Type | Preview |
|------|------|---------|
| [`examples/index.html`](examples/index.html) · [↗ Live](https://seokrae.github.io/claude-visualize/examples/) | — | Gallery index — all 6 types in one page |
| [`examples/report.html`](examples/report.html) | `report` | API migration analysis with summary, background, risks, and next actions |
| [`examples/flow.html`](examples/flow.html) | `flow` | End-to-end payment process from checkout to settlement |
| [`examples/comparison.html`](examples/comparison.html) | `comparison` | Session-cookie vs JWT auth architecture — metrics and recommendation |
| [`examples/timeline.html`](examples/timeline.html) | `timeline` | 6-phase stablecoin service launch rollout with status tracking |
| [`examples/dashboard.html`](examples/dashboard.html) | `dashboard` | Real-time payment system KPIs, service health, and recent alerts |
| [`examples/deck.html`](examples/deck.html) | `deck` | Q2 engineering strategy presentation — 6 slides |

## Usage Examples

```
visualize this document as a flow diagram
create a timeline from these steps
turn this analysis into an HTML report
make a dashboard from these metrics
```

## Design Principles

- Readability over flashiness
- Information hierarchy over card count
- Core message over decoration
- Vanilla HTML/CSS/JS by default — external libraries only when genuinely needed

## License

MIT
