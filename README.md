# 🚛 Spotter.ai TMS — FMCSA Hours of Service (HOS) & 24-Hour ELD Engine

[![Vercel Deployment](https://img.shields.io/badge/Vercel-Production%20Live-brightgreen?logo=vercel)](https://spotter-ai-tms.vercel.app)
[![Django](https://img.shields.io/badge/Django-5.2-092E20?logo=django)](https://www.djangoproject.com/)
[![React](https://img.shields.io/badge/React-18.3-61DAFB?logo=react)](https://react.dev/)
[![Leaflet](https://img.shields.io/badge/Leaflet-1.9-199900?logo=leaflet)](https://leafletjs.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> An enterprise-grade Transportation Management System (TMS) built for property-carrying commercial vehicle operations. Features automated **FMCSA 49 CFR Part 395 compliance calculations**, **deadhead repositioning routing**, an **interactive Leaflet route map with truck animation**, **turn-by-turn navigation instructions**, and an **SVG 24-Hour Digital ELD Log Sheet** with multi-day export.

- 🌐 **Live Web Application:** [https://spotter-ai-tms.vercel.app](https://spotter-ai-tms.vercel.app)
- 📹 **Loom Video Presentation Guide:** [steps.md](steps.md)

---

## 🌟 Key Features

### 1. Automated FMCSA HOS Engine (Backend)
- **11-Hour Driving Cap (§395.3(a)(3)):** Prevents more than 11 hours of total driving after 10 consecutive hours off duty.
- **14-Hour Consecutive Duty Window (§395.3(a)(2)):** Enforces no driving past the 14th consecutive hour after coming on duty.
- **30-Minute Mandatory Rest Break (§395.3(a)(3)(ii)):** Schedules a 30-minute off-duty break after 8 cumulative hours of driving.
- **10-Hour Sleeper Berth Reset (§395.3(a)(1)):** Automatically resets driving and shift clocks after 10 consecutive hours in Line 1 (Off Duty) or Line 2 (Sleeper Berth).
- **70-Hour / 8-Day Rolling Cycle Limit (§395.3(b)):** Tracks and validates cycle consumption against legal limits.
- **1,000-Mile Fueling Stops:** Automatically inserts 15-minute Line 4 on-duty fueling stops every 1,000 miles.
- **Shipper Loading & Unloading:** 1-hour pre-trip inspection and cargo loading at origin (Line 4), and 1-hour unloading at destination (Line 4).
- **Origin Pre-Trip Inspection:** 15-minute Line 4 inspection prior to deadhead repositioning transit.

### 2. Commercial Truck Routing & Deadhead Detection
- **Deadhead Routing Support:** Automatically detects when the driver's current position differs from the shipper pickup, calculating deadhead miles separately from loaded freight miles.
- **Multi-Stop Commercial Corridor Routing:** Powered by OSRM/Valhalla commercial truck routing with geodesic Haversine fallbacks.
- **Truck Restriction Checks:** Verified against Interstate standard clearances (13'6" height, 80,000 lbs Gross Vehicle Weight).
- **Turn-by-Turn Maneuver Instructions:** Real-time navigation step instructions with maneuver icons, highway names, leg-type milestone alerts, and duration.

### 3. Interactive Leaflet Map & Trip Simulation
- **Animated Truck Traversal:** Moves a custom semi-truck marker along the route geometry with variable playback speed (1x, 2x, 5x, 10x).
- **Floating HUD Telemetry:** Displays current duty status, simulated speed (55 mph), odometer progression, and dynamic countdowns to the next mandatory rest or fuel stop.
- **Synchronized Route Highlighting:** Selecting any Day tab highlights the specific route geometry traveled on that day.
- **Smart Waypoint Flags:** Interactive markers for Pickup, Dropoff, 30-min Meal Breaks, 10-hr Sleeper Berth resets, and Fuel Stops.

### 4. Scalable Vector Graphics (SVG) 24-Hour Digital ELD Sheet
- **Stepped Duty Polyline:** 96-increment resolution (15 minutes per slice) across the 4 official FMCSA duty lines:
  - **Line 1:** Off Duty
  - **Line 2:** Sleeper Berth
  - **Line 3:** Driving
  - **Line 4:** On Duty (Not Driving)
- **Midnight Boundary Splitter:** Mathematically guarantees the **24.0-hour daily total invariant** across multi-day trips by cleanly splitting continuous overnight events at 00:00 midnight.
- **Official FMCSA §395.8 Header Fields:** Carrier name, DOT number, Driver name & ID, Co-driver, B/L number, Trailer number, and Home Terminal.
- **Multi-Day PDF Export / Print:** One-click multi-day export producing print-ready, audit-compliant ELD driver daily logs.

---

## 🏗️ Architecture

```
spotter-tms/
├── api/                           # Vercel Serverless WSGI Entrypoint
│   └── index.py                   # Python 3.12 WSGI handler
├── backend/                       # Django Backend
│   ├── hos_engine/                # HOS Core Application
│   │   ├── services/
│   │   │   ├── hos_simulator.py   # Chronological HOS state machine
│   │   │   ├── midnight_splitter.py# 24-hr calendar day event partitioner
│   │   │   └── routing_service.py # Commercial truck routing & geocoding
│   │   ├── serializers.py         # Request validation serializers
│   │   ├── views.py               # REST API endpoints
│   │   ├── urls.py                # URL dispatchers
│   │   └── tests/                 # Automated test suite
│   ├── tms_core/                  # Django project configuration
│   └── manage.py
├── frontend/                      # React Frontend (Vite)
│   ├── src/
│   │   ├── components/
│   │   │   ├── RouteMap.jsx       # Leaflet map with animated truck & HUD
│   │   │   ├── EldLogSheet.jsx    # SVG 24-hour ELD log sheet & export
│   │   │   ├── RouteInstructions.jsx# Step-by-step turn maneuvers
│   │   │   ├── TripForm.jsx       # Location & cycle input panel
│   │   │   ├── TripSummaryCard.jsx# Telemetry metrics display
│   │   │   └── DayTabs.jsx        # Multi-day navigation tabs
│   │   ├── App.jsx                # Main layout and state orchestrator
│   │   └── index.css              # Cyber-sleek dark SaaS styling
│   └── package.json
├── steps.md                       # 5-Minute Loom video recording script
├── vercel.json                    # Vercel deployment configuration
└── requirements.txt               # Backend Python dependencies
```

---

## 🚀 Getting Started

### Prerequisites
- Python 3.11+
- Node.js 18+ & npm

### Backend Setup
```bash
# Clone the repository
git clone https://github.com/affan004/spotter-tms.git
cd spotter-tms/backend

# Create and activate virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r ../requirements.txt

# Run migrations and start server
python manage.py migrate
python manage.py runserver 127.0.0.1:8000
```

### Frontend Setup
```bash
cd ../frontend

# Install npm dependencies
npm install

# Start development server
npm run dev
```

Visit `http://localhost:5173` in your browser.

---

## 🧪 Running the Test Suite

The project includes unit and integration tests verifying all FMCSA regulations and boundary conditions:

```bash
cd backend
python manage.py test
```

### Verified Test Cases:
1. `test_11_hour_driving_limit`: Enforces 10-hour sleeper berth when driving cap is reached.
2. `test_14_hour_duty_window`: Enforces rest when on-duty duration hits 14 consecutive hours.
3. `test_30_minute_mandatory_rest_break`: Validates meal break after 8 hours of cumulative driving.
4. `test_70_hour_cycle_limit_detection`: Triggers cycle limit alert when 70 hours are exceeded.
5. `test_fuel_stop_every_1000_miles`: Verifies 15-minute Line 4 stops at 1,000-mile intervals.
6. `test_midnight_splitter_24_hour_invariant`: Proves exact 24.0 hours accounted on every single day.
7. `test_deadhead_repositioning_routing`: Ensures separate origin inspection and deadhead tracking.
8. `test_calculate_trip_api_endpoint`: Validates HTTP 200 JSON payload structure.
9. `test_sample_trips_api_endpoint`: Verifies pre-configured demo routes.

---

## 📡 REST API Reference

### `POST /api/hos/calculate-trip/`
Calculates full commercial truck route, HOS simulation timeline, turn-by-turn maneuvers, and daily ELD sheets.

**Payload:**
```json
{
  "current_location": "Indianapolis, IN",
  "pickup_location": "Indianapolis, IN",
  "dropoff_location": "Dallas, TX",
  "current_cycle_used": 14.5,
  "avg_speed": 55.0,
  "start_time": "2026-09-28T08:00:00",
  "truck_height_ft": 13.5,
  "truck_weight_lbs": 80000.0
}
```

### `GET /api/hos/sample-trips/`
Returns pre-configured demonstration routes (Midwest to Texas, Chicago to Atlanta, Cross-Country Coast).

---

## 📜 License
MIT License. Built for the Spotter.ai TMS assessment.
