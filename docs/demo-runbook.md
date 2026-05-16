# Demo Runbook — Stage Guide

This is the complete on-stage runbook for the three demos in the
**"Observability and Feature Flagging"** talk.

The demo runs on the
[OpenTelemetry astronomy shop fork](https://github.com/alexandraoberaigner/opentelemetry-demo),
branch `feat/talk-demo`. Flag definitions live in
`src/flagd/demo.flagd.json` in that repo.

---

## Setup (before going on stage)

### Prerequisites

```bash
brew install k6          # load generator
docker desktop running   # full stack needs ~4 GB RAM
```

### Start the stack

```bash
git clone https://github.com/alexandraoberaigner/opentelemetry-demo
cd opentelemetry-demo
git checkout feat/talk-demo
make start               # builds + starts all containers (~2 min)
make loadgen-background  # warms up services (Ctrl+C when dashboard shows data)
```

### Open four browser tabs

| Tab | URL | Purpose |
|---|---|---|
| Webshop | http://localhost:8080 | The astronomy shop |
| **Grafana** | http://localhost:8080/grafana/ → "Feature Flag — Observability Dashboard" | Main demo screen |
| Jaeger | http://localhost:8080/jaeger/ui | Trace drill-down |
| **flagd-ui** | http://localhost:8080/feature/ | Live flag changes |

### Load generator commands

```bash
make loadgen-background  # warm up (run while setting up, Ctrl+C when ready)
make loadgen-demo1       # Demo 1 traffic — recommendation + checkout (run continuously)
make loadgen-demo2       # Demo 2 traffic — product catalog canary (run continuously)
```

### Validation checklist

- [ ] `make start` completes, all containers healthy
- [ ] Jaeger shows `feature_flag.evaluation` span events on `recommendation` spans
- [ ] Grafana dashboard has data in all panels (wait ~2 min after `make loadgen-demo1`)
- [ ] flagd-ui targeting edits propagate within ~5 seconds
- [ ] AOV table shows `personalized` significantly higher than `popularity`
- [ ] **All flags reset to defaults** before stepping on stage

### Flag reset commands (run before every demo)

```python
# In the opentelemetry-demo directory:
python3 -c "
import json
with open('src/flagd/demo.flagd.json') as f: d=json.load(f)
d['flags']['productCatalogCanary']['targeting']['fractional'][1] = ['v1', 95]
d['flags']['productCatalogCanary']['targeting']['fractional'][2] = ['v2', 5]
d['flags']['productCatalogV2Severity']['defaultVariant'] = 'none'
d['flags']['recommendationAlgorithm']['defaultVariant'] = 'popularity'
with open('src/flagd/demo.flagd.json','w') as f: json.dump(d, f, indent=4)
print('All flags reset to defaults')
"
```

---

## Demo 1 — Recommendation A/B Test (~4 min)

**Story:** *"We added a feature flag. We wrote zero telemetry code. Let's see what we get for free."*

**Run first:** `make loadgen-demo1` (keep running throughout Demo 1)

### Act 1 — The hook in Jaeger (1 min)

1. Open **Jaeger** → Service: `recommendation` → Find Traces
2. Click any `oteldemo.RecommendationService/ListRecommendations` trace
3. Expand the span → **Events** tab → show `feature_flag.evaluation` event:
   - `feature_flag.key = recommendationAlgorithm`
   - `feature_flag.result.variant = popularity` (or `personalized`)
4. *"One line of code: `api.add_hooks([TracingHook()])`. That's it. The hook did the rest."*

### Act 2 — Per-variant metrics (1 min)

1. Switch to **Grafana** → "Feature Flag — Observability Dashboard" (top section)
2. Walk through the panels:
   - **Recommendation Impressions by Variant** — traffic split between `popularity` and `personalized`
   - **p95 Latency by Variant** — `personalized` higher (~100ms vs ~20ms)
3. *"Span events → collector transform → spanmetrics → Prometheus → Grafana. All YAML, no code."*

### Act 3 — Business impact: AOV by variant (1 min)

1. Scroll to the **AOV by Recommendation Variant** table
2. Show: `personalized` has ~5× higher average order value
3. *"Recommendation service logs the session ID and variant. Checkout service logs the same session ID and order amount. OpenSearch joins them with a PPL query. The checkout service has no idea the recommendation flag exists."*

### Act 4 — Live rollout change (1 min)

1. **flagd-ui** → `recommendationAlgorithm` → change `defaultVariant` to `personalized`
2. Wait ~30 seconds → Grafana: impressions panel shifts to 100% `personalized`
3. *"No deploy. No restart. The flag changed — and every span automatically reflects the new variant. That's the SemConv payoff."*

**Reset:** set `recommendationAlgorithm` `defaultVariant` back to `popularity`.

---

## Demo 2 — Product Catalog Progressive Rollout (~3 min)

**Story:** *"Safe canary release — observable regression, instant rollback. No code change required."*

**Run first:** `make loadgen-demo2` (keep running throughout Demo 2)

### How v2 works

Two flags give full on-stage control:

| Flag | Default | Role |
|---|---|---|
| `productCatalogCanary` | 95/5 (v1/v2) | Who gets v2 — fractional by session ID |
| `productCatalogV2Severity` | `none` | How bad v2 is: `none`=0% errors, `low`=15%, `high`=40%, `critical`=75% |

v2 always adds +50ms baseline latency. Severity escalates it.

### Step sequence

| Step | `productCatalogCanary` v2% | `productCatalogV2Severity` | Grafana shows |
|---|---|---|---|
| Baseline | 5% | `none` | Tiny yellow v2 blip, 0 errors |
| Step 1 | 25% | `none` | Yellow latency spike visible. Errors still zero — *"just slower"* |
| Step 2 | 25% | `low` | Errors start. *"Uh oh."* |
| Step 3 | 50% | `high` | More errors. *"Getting worse."* |
| Step 4 | 75% | `critical` | Errors dominate. *"SLO breach."* |
| Rollback | 5% | `none` | Both panels recover in ~30 seconds |

### Steps on stage

1. **Grafana** → scroll to Demo 2 row ("Product Catalog Progressive Rollout")
2. Confirm baseline: green v1 lines, tiny yellow v2 blip, zero errors
3. **flagd-ui** → `productCatalogCanary` → step v2 to 25%
   - p95 Latency panel: yellow line rises above green
   - Errors still zero — *"Hmm, slower but no errors yet"*
4. **flagd-ui** → `productCatalogV2Severity` → `low`
   - Red errors appear — *"There it is"*
5. Step to 50% canary + `high` severity
   - *"Getting worse — this is where an SLO alert would fire"*
6. Step to 75% canary + `critical` severity
   - *"Everything on fire — roll back"*
7. **Roll back**: canary to 5%, severity to `none`
   - Both panels recover within ~30 seconds
8. *"No deploy. No restart. The feature_flag.key is already on every span — that's the SemConv payoff."*

**Reset:** `productCatalogCanary` = 95/5, `productCatalogV2Severity` = `none`.

---

## Demo 3 — Multi-model AI Summary (~3 min) 🔜 Next PR

**Flag:** `productSummaryModel` · **Service:** `llm` (Python)

**Story:** *"Compare two AI models on latency and quality. Kill the bad one
instantly."*

> Implementation in a separate PR. Steps below are the planned flow.

### Steps

1. Show baseline traffic on `model-a` — latency and cost panels
2. **flagd-ui** → flip to `model-b` → watch metrics shift
3. Enable `llmRateLimitError=on` → errors appear, isolated to `model-b` spans
4. Flip flag to `off` (kill switch) → incident contained, no redeploy
5. *"The same SemConv attributes that power the A/B test also power the incident kill switch."*

---

## Timing guide

| Demo | Short | Full |
|---|---|---|
| **Demo 1** — Recommendation A/B | 2 min: Jaeger hook + dashboard panels | 4 min: + AOV table + live flag flip |
| **Demo 2** — Canary rollout | 2 min: baseline → 25% + low severity → rollback | 3 min: full escalation to critical |
| **Demo 3** — AI model kill switch | 2 min: flip to model-b + kill switch | 3 min: + error isolation per variant |

### By total time slot

| Total time | What to show |
|---|---|
| **4 min** | Demo 1 short |
| **5 min** | Demo 2 short |
| **6 min** | Demo 1 full |
| **7 min** | Demo 1 short + Demo 2 short |
| **9 min** | Demo 1 full + Demo 2 short |
| **10 min** | Demo 1 full + Demo 2 full |
| **12 min** | Demo 1 full + Demo 2 full + Demo 3 short |
| **13 min** | Demo 1 full + Demo 2 full + Demo 3 full |
