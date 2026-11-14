# VidyutFleet — Enterprise Fleet EV Transition & Depot Infrastructure Planner
### HACKNAUTS 2.0 | Team Vidyut (GNDEC Ludhiana)
> **Tagline:** *Optimizing EV adoption for Indian logistics fleets.*  
> **Git Repository:** [https://github.com/prince-saharan/hackathon](https://github.com/prince-saharan/hackathon)  
> **Committed Author:** `prince-saharan`

---

## ⚡ Live Unified Application
- **Unified Web Dashboard & API:** [http://localhost:5000](http://localhost:5000)
- **PowerPoint Pitch Deck:** `D:\hackathon\hackthon.pptx` *(Clean 11-slide presentation)*
- **Multi-Tab Excel Report:** `D:\hackathon\data\sample_report.xlsx`

---

## 📊 Master Datasets (`data/`)

1. **`data/fleet_routes.csv` (120 Vehicles, 25 Routes, 3 Depots, 47 Columns):**
   * Identification: `record_id`, `analysis_date`, `organization_id`, `depot_id`, `vehicle_id`, `route_id`, `driver_id`
   * Depot: `depot_name`, `depot_city`, `depot_state`, `available_chargers`, `grid_connection_capacity_kw`
   * Route & Timing: `daily_distance_km`, `number_of_trips`, `departure_time`, `arrival_time`, `depot_dwell_start`, `depot_dwell_end`, `delivery_deadline_time`, `stop_count`
   * Operating Conditions: `payload_kg`, `max_payload_kg`, `road_type`, `traffic_level`, `weather_condition`, `average_speed_kmph`
   * EV Specs & Costs: `battery_capacity_kwh`, `ev_range_km`, `charging_power_kw`, `electricity_tariff_per_kwh`, `diesel_price_per_ltr`

2. **`data/ev_catalogue.csv`:**
   * Certified specifications for Indian commercial EVs: Mahindra Treo Zor, Piaggio Ape E-Xtra, Tata Ace EV, Tata Magic EV, Euler HiLoad, Switch IeV4, Altigreen neEV.
   * Compares **ARAI Certified Range** vs **Practical Operating Range** (with battery safety buffer).

3. **`data/reference_factors.csv`:**
   * CEEW 2024 commercial vehicle operating benchmarks (EV ₹1.28/km vs Diesel ₹2.69/km).
   * Central Electricity Authority (CEA) Indian Power Sector CO2 Baseline Database Version 16 (0.675 kg CO2/kWh).
   * Ministry of Heavy Industries PM E-DRIVE Capital Subsidy (₹50,000 / vehicle).

---

## 🤖 Multi-Agent System Architecture

| Agent | Module | Responsibility & Output |
| :--- | :--- | :--- |
| **Agent 1: Data Validation Agent** | `analysis/core/validator.py` | Schema sanitation, 47-column and 6-column CSV intake, midnight dwell resolution, syntax checks. |
| **Agent 2: Fleet Analysis Agent** | `analysis/core/scoring.py` | Weighted 4-Factor EV Readiness Score ($0.40R + 0.30D + 0.20Q + 0.10S \in [0, 100]$) and transition priority queue. |
| **Agent 3: Charging Planner Agent** | `analysis/core/edf_scheduler.py` | Analytical lower bound charger sizing + Earliest Deadline First (EDF) bay timeline and conflict detector. |
| **Agent 4: Financial Analyst Agent** | `analysis/core/financials.py` | TCO savings, ROI, subsidized vs unsubsidized payback, and 5-yr/10-yr TCO projections. |
| **Agent 5: Sustainability Agent** | `analysis/core/sustainability.py` | Well-to-wheel CO2 emissions using CEA Database v16 grid baseline (0.675 kg/kWh) and green equivalencies. |
| **Agent 6: Report Generator Agent** | `analysis/reports/excel_generator.py` | Automated multi-tab formatted Excel workbook export (`openpyxl`). |
| **Agent 7: RAG Advisor Agent** | `analysis/rag/advisor_agent.py` | Natural language Q&A grounded strictly in analysis results and catalogue specs with clickable citations. |

---

## 🧮 Mathematical Formulations

### 1. EV Readiness Score Formula
$$\text{Score} = 0.40 \cdot \text{RouteFeasibility} + 0.30 \cdot \text{DistanceSuitability} + 0.20 \cdot \text{ChargingFeasibility} + 0.10 \cdot \text{OperationalSuitability}$$

- **Route Feasibility (40%):** Road type (Urban/Semi-Urban/Highway), traffic congestion, stop density penalty ($>30$ stops), and weather impacts.
- **Distance Suitability (30%):** Ratio of daily route km to practical EV battery range (with 20% safety reserve buffer).
- **Charging Feasibility (20%):** Overnight dwell window vs required 3.3 kW recharge duration + off-peak tariff alignment (23:00 to 06:00).
- **Operational Suitability (10%):** Cargo payload percentage ($>100\%$ overload penalty) and trip count.

#### Rollout Classification Thresholds:
- **Ready Now (Score $\ge 80$):** Direct drop-in EV replacement.
- **Ready with Depot Charger (Score $60 - 79$):** Requires dedicated overnight depot bay allocation.
- **Not Yet (Score $< 60$):** Retain ICE/CNG; exceeds current battery range or insufficient dwell.

### 2. Analytical Charger Sizing & EDF Scheduling
$$C_{\min} = \left\lceil \frac{\sum_{i=1}^N \text{Energy Required}_i / P_{\text{charger}}}{\text{Depot Common Dwell Window}} \right\rceil$$
- **Earliest Deadline First (EDF) Priority:** Arriving vehicles are queued by morning departure deadline ($\min t_{\text{departure}}$), preventing simultaneous 8 PM arrival demand spikes and flattening depot transformer load.

---

## 🖥️ Role-Based Frontend Dashboards (React 18)

1. **Fleet Manager View:** Route readiness scores, factor breakdown inspection, transition priority list, and search filters.
2. **Depot Manager View:** Sized charger bays, EDF bay timeline, bay utilization rate, and conflict alerts.
3. **Finance Team View:** TCO comparison, ROI, subsidized/unsubsidized payback, and interactive sensitivity sliders for fuel and electricity prices.
4. **Sustainability Team View:** Well-to-wheel CO2 abated, CEA v16 baseline metrics, and tree planting equivalencies.
5. **CSV Upload & Ingest View:** Drag-and-drop CSV upload, column mapping validation, and template downloads.
6. **Admin View:** Complete EV specifications catalogue editor (`ev_catalogue.csv`).
7. **AI Advisor Drawer:** VidyutFleet Advisor RAG chat with grounded answers and verifiable citations.

---

## 🚀 Quick Start Instructions

### Local Execution:
```bash
# 1. Start unified server
cd D:\hackathon\backend
node server.js

# 2. Open browser
http://localhost:5000

# 3. Run unit tests
cd D:\hackathon
python -m unittest analysis.tests.test_agents
```

### Docker Compose:
```bash
docker-compose up --build
```
