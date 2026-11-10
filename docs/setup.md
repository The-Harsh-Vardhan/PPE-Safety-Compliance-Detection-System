---
layout: default
title: Setup
---

# Setup

Prerequisites: Python 3.11+, Node.js 18+ with pnpm, and `uv`. A CUDA GPU is recommended.

## Backend

Create your environment file from the committed template in `backend/` before starting (see the README quick start).

```bash
git clone https://github.com/The-Harsh-Vardhan/PPE-Safety-Compliance-Detection-System.git
cd PPE-Safety-Compliance-Detection-System/backend
uv sync
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

API docs: `http://localhost:8000/docs`

## Frontend

```bash
cd frontend
pnpm install
pnpm dev
```

UI: `http://localhost:3000`. Set `NEXT_PUBLIC_API_URL` (default `http://localhost:8000`) if the backend runs elsewhere. If the backend is unreachable the UI falls back to a demo mode with sample data.

## Model weights

Weights are not in the repository:

- SAM3: `backend/weights/sam3/sam3.pt`
- Custom YOLOv11 PPE detector: `backend/weights/ppe_detector/best.pt`

If you do not have SAM3 weights, disable SAM3 through the environment (`USE_SAM3`) or download them before running.

## Using it

Process an uploaded video:

```bash
curl -X POST http://localhost:8000/api/stream/upload -F "file=@lab_demo.mp4"
curl -X POST http://localhost:8000/api/stream/process \
  -H "Content-Type: application/json" \
  -d '{"video_path": "lab_demo.mp4", "save_output": true}'
```

Poll `GET /api/stream/jobs/{job_id}` and fetch the annotated video from `GET /api/stream/processed/{job_id}`.

Live monitoring: open `http://localhost:8000/api/stream/live/feed` for the annotated webcam MJPEG stream; alerts arrive on `ws://localhost:8000/api/ws`.

## Tuning

Common settings: `REQUIRED_PPE`, `DETECTION_CONFIDENCE_THRESHOLD`, `VIOLATION_CONFIDENCE_THRESHOLD`, `TEMPORAL_FUSION_STRATEGY`, `LIVE_STREAM_PROCESS_FPS`, `LIVE_STREAM_QUEUE_SIZE`, `MULTI_SCALE_ENABLED`, `USE_SAM3`, `USE_SAM2`, `DATABASE_URL`, `ENABLE_SNAPSHOT_CAPTURE`. See the [README troubleshooting section](https://github.com/The-Harsh-Vardhan/PPE-Safety-Compliance-Detection-System#troubleshooting) for out-of-memory, lag and no-detection fixes.
