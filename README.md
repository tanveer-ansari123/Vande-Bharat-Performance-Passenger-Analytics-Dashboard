<div align="center">

# 🚄 Vande Bharat Express — Performance & Passenger Analytics Dashboard

**An end-to-end Power BI project analyzing operational performance, revenue, and passenger behavior across India's Vande Bharat Express network.**

[![Power BI](https://img.shields.io/badge/Power%20BI-Report-F2C811?logo=powerbi&logoColor=black)](https://app.powerbi.com/view?r=eyJrIjoiZTE4MDdmNWUtYjQ5Mi00MTRjLWFlZDMtYzU1ODUyZWUwMTUxIiwidCI6ImM5YzUwODQ4LWIwM2EtNGJlNC1iNjU1LTZlZGQ3ZmI4MWM1YSJ9)
![Made with Excel](https://img.shields.io/badge/Data-Excel-217346?logo=microsoftexcel&logoColor=white)

**🔗 [View Live Interactive Dashboard →](https://app.powerbi.com/view?r=eyJrIjoiZTE4MDdmNWUtYjQ5Mi00MTRjLWFlZDMtYzU1ODUyZWUwMTUxIiwidCI6ImM5YzUwODQ4LWIwM2EtNGJlNC1iNjU1LTZlZGQ3ZmI4MWM1YSJ9)**

</div>

---

## 📌 Overview

This project simulates and analyzes booking, revenue, and punctuality data for the **Vande Bharat Express**, India's semi-high-speed train network, and presents it as an interactive **Power BI dashboard**. The goal is to give operations and revenue teams a single view of network health — passenger volume, on-time performance, route profitability, and class-wise demand — that supports route planning and service-quality decisions.

The dashboard covers **164 trains**, **~100,000 booking records**, and **23 states**, spanning **January–May 2026**.

---

## 🖼️ Dashboard Preview

### Cover Page
<img src="docs/screenshots/01-cover-page.jpg" alt="Vande Bharat Express dashboard cover page" width="850">

### Main Dashboard
<img src="docs/screenshots/02-dashboard-overview.jpg" alt="Vande Bharat Express main dashboard" width="850">

> 📂 Full-resolution export: [`reports/Vande_Bharat_Express_Dashboard_Export.pdf`](reports/Vande_Bharat_Express_Dashboard_Export.pdf)

---

## ✨ Key Features

- **KPI Summary Strip** — Total trains, total passengers, average occupancy, average speed, on-time %, and total revenue at a glance.
- **Monthly Trend Analysis** — Passenger and revenue trends (Jan–May 2026) to spot seasonality and growth.
- **Route-Level Breakdown** — Passenger volume, occupancy %, and revenue ranked by route.
- **Class-Wise Booking Split** — Executive Class vs. Chair Car demand as a donut chart.
- **On-Time Performance Gauge** — Network-wide punctuality tracked against a target.
- **Revenue vs. Passenger Count Scatter** — Identify high-yield vs. high-volume routes.
- **Train Performance Table** — Sortable, drillable table of total passengers, revenue, occupancy, delay, and on-time % per train.
- **Leaderboard Cards** — Highest revenue route, busiest train, best occupancy, highest speed, and least average delay, surfaced automatically.
- **Slicer Panel** — Filter every visual by State, Route, Train Name/No, Coach Type, Class, Journey Date, Month, Passenger Type, and Gender.

---

## 🧱 Data Model

The report is built on a **star schema** for fast, accurate DAX calculations.

```
                         ┌────────────────────┐
                         │   Train_Master      │
                         │  (164 trains)        │
                         │──────────────────────│
                         │ Train_No (PK)         │
                         │ Train_Name            │
                         │ Route                 │
                         │ Source / Destination  │
                         │ Distance_km            │
                         │ Capacity               │
                         │ Coach_Type             │
                         └──────────┬────────────┘
                                    │ 1
                                    │
                                    │ *
┌───────────────────┐     ┌────────┴────────────┐     ┌──────────────────────┐
│   State_Master      │───│    Fact_Bookings      │     │  Date (Journey_Date)   │
│  (23 states)         │ * │   (~100,000 rows)      │ *   │  auto date hierarchy   │
│──────────────────────│   │────────────────────────│───│                        │
│ State (PK)            │   │ Booking_ID (PK)          │
│ Region                │   │ Journey_Date / Booking_Date│
└───────────────────────┘   │ Month / Year               │
                             │ State, Route, Train_No       │
                             │ Coach_Type, Class             │
                             │ Passenger_Type, Gender, Age     │
                             │ Ticket_Price, Passengers          │
                             │ Occupancy_%, Revenue                │
                             │ Scheduled/Actual Departure             │
                             │ Delay_Minutes, On_Time                   │
                             │ Speed_kmph, Train_Capacity, Seats_Booked   │
                             └─────────────────────────────────────────────┘
```

**Relationships**
| From | To | Cardinality | Cross-filter |
|---|---|---|---|
| `Fact_Bookings[Train_No]` | `Train_Master[Train_No]` | Many-to-one | Single |
| `Fact_Bookings[State]` | `State_Master[State]` | Many-to-one | Single |

Full column-level definitions are in [`data/data_dictionary.md`](data/data_dictionary.md).

---

## 📐 Key Measures (DAX)

Core measures used to power the KPI cards and visuals. Full list with logic notes: [`docs/DAX_measures.md`](docs/DAX_measures.md).

```dax
Total Passengers      = SUM ( Fact_Bookings[Passengers] )
Total Revenue         = SUM ( Fact_Bookings[Revenue] )
Avg Occupancy         = AVERAGE ( Fact_Bookings[Occupancy_%] )
Avg Speed             = AVERAGE ( Fact_Bookings[Speed_kmph] )
Avg Delay (mins)      = AVERAGE ( Fact_Bookings[Delay_Minutes] )

On Time %             =
DIVIDE (
    CALCULATE ( SUM ( Fact_Bookings[Passengers] ), Fact_Bookings[On_Time] = TRUE ),
    [Total Passengers]
)

Total Trains           = DISTINCTCOUNT ( Fact_Bookings[Train_No] )
```

---

## 📊 Insights Surfaced by the Dashboard

- **Overall network on-time performance sits at ~83.8%**, with average delay of ~5.5 minutes across the top-performing trains.
- **New Delhi – Varanasi is the highest-revenue route**, generating the largest share of total revenue among all corridors.
- **Chair Car accounts for ~68% of bookings vs. ~32% for Executive Class**, showing demand is heavily weighted toward the standard class.
- **Occupancy is strongest on the Shri Mata Vaishno Devi Katra corridor** (~92%), suggesting potential for capacity or frequency increases.
- **Revenue grew month-over-month from January to May 2026**, with the steepest jump between March and April.

*(These are dashboard-level observations — see the live report for the full, filterable breakdown.)*

---

## 🗂️ Repository Structure

```
vande-bharat-express-dashboard/
│
├── README.md                          ← You are here
├── LICENSE
├── .gitignore
│
├── data/
│   ├── VandeBharat_Dashboard_Dataset.xlsx   ← Source dataset (3 tables, ~100K rows)
│   └── data_dictionary.md                    ← Column-level field definitions
│
├── docs/
│   ├── DAX_measures.md                 ← All measures used in the report
│   ├── methodology.md                  ← Data prep, modeling & design notes
│   └── screenshots/
│       ├── 01-cover-page.jpg
│       └── 02-dashboard-overview.jpg
│
└── reports/
    └── Vande_Bharat_Express_Dashboard_Export.pdf   ← Full PDF export of the report
```

> **Note:** The `.pbix` Power BI source file is not included in this repository (large binary / not exported). The dataset in `data/` plus the DAX measures and modeling notes in `docs/` are sufficient to reproduce the report from scratch — see [`docs/methodology.md`](docs/methodology.md).

---

## 🛠️ Tools & Tech Stack

| Tool | Purpose |
|---|---|
| **Power BI Desktop** | Data modeling, DAX measures, dashboard design |
| **Microsoft Excel** | Source dataset (Fact_Bookings, Train_Master, State_Master) |
| **Power Query** | Data cleaning and shaping (ETL) |
| **DAX** | Calculated measures and KPIs |
| **Power BI Service** | Publishing & sharing the live report |

---

## 🚀 How to Reproduce This Project

1. Clone this repository.
2. Open **Power BI Desktop** → *Get Data* → *Excel Workbook* → select `data/VandeBharat_Dashboard_Dataset.xlsx`.
3. Load all three tables: `Fact_Bookings`, `Train_Master`, `State_Master`.
4. Build the relationships as described in [Data Model](#-data-model).
5. Create the measures listed in [`docs/DAX_measures.md`](docs/DAX_measures.md).
6. Rebuild the visuals using the layout shown in the [dashboard preview](#-dashboard-preview) as a reference, or design your own.
7. Publish to the Power BI Service to generate a shareable link.

---

## 🔗 Live Dashboard

**[Open the interactive Power BI report ↗](https://app.powerbi.com/view?r=eyJrIjoiZTE4MDdmNWUtYjQ5Mi00MTRjLWFlZDMtYzU1ODUyZWUwMTUxIiwidCI6ImM5YzUwODQ4LWIwM2EtNGJlNC1iNjU1LTZlZGQ3ZmI4MWM1YSJ9)**

---

## 👤 Author

**Ishwari A.**
Dashboard design & development — Power BI

---

## 📄 License

This project is licensed under the [MIT License](LICENSE). The dataset is synthetic/illustrative and created for portfolio and learning purposes; it does not represent official Indian Railways data.
