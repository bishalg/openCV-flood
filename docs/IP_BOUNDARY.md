# Public vs Proprietary Boundary

> **Public repo**: [`openCV-flood`](https://github.com/bishalg/openCV-flood) — OpenCV AI Competition 2026 demo  
> **Private R&D**: separate closed-source monorepo (not mirrored here)

Yes: **local labeled product data, training loops, and learning algorithms for proprietary domains stay closed.**  
This public repo showcases **OpenCV-powered perception + agentic flood response**, not how we train product models.

---

## What is public (showcase freely)

| Category | Examples in this repo |
|---|---|
| **OpenCV usage** | Steger via `sepFilter2D`, ECC registration, Laplacian gates, VideoCapture, overlays, COOL benchmarks — [`opencv_showcase.md`](opencv_showcase.md) |
| **Flood science method** | Sentinel-2 provenance, dual controls, `flood_delta.json` schema, geospatial pack |
| **Classical calibration** | Documented \(\sigma\)/polarity **grid search** against weakly supervised masks (not a trained proprietary network) |
| **Agentic Vision IaC** | CDK stacks, Bedrock tool contracts, idempotency design |
| **Offline demos** | CCTV fixtures, `video_pipeline_demo`, `live_snapshot_demo`, Cesium twin |
| **Reproducible gates** | ctest, synthetic RMSE &lt; 0.1 px |

Judges should be able to answer: *What OpenCV tools? How does evidence become an alert?*

---

## What stays proprietary (do not publish)

| Category | Why it stays closed |
|---|---|
| **Product training data** | User palms, correction labels, private captures, internal holdout sets |
| **Training / learning algorithms** | Retrain loops, scorers, U-Net/VLM fine-tunes, label pipelines, active-learning queues |
| **Domain product packs beyond flood demo** | Palm crease product logic, mobile ASO, closed admin training dashboards |
| **Production secrets** | API keys, Apple/Google signing, Match cert repos, customer configs |
| **Unreleased models & weights** | Any fine-tuned checkpoints not licensed for open release |

Human-in-the-loop *idea* (“propose → human corrects → better proposals”) can be described at a **high level** for product vision.  
The **datasets, training code, and algorithms** that implement that loop for non-flood products remain private.

---

## Safe wording for Devpost / slides

**Do say**
- “Built on OpenCV 5: ECC co-registration, scale-space Steger ridges, Laplacian quality gates, VideoCapture demos, COOL on Graviton3.”
- “Flood parameters were selected via a documented classical grid search with spatial/temporal controls.”
- “Agent receives cryptographic evidence from the C++/OpenCV pipeline — it does not invent metrics.”

**Don’t say / don’t ship**
- Proprietary label counts, private image dumps, training hyperparameters for product models
- Internal training dashboards, palm product roadmaps, App Store packaging internals
- “Here’s our full training pipeline / loss / dataset” for closed domains

---

## Rule of thumb

```text
OpenCV APIs + flood method + agentic evidence loop  →  PUBLIC (this repo)
Product labels + train/retrain algorithms + mobile product IP  →  PRIVATE
```

When adding files to `openCV-flood`, ask: *Would a competitor clone this and steal our product learning loop?* If yes → keep it private.
