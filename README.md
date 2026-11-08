[github_project_documentation.md](https://github.com/user-attachments/files/34550027/github_project_documentation.md)
# Apex Hospitality: Full-Cycle Revenue, Operations & Demographics Intelligence Suite

An enterprise-grade business intelligence and financial modeling suite engineered for luxury hospitality leadership. This project translates multi-departmental hotel operations—spanning room bookings, F&B outlets, P&L departmental overhead, and guest demographic profiles—into actionable, high-margin strategic growth levers.

---

## Executive Summary & Business Challenge

In the hospitality sector, headline occupancy rates often mask operational inefficiencies and profit leaks. High occupancy driven primarily by high-commission Online Travel Agencies (OTAs) can severely compress Gross Operating Profit (GOP), while unmonitored departmental overhead (such as housekeeping and utilities) erodes bottom-line margins.

This intelligence suite addresses three core organizational dilemmas:
1. **Channel Yield Disparity:** Comparing net profitability between direct booking channels and third-party OTAs after commission structures.
2. **Granularity Alignment:** Resolving the analytical gap between transaction-level room reservations and high-level departmental expense allocations.
3. **Guest Lifetime Value & Root-Cause Drivers:** Decoding demographic behavior, loyalty tier adoption, and tracing revenue paths down to length of stay and room amenities.

---

## Data Architecture & Semantic Modeling

### Dimensional Modeling Strategy
The data foundation utilizes an optimized Star/Snowflake schema tailored for scalable enterprise reporting:

* **Fact Tables:**
  * `Fact_Reservations`: Transaction-level booking records, daily room rates, check-in/out timestamps, booking status, and outlet charges.
  * `Fact_Expenses`: Periodic departmental expenditure categorized into fixed and variable allocations.
  * `Fact_FB_Sales`: Point-of-sale dining revenue across hotel outlets (Fine Dining, Pool Bar, Room Service, etc.).
* **Dimension Tables:**
  * `Dim_Guests`: Unique guest identifiers, demographic age brackets, gender, nationality, and loyalty membership tiers.
  * `Dim_Rooms`: Room inventory, room tier hierarchy (`Presidential`, `Suite`, `Deluxe`, `Standard`), views (`Sea`, `Pool`, `Garden`, `City`), and base capacity.
  * `Dim_Date`: Standard calendar hierarchy enabling accurate Month-over-Month (MoM) and seasonal variance calculations.

### Technical Problem Solved: Granularity Mismatch & Filter Propagation
* **The Challenge:** Departmental expenses exist at the hotel/department level, whereas bookings exist at the individual room and channel level. Initial models caused expense figures and Net Operating Profit to artificially repeat across granular room-type slicers.
* **The Resolution:** Strict separation of concerns was implemented in the analytical model. Global P&L metrics were decoupled from micro-operational room grids, ensuring that transactional filters (e.g., specific booking channels or room amenities) accurately aggregate operational metrics without skewing broader hotel-level overhead calculations.

---

## Core DAX Business Logic & Metrics Catalog

### 1. Financial Performance Metrics

* **Total Hotel Revenue:**
  $$\text{Total Hotel Revenue} = \sum(\text{Reservations}[\text{Room\_Revenue}]) + \sum(\text{FB\_Sales}[\text{Sales\_Amount}])$$
  ```dax
  Total Hotel Revenue = 
  SUM(Reservations[Total_Room_Revenue]) + SUM(F&B_Sales[Sales_Amount])
  ```

* **Net Operating Profit (NOP):**
  $$\text{NOP} = \text{Total Hotel Revenue} - \text{Total Expenses}$$
  ```dax
  Net Operating Profit = [Total Hotel Revenue] - [Total Expenses]
  ```

* **Gross Operating Profit Margin (GOP Margin %):**
  $$\text{GOP Margin \%} = \frac{\text{Net Operating Profit}}{\text{Total Hotel Revenue}}$$
  ```dax
  GOP Margin % = 
  DIVIDE([Net Operating Profit], [Total Hotel Revenue], 0)
  ```

* **Revenue Per Available Room (RevPAR):**
  $$\text{RevPAR} = \text{ADR} \times \text{Occupancy Rate \%}$$
  ```dax
  RevPAR = 
  [ADR] * [Occupancy Rate %]
  ```

### 2. Operational & Guest Demographic Metrics

* **Average Length of Stay (ALOS):**
  ```dax
  Average Length of Stay = 
  AVERAGE(Reservations[Number_of_Nights])
  ```

* **Loyalty Program Penetration Rate (%):**
  ```dax
  Loyalty Members % = 
  DIVIDE(
      CALCULATE(
          DISTINCTCOUNT(Guests[Guest_ID]), 
          Guests[Loyalty_Level] <> "None" && NOT(ISBLANK(Guests[Loyalty_Level]))
      ),
      DISTINCTCOUNT(Guests[Guest_ID]),
      0
  )
  ```

* **Repeat Guest Base:**
  ```dax
  Repeat Guests = 
  COUNTROWS(
      FILTER(
          VALUES(Guests[Guest_ID]),
          CALCULATE(COUNTROWS(Reservations)) > 1
      )
  )
  ```

---

## Analytical Modules & Report Architecture

The solution is divided into five dedicated analytical environments accessed through an executive landing navigation portal:

### 1. Executive Macro Overview
* **Primary Focus:** High-level executive pulse monitoring financial KPIs, MoM trends, and monthly seasonal cash flows.
* **Key Visualizations:** Dual-axis monthly revenue versus operational expense trajectories, booking channel revenue breakdown ($1.3M OTA vs. $1.18M Direct), room type revenue distribution, and reservation status breakdowns (Confirmed vs. Cancelled vs. No-Show).

### 2. Rooms & Operational Occupancy
* **Primary Focus:** Granular operational metrics evaluating room category yield and distribution channel effectiveness.
* **Key Visualizations:** Stacked channel volume, room view preference distributions (Sea, Garden, Pool, City), and a detailed performance matrix detailing ADR, Cancellation Rate %, and RevPAR by room type.

### 3. Financial & Departmental Profitability
* **Primary Focus:** Rigorous P&L expense control and ancillary revenue contribution.
* **Key Visualizations:** Waterfall cost categorization separating Fixed ($1.31M) from Variable ($1.27M) expenses, departmental expense distribution (Utilities, Marketing, Maintenance, Housekeeping, F&B), and secondary outlet revenue monitoring across payment methods (Room Charge, Credit Card, Cash).

### 4. Guest Demographics & Behavioral Profiling
* **Primary Focus:** Customer segmentation, guest profile mapping, and loyalty drivers.
* **Key Visualizations:** Dynamic age bracket distributions (`Under 25`, `26-35`, `36-50`, `51-65`, `65+`), loyalty tier classification (Platinum, Gold, Silver, None), gender ratios, and special amenity request distributions (High Floor, Early Check-in, Late Check-out, Non-smoking).

### 5. Root-Cause Revenue Decomposition Tree
* **Primary Focus:** Unconstrained exploratory pathway analysis for senior leadership.
* **Analytical Flow:** Dissects the total $3.16M+ room revenue along recursive branches:
  $$\text{Month} \longrightarrow \text{Booking Channel} \longrightarrow \text{Loyalty Tier} \longrightarrow \text{Room Type} \longrightarrow \text{Special Requests} \longrightarrow \text{Demographics} \longrightarrow \text{Length of Stay}$$

---

## Strategic Business Findings & Recommendations

1. **Direct Channel Margin Optimization:**
   While third-party OTAs drive the highest aggregate volume ($1.30M), direct bookings generate nearly comparable revenue ($1.18M) without third-party commission deductions. Incentivizing direct booking perks can materially lift overall GOP Margin.
2. **F&B as an Essential Profit Driver:**
   Ancillary outlets generated $440K in incremental revenue, with Fine Dining accounting for over 50% ($223K). Promoting specialized dining packages during room booking will optimize Average Revenue Per Guest.
3. **Core Demographic Concentration:**
   The primary booking segment resides in the 51–65 age demographic, followed closely by the 36–50 demographic. Operational tailoring (e.g., quiet room locations, health amenities, flexible check-outs) directly correlates with the observed 90%+ repeat guest affinity.

---

## Project Repository Structure

```text
├── datasets/
│   ├── Fact_Reservations.csv
│   ├── Fact_Expenses.csv
│   ├── Fact_FB_Sales.csv
│   ├── Dim_Guests.csv
│   └── Dim_Rooms.csv
├── documentation/
│   ├── data_dictionary.md
│   └── dax_reference_guide.md
├── reports/
│   └── Apex_Hospitality_Intelligence_Suite.pbix
└── README.md
```

---

## Author & Contact Information

* **Lead Business Intelligence Developer:** Abdallah Rady
* **Email:** a.radyy960@gmail.com
* **Phone / WhatsApp:** +20 109 604 3027
* **Specialization:** Enterprise BI Architecture, Advanced DAX Modeling & Revenue Intelligence
