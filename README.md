# Incident_management_PowerBi
Incident_management_Dashboard

**Overview**

This is an end-to-end **Incident Management Power BI Dashboard** built from scratch using a custom-generated realistic dataset simulating a production-grade IT incident management system.

The project covers the full data pipeline — from raw Excel data ingestion, Power Query transformation, data modelling, DAX measure creation, to multi-page interactive dashboard publishing.

The dashboard is designed around real-world business rules including SLA tracking, warranty spike detection, priority-based incident management, and aging analysis across two platforms.

---

**Key Features**

- **Active Incident KPI Tracking** — Total active incidents, SLA breach %, reassignment count, priority breakdown, and customer complaints
- **SLA Monitoring** — Real-time SLA percentage tracking with breach detection and bucketed analysis
- **Aging Analysis** — Incidents categorised by age (0–10, 11–20, 21–30, Over 30 days) and days since last update
- **Incident Trend Analysis** — Line chart with drill-down from Year → Month → Week → Day
- **Warranty Spike Detection** — Controlled warranty spikes visible in trend analysis for Home and BULK groups
- **Incidents by State** — Live count of Active, Awaiting User Info, and New incidents
- **Cross-page Sync Slicers** — Platform, Assignment Group, and Manager filters sync across all 3 pages
- **Platform & Group Breakdown** — M&FW vs Payments, Home / Landlord / BULK / ICS comparison

---

## 📊 Dashboard Pages

### Page 1 — Active Incidents Dashboard
Focuses on currently open incidents with KPI cards, aging tables, incidents exceeding 30 days, and incidents not updated in 7+ days.

### Page 2 — SLA Raw Data
Sortable table of all active incidents ranked by SLA percentage with a bucketed bar chart showing SLA distribution across platforms.


### Page 3 — Incident Trend Analysis
Line chart showing total incident volume and warranty incidents over time with drill-down hierarchy and platform/group slicers.

---

## Data Modelling

- **Star Schema** — Incident Data (fact table) connected to DateTable (dimension)
- **Dedicated DateTable** — Created using DAX `CALENDAR()` with Year, Month, Week, and Day columns
- **Relationships** — Many-to-one relationship from Incident Data[Created] to DateTable[Date]
- **Calculated Columns** — IsOpen, AgeingBucket, UpdateBucket, SLA Bucket, MonthCreated, WeekNum2, DayDate2 and sort columns for each
- **Sort Columns** — MonthSort, WeekSort, DaySort, AgeingBucketSort, UpdateBucketSort, SLABucketSort — ensures correct ordering in all visuals

---

## 📐 DAX Measures

| Measure | Purpose |
|---|---|
| `Total Active Incidents` | Count of all open incidents (IsOpen = 1) |
| `Total Incidents` | Count of all 1350 incidents |
| `Over 30 Days` | Open incidents older than 30 days |
| `Last Updated > 7 Days` | Open incidents not updated in 7+ days |
| `Reassignment > 5` | Incidents reassigned more than 5 times |
| `P2 Active` | Open P2 priority incidents |
| `P3 Active` | Open P3 priority incidents |
| `P4 Active` | Open P4 priority incidents |
| `Customer Complaints Active` | Open incidents with complaint = Yes |
| `SLA Breached %` | % of open incidents with SLA >= 100% |
| `Active Count` | Open incidents with status = Active |
| `Awaiting User Info Count` | Open incidents awaiting user response |
| `New Count` | Open incidents with status = New |
| `Warranty Incidents` | Incidents with Warranty status |
| `MFW Incidents` | Incidents on M&FW platform |
| `Payments Incidents` | Incidents on Payments platform |
| `Last Refresh` | Live timestamp of latest report refresh |

---

## 🔄 Power Query (M Code)

- Loaded Excel dataset and verified all 13 column data types
- Created **IsOpen** custom column using M language:
```m
if [Status] = "Resolved" or [Status] = "Warranty" then 0 else 1
```
- Created **SLA Bucket** custom column:
```m
if [SLA Percentage] <= 50 then "0 to 50%"
else if [SLA Percentage] <= 75 then "50 to 75%"
else if [SLA Percentage] <= 85 then "75 to 85%"
else if [SLA Percentage] <= 100 then "85 to 100%"
else "Over 100% (Breached)"
```
- Date columns (Created, Updated Date, Closed Date) stored as proper `datetime64` in source Excel

---

## 📁 Dataset Description

The dataset (`Incident_Management_Dataset_v2.xlsx`) is a custom-generated realistic dummy dataset with **1,350 rows** simulating a production incident management system.

| Column | Type | Description |
|---|---|---|
| Number | Text | Unique incident ID (INC003XXXX) |
| Priority | Text | P1 / P2 / P3 / P4 |
| Created | DateTime | Incident creation timestamp |
| Status | Text | New / Active / Awaiting User Info / Awaiting Third Party / Warranty / Resolved |
| Customer Complaint | Text | Yes / No |
| Assigned To | Text | Employee name (20 realistic names) |
| Assignment Group | Text | Home / Landlord / BULK / ICS |
| Platform | Text | M&FW / Payments |
| Manager | Text | Swagat (M&FW) / Himanshu (Payments) |
| Reassignment Count | Number | 1–8 |
| Updated Date | DateTime | Last update timestamp |
| SLA Percentage | Decimal | 0–200% |
| Closed Date | DateTime | Populated only for Resolved/Warranty |

### Business Rules Applied
- **P1 incidents** — exactly 1 per assignment group, all closed
- **P2 incidents** — 1–15 per group, all closed, no "Awaiting Third Party"
- **January** — all incidents closed (Resolved or Warranty)
- **February** — fewer than 10 open incidents
- **March 17+** — no closed incidents
- **Warranty Spikes:**
  - Home: Jan 16–19 (53 incidents), Feb 16–24 (87 incidents)
  - BULK: Jan 5–12 (67 incidents), Feb 9–13 (58 incidents)

---

## 🛠 Tools Used

| Tool | Purpose |
|---|---|
| **Power BI Desktop** | Dashboard development |
| **Power Query (M)** | Data transformation and column creation |
| **DAX** | Calculated columns and measures |
| **Microsoft Excel** | Source dataset (datetime-typed columns) |
| **Python (pandas, openpyxl)** | Dataset generation with business rules |

---

## 📂 Project Structure

```
incident-management-dashboard/
│
├── 📊 Incident_Management_Dashboard.pbix     # Power BI report file
├── 📁 Incident_Management_Dataset_v2.xlsx    # Source Excel dataset
├── 📸 screenshots/
│   ├── page1_active_incidents.png
│   ├── page2_sla_raw_data.png
│   └── page3_trend_analysis.png
└── 📄 README.md
```

---

## 🎯 Business Impact

This dashboard helps identify and monitor:

- **SLA breaches** — instantly see which incidents are at risk or already breached
- **Aging incidents** — catch incidents stuck without resolution or update
- **Warranty patterns** — detect unusual warranty spikes by group and time period
- **Workload distribution** — compare incident volumes across platforms, groups and managers
- **Priority compliance** — ensure P1/P2 incidents are resolved and not stuck in wrong statuses
- **Trend patterns** — track whether incident volume is increasing or decreasing over time

---

## 📚 Learnings

This project helped develop hands-on experience in:

- End-to-end Power BI dashboard development
- Writing DAX measures using `CALCULATE`, `DATEDIFF`, `SWITCH`, `DIVIDE`, `FORMAT`
- Power Query M language for data transformation
- Data modelling — relationships, star schema, DateTable
- Solving real-world issues like circular dependencies, date type mismatches, and sort ordering
- Cross-page slicer synchronisation using Sync Slicers
- Designing production-like dashboards with dark themes and KPI cards

---

## 👩‍💻 Author

Built as a personal learning project to develop Power BI skills from scratch — data generation → modelling → dashboard → documentation.

---

> ⭐ If you found this helpful, feel free to star the repo!

