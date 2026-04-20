# claude-visualize

A Claude Code plugin that converts documents and data into readable, self-contained HTML visualizations.

[한국어](README.ko.md)

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
