# End-to-End Project Brief: BiciMAD Urban Mobility & Fleet Rebalancing

## 1. Dataset Card

| Field | Details |
|---|---|
| **Name** | BiciMAD Trip Logs & Station Usage |
| **Publisher & URL** | Ayuntamiento de Madrid — Open Data Portal ([datos.madrid.es](https://datos.madrid.es)) |
| **Licence** | Open Data Licence — Ayuntamiento de Madrid (CC-BY 4.0 compatible) |
| **Download Date & Version** | October 2026 / Annual Trip Dataset (2023–2024) |
| **Grain (One Row Represents)** | One individual bike trip from pick-up (unplug) to drop-off (plug) |
| **Period Covered** | 12 consecutive months (January 2023 – December 2023) |
| **Size (Rows × Columns, File Size)** | ~3.8 million rows × 11 columns (~480 MB CSV format) |
| **Key Columns** | **Dates:** `unplug_hourTime` (start timestamp), `plug_hourTime` (end timestamp)<br/>**Dimensions:** `idUnplug_station` (origin), `idPlug_station` (destination), `user_type` (annual member vs. casual subscriber), `ageRange`<br/>**Measures:** `travel_time` (seconds), trip distance (meters)<br/>**Candidate Target:** `is_long_trip` (binary: trip duration > 30 min threshold) or `high_demand_flag` (station hourly demand prediction) |
| **Known Quality Issues** | Extreme duration outliers (trips exceeding 24 hours), null values in destination station IDs due to maintenance, and encoding artifacts in timestamps |
| **Additional Sources to Join** | AEMET / Madrid Hourly Weather Dataset (temperature, rain precipitation, wind speed) joined by date and hour |

---

## 2. Business Problem

### 2.1 Organisation, Department & Stakeholder
* **Organisation:** Empresa Municipal de Transportes de Madrid (EMT Madrid), the public mobility operator managing the municipal electric bike-sharing system (BiciMAD) across Madrid's districts.
* **Department:** Field Operations & Logistics Team, a dedicated unit of 18 field operators managing fleet maintenance, battery swapping, and bike rebalancing using service vans.
* **Stakeholder:** Operations Logistics Supervisor, who monitors station occupancy in real time and schedules morning and evening van rebalancing routes.

### 2.2 The Decision & Constraints
* **Decision to Support:** With this tool, the Operations Logistics Supervisor will decide which bike stations to prioritize for morning rebalancing routes (6:00 AM – 9:00 AM), how many bikes to relocate between residential and business districts, and whether bad weather forecasts warrant reducing rebalancing shifts to optimize operational costs.
* **Constraints:** A fixed fleet capacity of 6 rebalancing vans, strict budget caps on extra operator shifts, low-emission zone traffic restrictions in central Madrid (Distrito Centro), and real-time station slot capacities.

### 2.3 Hypotheses

| ID | Hypothesis Statement | Metric & Definition | Expected Direction | Test Method | Action if True | Action if False |
|---|---|---|---|---|---|---|
| **H1** | On weekdays, bike trips heavily concentrate during morning rush hours (7:00–9:00 AM) flowing from residential districts toward business hubs. | Share of total trips per hour filtered by origin-destination district pair. | Concentration > 45% of morning trips in residential-to-business corridors. | Hourly stacked bar chart and origin-destination heatmap (Lesson 04 visual analysis). | Prioritize repositioning empty bikes to residential stations before 6:30 AM. | Distribute rebalancing vans evenly across all districts regardless of morning peak hours. |
| **H2** | Rainfall cuts daily BiciMAD demand by more than 30% compared to dry days under similar temperature conditions. | Daily trip volume on rainy days vs. dry days with matching month and day-of-week. | Trip volume reduction >= 30% when precipitation > 2mm. | Two-sample t-test and time-series line comparison with weather overlay (Lesson 05). | Cancel extra van operator shifts on forecasted rainy days to minimize operating costs. | Maintain standard shift scheduling regardless of rain forecasts. |
| **H3** | Trips originating from stations near major transit hubs (e.g., Atocha, Moncloa) exceed the 30-minute free/standard duration tier more frequently than neighborhood trips. | Percentage of trips per station with `travel_time` > 1,800 seconds. | Proportion of long trips > 25% near intermodal hubs. | Boxplot of trip durations grouped by station type and district. | Partner with EMT to introduce dedicated commuter usage alerts or station capacity expansions. | Retain current station slot allocations and uniform pricing alerts. |

### 2.4 Key Performance Indicators (KPIs)
1. **Total Daily & Monthly Trips:** Total count of completed trips per day, month, and district (Grain: Daily; Target: track seasonal growth vs. prior period).
2. **Station Empty-Hour Ratio:** Percentage of operational hours per day in which a station has 0 available bikes (Grain: Station/Day; Target: < 5% empty hours per station).
3. **Average Trip Duration & Long-Trip Share:** Mean travel time in minutes and percentage of trips exceeding 30 minutes (Grain: District/User Type; Target: track operational efficiency).
4. **Rebalancing Efficiency Index:** Estimated number of trips generated per rebalanced bike moved by field vans (Grain: Daily/Route; Target: maximize trips per van deployment).

---

## 3. Solution Design & Architecture

### 3.1 Solution Type
* **Type:** Analytical Engine & Operational Dashboard.
* **Overview (Inputs → Logic → Outputs):** 
  * **Inputs:** Historical BiciMAD trip logs, real-time station occupancy levels, and hourly weather conditions (AEMET).
  * **Logic:** Hourly demand forecasting rules per station combined with optimization logic to identify deficit vs. surplus stations.
  * **Outputs:** A prioritized list of stations requiring rebalancing, presented on an interactive operational dashboard.

### 3.2 General Architecture

```mermaid
flowchart LR
    A --> B[Ingestion: Python / pandas]
    B --> C[Cleaning & Quality: Types, Nulls, Outliers]
    C --> D[Data Model: Fact + Dimensions]
    D --> E[Analytics / Engine: KPIs & Demand Model]
    E --> F[Dashboard: Power BI / Streamlit]
    F --> G[Stakeholder Decision]


