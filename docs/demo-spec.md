# Demo Specification

The live portion of the talk runs on top of the **OpenTelemetry community
demo** (astronomy shop) — <https://github.com/open-telemetry/opentelemetry-demo>.

The working fork lives at
<https://github.com/alexandraoberaigner/opentelemetry-demo>

The full stage runbook is in [demo-runbook.md](demo-runbook.md).

> **Stage order:** Demo 2 → Demo 3 → Demo 1. The numbers in the demo
> headings reflect flag/service identity (and the order in which they
> were implemented), **not** the on-stage order. On stage we open with
> release safety (Demo 2), move through the AI angle (Demo 3), and close
> with the business-impact AOV story (Demo 1). The sections below are
> ordered to match the stage order.

---

## Demo 2 — Product Catalog Progressive Rollout ✅ Implemented

**Stage slot 1 (opener) · ~3 min on stage · Maps to: Release safety**

**Flags:** `productCatalogCanary` (string, v1/v2) + `productCatalogV2Severity` (int, 0/15/40/75)  
**Service:** `product-catalog` (Go)  
**Targeting:** `$flagd.targetingKey` (session ID) for deterministic per-user assignment

### The story

*"Safe canary release — observable regression, instant rollback. We didn't
touch a single monitoring configuration."*

This is also where we establish the **"telemetry for free"** lesson for
the rest of the talk: one line of hook registration is what put
`feature_flag.evaluation` on every span.

### What it shows

1. **Baseline** — 95% v1 (green), 5% v2 (yellow). Tiny yellow latency blip.
   *"v2 is live for 5% of users. Looks fine."*

2. **Step up rollout** — 25% v2. Yellow p95 line rises above green. Errors
   still zero. *"Hmm, slower but no errors yet."*

3. **Flip severity** — `productCatalogV2Severity = low`. Red errors appear
   on the traffic panel. *"There it is."*

4. **Escalate** — 75% canary + `critical` severity. Both panels alarming.
   *"SLO breach. Roll back."*

5. **Roll back** — Back to 5%, severity `none`. Panels recover in ~30 seconds.
   *"No deploy. No restart. The flag key is already on every span — that's
   the SemConv payoff."*

### Key slide moments

- **"The feature_flag.key is on every span"** — show the `app.catalog.version` attribute in Jaeger alongside the flag evaluation span event
- **"Rollback in 30 seconds"** — the live panel recovery is the demo

### OpenFeature concepts demonstrated

- Fractional targeting by session ID — consistent per-user assignment
- Two-flag composition: rollout % controls who; severity controls how bad
- `TracingHook` (Go) + collector spanevent→span attribute transform

### Technical implementation notes

v2 adds latency and errors scaled by `productCatalogV2Severity`:

| Severity variant | Latency | Error rate |
|---|---|---|
| `none` (0) | +50ms | 0% |
| `low` (15) | +140ms | 15% |
| `high` (40) | +290ms | 40% |
| `critical` (75) | +500ms | 75% |

Dashboard uses `app.catalog.version` (set by the service) rather than
`feature_flag.result.variant` (set by the hook) to avoid contamination from the
severity flag evaluation on the same span.

---

## Demo 3 — Multi-model AI Summary 🔜 Separate PR

**Stage slot 2 (middle, optional) · ~3 min on stage · Maps to: cost/quality experimentation → incident response**

**Flag:** `productSummaryModel` (string)  
**Variants:** `off` (default) | `model-a` | `model-b`  
**Service:** `llm` (Python)

> Implementation coming in a separate PR on the demo repo.

### The story

*"Compare two AI models on latency and quality. Kill the bad one instantly.
Same flag, same SemConv — the infrastructure we just used for canary
rollout powers experimentation and incident response too."*

### What it will show

1. Baseline on `model-a` — latency and cost metrics per variant in Grafana
2. Flip to `model-b` — metrics shift, `model-b` spans carry `feature_flag.result.variant=model-b`
3. Enable `llmRateLimitError=on` — errors isolated to `model-b` cohort
4. Kill switch — flip flag to `off`, errors stop immediately

### Still to implement (next PR)

- Branch behaviour in `llm/app.py` based on `productSummaryModel` variant (different simulated latency + token cost per model)
- Per-variant metrics: token cost, request count, error rate
- Grafana dashboard row for Demo 3
- Stage runbook section in `demo-runbook.md`

### Key slide moment

This is the **bridge** between the release-safety opener (Demo 2) and the
business-impact closer (Demo 1): show that the exact same SemConv
attributes (`feature_flag.key`, `feature_flag.result.variant`) we just
used for canary observation now power experimentation and the incident
kill switch. One open standard, all use cases.

---

## Demo 1 — Recommendation Algorithm A/B Test ✅ Implemented

**Stage slot 3 (climax) · ~4 min on stage · Maps to: Experimentation / tracking — the business-impact closer**

**Flag:** `recommendationAlgorithm` (string)  
**Variants:** `popularity` (default) | `collaborative` | `personalized`  
**Service:** `recommendation` (Python)  
**Targeting:** `userTier=premium` → `personalized`; rest: 50/50 fractional

### The story

*"We've seen the same hook power release safety and AI incident response.
Now: is the new recommendation model actually making money?"*

Closing line of the whole talk: *"Personalized recommendations drive 5×
larger baskets. The checkout service has no idea the flag exists. One
open standard. All use cases."*

### What it shows

1. **The hook in Jaeger** — Open any recommendation trace. The `feature_flag.evaluation`
   span event is there: `key`, `variant`, `reason`. Already familiar from
   the earlier demos. One line: `api.add_hooks([TracingHook()])`.

2. **Per-variant Grafana panels** — Impressions by variant (stacked), p95
   latency by variant (`personalized` is visibly higher — the model is heavier).
   Collector transform + spanmetrics connector + Prometheus. All YAML.

3. **Average Order Value correlation** — The recommendation service logs `app.user.id` and
   `app.recommendation.algorithm`. The checkout service logs `app.user.id`
   and `app.order.amount`. OpenSearch PPL joins them on the session ID.
   Premium users (`personalized`) buy larger baskets → ~5× higher AOV.
   *"The checkout service has no idea the recommendation flag exists."*

4. **Live rollout** — Flip `recommendationAlgorithm` defaultVariant to
   `personalized` in flagd-ui. Dashboard shifts in ~30 seconds. No deploy.

### Key slide moments

- **"One line"** — show the `TracingHook` registration in `recommendation_server.py`
- **"All YAML"** — show the 6-line collector transform in `otelcol-config.yml`
- **"The checkout service doesn't know"** — show the PPL query in OpenSearch

### OpenFeature concepts demonstrated

- `EvaluationContext` with `userTier` (deterministically derived from session ID)
- `TracingHook` — global hook, zero per-evaluation code
- Fractional targeting with user-consistent assignment

---

## How SemConv attributes get on every span

```python
# Python services (recommendation, llm)
api.add_hooks([TracingHook()])
```

```go
// Go services (product-catalog)
openfeature.AddHooks(otelhooks.NewTracesHook())
```

After that, every flag evaluation automatically attaches to the active span:

```
feature_flag.key            = "recommendationAlgorithm"
feature_flag.result.variant = "personalized"
feature_flag.provider_name  = "flagd"
```

The collector's `transform/sanitize_spans` processor promotes these from
**span events** to **span attributes** so the spanmetrics connector can use
them as Prometheus metric dimensions.

This is the *"OpenFeature contributed SemConv to OpenTelemetry"* point made
concrete in a live system.

---

## Slide suggestions per demo

Listed in **stage order** (slot 1 → slot 2 → slot 3).

### Stage slot 1 — Demo 2 slides (Canary rollout, opener)

| Slide | Content |
|---|---|
| "The problem" | Traditional canary: custom dashboards, manual correlation |
| "The solution" | One flag + TracingHook = automatic observability |
| "The regression" | Screenshot of p95 panel: yellow line rising, red errors appearing |
| "The rollback" | Screenshot: panels recovering within 30 seconds |
| "The punchline" | *"No deploy. No restart. The flag key was already on every span."* |

### Stage slot 2 — Demo 3 slides (Multi-model AI, optional middle)

> Once implemented.

| Slide | Content |
|---|---|
| "Multi-model" | Two models, same hook, same SemConv |
| "Cost vs quality" | Latency and token cost visible per variant |
| "Kill switch" | Incident response without redeploy |
| "Same substrate" | One hook just powered release safety; now it powers experimentation and incident response |

### Stage slot 3 — Demo 1 slides (Recommendation A/B + AOV, climax)

| Slide | Content |
|---|---|
| "The contract" | Show the one-line hook registration |
| "What you get" | Screenshot of Jaeger span event with `feature_flag.*` attributes |
| "No code" | Show the 6-line collector YAML transform |
| "Is it making money?" | Show the Grafana AOV table — personalized 5× higher |
| "The answer" | Quote: *"Personalized recommendations drive larger baskets — and the only telemetry code we wrote was one line."* |
