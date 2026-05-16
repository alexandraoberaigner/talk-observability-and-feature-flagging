---
theme: '@openfeature/slidev-theme-open-feature'
title: Why Are Observability Companies Investing in Feature Flagging?
info: |
  ## Why Are Observability Companies Investing in Feature Flagging?
  What OpenFeature and OpenTelemetry give you, on any stack you already run.
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
mdc: true
layout: cover
---

# Why Are Observability Companies <br/>Investing in <span class="text-accent">Feature Flagging</span>?

What OpenFeature and OpenTelemetry give you, on any stack you already run.

<div class="pt-8">
  <OpenFeatureLogo size="180px" />
</div>

<div class="pt-12 flex justify-center gap-12">
  <PresenterProfile name="Alexandra Oberaigner" company="Dynatrace" size="72px" photo="/alexandra-oberaigner.jpg" />
  <PresenterProfile name="Lukas Reining" company="codecentric" size="72px" photo="/lukas-reining.jpg" />
</div>

<!--
Both maintainers of OpenFeature. The talk is not an advertisement for either company. The demos run on open-source standards and tools; we only use Dynatrace for parts of the demos.
-->

---
layout: section
---

# Two acquisitions <br /> in twelve months.

<!--
We open here because it is the clearest external signal that observability and feature flagging are converging. This talk is not about the acquisitions and not about either company. Everything after this slide is about the open-source layer that any practitioner can use, regardless of which observability backend they happen to run.
-->

---

# The headlines

<div class="grid grid-cols-2 gap-6 mt-6">

<div class="border border-gray-600 rounded-lg overflow-hidden text-sm shadow-lg">
  <div class="bg-gray-800 px-3 py-1 text-xs text-gray-400 flex items-center gap-2">
    <span class="w-2 h-2 rounded-full bg-red-500 inline-block"></span>
    <span class="w-2 h-2 rounded-full bg-yellow-500 inline-block"></span>
    <span class="w-2 h-2 rounded-full bg-green-500 inline-block"></span>
    <span class="ml-2 truncate">dynatrace.com/news</span>
  </div>
  <div class="p-4 bg-gray-900">
    <div class="text-xs text-gray-500 mb-2">DYNATRACE · JANUARY 2026</div>
    <div class="font-bold text-white leading-snug mb-2">Dynatrace acquires DevCycle to strengthen feature delivery</div>
    <div class="text-gray-400 text-xs leading-relaxed">"Unifying runtime control with real-time, AI-powered intelligence so teams can close the loop between change and outcome."</div>
  </div>
</div>

<div class="border border-gray-600 rounded-lg overflow-hidden text-sm shadow-lg">
  <div class="bg-gray-800 px-3 py-1 text-xs text-gray-400 flex items-center gap-2">
    <span class="w-2 h-2 rounded-full bg-red-500 inline-block"></span>
    <span class="w-2 h-2 rounded-full bg-yellow-500 inline-block"></span>
    <span class="w-2 h-2 rounded-full bg-green-500 inline-block"></span>
    <span class="ml-2 truncate">datadoghq.com/about</span>
  </div>
  <div class="p-4 bg-gray-900">
    <div class="text-xs text-gray-500 mb-2">DATADOG · MAY 2025</div>
    <div class="font-bold text-white leading-snug mb-2">Datadog acquires Eppo to expand AI, product analytics, experimentation and feature flag capabilities</div>
    <div class="text-gray-400 text-xs leading-relaxed">"Compare multiple models side-by-side. Determine user engagement against cost tradeoffs."</div>
  </div>
</div>

</div>

<div class="text-xs text-muted mt-6">
  Sources: dynatrace.com/news/blog/dynatrace-acquires-devcycle · datadoghq.com/about/latest-news/press-releases/datadog-acquires-eppo
</div>

<!--
Dynatrace bought DevCycle in January 2026. Datadog bought Eppo in May 2025. Two of the biggest observability vendors made significant investments in feature flagging within twelve months of each other. We use this as evidence that the convergence is real, then move past it. The talk is about what the open-source layer underneath gives you regardless of vendor.
-->

---
layout: intro
---

# Why now?

<!--
This is the talk's question. The audience leaves with an answer that is not vendor-shaped.
-->

---

# Agenda

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-3 mt-8">

<div class="text-accent font-bold">01</div>
<div>What is feature flagging</div>

<div class="text-accent font-bold">02</div>
<div>OpenFeature and OpenTelemetry, briefly</div>

<div class="text-accent font-bold">03</div>
<div>Three live demos on the OpenTelemetry community demo</div>

<div class="text-accent font-bold">04</div>
<div>What the open standards give you, on any stack you already run</div>

</div>

<!--
Three short background sections, three demos, one synthesis. Q&A at the end.
-->

---
layout: section
---

# 01 · Feature flagging

A runtime switch.

<!--
Quick grounding for the novice audience. Two slides total.
-->

---

# What a feature flag does

<div class="text-2xl mt-12">

It decouples <span class="text-accent">deploy</span> from <span class="text-accent">release</span>.

</div>

<div class="mt-8 text-muted">

Ship code to production. Decide later who sees it.

</div>

<!--
That single sentence is the whole concept. Deploy is a build-and-restart event. Release is a config change. Once you separate them, everything else falls out: progressive rollout, kill switches, A/B tests, beta cohorts.
-->

---

# What you get, and where it hurts

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### <span class="text-green">What you get</span>

<v-clicks>

- Ship dark, release when ready
- Roll out by percentage or cohort
- Kill switch for incidents
- A/B test in production
- Targeted access by region or tier

</v-clicks>

</div>

<div>

### <span class="text-accent">Where it hurts</span>

<v-clicks>

- Flags multiply, become permanent
- "Did the new variant cause the spike?"
- "Is this rollout moving the KPI?"
- Telemetry has no idea flags exist
- <span class="text-green">This is what the open standards close.</span>

</v-clicks>

</div>

</div>

<!--
The right column is the bridge into the rest of the talk. Once flags drive runtime behavior, your telemetry needs to know what variant was active for every request. Otherwise you cannot answer the only questions that matter: did it work, is it safe, is it making money.
-->

---

# Not all flags are the same

<div class="flex justify-center mt-2">
  <img :src="'/fowler-ff-types.png'" alt="Feature toggle taxonomy by longevity and dynamism" class="max-h-96" />
</div>

<div class="text-xs text-muted mt-3 text-center">martinfowler.com/articles/feature-toggles.html</div>

<!--
This taxonomy is from Pete Hodgson's article on martinfowler.com. The categories matter because they have very different lifetimes and very different decision points. A release toggle is short-lived and mostly static. An experiment toggle is dynamic, per-request, and lives long enough to gather data. An ops toggle is a circuit breaker. Permissioning toggles are long-lived by design.

The three demos cover the three most observability-relevant categories: release, ops, experiment. Watch which category each demo lives in.
-->

---
layout: section
---

# 02 · Open standards

OpenFeature and OpenTelemetry.

<!--
Briefly. Just enough to make the demo and the synthesis land.
-->

---

# OpenFeature

The vendor-neutral standard for flag evaluation. A CNCF incubating project.

<div class="flex justify-center mt-4">
  <img :src="'/of-architecture.svg'" alt="OpenFeature architecture" class="max-h-84" />
</div>

<div class="text-xs text-muted mt-4 text-center">openfeature.dev/docs/reference/intro</div>

<!--
The architecture in one diagram. Application code talks to the Evaluation API. Hooks run around every evaluation. The provider translates to a specific flag management system: flagd, DevCycle, LaunchDarkly, Statsig, anything that implements the contract. Swap providers without touching application code.

Five concepts to know: Evaluation API, Provider, Evaluation Context, Hooks, Tracking API. The hook is the one to remember. It is how a global decision, "send flag data with my traces," turns into zero per-evaluation code.
-->

---

# OpenTelemetry

The vendor-neutral standard for telemetry: traces, metrics, logs.

<div class="mt-8 text-xl">

What matters for this talk: the <span class="text-accent">feature flag semantic conventions</span>.

</div>

```yaml
feature_flag.key:            recommendationAlgorithm
feature_flag.result.variant: personalized
feature_flag.provider.name:  flagd
```

<div class="text-xs text-muted mt-4">opentelemetry.io/docs/specs/semconv/feature-flags · development status</div>

<div class="mt-6">

Three attributes. Plus standardised event names and metric counters. (TODO)

</div>

<!--
SemConv is a small, important piece of standardisation. With these attribute names agreed on, any backend you happen to run can pivot any signal on flag key or variant. No bespoke per-vendor integration. The OpenFeature and OpenTelemetry communities collaborated directly on this, which is why it works as cleanly as it does.
-->

---

# Wiring things up

```python
# Python
from openfeature.contrib.hook.opentelemetry import TracingHook
api.add_hooks([TracingHook()])
```

```go
// Go
openfeature.AddHooks(otelhooks.NewTracesHook())
```

<div class="mt-8">

After this runs at startup, <span class="text-accent">every flag evaluation</span> attaches `feature_flag.*` attributes to the active span.

</div>

<div class="mt-4 text-muted">

No per-call code. No bespoke instrumentation. Standards-defined attributes on every span, for free.

</div>

<!--
This is the single most important slide in the deck. Three lines of code. The hook intercepts every flag evaluation and writes the SemConv attributes onto the span that is currently active. Everything you are about to see on stage is downstream of these three lines.
-->

---
layout: section
---

# 03 · Live demo

Three scenarios. One hook.

<!--
We switch screens here. Three demos, run in this order: canary, AI, recommendation. The infrastructure does not change between them. Only the flags change.
-->

---

# The astronomy shop

The <span class="text-accent">OpenTelemetry community demo</span>. A small e-commerce stack instrumented end-to-end with OpenTelemetry.

<div class="mt-6">

- Already uses OpenFeature with the flagd provider
- Already ships `TracingHook` in Go and Python services
- SemConv attributes flow onto spans automatically

</div>

<div class="mt-8 text-xs text-muted">
github.com/open-telemetry/opentelemetry-demo · talk fork: github.com/alexandraoberaigner/opentelemetry-demo
</div>

<!--
The talk runs on a fork of the OpenTelemetry community demo, with three demo-specific flags added. Everything else is upstream.
-->

---
layout: default
---

# Inside the astronomy shop



<div class="flex justify-center items-center">

```mermaid {scale: 0.39, theme: 'neutral'}
graph TD
accounting(Accounting):::dotnet
ad(Ad):::java
cache[(Cache<br/>&#40Valkey&#41)]
cart(Cart):::dotnet
checkout(Checkout):::golang
currency(Currency):::cpp
email(Email):::ruby
flagd(Flagd):::golang
flagd-ui(Flagd-ui):::elixir
fraud-detection(Fraud Detection):::kotlin
frontend(Frontend):::typescript
frontend-proxy(Frontend Proxy <br/>&#40Envoy&#41):::cpp
image-provider(Image Provider <br/>&#40nginx&#41):::cpp
llm(LLM):::python
load-generator([Load Generator]):::python
payment(Payment):::javascript
product-catalog(Product Catalog):::golang
product-reviews(Product Reviews):::python
quote(Quote):::php
recommendation(Recommendation):::python
shipping(Shipping):::rust
queue[(queue<br/>&#40Kafka&#41)]:::java
react-native-app(React Native App):::typescript
postgresql[(Database<br/>&#40PostgreSQL&#41)]

accounting ---> postgresql

ad ---->|gRPC| flagd

checkout -->|gRPC| currency
checkout -->|gRPC| cart
checkout -->|TCP| queue

cart --> cache
cart -->|gRPC| flagd

checkout -->|gRPC| payment
checkout --->|HTTP| email
checkout -->|gRPC| product-catalog
checkout -->|HTTP| shipping

fraud-detection -->|gRPC| flagd

frontend -->|gRPC| ad
frontend -->|gRPC| currency
frontend -->|gRPC| cart
frontend -->|gRPC| checkout
frontend -->|HTTP| shipping
frontend ---->|gRPC| recommendation
frontend -->|gRPC| product-catalog
frontend -->|gRPC| product-reviews

frontend-proxy -->|gRPC| flagd
frontend-proxy -->|HTTP| frontend
frontend-proxy -->|HTTP| flagd-ui
frontend-proxy -->|HTTP| image-provider

llm -->|gRPC| flagd
llm ---> product-reviews

payment -->|gRPC| flagd

product-reviews -->|gRPC| flagd
product-reviews -->|gRPC| product-catalog
product-reviews -->|gRPC| llm
product-reviews ---> postgresql

queue -->|TCP| accounting
queue -->|TCP| fraud-detection

recommendation -->|gRPC| flagd
recommendation -->|gRPC| product-catalog

shipping -->|HTTP| quote

Internet -->|HTTP| frontend-proxy
load-generator -->|HTTP| frontend-proxy
react-native-app -->|HTTP| frontend-proxy

classDef dotnet fill:#178600,color:white;
classDef cpp fill:#f34b7d,color:white;
classDef elixir fill:#b294bb,color:black;
classDef golang fill:#00add8,color:black;
classDef java fill:#b07219,color:white;
classDef javascript fill:#f1e05a,color:black;
classDef kotlin fill:#560ba1,color:white;
classDef php fill:#4f5d95,color:white;
classDef python fill:#3572A5,color:white;
classDef ruby fill:#701516,color:white;
classDef rust fill:#dea584,color:black;
classDef typescript fill:#e98516,color:black;

style recommendation stroke:#000000,stroke-width:8px
style llm stroke:#000000,stroke-width:8px
style product-catalog stroke:#000000,stroke-width:8px
style checkout stroke:#000000,stroke-width:8px
style cart stroke:#000000,stroke-width:8px
style frontend stroke:#000000,stroke-width:8px
style flagd stroke:#000000,stroke-width:8px
style product-reviews stroke:#000000,stroke-width:8px
```

</div>

<!--
All services instrumented with OpenTelemetry. Feature flags via flagd.
-->

---
layout: two-cols
---

# Demo 1 of 3 <br/> <span class="text-accent">Canary rollout</span>

`productCatalogCanary` + `productCatalogV2Severity`<br/>
Service: `product-catalog` (Go)

<div class="mt-4 text-sm text-muted">
Flag category: <span class="text-green">release toggle</span>. Short-lived, percentage-based.
</div>

::right::

# <span class="text-handwritten text-green">On stage</span>

- Baseline. 5% v2, no errors
- Step rollout. Yellow latency rises
- Add severity. Red errors appear
- Roll back. Panels recover in ~30s

<div class="mt-6 text-muted">

The `feature_flag.key` is already on every span. No deploy. No restart.

</div>

<!--
Stage slot 1. About 3 minutes on the screen. Two flags compose: one controls who gets v2, the other controls how broken v2 is. The dashboard reads app.catalog.version so the severity flag does not contaminate the rollout cohort.

Closing line for this demo: "No deploy. No restart. The flag key was already on every span. That is the SemConv payoff. Remember the hook. The next two demos use the exact same one."
-->

---
layout: two-cols
---

# Demo 2 of 3 <br/> <span class="text-accent">Multi-model AI</span>

`productSummaryModel`<br/>
Service: `llm` (Python)

<div class="mt-4 text-sm text-muted">
Flag category: <span class="text-green">ops toggle</span>. Cost and quality comparison. Incident kill switch.
</div>

::right::

# <span class="text-handwritten text-green">On stage</span>

- Baseline on `model-a`
- Flip to `model-b`. Latency and cost shift
- Inject errors. Isolated to `model-b`
- Flip to `off`. Incident contained

<div class="mt-6 text-muted">

Same hook. Same SemConv attributes. Now powering experimentation and incident response.

</div>

<!--
Stage slot 2. About 3 minutes. This is the bridge demo. The point is not the AI. The point is that the exact same attributes we just used for canary observation now drive a completely different concern: comparing model cost and quality, then killing the bad one. One open standard, all use cases.
-->

---
layout: two-cols
---

# Demo 3 of 3 <br/> <span class="text-accent">Recommendation A/B</span>

`recommendationAlgorithm`<br/>
Variants: `popularity`, `collaborative`, `personalized`<br/>
Service: `recommendation` (Python)

<div class="mt-4 text-sm text-muted">
Flag categories: <span class="text-green">experiment</span> plus <span class="text-green">permissioning</span>. Premium users get personalized; the rest are split 50/50.
</div>

::right::

# <span class="text-handwritten text-green">On stage</span>

- Hook in Jaeger. The span event
- Per-variant Grafana. Impressions, p95
- AOV by variant. OpenSearch PPL
- Live flip. Dashboard shifts in ~30s

<!--
Stage slot 3. About 4 minutes. Premium users get personalized via EvaluationContext. The rest are split 50/50 by fractional targeting. We will walk through four panels in order: the span event in Jaeger, per-variant metrics in Grafana, the AOV correlation in OpenSearch, then a live flag flip to watch the dashboard catch up.
-->

---
layout: statement
---

# Personalized drives 5x larger baskets.<br/>The checkout service has no idea the flag exists.

<div class="mt-12 text-xl text-muted">
One open standard. All use cases.
</div>

<!--
Closing beat of the demo arc. The recommendation service logs the user id and the variant. The checkout service logs the user id and the order amount. A PPL query in OpenSearch joins them. That correlation works because both services emit standard OpenTelemetry signals, and the recommendation service emits the standard feature-flag attributes. No bespoke integration. No coupling between services. That is what vendor-neutral means in practice.
-->

---
layout: section
---

# 04 · Why now?

Safe releases, AI risk, experimentation.

---

# Two stories, one foundation (TODO)

<div class="text-sm">

| | Dynatrace and DevCycle | Datadog and Eppo |
|---|---|---|
| **Centre of gravity** | Release safety, progressive delivery | Experimentation, product analytics |
| **Headline use case** | Risk reduction, kill switches | Compare AI models, engagement vs cost |

</div>

<div class="mt-8">

Different framings of the same convergence. Both rely on a layer underneath that you can use directly:

</div>

<div class="mt-4 grid grid-cols-2 gap-6">

<div>

### <span class="text-green">OpenFeature</span>
The control plane. Your code, your evaluation context, your hooks.

</div>

<div>

### <span class="text-green">OpenTelemetry SemConv</span>
The correlation layer. Flag attributes on every signal, every backend.

</div>

</div>

<!--
The two vendors emphasize different facets of the same convergence. Dynatrace led with release safety. Datadog led with experimentation. We use them as the hook, not the message. The message is that the layer underneath belongs to you. Your application talks to OpenFeature, not a vendor SDK. Your telemetry follows the OpenTelemetry feature-flag semantic conventions, regardless of which backend you send signals to. That layer is open source. That layer is yours.
-->

---
layout: statement
---

# OpenFeature + OpenTelemetry SemConv<br/>is yours, on any stack you already run.

<!--
The talk's thesis in one line. The convergence is real. The vendor stories are real. The value layer underneath them is open, vendor-neutral, and adoptable today. Pick any backend, swap any backend, run your own. The hook, the attributes, the correlation, all of it keeps working.
-->

---

# Takeaways (TODO)

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-6 mt-8">

<div class="text-accent font-bold text-2xl" v-click>1</div>
<div v-after>One hook turns every flag evaluation into first-class telemetry.</div>

<div class="text-accent font-bold text-2xl" v-click>2</div>
<div v-after>SemConv lets you pivot traces, metrics, and logs on flag key or variant. No bespoke integration per backend.</div>

<div class="text-accent font-bold text-2xl" v-click>3</div>
<div v-after><span class="text-green">OpenFeature</span> and <span class="text-green">OpenTelemetry</span> are open standards. Pick any vendor, swap any vendor, run your own stack. The code above stays the same.</div>

<div class="text-accent font-bold text-2xl" v-click>4</div>
<div v-after>Adoptable today. One line on a service you already run.</div>

</div>

---

# Get started (TODO)

<div class="grid grid-cols-3 gap-4 mt-6">
  <div class="card text-center">
    <h3>Learn</h3>
    <p>openfeature.dev<br/>opentelemetry.io/docs/specs/semconv/feature-flags</p>
  </div>
  <div class="card text-center">
    <h3>Try</h3>
    <p>Run the <a href="https://github.com/open-telemetry/opentelemetry-demo">OTel community demo</a>. Flags already wired.</p>
  </div>
  <div class="card text-center">
    <h3>Connect</h3>
    <p><code>#openfeature</code> on CNCF Slack</p>
  </div>
</div>

<div class="mt-10 text-center">
  <QRCode url="https://github.com/alexandraoberaigner/talk-observability-and-feature-flagging" size="160px" />
  <div class="text-xs text-muted mt-2">Slides and notes</div>
</div>

---
layout: end
---

# Thank you

Questions?

<div class="mt-8">
  <OpenFeatureLogo size="200px" />
</div>

<div class="pt-10 flex justify-center gap-12">
  <PresenterProfile name="Alexandra Oberaigner" company="Dynatrace" size="64px" photo="/alexandra-oberaigner.jpg" />
  <PresenterProfile name="Lukas Reining" company="codecentric" size="64px" photo="/lukas-reining.jpg" />
</div>
