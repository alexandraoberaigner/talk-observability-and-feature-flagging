---
theme: default
colorSchema: light
background: '#ffffff'
title: Observability and Feature Flagging
info: |
  ## Observability and Feature Flagging
  Why observability companies invest in feature flagging — and how you can benefit.
class: text-left
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
mdc: true
fonts:
  sans: 'Inter'
  mono: 'JetBrains Mono'
---

# Observability and<br/>Feature Flagging

Why observability companies invest in feature flagging<br/>— and how you can benefit.

<div class="pt-12 text-sm opacity-70">
  Alexandra Oberaigner · 2026
</div>

---

# Agenda

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-3 mt-8">

<div class="text-indigo-500 font-bold">01</div>
<div>Current happenings — two big acquisitions</div>

<div class="text-indigo-500 font-bold">02</div>
<div>What is feature flagging?</div>

<div class="text-indigo-500 font-bold">03</div>
<div>Why are observability companies investing in feature flagging?</div>

<div class="text-indigo-500 font-bold">04</div>
<div>Live: three demo scenarios on the OpenTelemetry demo</div>

<div class="text-indigo-500 font-bold">05</div>
<div>Why OpenFeature + OpenTelemetry are more than the sum of their parts</div>

</div>

---
layout: section
---

# 01 · Current happenings

Two acquisitions, one convergence

---

# Two acquisitions, two halves of the same story

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### Dynatrace acquires **DevCycle**

- Release safety, progressive delivery, kill switches
- "Releases have become harder to understand and riskier to manage"
- **OpenFeature called out** — Dynatrace helped establish it in 2022
- Forward-looking: *"health-driven feature control"*

</div>

<div>

### Datadog acquires **Eppo**

- Experimentation, measurement, product analytics
- AI as centrepiece: *"compare multiple models side-by-side,<br/>determine engagement against cost tradeoffs"*
- **OpenFeature not mentioned**
- Statsig (Vijaye Raji): "experimentation is central to the modern dev stack"

</div>

</div>

<div class="mt-8 text-sm opacity-70">
  Neither press release mentions OpenTelemetry or semantic conventions.
</div>

---
layout: section
---

# 02 · What is feature flagging?

---

# Feature flagging in one slide

A **runtime switch** that decouples *deploy* from *release*.

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### What it gives you

- Ship dark, release later
- Progressive rollout (1% → 5% → 100%)
- Kill switches for incidents
- A/B testing & experimentation
- Targeted access (beta cohorts, regions, tiers)

</div>

<div>

### Where the pain starts

- Flags multiply, become permanent
- *"Did the new variant cause the latency spike?"*
- *"Is this rollout actually moving the KPI?"*
- Telemetry doesn't know flags exist
- → **this is where observability comes in**

</div>

</div>

---

# The SDLC with feature flags

```mermaid {scale: 0.7}
flowchart LR
  A[Code] --> B[Deploy<br/>flag OFF]
  B --> C[Targeted<br/>release]
  C --> D[Progressive<br/>rollout]
  D --> E{Healthy?}
  E -- yes --> F[100% +<br/>flag cleanup]
  E -- no --> G[Flip flag<br/>OFF]
  G --> H[Investigate]
  H --> A
```

Every arrow after **Deploy** is a flag operation — and every one of them is invisible to your dashboards by default.

---
layout: section
---

# 03 · Why observability companies are investing

The feature-flag lifecycle is best understood with OpenTelemetry.

---

# Three jobs observability needs flags for

<div class="grid grid-cols-3 gap-6 mt-8">

<div class="p-5 rounded-lg bg-indigo-50/60 border border-indigo-100">

### 💰 Business impact
Did this variant make money?<br/>
→ **Tracking API**
<div class="mt-3 text-xs opacity-60">Datadog / Eppo angle</div>

</div>

<div class="p-5 rounded-lg bg-indigo-50/60 border border-indigo-100">

### 🔎 Troubleshooting
Which cohort is slow / erroring?<br/>
→ **SemConv on spans**
<div class="mt-3 text-xs opacity-60">Both angles</div>

</div>

<div class="p-5 rounded-lg bg-indigo-50/60 border border-indigo-100">

### 🛟 Releasing safely
Canary, kill switch, rollback<br/>
→ **Targeting + telemetry**
<div class="mt-3 text-xs opacity-60">Dynatrace / DevCycle angle</div>

</div>

</div>

<div class="mt-10">

Each one only works if flag evaluations are **correlated with telemetry** — which only works because of a small, standardised set of attributes.

</div>

---

# OpenTelemetry feature-flag semantic conventions

A tiny set of standardised attributes — that's it.

```yaml
feature_flag.key:           recommendationAlgorithm
feature_flag.variant:       personalized
feature_flag.provider_name: flagd
```

<div class="mt-6">

Plus span event names and metric counters for evaluations and errors.

</div>

<div class="mt-8 p-4 rounded bg-indigo-50 border border-indigo-100">

These attributes are what let traces, metrics and logs **pivot on flag key or variant** — without bespoke per-vendor integration.

</div>

---

# OpenFeature concepts we'll touch on stage

| Concept | One-liner |
|---|---|
| **Evaluation API** | The user-facing interface application code uses to evaluate flags |
| **Provider** | Translation layer between the SDK and a flag-management system |
| **Evaluation context** | Container for arbitrary data driving dynamic evaluation (user, tier, region) |
| **Hooks** | Middleware-like callbacks (`before` / `after` / `error` / `finally`) — the observability extension point |
| **Tracking API** | Associates flag evaluations with later business actions — enables experimentation analysis |

---

# How SemConv ends up on every span — for free

Register the OpenTelemetry hook once at startup:

```python
# Python (recommendation, llm)
from openfeature.contrib.hook.opentelemetry import TracingHook
api.add_hooks([TracingHook()])
```

```go
// Go (product-catalog)
openfeature.AddHooks(otelhooks.NewTracesHook())
```

<div class="mt-8">

After that, **every** flag evaluation in those services automatically attaches `feature_flag.*` attributes to the active span.

</div>

<div class="mt-4 text-sm opacity-70">
  This is the "OpenFeature contributed SemConv to OpenTelemetry" point — made concrete.
</div>

---
layout: section
---

# 04 · Live demo

Three scenarios on the OpenTelemetry community demo

---

# Demo setup

The astronomy shop — **OpenTelemetry community demo**:<br/>
<https://github.com/open-telemetry/opentelemetry-demo>

<div class="mt-6">

- Already uses **OpenFeature** with the **flagd** provider
- Ships the **OpenTelemetry TracingHook** in Go and Python services
- → SemConv attributes flow onto spans **for free** in those services
- Talk branch: `feat/openfeature-talk-demo` on the fork

</div>

---

# Demo 1 — Recommendation A/B test

**Datadog / Eppo pillar — "is the new model actually making money?"**

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

**Flag:** `recommendationAlgorithm`<br/>
**Variants:** `popularity` · `collaborative` · `personalized`<br/>
**Service:** `recommendation` (Python)

OpenFeature concepts:
- Evaluation context: `userId`, `userTier`, `region`
- Targeting: `personalized` for `userTier=premium`
- **Tracking API**: `add_to_cart`, `checkout_completed`

</div>

<div>

OTel signals:
- Spans on `recommendation.ListRecommendations` carry `feature_flag.key/variant`
- Counters split by variant: impressions, CTR, conversion

**On stage:** Grafana panel split by variant — conversion rate, AOV, p95 latency.<br/>
Personalized lifts conversion **+X%** but adds latency.

</div>

</div>

---

# Demo 2 — Product-catalog canary

**Dynatrace / DevCycle pillar — releasing safely**

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

**Flag:** `productCatalogCanary`<br/>
**Variants:** `v1` · `v2` (fractional 5/25/50/100%)<br/>
**Service:** `product-catalog` (Go)

OpenFeature concepts:
- Provider: flagd, server-side resolution
- Hooks: Go OTel `TracesHook` attaches `feature_flag.*` per SemConv
- Targeting rules edited live in flagd-ui

</div>

<div>

OTel signals:
- Filter spans by `feature_flag.variant=v2`
- Error / latency spike isolated to the canary cohort
- Flip back to `5%` → metrics recover live

**On stage:** "no code change, no redeploy — the flag key is already on every span. That's the SemConv payoff."

</div>

</div>

---

# Demo 3 — Multi-model AI summary

**Shared AI theme — covers both pillars**

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

**Flag:** `productSummaryModel`<br/>
**Variants:** `off` · `model-a` · `model-b`<br/>
**Service:** `llm` (Python — adds the missing `TracingHook`)

OpenFeature concepts:
- Hooks: add `TracingHook` → SemConv on LLM spans
- Evaluation context: `userTier=beta`
- Tracking API: `summary_helpful_clicked`

</div>

<div>

OTel signals:
- Per-variant: **token cost**, **latency**, **error rate**
- Spans carry `feature_flag.key` / `feature_flag.variant`

**On stage closing beat:** flip `productSummaryModel=model-b` plus the existing `llmRateLimitError` → model-B degrades, errors visible **per variant**. Flip back → incident contained, no redeploy.

</div>

</div>

---
layout: section
---

# 05 · More than the sum of their parts

OpenFeature + OpenTelemetry — the vendor-neutral path

---

# The unique angle

|  | Dynatrace / DevCycle | Datadog / Eppo |
|---|---|---|
| **Centre of gravity** | Release safety, progressive delivery | Experimentation, product analytics |
| **Headline use case** | Risk reduction, incident response | Compare AI models, engagement vs cost |
| **Mentions OpenFeature?** | ✅ Yes — helped establish it in 2022 | ❌ No |
| **Mentions OpenTelemetry / SemConv?** | ❌ No | ❌ No |

<div class="mt-8 p-4 rounded bg-indigo-50 border border-indigo-100">

> Dynatrace bought the release-safety half. Datadog bought the experimentation half.<br/>
> Both lean on OpenFeature; neither mentions OpenTelemetry.<br/>
> The **OpenFeature + OTel SemConv** combination is the only **vendor-neutral** path through this convergence.

</div>

---

# Takeaways

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-5 mt-8">

<div class="text-indigo-500 font-bold text-2xl">1</div>
<div>Flag evaluations are first-class telemetry — once you register one hook.</div>

<div class="text-indigo-500 font-bold text-2xl">2</div>
<div>SemConv is the standardisation layer Dynatrace and Datadog implicitly need but don't advertise.</div>

<div class="text-indigo-500 font-bold text-2xl">3</div>
<div>OpenFeature + OpenTelemetry is the vendor-neutral version of the same convergence story.</div>

<div class="text-indigo-500 font-bold text-2xl">4</div>
<div>You can adopt it today: <code>add_hooks([TracingHook()])</code> on a service you already run.</div>

</div>

---
layout: center
class: text-center
---

# Thank you

Questions?

<div class="mt-12 text-sm opacity-70">

[github.com/alexandraoberaigner/talk-observability-and-feature-flagging](https://github.com/alexandraoberaigner/talk-observability-and-feature-flagging)

</div>
