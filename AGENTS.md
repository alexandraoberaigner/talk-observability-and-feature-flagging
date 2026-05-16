# AGENTS.md

## Repository purpose

Talk repo for a conference talk on observability and feature flagging (OpenFeature + OpenTelemetry). Contains research docs, talk preparation notes, and a **Slidev** slide deck.

The companion **live-demo code** lives in a separate repo:
<https://github.com/alexandraoberaigner/opentelemetry-demo>, branch `feat/openfeature-talk-demo`.
Do not add demo application code here — only slides and supporting docs.

## Structure

```
README.md                  — overview and links
docs/talk-foundation.md    — abstract, agenda, narrative hook
docs/research-notes.md     — research summaries (Dynatrace, Datadog, Statsig, OpenFeature)
docs/demo-spec.md          — three demo scenarios, flag definitions, implementation status
```

Slidev presentation will be added at the project root (typically `slides.md` + Slidev config).

## Branching

- Single long-lived branch: `feat/talk-foundation` (also the default via `origin/HEAD`).

## Conventions

- Keep docs concise; use markdown links to external sources rather than duplicating content.
- When referencing demo implementation details, point to `docs/demo-spec.md` or the companion demo repo.
