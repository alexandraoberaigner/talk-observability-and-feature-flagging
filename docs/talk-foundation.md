# Talk Foundation

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

## Agenda (current draft)

1. **Current happenings** — Dynatrace acquires DevCycle, Datadog acquires
   Eppo. Blog posts and news framing.
2. **What is feature flagging?** Challenges. SDLC with feature flags.
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
`feature_flag.variant`, `feature_flag.provider_name` — that OpenFeature
contributed to.

> **The unique angle of this talk:** Dynatrace bought the release-safety
> half. Datadog bought the experimentation half. Both lean on OpenFeature;
> neither mentions OpenTelemetry. The OpenFeature + OTel SemConv combination
> is the only **vendor-neutral** path through this convergence — and that is
> what we demonstrate live.

## Mapping examples to pillars

| Slot | Example | Maps to acquisition | Pillar from agenda |
|---|---|---|---|
| 1 | Recommendation algorithm A/B + tracking | Datadog / Eppo | Business case — tracking |
| 2 | Product-catalog progressive rollout (canary) | Dynatrace / DevCycle | Releasing safely; troubleshooting via SemConv |
| 3 | Multi-model AI summary with cost/quality tracking and kill switch | Both — AI is the shared theme | Cost/quality experimentation → incident response |

Detailed scenario specs live in [demo-spec.md](demo-spec.md).
