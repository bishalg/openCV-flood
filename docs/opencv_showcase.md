# OpenCV Showcase — What We Used & How (Public Demo)

> **Repo**: [`openCV-flood`](https://github.com/bishalg/openCV-flood) · OpenCV AI Competition 2026  
> **Purpose**: Highlight modern OpenCV 5 / COOL capabilities that power the flood demo.  
> **Companion**: IP boundary — [`docs/IP_BOUNDARY.md`](IP_BOUNDARY.md)

This page is the **judge-facing OpenCV story**: which modules we lean on, where they live in the repo, and how they chain into a reproducible disaster-response pipeline.

---

## 1. Stack at a glance

| Layer | OpenCV surface | Role in CurvFlood |
|---|---|---|
| **OpenCV 5.0** (desktop) | `core`, `imgproc`, `imgcodecs`, `videoio`, `highgui`, `dnn` (build link) | Perception + I/O + demos |
| **COOL on Graviton3** | Same APIs, Arm-optimized build (`NEON` / `SVE`) | Latency & $/scene award path |
| **Python `cv2`** | ECC, ORB, HSV, I/O | Flood registration + comparison panels |
| **C++20 engine** | `sepFilter2D`, `parallel_for_`, `Laplacian`, geometry | Sub-pixel Steger ridges |

We treat OpenCV as the **primary scientific toolkit**, not a thin wrapper around a black-box model.

---

## 2. OpenCV tools we showcase (latest & greatest for this track)

### A. Scale-space derivatives — `cv::sepFilter2D` + custom kernels
**Where:** `core/src/ridge/StegerRidgeExtractor.cpp`  
**What:** Separable Gaussian / first / second derivative kernels → \(r_x, r_y, r_{xx}, r_{xy}, r_{yy}\).  
**Why it matters:** Classic Steger line extraction on OpenCV primitives; no GPU DNN required for the disaster signal.

### B. Parallel pixel work — `cv::parallel_for_`
**Where:** Steger Hessian loop + `tools` benchmark harness  
**What:** Multi-threaded eigenvalue / Taylor zero-crossing per row range.  
**Why it matters:** Same code path profiles cleanly on x86 vs **COOL + Graviton3** (`scripts/benchmark_cool_vs_x86.sh`).

### C. Sub-pixel co-registration — `cv2.findTransformECC`
**Where:** `scripts/check_registration.py`  
**What:** `MOTION_TRANSLATION` ECC; ORB+RANSAC fallback; warp if \(\|\mathbf{t}\| > 3\) px.  
**Result on Nepal tiles:** **0.154 px** shift (gate ≤ 3.0 px).

### D. Quality gate — `cv::Laplacian` + `meanStdDev`
**Where:** `core/src/quality/LaplacianQualityAnalyzer.cpp`  
**What:** Blur / usability gate before ridge extraction (CCTV + CLI).  
**Why it matters:** Rejects unusable frames instead of hallucinating channels.

### E. Color / cloud logic — `cvtColor` + HSV thresholds
**Where:** flood delta scripts + geospatial pack path  
**What:** Valid-alpha masks; HSV cloud exclusion (\(S < 30 \land V > 200\)).  
**Why it matters:** Keeps monsoon cloud from looking like flood water.

### F. Geometry — `getPerspectiveTransform`, `warpAffine`, `pointPolygonTest`
**Where:** `core/src/geometry/RoiWarper.cpp`, `ZoneIntersector.cpp`  
**What:** Canonical warps + polygon membership for zone metrics.  
**Why it matters:** Domain packs stay thin; geometry stays in OpenCV/`core`.

### G. Video & live I/O — `cv::VideoCapture` / `VideoWriter` / `imdecode`
**Where:** `adapters/cctv/*`, `video_pipeline_demo`, `live_snapshot_demo`  
**What:** File AVI → annotated MJPG/MP4; HTTP JPEG decode; HLS/RTSP via `CAP_FFMPEG`.  
**Why it matters:** Gradual Stage 1→3 story judges can run offline with `mock://`.

### H. Visualization — anti-aliased overlays (`LINE_AA`, `putText`, panels)
**Where:** `OverlayRenderer`, `scripts/make_comparison.py`, Devpost thumbnail  
**What:** 16× sub-pixel ridge overlays, PRE/POST comparison boards.  
**Why it matters:** Evidence is visible, not only JSON.

### I. Cloud-Optimized OpenCV Library (COOL)
**Where:** `docs/aws_benchmark_guide.md`, `scripts/benchmark_cool_vs_x86.sh`  
**What:** Same C++ extraction on Graviton3 with verified SIMD flags.  
**Headline:** **−25.4% P50**, **−32.3% $/scene**, **+47.8% scenes per $**.

---

## 3. How we did it (public method — not proprietary training)

```text
Sentinel-2 L2A tiles (CDAS)
        │
        ▼
OpenCV ECC co-registration  ──►  0.154 px shift
        │
        ▼
HSV + alpha valid masks     ──►  exclude clouds / nodata
        │
        ▼
Steger via sepFilter2D + parallel_for_  ──►  ridge graph
        │
        ▼
GeospatialFloodPack metrics  ──►  width / surge / controls
        │
        ▼
flood_delta.json → S3 → Bedrock Agent (getFloodEvidence → dispatchFloodAlert)
```

**Calibration style used in the public demo (classical CV):**
1. Grid search \(\sigma \times\) polarity against **weakly supervised** color masks (not a learned network).
2. Freeze dual-polarity union in `configs/geospatial_wide_ridge.json`.
3. Enforce dual controls (spatial + temporal) so the LLM cannot invent a disaster.
4. Gate science with synthetic RMSE (`evaluate` < 0.1 px) and ctest.

That is **OpenCV parameter science + evidence gating**, not a closed training recipe for a product model.

---

## 4. Reproduce the OpenCV story locally

```bash
cmake -B build -GNinja -DCMAKE_BUILD_TYPE=Release && cmake --build build -j
ctest --test-dir build --output-on-failure

# Registration + flood delta (requires local tiles under data/flood_nepal_2026/)
python3 scripts/check_registration.py
python3 scripts/compute_flood_delta.py
python3 scripts/make_comparison.py

# OpenCV video / live showcase
./build/tools/video_pipeline_demo \
  --input data/fixtures/cctv_video/caltrans_i80_sample.avi \
  --output output/annotated_caltrans_i80.avi --fps-stride 2 --side-by-side

./build/tools/live_snapshot_demo --url mock://caltrans_i80_day --interval-sec 1 --duration-sec 5

# COOL path (on Graviton or dry-run)
./scripts/benchmark_cool_vs_x86.sh --dry-run
```

---

## 5. Talking points for judges (30 seconds)

1. **OpenCV 5 is the perception core** — Steger on `sepFilter2D` + `parallel_for_`, not a single DNN call.  
2. **ECC gives sub-pixel honesty** — 0.154 px before we trust PRE/POST deltas.  
3. **COOL proves efficiency** — same API, Arm SIMD, measurable cost win.  
4. **Agentic Vision consumes OpenCV evidence** — Bedrock never invents metrics; DynamoDB tokens gate alerts.  
5. **Product training stays private** — see [`IP_BOUNDARY.md`](IP_BOUNDARY.md).

---

## 6. Module → file cheat sheet

| OpenCV API / product | Primary files |
|---|---|
| `sepFilter2D`, Hessian path | `core/src/ridge/StegerRidgeExtractor.cpp` |
| `parallel_for_` | Steger + `tools/lib/curv/tools/benchmark_lib.cpp` |
| `Laplacian` | `core/src/quality/LaplacianQualityAnalyzer.cpp` |
| `findTransformECC` / ORB | `scripts/check_registration.py` |
| `VideoCapture` / `VideoWriter` | `adapters/cctv/`, `tools/*video*` |
| `imdecode` / HTTP JPEG | `adapters/cctv/src/CctvHttpSnapshot.cpp` |
| Overlay / `LINE_AA` | `adapters/opencv/src/OverlayRenderer.cpp` |
| COOL benchmark | `scripts/benchmark_cool_vs_x86.sh` |
