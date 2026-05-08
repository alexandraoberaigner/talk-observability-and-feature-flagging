# Demo Specification

The live portion of the talk runs on top of the **OpenTelemetry community
demo** (astronomy shop) — <https://github.com/open-telemetry/opentelemetry-demo>.
The demo already uses OpenFeature with the **flagd** provider and ships the
OpenTelemetry hook in the Go and Python services, so SemConv attributes
flow onto spans for free in those services.

A companion fork lives at
<https://github.com/alexandraoberaigner/opentelemetry-demo>, branch
`feat/openfeature-talk-demo`, which adds the three flags and supporting
documentation described below.

## Three scenarios

### 1. Recommendation algorithm A/B test (Datadog / Eppo pillar)

**Flag:** `recommendationAlgorithm`
**Type:** string
**Variants:** `popularity` (default) | `collaborative` | `personalized`
**Service:** `recommendation` (Python — already wired with OpenFeature +
`TracingHook`).

**OpenFeature concepts demonstrated:**
- **Evaluation context** carrying `userId`, `userTier`, `region`.
- **Targeting** — `personalized` only for `userTier=premium`, others split
  by fractional rollout.
- **Tracking API** — frontend records `add_to_cart` and
  `checkout_completed` with cart value/currency.
- **Hook** — the OTel `TracingHook` already emits SemConv attributes on
  spans automatically.

**OTel signals:**
- Spans on `recommendation.ListRecommendations` carry `feature_flag.key`,
  `feature_flag.variant`.
- Counters split by variant for impressions, click-through, conversion.

**On stage:** Grafana panel split by variant — conversion rate, AOV, p95
latency. The personalized variant lifts conversion +X% but adds latency.
Live demonstration of "is the new model actually making money?"

### 2. Product-catalog progressive rollout / canary (Dynatrace / DevCycle pillar)

**Flag:** `productCatalogCanary`
**Type:** string
**Variants:** `v1` (default) | `v2`, with **fractional rollout** in flagd
targeting (`5%` / `25%` / `50%` / `100%` step-up).
**Service:** `product-catalog` (Go — already wired with OpenFeature + Go
OTel TracesHook).

**OpenFeature concepts demonstrated:**
- **Provider:** flagd, server-side resolution.
- **Hooks (before/after/error)** — Go OTel TracesHook attaches
  `feature_flag.*` attributes per SemConv on every evaluation.
- **Targeting rules** — the demo shows the live JSON in flagd-ui changing.

**OTel signals:** filter spans by `feature_flag.variant=v2` — error/latency
spike isolated to the canary cohort, propagating up to checkout. Flip back
to `5%` and metrics recover live.

**On stage:** "no code change, no redeploy — the flag key is already on
every span, that's the SemConv payoff." Slide referencing Dynatrace's
"health-driven feature control" wording: today the flip is manual, the
natural extension is autonomous.

### 3. Multi-model AI summary (shared AI theme — both pillars)

**Flag:** `productSummaryModel`
**Type:** string
**Variants:** `off` (default) | `model-a` | `model-b`
**Service:** `llm` (Python — currently uses OpenFeature but is missing the
OTel `TracingHook`; the demo branch adds it).

**OpenFeature concepts demonstrated:**
- **Hooks** — adds the missing `TracingHook` so SemConv attributes appear
  on LLM spans.
- **Evaluation context** with `userTier=beta` for opt-in.
- **Tracking API** — `summary_helpful_clicked` event with engagement
  signal.

**OTel signals:**
- Per-variant metrics for **token cost**, **latency**, **error rate**.
- Spans carry `feature_flag.key` / `feature_flag.variant` as SemConv
  attributes.

**On stage closing beat:** mid-demo, flip `productSummaryModel=model-b`
together with the existing `llmRateLimitError` to simulate model-B
degrading. Errors visible per variant. Flip the flag back to `model-a` (or
`off`) — incident contained without redeploy. This single example covers
both Datadog's AI experimentation framing and Dynatrace's incident-response
framing.

## How the SemConv attributes end up everywhere

The OpenFeature OpenTelemetry contrib hooks are registered globally during
service startup:

```python
# Python (recommendation, llm)
api.add_hooks([TracingHook()])
```

```go
// Go (product-catalog)
openfeature.AddHooks(otelhooks.NewTracesHook())
```

After that, **every** flag evaluation in those services automatically
attaches SemConv-defined `feature_flag.*` attributes to the active span.
This is the "OpenFeature contributed SemConv to OpenTelemetry" point made
concrete.

## Implementation status

See the OTel demo branch `feat/openfeature-talk-demo`, file
`src/flagd/openfeature-talk-demo.md` for:

- Exact flag JSON definitions.
- Per-service code touch-points and remaining wiring.
- A "what to flip and what to look for" runbook for stage practice.

## Open questions / next steps for the talk

1. **Backend choice on stage** — Grafana ships with the demo and is the
   safest default; can also show Jaeger for raw spans. Decide whether to
   show a vendor backend (Dynatrace / Datadog) screenshot for the "see, it
   looks exactly the same with SemConv" point.
2. **Frontend tracking calls** — currently only conceptual; need to wire
   `client.track(...)` in the Next.js frontend (`src/frontend`) for the
   recommendation A/B example to actually feed Grafana panels.
3. **Targeting demo** — flagd-ui will be on stage. Decide whether to edit
   targeting rules live or pre-stage configurations and switch between
   them.
4. **Slide hand-off points** — three planned slide↔demo handoffs, one per
   example. Time-box each example to ~3 minutes.
