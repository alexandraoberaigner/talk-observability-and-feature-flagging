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
   - The feature-flag lifecycle is best understood with OpenTelemetry.
   - Understanding the impact of new features:
     - Business case → **tracking**
     - Troubleshooting / performance
     - Releasing safely
   - Semantic conventions support this.
   - **2–3 examples** (the live demo).
4. **Why OpenFeature & OpenTelemetry are more than the sum of their parts.**
   - SemConv as joined-forces work.
   - What is OpenFeature, and why standards matter for observability.

## The narrative hook

The two big 2025–2026 acquisitions tell **different halves** of the same
convergence story:

| | Dynatrace / DevCycle | Datadog / Eppo |
|---|---|---|
| **Centre of gravity** | Release safety, progressive delivery, kill switches | Experimentation, measurement, product analytics |
| **Headline use cases** | Risk reduction, incident response, "health-driven feature control" | "Compare multiple [AI] models side-by-side, determine user engagement against cost tradeoffs" |
| **Mentions OpenFeature?** | Yes — Dynatrace helped establish it in 2022 | No |
| **Mentions OpenTelemetry / SemConv?** | No | No |

Both vendors are converging on **OpenFeature for the control plane** but
neither press release talks about how the resulting flag evaluations
correlate to telemetry. That correlation only works because of the
**OpenTelemetry feature-flag semantic conventions** — `feature_flag.key`,
`feature_flag.variant`, `feature_flag.provider_name` — result of a collaboration with OpenFeature.

**The unique angle of this talk:** Dynatrace bought the release-safety
half. Datadog bought the experimentation half. Both lean on OpenFeature;
neither mentions OpenTelemetry. The OpenFeature + OTel SemConv combination
is the only **vendor-neutral** path through this convergence — and that is
what we demonstrate live to show how you can benefit, regardless of which vendor you choose.

## Mapping examples to pillars

| Slot | Example | Maps to acquisition | Pillar from agenda |
|---|---|---|---|
| 1 | Recommendation algorithm A/B + AOV correlation | Datadog / Eppo | Business case — experimentation |
| 2 | Product-catalog progressive rollout (canary) | Dynatrace / DevCycle | Releasing safely; observability without code |
| 3 | Multi-model AI summary + kill switch | Both — AI is the shared theme | Cost/quality experimentation → incident response |

Detailed scenario specs and slide suggestions: [demo-spec.md](demo-spec.md).  
Stage runbook and setup instructions: [demo-runbook.md](demo-runbook.md).

## The demo arc

The three demos build on each other to tell one coherent story:

**Demo 1** establishes the foundation: *you get telemetry for free with one
line of code.* The TracingHook attaches `feature_flag.*` attributes to every
span — no custom instrumentation. The audience sees Jaeger traces, Grafana
panels, and AOV correlation across services, all without writing telemetry
code. The closing line: *"Personalized recommendations drive 5× larger
baskets. The checkout service has no idea the flag exists."*

**Demo 2** shows the same infrastructure handling a different problem:
release safety. The same SemConv attributes that power the A/B test
dashboard now power a canary regression dashboard. Step up the rollout,
watch errors appear, roll back, watch them disappear. *"No deploy. No
restart. The flag key was already on every span."*

**Demo 3** (closing beat) brings the two pillars together: AI
experimentation (Datadog framing) becomes an incident kill switch (Dynatrace
framing) using the exact same `feature_flag.*` attributes. *"One open
standard. All use cases."*

## Suggested talk structure

```
1. News hook (2 min)
   - Dynatrace buys DevCycle. Datadog buys Eppo. Neither press release 
     mentions OpenTelemetry. Why not?

2. Background (3 min)
   - Feature flags in the SDLC. Why they need observability.
   - OpenFeature: the open standard for flag evaluation.
   - OpenTelemetry SemConv: feature_flag.key / feature_flag.variant.
   - "These two communities collaborated on this. Let's see what that buys."

3. Demo 1 — Recommendation A/B (4 min)
   [see demo-spec.md]

4. Demo 2 — Canary rollout (3 min)
   [see demo-spec.md]

5. Demo 3 — AI model + kill switch (3 min, optional)
   [see demo-spec.md]

6. Synthesis (3 min)
   - OpenFeature + OTel = vendor-neutral path through the convergence.
   - Dynatrace bought release safety. Datadog bought experimentation.
     Neither vendor owns the telemetry layer — that's the open standard.
   - What's next: autonomous rollback, SLO-driven flag control.
```
