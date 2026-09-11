# Milestone Ledger — openCV-flood (CurvFlood)

> **Public hackathon demo** for OpenCV AI Competition 2026 (Powered by AWS)  
> **Repo**: https://github.com/bishalg/openCV-flood  
> **Updated**: 2026-09-11  

Private product R&D continues in a separate closed-source monorepo. This ledger only tracks what ships publicly for the flood / disaster-response demo.

---

## Achieved (showcase-ready)

| # | Milestone | Proof points |
|---|---|---|
| M1–M5 | Core C++20 Steger engine + evidence JSON + CLI | RMSE **0.0301 px** (&lt; 0.1 gate), overlays, `curv_cli` |
| M6 | Eval / benchmark tooling | `synth_generator`, `evaluate`, `benchmark`, ctest RMSE gate |
| M7 | Nepal glacier-flood geospatial pack | +**88.4%** surge · +**121.1%** width · spatial **0.151×** · temporal **0.905×** — [`docs/flood_nepal_2026.md`](flood_nepal_2026.md) |
| M8 | AWS Agentic Vision + COOL | Bedrock 3-node loop · DynamoDB idempotency · Graviton3 **−25.4% P50** / **−32.3% $/scene** — [`docs/devpost_submission.md`](devpost_submission.md) |
| M9 | Phase 1 static CCTV POC | `adapters/cctv` + `data/fixtures/cctv/` day/night/rain |
| M10 | Phase 2 recorded video | `CctvVideoSource` + `video_pipeline_demo` + fixture AVI |
| M11 | Phase 3 live HTTP snapshots | `CctvHttpSnapshot` + `live_snapshot_demo` (`mock://` + Caltrans) |
| M12 | Phase 4 Cesium 3D twin | `apps/cesium_viewer` + `tools/geojson_exporter.py` |

---

## Showcase commands

```bash
# Science core
cmake -B build -GNinja -DCMAKE_BUILD_TYPE=Release && cmake --build build -j
ctest --test-dir build --output-on-failure

# Satellite flood delta (needs local Sentinel tiles under data/flood_nepal_2026/)
python3 scripts/compute_flood_delta.py
python3 scripts/make_comparison.py

# Phase 2 — annotated highway video
./build/tools/video_pipeline_demo \
  --input data/fixtures/cctv_video/caltrans_i80_sample.avi \
  --output output/annotated_caltrans_i80.avi \
  --fps-stride 2 --side-by-side

# Phase 3 — presentation-safe live loop
./build/tools/live_snapshot_demo \
  --url mock://caltrans_i80_day --interval-sec 1 --duration-sec 5

# Phase 4 — digital twin
python3 tools/geojson_exporter.py
cd apps/cesium_viewer && npm install && npm run dev
```

Live demo slide pack + day-of runbook: [`docs/hackathon_presentation.md`](hackathon_presentation.md).

---

## Next phase (public)

| # | Focus | Exit criteria |
|---|---|---|
| **M13** | Phase 5 flood alert on twin | scheduled ingest → heuristics → alert banner on Cesium |
| **Robust W1** | Holdout GT + metric freeze | 20 flood/dry pairs, `docs/flood_baseline.md`, sweep log |
| **Robust W2** | SAR + river CCTV + cloud smoke | Enhanced Lee SAR path, pier waterline, `cdk synth`, tag `flood-v1.0` |

Day-by-day plan: [`docs/flood_robust_roadmap.md`](flood_robust_roadmap.md).  
Realtime architecture: [`docs/flood_realtime_architecture.md`](flood_realtime_architecture.md).

---

## Intentionally not in this public repo

- Proprietary mobile product apps, ASO listings, and closed training dashboards
- Non-flood domain packs beyond the geospatial flood pack needed for the competition demo
