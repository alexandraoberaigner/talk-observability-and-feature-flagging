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

## Slides Guidelines

- Use Slidev for slides. there is a skill definition in `.agents/skills/slidev/SKILL.md` with more details.
- Don't make up facts. Webseach information or ask. Add references to the slide as footnote.
- Zero fluff. Focus on the unique narrative hook of the talk and the value it provides to the audience.
- Avoid AI sounding language; No empty buzzwords or generic statements. No em-dashes or parentheticals. Write like a human. Keep it concise and to the point. No over the top adjectives or adverbs. Use emojis with care.
- Websearch openfeature.dev for OpenFeature-specific information, and opentelemetry.io for OpenTelemetry-specific information.
- Don't mention the acquisitions without framing them in the narrative hook. The talk is not about the acquisitions, but about the convergence they represent — and how OpenFeature + OpenTelemetry are the only vendor-neutral way to navigate that convergence.
- Include live code examples, diagrams, and animations where relevant.
