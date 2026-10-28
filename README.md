# GeoGuard — Disaster Monitoring Center

GeoGuard is a dark control-room style disaster-monitoring dashboard with a React frontend, Flask API, and SQLite sample-data store. Its built-in simulation supports scenario changes, playback speed, pause/resume, reset, demo fault events, map selection/zoom, alert acknowledgement, theme switching, and multilingual advisory templates. The MVP is designed for local development and demonstration: sensor readings, alert history, maps, risk scores, and advisory text are illustrative and are **not** operational forecasts or emergency instructions.

## Quick start

Requirements: Node.js 18+, npm, and Python 3.10+.

### 1. Start the API

From the project root, create a virtual environment and install dependencies:

```powershell
py -m venv backend\.venv
backend\.venv\Scripts\Activate.ps1
pip install -r backend\requirements.txt
python -m backend.app
```

The API listens on `http://localhost:5000`. It initializes `backend/instance/geoguard.db` and inserts a small example dataset on first launch.

### 2. Start the dashboard

In a second terminal:

```powershell
cd frontend
npm install
npm run dev
```

Open the Vite URL shown in the terminal (normally `http://localhost:5173`). The dashboard also works without the API by using clearly labeled sample readings. To configure another API address, copy `frontend/.env.example` to `frontend/.env` and set `VITE_API_BASE_URL`.

### 3. Run tests and build

From the project root:

```powershell
pip install -r backend\requirements.txt
pytest
cd frontend
npm run build
```

## What is included

- Overview dashboard with trend chart, Google Maps location view, alerts, weather snapshot, and sensors.
- Google Maps search and Roadmap, Satellite, Hybrid, and Terrain views on the dashboard and live-monitoring page, with zoom and browser geolocation controls. Maps require an internet connection; current location requires browser location permission.
- Navigation for live monitoring, risk analysis, alerts, sensor management, reports, and settings.
- Flask endpoints for health, sensor readings, alerts, risk summaries, and dashboard data.
- SQLite schema and sample seed records.
- Deterministic, transparent risk estimate endpoint for integration prototyping.
- Initial IoT and model examples under `iot/` and `ai_models/`.

## API endpoints

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/api/health` | API health |
| GET | `/api/dashboard` | Combined sample dashboard data |
| GET | `/api/sensors` | Sensor list |
| GET | `/api/alerts` | Alert list |
| GET | `/api/risk/summary` | Illustrative regional risk summary |
| POST | `/api/risk/estimate` | Estimate from `rainfall_mm`, `river_level_m`, and `seismic_magnitude` |

The estimate endpoint accepts non-negative numeric values and returns a 0–100 score with a risk label. This simple formula has not been calibrated or validated for real-world use.

## Before operational use

Connect authenticated devices and validated ingestion, replace the schematic map and sample weather source, calibrate models using locally verified datasets, implement authorization and audit controls, configure tested notification channels, and obtain review from disaster-management authorities. Never use this starter dashboard as the sole basis for evacuation or other safety decisions.


## Environmental workspace updates

The current branch includes an additive sensor-ingestion API, SQLite observation history, a local preferences API, an AI assistant with a deterministic fallback, and a configurable prototype risk indicator. Existing pages and API endpoints were retained where possible.

### Additional API routes

- `POST /api/sensors/ingest` — validate and store supported readings.
- `GET /api/sensors/history?limit=100` — retrieve recent stored observations.
- `POST /api/risk/indicator` — transparent configurable demonstration heuristic using rainfall, soil moisture, tilt, and vibration.
- `POST /api/assistant/chat` — data-grounded explanation; optional LLM requires backend-only `LLM_API_KEY`, `LLM_BASE_URL`, and `LLM_MODEL`.
- `GET /api/preferences` and `PUT /api/preferences` — persist local workspace settings.

Example sensor submission (example values only):

```bash
curl -X POST http://localhost:5000/api/sensors/ingest \
  -H "Content-Type: application/json" \
  -d '{"device_id":"ESP32_A1","data_source":"simulation","readings":{"rainfall_mm":12.5,"soil_moisture_pct":67.0}}'
```

### Honest implementation status

- **Implemented in code:** additive SQLite schema, input validation, sensor-reading ingestion/history endpoints, preference persistence, assistant endpoint with deterministic fallback, optional OpenAI-compatible server-side LLM call, prototype indicator endpoint, history CSV export UI.
- **Demo/sample only:** existing dashboard charts, sample sensor inventory, seeded alerts, several regional risk cards.
- **Not verified here:** backend pytest suite and frontend production build; dependencies could not be installed because package-index network access was unavailable in the execution environment.
- **Hardware not integrated:** existing ESP32 sketch still emits placeholder serial data. No real ESP32 telemetry, calibration, or sensor wiring has been tested.
- **Not validated:** no real-world landslide prediction, no genuine DNN implementation, no scientifically validated risk score. The current model experiment is a Random Forest baseline despite its legacy `dnn/` directory name.

See `docs/MODIFICATION_REPORT.md` for reused/changed/new files, compatibility notes, and verification limitations.
