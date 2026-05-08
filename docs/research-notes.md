# Research Notes

Source material gathered while preparing the talk. Each section captures the
key claims, the talk-relevant quotes, and what the source omits — because the
omissions are what the talk fills in.

## Dynatrace acquires DevCycle

Source: <https://www.dynatrace.com/news/blog/dynatrace-acquires-devcycle-to-strengthen-feature-delivery/>

**Framing:** "Releases have become harder to understand and riskier to
manage" as modern software grows more complex.

**Four pillars Dynatrace emphasises:**

1. **Risk reduction** — release to small cohorts, validate, scale gradually
   based on real-time performance and error monitoring.
2. **Experimentation** — compare feature variants using real production
   traffic.
3. **Incident response** — causal analysis, act immediately.
4. **Developer experience** — fast, intuitive way to ship and control
   features with immediate feedback from production telemetry.

**OpenFeature:** explicitly called out. DevCycle is built on OpenFeature, the
vendor-neutral CNCF standard. Dynatrace helped establish OpenFeature in 2022.
Customers can use DevCycle or any OpenFeature-compliant system — no lock-in.

**Forward-looking:** "Health-driven feature control" — AI assistants
autonomously disable underperforming features without redeployment.

**Not mentioned:** OpenTelemetry, semantic conventions, how the correlation
between flag evaluations and telemetry actually works.

## Datadog acquires Eppo

Sources:
- <https://www.datadoghq.com/about/latest-news/press-releases/datadog-acquires-eppo-to-expand-its-ai/>
- <https://www.statsig.com/blog/datadog-acquires-eppo>

**Datadog framing:** Eppo is a feature-flagging *and experimentation*
platform that integrates with Datadog Product Analytics to create
"end-to-end product analytics."

**Headline value props (Datadog press release):**
- Engineers track code changes with feature flags; product managers design
  and measure impact with experiments.
- Consolidate analytics from multiple tools so teams can understand feature
  impact on KPIs.
- **AI is the centrepiece**: "the use of multiple AI models increases the
  complexity of deploying applications in production… experimentation
  solves this correlation and measurement problem, enabling teams to
  compare multiple models side-by-side, determine user engagement against
  cost tradeoffs."

**Statsig commentary (CEO Vijaye Raji):**
- Experimentation is "central to the modern development stack"; "point
  solutions are being consolidated into a single product development
  platform."
- Datadog is replicating how tech giants integrated experimentation with
  "feature flags, dynamic configs, and release pipelines."
- Will likely push experimentation toward "infrastructure workflows" and
  causal inference — "the ability to say **why** an application or service
  went down, not just **when**."

**Not mentioned:** OpenFeature, OpenTelemetry, semantic conventions. The
press release positions experimentation as the answer to AI complexity but
does not address how that experimentation correlates to underlying
telemetry.

## OpenFeature concepts

Source: <https://openfeature.dev/>

The talk demo uses these specific OpenFeature concepts; getting the
terminology right on stage is part of the message.

| Concept | One-liner |
|---|---|
| **Evaluation API** | The user-facing interface application code uses to evaluate flags. |
| **Provider** | The translation layer between the SDK and a flag-management system (e.g. flagd, DevCycle). |
| **Client / SDK** | Vendor-agnostic, standardised feature-flagging client. |
| **Evaluation context** | Container for arbitrary contextual data driving dynamic evaluation (user id, tier, region…). |
| **Hooks** | Middleware-like callbacks at four stages (`before` / `after` / `error` / `finally`) — core extension point for observability. |
| **Tracking API** | Associates flag evaluations with subsequent business actions/states (conversions, revenue) — enables experimentation analysis. |
| **Events** | React to provider state changes (readiness, flag config changes). |

### Hooks for observability

The OpenFeature spec documents emitting OpenTelemetry spans from hooks: a
span is initialised in the `before` stage and closed in `after`, with
references stored in hook data. This is exactly what the
`openfeature.contrib.hook.opentelemetry` packages do — and it is how
`feature_flag.*` semantic-convention attributes end up on every span without
the application changing its instrumentation.

### Tracking for experimentation

Tracking enables the association of flag evaluations with subsequent actions
or application states "in order to facilitate experimentation and analysis
of the impact of feature flags on business objectives." Common uses: page
visibility, conversion monitoring, value measurement (numeric values like
transaction amounts), experimentation analysis.

## OpenTelemetry feature-flag semantic conventions

Source: <https://opentelemetry.io/docs/specs/semconv/feature-flags/>

The SemConv defines a small set of standardised attributes:

- `feature_flag.key` — the flag identifier.
- `feature_flag.variant` — the resolved variant.
- `feature_flag.provider_name` — name of the resolution provider.
- (plus span event names and metric counters for evaluations and errors.)

These attributes are what allow trace, metric, and log backends to **pivot
on flag key or variant** without bespoke per-vendor integration. They are
the standardisation layer Dynatrace and Datadog do not currently advertise
in their announcements but their products implicitly need.

## Why this matters for the talk

The convergence is real and accelerating, but it is currently being told as
a vendor story. The standards story — the OpenFeature + OpenTelemetry
combination, with SemConv as the joined-forces work — is the durable,
vendor-neutral version of the same story. That is the message the demo
makes tangible.
