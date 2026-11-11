# EstateView 3.0 Platform Engine

A full-stack, enterprise-grade real estate platform engineered with **FastAPI**, **PostgreSQL (PostGIS & btree_gist)**, and **React + Three.js / Pannellum**. The engine features high-precision spatial radius search, zero-conflict in-person tour booking via PostgreSQL exclusion constraints, dynamic 360° panoramas & 3D virtual showrooms, and a Super-Admin Visual Hotspot Studio.

---

## 1. System Architecture & Features

### Core Capabilities:
- **Phase 1: Foundation & RBAC Security**
  - FastAPI asynchronous backend with SQLAlchemy 2.0 & asyncpg.
  - JWT authentication with argon2 password hashing and multi-role RBAC (`buyer`, `agent`, `admin`).
  - Strict Pydantic v2 schemas and centralized configuration management.
- **Phase 2: PostGIS Geospatial Radius Search**
  - Native PostGIS geometry indexing (`GIST`) with SRID 4326.
  - Sub-50ms radius discovery queries using `ST_DWithin` and distance calculations via `ST_Distance`.
  - Comprehensive multi-attribute filtering (price ranges, bedroom/bathroom counts, property types, virtual showroom presence).
- **Phase 3: Guaranteed Conflict-Free Tour Booking**
  - PostgreSQL mutual exclusion constraint (`prevent_overlapping_agent_tours`) using `btree_gist` and `tsrange`.
  - Automated 45-minute booking slots with 15-minute buffers against agent availability.
  - Unauthenticated booking interception with direct return-to-slot flow.
  - RFC 5545 compliant `.ics` iCalendar file generation.
- **Phase 4: Dynamic 360° Panorama & 3D Virtual Showroom**
  - Hierarchical scene graph database structure (`showrooms`, `showroom_scenes`, `showroom_hotspots`).
  - Responsive 360° equirectangular panorama viewer with spherical coordinate navigation.
  - Interactive 3D architectural model viewer powered by React Three Fiber (R3F) and Three.js.
  - Room-to-room transitions with dynamic hotspot overlays.
- **Phase 5: Super-Admin Portal & Visual Hotspot Studio**
  - Interactive 360 canvas coordinate extraction: click anywhere on the viewer to capture `pitch` and `yaw`.
  - Hotspot CMS modal for pinning navigation links, architectural descriptions, and upgrade costs.
  - Real-time operational KPI telemetry (active listings, confirmed tours, registered buyers, active agents).
  - Transactional system audit logging (`system_audit_logs`) tracking actor, action, timestamp, IP, and state diffs.
  - Global master tour management and status override controls.

---

## 2. Default Test Credentials

| Role | Email | Password |
| :--- | :--- | :--- |
| **Admin** | `admin@example.com` | `AdminSecret123!` |
| **Buyer** | `buyer@example.com` | `Password123!` |
| **Agent** | `agent@example.com` | `AgentSecret123!` |

---

## 3. Quick Start & Development

### Prerequisites:
- Python 3.11+
- Node.js 18+ & npm
- PostgreSQL 15+ with `postgis` and `btree_gist` extensions enabled

### 1. Database Setup & Migrations:
```bash
# In the backend directory
cd backend

# Apply all Alembic migrations (Revisions 0001 through 0005)
py -m alembic upgrade head

# Seed initial properties and virtual showroom scene graphs
cd ..
py scripts/seed_test_properties.py
py scripts/seed_showroom_data.py
```

### 2. Start the Backend API Server:
```bash
cd backend
py -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```
- **Interactive Swagger Documentation**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **ReDoc Specification**: [http://localhost:8000/redoc](http://localhost:8000/redoc)

### 3. Start the Frontend Application:
```bash
cd frontend
npm.cmd install
npm.cmd run dev
```
- **Web App**: [http://localhost:5173](http://localhost:5173)
- **Showcase Property**: [http://localhost:5173/properties/luxury-penthouse](http://localhost:5173/properties/luxury-penthouse)
- **Admin Command Center**: [http://localhost:5173/admin/dashboard](http://localhost:5173/admin/dashboard)
- **Admin Hotspot Studio**: [http://localhost:5173/admin/properties/luxury-penthouse/showroom-studio](http://localhost:5173/admin/properties/luxury-penthouse/showroom-studio)

---

## 4. Automated Testing & Verification Suites

### Backend Automated Test Suite (100% Passing — 21 Tests)
```bash
cd backend
py -m pytest tests/ -v
```
- `tests/test_auth_api.py` (Registration, login, JWT validation, health checks)
- `tests/test_properties_api.py` (PostGIS spatial search, filtering, RBAC CRUD)
- `tests/test_tours_api.py` (GIST conflict prevention, availability matrix, .ics downloads)
- `tests/test_showroom_api.py` (Scene graph retrieval, coordinate bounds verification)
- `tests/test_admin_api.py` (Strict RBAC 403 guards, hotspot creation, audit log persistence, master tours)

### Frontend Multi-Device E2E Automation Suite (100% Passing — 27 Tests)
```bash
cd frontend
npx.cmd playwright test
```
Tested across:
1. **Desktop Chrome** (1280x720)
2. **Tablet** (iPad Pro 11)
3. **Mobile Safari** (iPhone 14)

Covers:
- `auth.spec.ts`: Registration, login, profile inspection, and modal tab switching.
- `property-search.spec.ts`: Radius filters, price range sliders, and dynamic listing grid updates.
- `tour-booking.spec.ts`: Unauthenticated flow interception, login redirection, slot selection, and booking confirmation.
- `showroom-interaction.spec.ts`: 360 canvas mounting, node navigation between rooms, and 360/3D mode toggling.
- `admin-studio.spec.ts`: Dynamic canvas click-to-pin coordinate extraction, scene creation, KPI telemetry, and audit logs.

### Frontend Production Build
```bash
cd frontend
npm.cmd run build
```
Builds verified assets to `dist/` with zero TypeScript or bundling errors.
