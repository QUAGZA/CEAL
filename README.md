# CEAL (Civic Emergency Access Layer)

CEAL is an **offline-first emergency response system** built around a BLE mesh protocol. It lets users trigger SOS events even in poor-network conditions, relays alerts across nearby devices, and syncs to a backend for coordination, escalation, and monitoring.

This repository contains a full multi-app stack:
- **Mobile app (Flutter):** SOS trigger, onboarding/KYC, BLE relay, offline queue, disaster reporting.
- **Backend API (Node.js + TypeScript + PostgreSQL):** SOS ingestion, onboarding, disaster verification, admin APIs.
- **Admin dashboard (React + Vite):** live events, users, disaster reports, status management.
- **Python QR service (FastAPI + OpenCV):** Aadhaar QR extraction from uploaded photos (optional companion service).

## Why CEAL

- Works in **low-connectivity scenarios** with BLE relay + store-and-forward behavior.
- Supports **identity onboarding and KYC** flows.
- Includes **SMS escalation hooks** (Twilio) for fallback notification.
- Provides **operational visibility** through an admin dashboard.

## Repository Structure

  | Path | Purpose |
|---|---|
| `backend/` | Express API, PostgreSQL migrations/repositories, auth, SOS/onboarding/disaster/admin routes, tests |
| `mobile/` | Flutter app with BLE scanner/advertiser, background relay, SOS UI, onboarding + settings |
| `admin-dashboard/` | React dashboard and protocol landing page |
| `python/` | FastAPI microservice for Aadhaar QR scan-from-photo pipeline |
| `docker-compose.yml` | Local Docker stack for backend + Postgres |
| `WHITEPAPER.md` | System and protocol design document |

## Tech Stack

- **Mobile:** Flutter, Riverpod, flutter_blue_plus, flutter_ble_peripheral, sqflite
- **Backend:** Node.js, TypeScript, Express, PostgreSQL (`pg`), Zod, JWT, Vitest
- **Dashboard:** React, TypeScript, Vite, React Router
- **Python Service:** FastAPI, OpenCV, NumPy

## Quick Start

### 1. Clone

```bash
git clone https://github.com/QUAGZA/CEAL.git
cd CEAL
```

### 2. Prerequisites

- Node.js 18+
- npm
- Docker + Docker Compose
- Flutter SDK (for mobile app)
- Python 3.12 (for optional QR service)

### 3. Environment setup

Copy and configure environment files:

```bash
# backend
cp backend/.env.example backend/.env

# dashboard
cp admin-dashboard/.env.example admin-dashboard/.env

# mobile
cp mobile/.env.example mobile/.env
```

At minimum, configure backend values for:
- `DATABASE_URL`
- `JWT_SECRET`
- `BLE_ENCRYPTION_SECRET`
- Twilio credentials (`TWILIO_*`) if SMS fallback is enabled

### 4. Run backend + Postgres (Docker, recommended)

```bash
docker compose up --build
```

Backend will be available at:
- `http://localhost:3000/`
- `http://localhost:3000/v1/health`

### 5. Run admin dashboard

```bash
cd admin-dashboard
npm install
npm run dev
```

Dashboard default URL:
- `http://localhost:5173`

### 6. Run mobile app

```bash
cd mobile
flutter pub get
flutter run
```

### 7. (Optional) Run Python QR service

```bash
cd python
python -m venv .venv
source .venv/bin/activate   # Windows PowerShell: .\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn app.app:app --host 0.0.0.0 --port 8001
```

Health endpoint:
- `http://localhost:8001/health`

## Backend API Surface (high level)

All primary backend routes are under `/v1`:
- `/v1/health`
- `/v1/auth`
- `/v1/onboarding`
- `/v1/sos`
- `/v1/users`
- `/v1/disaster`
- `/v1/admin`

## Testing

Backend tests:

```bash
cd backend
npm install
npm test
```

## Documentation

- Architecture and protocol details: [`WHITEPAPER.md`](./WHITEPAPER.md)

## Collaboration

This was a collaborative hackathon project, open sourced under the MIT License. Contributions, feedback, and improvements are welcome! Please open issues or pull requests as needed.
