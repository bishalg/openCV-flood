# openCV-flood (CurvFlood)

> **Sub-Pixel AI Glacier Flood Early Warning & Autonomous Cloud Response**  
> *Submission for OpenCV AI Competition 2026 (Powered by AWS) — Real-World Impact Track*

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![C++ Standard: C++20](https://img.shields.io/badge/Language-C%2B%2B20-orange.svg)](CMakeLists.txt)
[![OpenCV: Version 5](https://img.shields.io/badge/OpenCV-5.0.0-5C3EE8.svg)](CMakeLists.txt)
[![AWS Bedrock: Claude 3.5 Sonnet](https://img.shields.io/badge/AWS%20Bedrock-Claude%203.5%20Sonnet%20v2-FF9900.svg)](aws_infra/)
[![AWS Graviton: COOL Accelerated](https://img.shields.io/badge/AWS%20Graviton3-COOL%20Optimized-green.svg)](scripts/benchmark_cool_vs_x86.sh)
[![Devpost](https://img.shields.io/badge/Devpost-OpenCV%20AI%202026-003E54.svg)](https://opencv26.devpost.com/)

---

## Competition

**[OpenCV AI Competition 2026, powered by AWS](https://opencv26.devpost.com/)**  
Build vision systems that see, reason, and act with OpenCV 5 and Amazon Web Services (AWS). Win money and bragging rights!

This repository is our public Real-World Impact track demo for that competition.

---

## 1. Project Overview

On **26 August 2026**, a glacier collapse in the Langtang Himal initiated a catastrophic debris flood down the **Lende Khola and Bhote Koshi rivers in Nepal**. Mountain floods are notorious for destroying downstream communities before conventional monitoring can react. Standard optical satellite systems and simple pixel-difference thresholds fail in mountain terrain due to monsoon cloud cover, steep terrain shadows, and turbid sediment.

**openCV-flood (CurvFlood)** solves this by combining:
1. **Mathematical sub-pixel curvilinear ridge perception** (C++20 Steger line extractor, `< 0.0301 px` RMSE).
2. **Quantitative multi-temporal satellite flood delta analysis** (+88.4% flood surge, +121.1% channel widening, dual controls).
3. **Autonomous, evidence-gated cloud decision loops** (AWS Bedrock Claude 3.5 Sonnet v2 with DynamoDB idempotency).
4. **Cloud-Optimized vector acceleration** (Cloud-Optimized OpenCV Library on AWS Graviton3).

**OpenCV showcase (what & how):** [`docs/opencv_showcase.md`](docs/opencv_showcase.md)  
**Public vs proprietary boundary:** [`docs/IP_BOUNDARY.md`](docs/IP_BOUNDARY.md)

![Devpost Submission Thumbnail](docs/assets/devpost_thumbnail.jpg)

---

## 2. System Architecture

```mermaid
graph TD
    subgraph Sensing ["Data Ingestion & Pre-Processing"]
        S2["Sentinel-2 L2A 10m Imagery (Pre & Post Event)"] --> ECC["Sub-Pixel ECC Co-Registration (0.154 px Shift)"]
        ECC --> CloudMask["HSV Cloud Exclusion & Valid-Pixel Alpha Masking"]
    end

    subgraph Perception ["Core OpenCV Perception Engine (C++20)"]
        CloudMask --> Steger["Steger Sub-Pixel Ridge Extractor (Hessian Scale-Space)"]
        Steger --> Delta["Geospatial Flood Pack: Channel Width & Delta Quantification"]
        Delta --> Evidence["evidence.json & flood_delta.json"]
    end

    subgraph AWSCloud ["Autonomous Agentic Cloud Response (AWS CDK v2)"]
        Evidence --> S3["Amazon S3 Flood Data Bucket"]
        S3 --> Trigger["S3 Event Lambda Router"]
        Trigger --> Bedrock["AWS Bedrock Agent (Claude 3.5 Sonnet v2)"]
        Bedrock --> Tool1["Perception Tool: getFloodEvidence (Issues Token)"]
        Tool1 --> Dynamo["Amazon DynamoDB (Server-Side Idempotency Store)"]
        Dynamo --> Bedrock
        Bedrock --> Tool2["Action Tool: dispatchFloodAlert (Validates Token)"]
        Tool2 --> SNS["Amazon SNS (Disaster Alert Broadcast) & SQS"]
        Tool2 --> Audit["Dedicated Amazon S3 Audit Bucket (Immutable Receipts)"]
    end
```

---

## 3. Categories

### Use of COOL
We profiled 100 continuous iterations of Steger curvilinear extraction on full-resolution ($893 \times 1172$ px) disaster scenes, comparing **AWS Graviton3 (`c7g.2xlarge`) + COOL** against an Intel Sapphire Rapids (`c7i.2xlarge`) + Standard OpenCV baseline:

| Metric | AWS Graviton3 + COOL | Intel x86 + Standard OpenCV | Advantage |
|---|---|---|---|
| **P50 Execution Latency** | **185.3 ms** | 248.5 ms | **-25.4% faster** |
| **P95 Tail Latency** | **192.1 ms** | 271.3 ms | **-29.2% faster** |
| **Cost Per Disaster Scene** | **$0.00001685** | $0.00002490 | **-32.3% cheaper** |
| **Throughput Per $1.00** | **59,357 scenes** | 40,160 scenes | **+47.8% throughput** |

- **Hardware SIMD Intrinsics**: Verified via `cv2.getBuildInformation()`, confirming active Arm `NEON_DOTPROD`, `NEON_FP16`, and `SVE` vector intrinsics compiled with `-mcpu=neoverse-v1`.
- **Reproducibility**: Run `./scripts/benchmark_cool_vs_x86.sh` (see [docs/aws_benchmark_guide.md](docs/aws_benchmark_guide.md)).

### Agentic Vision
Rather than asking an LLM to blindly summarize pre-digested numbers, CurvFlood enforces a verifiable **Perception $\to$ Reasoning $\to$ Action** loop:
- **Perception (`getFloodEvidence`)**: Triggered with only an `eventId`. The agent autonomously calls the tool, inspects flood metrics, and receives a cryptographic `evidenceToken`.
- **Reasoning Policy**: Claude 3.5 Sonnet v2 acts as a strict policy enforcer, evaluating flood thresholds (`flood_signal == true`, `width_ratio >= 2.0`, controls $< 1.10$).
- **Action (`dispatchFloodAlert`)**: Requires the valid `evidenceToken`. AWS Lambda validates the token and checks DynamoDB server-side idempotency, eliminating duplicate alerts on S3 retries, writes immutable receipts to an isolated audit bucket, and broadcasts via Amazon SNS/SQS.

---

## 4. Quantitative Disaster Analysis (Nepal 2026 Event)

Full scientific provenance and validation methodology documented in [`docs/flood_nepal_2026.md`](docs/flood_nepal_2026.md):

| Metric | Baseline PRE (2026-08-24) | Post-Event (2026-08-27) | Delta / Ratio | Status |
|---|---|---|---|---|
| **Sub-Pixel Registration Shift** | — | — | **0.154 px** | **Passes $\le 3.0$ px** |
| **Median Active Channel Width** | 19.0 px (190 m) | 42.0 px (420 m) | **+121.1% (2.211x)** | **Passes $\ge 1.25$** |
| **Flood Signature Fraction** | 0.0528 | 0.0995 | **+88.4% (1.884x)** | **Passes $\ge 1.10$** |
| **Spatial Control (West Slope)** | 0.0594 | 0.0090 | **0.151x** | **Passes $< 1.10$ (Localized)** |
| **Temporal Control (12 Aug vs 24 Aug)**| 19.0 px | 21.0 px | **0.905x** | **Passes $< 1.10$ (Nominal)** |
| **Scientific RMSE Accuracy** | Synthetic Truth | Steger Extraction | **0.0301 px** | **Passes $< 0.1$ px Gate** |

---

## 5. Milestone Status (hackathon progress)

Full ledger: [`docs/MILESTONES.md`](docs/MILESTONES.md) · OpenCV tools: [`docs/opencv_showcase.md`](docs/opencv_showcase.md) · presentation: [`docs/hackathon_presentation.md`](docs/hackathon_presentation.md)

- [x] **M1–M6** — C++20 Steger core, evidence JSON, CLI, RMSE &lt; 0.1 px gate, benchmarks
- [x] **M7** — Nepal flood geospatial pack (+88.4% surge, +121.1% width, dual controls)
- [x] **M8** — AWS Bedrock Agentic Vision + COOL on Graviton3
- [x] **M9** — Phase 1 static CCTV fixtures (`data/fixtures/cctv/`)
- [x] **M10** — Phase 2 recorded video pipeline (`video_pipeline_demo`)
- [x] **M11** — Phase 3 live HTTP snapshots (`live_snapshot_demo`, `mock://` safe mode)
- [x] **M12** — Phase 4 CesiumJS 3D digital twin (`apps/cesium_viewer`)
- [ ] **M13** — Phase 5 flood-alert production demo on the twin
- [ ] **Next** — Robust holdout + SAR/CCTV waterline week (see [`docs/flood_robust_roadmap.md`](docs/flood_robust_roadmap.md))

Realtime multi-source architecture: [`docs/flood_realtime_architecture.md`](docs/flood_realtime_architecture.md).

---

## 6. Repository Structure

```text
openCV-flood/
├── cmake/               # Compiler warnings (-Werror), toolchains, sanitizers
├── core/                # Domain-agnostic C++20 perception core (Hessian math, Steger lines)
├── domains/             # Geospatial flood pack (GeospatialFloodPack)
├── adapters/            # C-ABI, OpenCV overlays, CCTV frame/video/HTTP sources
├── apps/
│   ├── desktop_pipeline_demo/
│   └── cesium_viewer/   # Phase 4 3D twin (Vite + Cesium)
├── aws_infra/           # AWS CDK v2 Python IaC (Storage, Alert, IAM, Agent stacks)
├── tools/               # curv_cli, evaluate, video_pipeline_demo, live_snapshot_demo, …
├── data/
│   ├── flood_nepal_2026/  # metadata tracked; tiles download locally
│   └── fixtures/          # CCTV day/night/rain + short AVI demos
├── docs/                # Science write-up, Devpost, milestones, roadmaps
├── scripts/             # Benchmark suite, delta computation, formatting
└── tests/               # GoogleTest unit suite
```

---

## 7. Build & Verification

### Prerequisites
- Modern C++20 compiler (Clang 16+, GCC 13+, or Apple Clang)
- CMake 3.25+ and Ninja
- OpenCV 4.x or OpenCV 5.x (with `videoio` / `highgui`)
- libcurl (for live HTTP snapshot demos)
- Node 18+ (optional, Cesium twin)

### Build & Run Tests
```bash
cmake -B build -GNinja -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
ctest --test-dir build --output-on-failure
```

### Showcase demos
```bash
# Satellite flood delta (local tiles under data/flood_nepal_2026/)
python3 scripts/compute_flood_delta.py
python3 scripts/make_comparison.py

# Phase 2 — annotated recorded video
./build/tools/video_pipeline_demo \
  --input data/fixtures/cctv_video/caltrans_i80_sample.avi \
  --output output/annotated_caltrans_i80.avi \
  --fps-stride 2 --side-by-side

# Phase 3 — presentation-safe live loop
./build/tools/live_snapshot_demo \
  --url mock://caltrans_i80_day --interval-sec 1 --duration-sec 5

# Phase 4 — 3D twin
python3 tools/geojson_exporter.py
cd apps/cesium_viewer && npm install && npm run dev
```

### Deploy AWS Cloud Infrastructure (Optional)
```bash
cd aws_infra
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements-cdk.txt
cdk synth
```

---

## 8. License

Licensed under the [Apache License, Version 2.0](LICENSE).

> This repository is the **public OpenCV AI Competition 2026 demo**. Broader proprietary product work is maintained separately and is not mirrored here.
