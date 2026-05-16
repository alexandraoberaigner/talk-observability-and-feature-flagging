# AGENTS.md

## Repository purpose

Documentation-only repo for a conference talk on observability and feature flagging (OpenFeature + OpenTelemetry). There is **no application code, no build system, and no tests** in this repo. All content is markdown.

The companion **live-demo code** lives in a separate repo:
<https://github.com/alexandraoberaigner/opentelemetry-demo>, branch `feat/openfeature-talk-demo`.

## Structure

```
README.md                  — overview and links
docs/talk-foundation.md    — abstract, agenda, narrative hook
docs/research-notes.md     — research summaries (Dynatrace, Datadog, Statsig, OpenFeature)
docs/demo-spec.md          — three demo scenarios, flag definitions, implementation status
```

## Branching

- Single long-lived branch: `feat/talk-foundation` (also the default via `origin/HEAD`).

## Conventions

- Keep docs concise; use markdown links to external sources rather than duplicating content.
- When referencing demo implementation details, point to `docs/demo-spec.md` or the companion demo repo — do not add code files here.
