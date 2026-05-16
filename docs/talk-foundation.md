# Talk Foundation

- Length: 45min slot, including live demo, Q&A
- Audience: Cloud-native conference attendees. 
- Level: Novice
- Unique angle: The talk is not about the acquisitions themselves & should not be an advertisement in any way of either of these companies, but about the convergence they represent — and how OpenFeature + OpenTelemetry are the only vendor-neutral way to navigate that convergence. The talk uses the acquisitions as a narrative hook to explore this broader trend and its implications for practitioners.
- Speaker background: both maintainers of OpenFeature.
- Open-source: the talk is based on open-source standards and tools, we only use Dynatrace for parts of the demos.

## Abstract

> Observability and feature flagging are converging, reshaping how
> organisations deliver, monitor, and understand the technical and business
> impact of new features. Companies like Dynatrace and Datadog are making
> significant investments in feature flagging. This talk explores the
> technical and strategic drivers behind this trend and showcases practical
> examples.
>
> We will analyze the role of the open standards OpenFeature and
> OpenTelemetry, highlighting why OpenFeature contributed to defining the
> feature flagging semantic conventions in OpenTelemetry. The examples will
> include the use of various OpenFeature concepts, such as hooks and
> tracking, alongside OpenTelemetry tracing and metrics in combination with
> different frontend and backend technologies and observability platforms,
> demonstrating a variety of use cases.
>
> Join us to discover why observability companies invest in feature
> flagging — and how you can benefit.

## References

Use these references as foundational research for the talk. When in doubt, websearch for more information or ask.

- [Dynatrace acquires DevCycle](https://www.dynatrace.com/news/blog/dynatrace-acquires-devcycle-to-strengthen-feature-delivery/)
- [Datadog acquires Eppo](https://www.datadoghq.com/about/latest-news/press-releases/datadog-acquires-eppo-to-expand-its-ai/)
- [OpenFeature and OpenTelemetry SemConv (1)](https://openfeature.dev/blog/feature-observability-semantic-conventions/)
- [OpenFeature and OpenTelemetry SemConv (2)](https://opentelemetry.io/docs/specs/semconv/feature-flags/feature-flags-events/)
- [OpenFeature documentation](https://openfeature.dev/docs/reference/intro)
- [OpenTelemetry documentation](https://opentelemetry.io/docs/)


## Agenda (current draft)

1. **Current happenings** — Dynatrace acquires DevCycle, Datadog acquires
   Eppo. Blog posts and news framing.

2. **What is feature flagging?** What is OpenFeature? Architecture (client, providers, spec; diagram). Challenges. SDLC with feature flags.

3. **Why are observability companies investing in feature flagging?**
   - The feature-flag lifecycle (diagram as circle: creation > deployment > activation > observation > clean up > archive) is best understood with OpenTelemetry.
   - Understanding the impact of new features:
     - Releasing safely, troubleshooting / performance
       - demo 2 
     - Cost/quality experimentation → incident response
       - demo 3 (optional)
     - Business case → **tracking**
       - demo 1
   - Semantic conventions support this.

4. **This is why OpenFeature & OpenTelemetry are more than the sum of their parts.**
    - SemConv as joined-forces work.
    - why standards matter for observability.

## The narrative hook

The two big 2025–2026 acquisitions emphasize **different facets** of the
same convergence story in their press releases. Both vendors are investing
in the full convergence; they just foreground different slices in their
announcements.

> **Internal reference — not for slides.** The comparison below is
> background context only. The slides should focus on the convergence
> itself, not on which use case each company chose to lead with in
> marketing, and not on whether either press release mentions OpenFeature
> or OpenTelemetry.

| | Dynatrace / DevCycle | Datadog / Eppo |
|---|---|---|
| **Centre of gravity (press release framing)** | Release safety, progressive delivery, kill switches | Experimentation, measurement, product analytics |
| **Headline use cases (press release framing)** | Risk reduction, incident response, "health-driven feature control" | "Compare multiple [AI] models side-by-side, determine user engagement against cost tradeoffs" |

Both vendors are converging on **OpenFeature for the control plane**. The
telemetry side of that convergence — the correlation between flag
evaluations and traces, metrics, logs — only works because of the
**OpenTelemetry feature-flag semantic conventions**
(`feature_flag.key`, `feature_flag.result.variant`,
`feature_flag.provider_name`), a collaboration between the OpenFeature and
OpenTelemetry communities.

**The unique angle of this talk:** OpenFeature + OTel SemConv is the only
**vendor-neutral** path through this convergence — and that is what we
demonstrate live, to show how you can benefit regardless of which vendor
you choose.

## Mapping demos to pillars

All three demos run on the OpenTelemetry community demo (astronomy shop),
fork at <https://github.com/alexandraoberaigner/opentelemetry-demo>.

> **Stage order:** Demo 2 → Demo 3 → Demo 1. The demos keep their content
> labels (the numbers reflect flag/service identity), but on stage we open
> with release safety, move through the AI angle, and close with the
> business-impact AOV story.
>
> The **Status** column is for development tracking only — not for slides.

| Stage slot | Demo | Flag(s) · Service | Maps to acquisition | Pillar from agenda | Status |
|---|---|---|---|---|---|
| 1 (opener) | **Demo 2** — Product-catalog progressive rollout (canary) | `productCatalogCanary` + `productCatalogV2Severity` · `product-catalog` (Go) | Dynatrace / DevCycle | Releasing safely; observability without code | ✅ Implemented |
| 2 (middle) | **Demo 3** — Multi-model AI summary + kill switch | `productSummaryModel` · `llm` (Python) | Both — AI is the shared theme | Cost/quality experimentation → incident response | 🔜 Next PR |
| 3 (climax) | **Demo 1** — Recommendation algorithm A/B + AOV correlation | `recommendationAlgorithm` · `recommendation` (Python) | Datadog / Eppo | Business case — experimentation, **tracking** | ✅ Implemented |

Detailed scenario specs and slide suggestions: [demo-spec.md](demo-spec.md).  
Stage runbook and setup instructions: [demo-runbook.md](demo-runbook.md).

## The demo arc

The three demos build on each other to tell one coherent story. **The
infrastructure never changes** — only the flags do. Every span carries
`feature_flag.key` and `feature_flag.result.variant` because of one line:

```python
api.add_hooks([TracingHook()])         # Python
```
```go
openfeature.AddHooks(otelhooks.NewTracesHook())   // Go
```

**Stage slot 1 — Demo 2: Release safety (canary rollout).** Open with the
most concrete moment: a feature flag changing what real users see. Two
composed flags on the product-catalog service give full stage control:
`productCatalogCanary` (95/5 → 25% → 50% → 75%) controls *who* gets v2;
`productCatalogV2Severity` (`none` → `low` → `high` → `critical`) controls
*how bad* v2 is. v2 adds +50ms baseline latency; severity escalates errors
(0% → 15% → 40% → 75%). Step up rollout and severity, watch the yellow p95
line rise and red errors appear in the same Grafana row, then roll back to
5% + `none` and watch both panels recover in ~30s. The dashboard reads
`app.catalog.version` (set by the service) rather than
`feature_flag.result.variant` (set by the hook) to avoid contamination
from the severity flag evaluation on the same span.

This is also where we establish the **"telemetry for free"** lesson: one
line of code added the `feature_flag.evaluation` span event to every span.
Closing line: *"No deploy. No restart. The flag key was already on every
span."*

**Stage slot 2 — Demo 3: Multi-model AI + kill switch.** Same SemConv
attributes, now for cost/quality and incident response. Baseline on
`model-a`; flip to `model-b` and watch latency/cost shift per variant;
trigger `llmRateLimitError=on` and watch errors isolated to the `model-b`
cohort; flip the flag to `off` to kill the incident. *"Same hook, same
attributes — experimentation and incident response on one substrate."*

**Stage slot 3 (climax) — Demo 1: Recommendation A/B + AOV correlation.**
The business-impact closer. Variants `popularity` / `collaborative` /
`personalized` on the recommendation service. Premium users get
`personalized` via `EvaluationContext`; the rest are split 50/50 by
fractional targeting. The audience sees:

1. The `feature_flag.evaluation` span event in Jaeger — already familiar
   from the earlier demos, now driving experimentation rather than rollout
   or incident response.
2. Per-variant impressions and p95 latency in Grafana (`personalized` is
   visibly heavier).
3. AOV correlation in OpenSearch PPL: recommendation logs `app.user.id` +
   `app.recommendation.algorithm`; checkout logs `app.user.id` +
   `app.order.amount`; PPL joins them. Personalized drives ~5× larger
   baskets.
4. Live flip in flagd-ui — dashboard shifts in ~30s.

Closing line — and the closing line of the talk: *"Personalized
recommendations drive 5× larger baskets. The checkout service has no idea
the flag exists. One open standard. All use cases."*

## Suggested talk structure (45 min slot)

```
1. News hook (3 min)
   - Dynatrace buys DevCycle. Datadog buys Eppo. Two major observability
     vendors converging on feature flagging within months. Why now — and
     what does it mean for practitioners?

2. Background (8 min)
   - Feature flags in the SDLC; why observability matters.
   - OpenFeature: the open standard for flag evaluation (client,
     providers, spec). Architecture diagram.
   - The feature-flag lifecycle as a circle: create > deploy > activate >
     observe > clean up > archive. Observation is where OpenTelemetry
     plugs in.
   - OpenTelemetry: the open standard for telemetry (traces, metrics,
     logs). Vendor-neutral instrumentation, collector, and exporters —
     the substrate on which observability vendors build.
   - OpenTelemetry SemConv: feature_flag.key / feature_flag.result.variant /
     feature_flag.provider_name.
   - "These two communities collaborated on this. Let's see what that buys."

3. Demo — Canary rollout (6 min) — stage slot 1
   Maps to: release safety (Dynatrace framing).
   Content: Demo 2 in [demo-spec.md](demo-spec.md), runbook in [demo-runbook.md](demo-runbook.md).
   Baseline → 25% + low → 50% + high → 75% + critical → rollback.

4. Demo — Multi-model AI + kill switch (5 min, optional) — stage slot 2
   Maps to: cost/quality experimentation → incident response.
   Content: Demo 3 in [demo-spec.md](demo-spec.md).
   Flip model-a → model-b → error injection → kill switch.

5. Demo — Recommendation A/B + AOV (8 min, climax) — stage slot 3
   Maps to: experimentation / tracking (Datadog framing).
   Content: Demo 1 in [demo-spec.md](demo-spec.md), runbook in [demo-runbook.md](demo-runbook.md).
   Jaeger hook → per-variant Grafana → AOV in OpenSearch → live flip.

6. Synthesis (5 min)
   - OpenFeature + OTel SemConv = vendor-neutral path through the
     convergence.
   - Both Dynatrace and Datadog are converging on feature flagging; the
     open standards layer is what stays vendor-neutral underneath.
   - Why standards matter for observability.
   - What's next: autonomous rollback, SLO-driven flag control.

7. Q&A (10 min)
```

### Time-budget fallbacks for the demo block

If demos run long, drop **Demo 3 (the optional middle slot)** first.
Always keep Demo 2 (opener) and Demo 1 (climax).

| Total demo time | Cover |
|---|---|
| 4 min (tight) | Demo 2 only: baseline → 25% + low severity → rollback |
| 7 min | Demo 2 (full escalation + rollback) + Demo 1 (Jaeger hook + dashboard panels, skip AOV) |
| 9 min | Demo 2 + Demo 1 (full, including AOV table + live flip) |
| 10 min (full) | Demo 2 + Demo 3 + Demo 1 (all three) |
