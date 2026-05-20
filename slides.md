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
  <div class="flex flex-col items-center gap-2">
    <img src="./public/alexandra-oberaigner.jpg" alt="Alexandra Oberaigner" class="w-18 h-18 rounded-full object-cover" />
    <span class="font-semibold">Alexandra Oberaigner</span>
    <span class="text-sm" style="color:var(--of-text-muted)">Dynatrace</span>
  </div>
  <div class="flex flex-col items-center gap-2">
    <img src="./public/lukas-reining.jpg" alt="Lukas Reining" class="w-18 h-18 rounded-full object-cover" />
    <span class="font-semibold">Lukas Reining</span>
    <span class="text-sm" style="color:var(--of-text-muted)">codecentric</span>
  </div>
</div>

<!--
It's great to see so many people interested in ...title

We not only aim to answer this question today from the company's strategic motivation but also we want to equip you with open-source tooling that can help you xxxx observable feature flagging. 
The talk is about what the open-source layer underneath gives you regardless of the company.

Both maintainers of OpenFeature.


Intro		| 5   - A
Feature flagging| 4   - L
OF 		| 2   - A
 Otel		| 4   - L
webshop 	| 3   - A
demo 1	 	| 2   - L
demo 2		| 2   - A
demo 3		| 2/3 - L
summary 	| 4   - A
-->

---
layout: section
---

# Two Acquisitions <br /> in Nine Months.

<!--
We open here because it is the clearest external signal that observability and feature flagging are converging. This talk is not about the acquisitions and not about either company. Everything after this slide is about the open-source layer that any practitioner can use, regardless of which observability backend they happen to run.
-->

---

<div class="grid grid-cols-2 grid-rows-2 gap-x-4 gap-y-0 mt-4">

<div class="col-start-1 row-start-1">
<div class="border border-gray-600 rounded-lg overflow-hidden text-sm shadow-lg">
    <div class="bg-gray-800 px-3 py-1 text-xs text-gray-400 flex items-center gap-2">
      <span class="w-2 h-2 rounded-full bg-red-500 inline-block"></span>
      <span class="w-2 h-2 rounded-full bg-yellow-500 inline-block"></span>
      <span class="w-2 h-2 rounded-full bg-green-500 inline-block"></span>
      <span class="ml-2 truncate">datadoghq.com/about/latest-news</span>
    </div>
    <div class="p-4 bg-gray-900">
      <div class="text-xs text-gray-500 mb-2">DATADOG · MAY 2025</div>
      <div class="font-bold text-white leading-snug mb-2">Datadog acquires Eppo to expand AI, product analytics, experimentation and feature flag capabilities</div>
      <div class="text-gray-400 text-xs leading-relaxed">"Compare multiple models side-by-side. Determine user engagement against cost tradeoffs."</div>
    </div>
  </div>
  <div class="text-xs text-muted mt-2 wrap">https://www.datadoghq.com/about/latest-news/press-releases/datadog-acquires-eppo-to-expand-its-ai/</div>
 
</div>

<div class="col-start-2 row-start-2">
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
      <div class="text-gray-400 text-xs leading-relaxed">"This acquisition further advances observability into an active system of control."</div>
    </div>
  </div>
  <div class="text-xs text-muted mt-2 truncate">dynatrace.com/news/blog/dynatrace-acquires-devcycle</div>
</div>

</div>

<!--
* Datadog bought Eppo in May 2025. 
* Dynatrace bought DevCycle in January 2026. 
* Two of the biggest observability vendors made significant investments in feature flagging. 
* While DDog focuses on the experimentation use case of feature flagging, DT focuses on release safely & progressive delivery

Why?
-->

---
layout: intro
---

# Why Now?

<!--
This is the talk's question. The audience leaves with an answer that is not vendor-shaped.
-->

---

# Agenda

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-3 mt-8">

<div class="text-accent font-bold">01</div>
<div>What is feature flagging</div>

<div class="text-accent font-bold">02</div>
<div>OpenFeature and OpenTelemetry</div>

<div class="text-accent font-bold">03</div>
<div>Use case demos on the OpenTelemetry demo</div>

<div class="text-accent font-bold">04</div>
<div>What the open standards give you, on any stack you already run</div>

<div class="text-accent font-bold">05</div>
<div>Q&A</div>

</div>

<!--
To answer this question: we are starting with the basics. Talking about feature flagging & the 2 open-source projects which are building the foundation. OF & Otel. We are working our way toward the answer with our 3 live demos.

And finally summarize the answer
-->

---
layout: section
---

# 01 · Feature Flagging

A runtime switch.

<!--
What is feature flagging?

Audience: 
* Who has feature flagged before?
*
-->

---

# What a Feature Flag Does

<div class="text-2xl mt-8">

It decouples <span class="text-accent">deploy</span> from <span class="text-accent">release</span>.

</div>

<div class="mt-4 text-muted">

Ship code to production. Decide later who sees it.

</div>

<div class="flex justify-center mt-8">
  <img src="./public/feature-flag.svg" alt="Feature flag" class="max-h-64" />
</div>

<!--
This is our definition.

That single sentence is the whole concept. Deploy is a build-and-restart event. Release is a config change. Once you separate them, everything else falls out: progressive rollout, kill switches, A/B tests, beta cohorts.
-->

---

# Not All Flags Are the Same

<div class="flex justify-center mt-2">
  <img src="./public/fowler-ff-types.png" alt="Feature toggle taxonomy by longevity and dynamism" class="max-h-96" />
</div>

<div class="text-xs text-muted mt-3 text-center">martinfowler.com/articles/feature-toggles.html</div>

<!--
This taxonomy is from Pete Hodgson's article on martinfowler.com. The categories matter because they have very different lifetimes and very different decision points. A release toggle is short-lived and mostly static. An experiment toggle is dynamic, per-request, and lives long enough to gather data. An ops toggle is a circuit breaker. Permissioning toggles are long-lived by design.

The three demos cover the three most observability-relevant categories: release, ops, experiment. Watch which category each demo lives in.
-->

---

# Feature Flag Lifecycle

<div class="absolute inset-0 flex justify-center items-center pt-24 pb-8 px-8">
  <img src="./public/fflc.svg" alt="Feature flag lifecycle" class="max-h-full max-w-full object-contain" />
</div>

<!--
explain steps.

lifeycle is idealized, reality often different, flags dont get deleted, code doesnt get cleaned up, observation is hard

Issues along the way:
* Observation step: did a new feature cause the observed error rate increase? Which feature flag? When was it toggled?
* Clean-up: is the feature rolled out completely - can we remove the feature flag & the old code
-->

---
layout: section
---

# 02 · Open Standards

OpenFeature and OpenTelemetry.

<!--
Briefly. Just enough to make the demo and the synthesis land.
-->

---

# OpenFeature

The vendor-neutral standard for flag evaluation. A CNCF incubating project.

<div class="flex justify-center mt-4">
  <img src="./public/of-architecture.svg" alt="OpenFeature architecture" class="max-h-84" />
</div>

<div class="text-xs text-muted mt-4 text-center">openfeature.dev/docs/reference/intro</div>

<!--
The architecture in one diagram. Application code talks to the Evaluation API. Hooks run around every evaluation. The provider translates to a specific flag management system: flagd, DevCycle, LaunchDarkly, Statsig, anything that implements the contract. Swap providers without touching application code.

Five concepts to know: Evaluation API, Provider, Evaluation Context, Hooks, Tracking API. The hook is the one to remember. It is how a global decision, "send flag data with my traces," turns into zero per-evaluation code.
-->

---

# How it Looks in Code

```java {1-3|5-6|8-11|13-14|all}
// 1. Configure provider and OTel hook
OpenFeatureAPI api = OpenFeatureAPI.getInstance();
api.setProviderAndWait(new MyFeatureProvider());

// 2. Create a client
Client client = api.getClient();

// 3. Evaluation context for targeting
Map<String, Value> attrs = new HashMap<>();
attrs.put("tier", new Value("premium"));
EvaluationContext ctx = new ImmutableContext("user-42", attrs);

// 4. Evaluate
boolean enabled = client.getBooleanValue("v2_enabled", false, ctx);
```

<div class="text-xs text-muted mt-3">openfeature.dev/docs/reference/sdks/server/java</div>

<!--
Four steps. Register a provider, add the OTel hook, create a client, evaluate a flag. The provider is the only vendor-specific line. Swap MyFeatureProvider for flagd, DevCycle, LaunchDarkly, or anything that implements FeatureProvider. The TracesHook emits a feature_flag.evaluation span event on the active OTel span for every evaluation. The evaluation context carries the targeting key and attributes the provider uses for targeting rules. The flag evaluation itself returns a string variant. All of this is vendor-neutral. The only thing that changes between vendors is the provider constructor.
-->

---

# OpenTelemetry

The vendor-neutral standard for telemetry: traces, metrics, logs.

<div class="mt-12 text-xl">

What matters for this talk: the <span class="text-accent">`feature_flag.evaluation`</span> event.

</div>

<div class="mt-6 text-muted">

Defined together by the OpenFeature and OpenTelemetry communities.

</div>

---

# Feature Flagging Semantic Conventions

| Attribute | Requirement | Example |
|---|---|---|
| `feature_flag.key` | **Required** | `recommendationAlgorithm` |
| `feature_flag.result.variant` | **Cond. Required** | `personalized` |
| `feature_flag.provider.name` | Recommended | `flagd` |
| `feature_flag.result.reason` | Recommended | `targeting_match`, `split` |
| `feature_flag.context.id` | Recommended | `5157782b-...` |
| `error.type` | Cond. Required | `flag_not_found` |

<div class="text-xs text-muted mt-4">

opentelemetry.io/docs/specs/semconv/feature-flags/feature-flags-events · openfeature.dev/specification/appendix-d

</div>

<!--
Walk through the table top to bottom. The key is always required. Variant is required when the provider returns one, otherwise the raw value is required. Reason codes map directly to OpenFeature resolution reasons, lowercased to snake_case: targeting_match means a rule matched, split means random assignment, default means no dynamic evaluation happened. The context ID is typically the targeting key. The set ID and version help correlate evaluations to specific flag configurations. Error type and message are only present when something went wrong.

The OpenFeature hook implementations follow the mapping defined in Appendix D of the OpenFeature spec. That is why a single hook setup gives you all of these attributes automatically.
-->

---

# How it Works Together

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

After this runs at startup, <span class="text-accent">every flag evaluation</span> emits a `feature_flag.evaluation` span event on the active span.

</div>

<div class="mt-4 text-muted">

No per-call code. No bespoke instrumentation per flag. One line at startup.

</div>

<!--
This is the single most important slide in the deck. Three lines of code. The hook intercepts every flag evaluation and emits a feature_flag.evaluation span event on the currently active span. SemConv defines the attribute names on that event. Everything you are about to see on stage is downstream of these three lines.
-->

---
layout: section
---

# 03 · Live Demo

Three scenarios. One hook.

<!--
We switch screens here. Three demos, run in this order: canary, AI, recommendation. The infrastructure does not change between them. Only the flags change.
-->

---

# The Astronomy Shop

The <span class="text-accent">OpenTelemetry community demo</span>. A small e-commerce stack instrumented end-to-end with OpenTelemetry.

<div class="mt-6">

- Already uses OpenFeature with the flagd provider
- Already ships `TracingHook` in Go and Python services
- Flag evaluation events flow onto spans automatically

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

<div class="flex justify-center items-center h-full">
  <img src="./public/astronomy-shop-home.png" alt="Astronomy shop home page" class="max-h-full max-w-full object-contain rounded shadow-lg" />
</div>

<!--
What the audience actually sees. The astronomy shop is just a webshop. Nothing special on the surface. The interesting part is what's underneath, which is the next slide.
-->

---
layout: default
---

# Inside the Astronomy Shop


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

Flag: `productCatalogCanary`<br/>
Service: `product-catalog` (Go)

<div class="mt-4 text-sm text-muted">
Flag category: <span class="text-green">release toggle</span>. Short-lived, percentage-based.
</div>

::right::

# <span class="text-handwritten text-green">On stage</span>

- Baseline: 5% v2, no errors
- Step rollout: Yellow latency rises, errors arise
- Jaeger: `feature_flag.key=productCatalogCanary feature_flag.result.variant=v2`
- Roll back: Panels recover in ~30s

<div class="mt-6 text-muted">

Rollback: one flag flip. No deploy or restart.

</div>

<!--
- Product catalog service with 1 feature flag
- Two flags compose: one controls who gets v2, the other controls how broken v2 is. 

  step 0  baseline     0% v2, severity=none
  step 1  canary       5% v2, severity=none
  step 2  escalate    25% v2, severity=low  (15% errors)
  step 3  escalate    50% v2, severity=high (40% errors)
  step 4  critical    75% v2, severity=critical (75% errors)
  rollback             0% v2, severity=none

To find error traces in Jaeger, use the Tags field:
  feature_flag.key=productCatalogCanary feature_flag.result.variant=v2
Both tags in one search — space-separated. Every v2 span carries both because the TracingHook attaches them automatically.

Closing line for this demo: "Rollback was one flag flip. No deploy. No restart. Remember the hook — the next two demos use the exact same one."
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
- Flip to `model-b`: Latency and cost shift, errors appear
- Flip to `off`: Incident contained

<div class="mt-6 text-muted">

Same hook and OTel span events, now covering experimentation and incident response.

</div>

<!--
Stage slot 2. About 3 minutes. This is the bridge demo. The point is not the AI. The point is that the exact same attributes we just used for canary observation now drive a completely different concern: comparing model cost and quality, then killing the bad one. One open standard, all use cases.

Flag changes for this demo — use flagd-ui (http://localhost:8080/feature/) or make reset first:
  productSummaryModel: model-a → model-b (show degraded model: +300-800ms, ~10% errors)
  productSummaryModel: model-b → off    (kill switch — 503s)
  productSummaryModel: off    → model-a (recover)
-->

---
layout: default
---

# How it Looks in Code

<div class="text-xs text-muted">

Backend (Python): pick the model from a flag.

</div>

```python
def get_product_summary_model() -> str:
    client = api.get_client()
    return client.get_string_value("productSummaryModel", "model-a")
```

<v-click>
<div class="text-xs text-muted mt-3">

Frontend (TS): emit the result through the OpenFeature Tracking API.

</div>

```ts
const handleHelpful = (helpful: boolean) => {
  OpenFeature.getClient().track(
    'summary_helpful_clicked',
    {
      value: helpful ? 1 : 0,
      helpful,
      productId,
    },
  );
  setHelpfulFeedback(helpful ? 'yes' : 'no');
};
```
</v-click>


<!--
Two halves of the same flag. The Python backend reads `productSummaryModel` to choose which model to call — vendor-neutral string evaluation through OpenFeature, with the TracingHook attaching `feature_flag.*` to the active span automatically. The frontend uses the OpenFeature Tracking API (`client.track(...)`) to record user feedback whenever someone reacts to a summary. Same client, same provider chain — the tracking event flows into the same observability backend with trace context attached. Read flags, emit metrics, both sides of the loop, one API.
-->

---
layout: two-cols
---

# Demo 3 of 3 <br/> <span class="text-accent">Recommendation A/B</span>

`recommendationAlgorithm`<br/>
Variants: `popularity`, `collaborative`, `personalized`<br/>
Service: `recommendation` (Python)

<div class="mt-4 text-sm text-muted">
Flag categories: <span class="text-green">experiment</span> plus <span class="text-green">permissioning</span>. Premium users get personalized; the rest popularity.
</div>

::right::

# <span class="text-handwritten text-green">On stage</span>

- `client.Track("checkout.completed", ...)`
- Per-variant Dashboard
- Average order value (AOV) by variant: OpenSearch PPL joins on session ID

<!--
Stage slot 3. About 4 minutes. Premium users get personalized via EvaluationContext. The rest get popularity. Walk in order: span event in Jaeger, per-variant metrics in Grafana, the Track call in checkout, AOV in OpenSearch, live flip.

Run before this demo: make demo3   (stop after: make loadgen-stop)
This starts k6 demo3 load automatically and handles the flag flip interactively.
Press Enter to advance each step.

  step 0  baseline  recommendationAlgorithm=popularity
  step 1  flip      recommendationAlgorithm=personalized
  step 2  AOV beat  (narration — no flag change, just show the table)
  opt.    rollback  recommendationAlgorithm=popularity
-->

---
layout: two-cols
---

# How it Looks in Code

```go {2-11|13-18|all}
// otelTrackingProvider
func (p *otelTrackingProvider) Track(
    ctx context.Context, eventName string,
    evalCtx openfeature.EvaluationContext,
    details openfeature.TrackingEventDetails,
) {
    slog.InfoContext(ctx, eventName,
        slog.String("app.user.id", evalCtx.TargetingKey()),
        slog.Float64("app.order.amount", details.Value()),
    )
}

// PlaceOrder — no flag knowledge
openfeature.NewClient("checkout").Track(ctx,
    "checkout.completed",
    openfeature.NewEvaluationContext(userID, nil),
    openfeature.NewTrackingEventDetails(orderTotal),
)
```

::right::

<div class="pl-6 mt-10">

```sql {hide|1-2|3-11|12-15|all}
SOURCE otel-logs-*
| WHERE body = 'checkout.completed'
| EVAL user_id    = attributes.app.user.id,
       order_amount = attributes.app.order.amount
| JOIN ON user_id [
    SOURCE otel-logs-*
    | WHERE body = 'recommendation served'
    | DEDUP attributes.app.user.id
    | RENAME attributes.app.user.id        AS user_id,
             attributes.app.recommendation.algorithm AS algorithm
  ]
| STATS avg(order_amount) AS aov,
        count()            AS checkouts
  BY algorithm
| SORT - aov
```
<v-after>
<div class="mt-4 text-muted text-sm">

Two log streams. One join key: `app.user.id`.<br/>
Neither service knows about the other.

</div>
</v-after>
</div>

<!--
Left: the Tracking API call and otelTrackingProvider. Right: the PPL query that makes sense of it.

Walk through the query in steps:
1. Start with "checkout.completed" logs — the tracking events from the checkout service.
2. Join with "recommendation served" logs on user_id — those come from the recommendation service, which logged which algorithm it used.
3. Aggregate: average order value and checkout count per algorithm variant.

The checkout service has no idea the recommendation flag exists. It just tracked the outcome. The join happens in the query layer, not in the application code. That is what decoupled means in practice.

Speaker note on the code side: otelTrackingProvider.Track emits the log record. The multi-provider fans the Track() call from the OpenFeature client to this provider. Flag evaluation still goes to flagd — StrategyFirstMatch skips otelTrackingProvider for all flag evaluations since it returns FLAG_NOT_FOUND.
-->

---
layout: section
---

# 04 · Why Now?

Safe releases, AI risk, experimentation.

---

# The Layer Both Companies Depend on Is Open.

Dynatrace acquired DevCycle &rarr; release safety, progressive delivery

Datadog acquired Eppo &rarr; experimentation, product analytics

<div class="mt-6 text-lg">

Different focus, same requirement: **flag evaluations need to be observable.**

</div>

<div class="mt-6 grid grid-cols-2 gap-6">

<div>

### <span class="text-green">OpenFeature</span>
The vendor-neutral API for flag evaluation. Swap providers without touching application code.

</div>

<div>

### <span class="text-green">OpenTelemetry</span>
OpenFeature Hooks emit flag evaluations as OTel span events. SemConv standardizes the attribute names so every backend can query them consistently.

</div>

</div>

<!--
The two acquisitions tell the same story from different angles. Dynatrace led with release safety. Datadog led with experimentation. Both converge on the same need: correlating flag evaluations with telemetry. OpenFeature gives you vendor-neutral flag evaluation — swap providers without touching application code. OpenTelemetry is the key enabler: hooks emit flag evaluations as span events, the collector routes them, backends query them. SemConv just standardizes the attribute names so that correlation works consistently across every backend. The layer is open. The layer works today.
-->

---

# Takeaways

<div class="grid grid-cols-[auto_1fr] gap-x-4 gap-y-6 mt-8">

<div class="text-accent font-bold text-2xl" v-click>1</div>
<div v-after>One hook emits a span event on every flag evaluation. No manual instrumentation per flag.</div>

<div class="text-accent font-bold text-2xl" v-click>2</div>
<div v-after>SemConv standardizes the attribute names. Any OTel-compatible backend can query flag key and variant without a bespoke integration.</div>

<div class="text-accent font-bold text-2xl" v-click>3</div>
<div v-after><span class="text-green">OpenFeature</span> and <span class="text-green">OpenTelemetry</span> are open standards. Swap providers, swap backends but the instrumentation does not change.</div>

<div class="text-accent font-bold text-2xl" v-click>4</div>
<div v-after>Release safety, incident response, experimentation all covered by one setup.</div>

</div>

---
layout: end
---

# Thank You

Questions?

<div class="mt-6">
  <OpenFeatureLogo size="180px" />
</div>

<div class="pt-6 flex justify-center gap-12">
  <div class="flex flex-col items-center gap-2">
    <img src="./public/alexandra-oberaigner.jpg" alt="Alexandra Oberaigner" class="w-16 h-16 rounded-full object-cover" />
    <span class="font-semibold">Alexandra Oberaigner</span>
    <span class="text-sm" style="color:var(--of-text-muted)">Dynatrace</span>
  </div>
  <div class="flex flex-col items-center gap-2">
    <img src="./public/lukas-reining.jpg" alt="Lukas Reining" class="w-16 h-16 rounded-full object-cover" />
    <span class="font-semibold">Lukas Reining</span>
    <span class="text-sm" style="color:var(--of-text-muted)">codecentric</span>
  </div>
</div>

<div class="mt-6 flex justify-center gap-8 text-sm">
  <a href="https://openfeature.dev">openfeature.dev</a>
  <a href="https://opentelemetry.io/docs/specs/semconv/feature-flags">OTel SemConv</a>
  <a href="https://cloud-native.slack.com/archives/C0344AANLA1">#openfeature on CNCF Slack</a>
</div>
