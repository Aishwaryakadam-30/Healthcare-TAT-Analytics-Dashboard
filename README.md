# Healthcare TAT Analytics Dashboard
**Power BI | DAX | Power Query | SQL**
---
## The Problem

In diagnostics labs, TAT (Turn Around Time) is the metric that keeps 
operations managers up at night. When a test misses its SLA window, the 
downstream effects are immediate: delayed diagnoses, doctor complaints, 
and in high-volume labs, accreditation risk.

The problem is that most labs track TAT as a single headline number. 
They know their overall % IN TAT but not where the breakdown is happening, 
which department, which processing centre, which hour of the day, or which 
patient age group is driving the failures.

This dashboard was built from a formal Business Requirement Document scoped 
for a diagnostics/lab operations client. The goal was to move from a single 
headline number to a full operational picture.

Dataset: 262K+ test transactions across 18 departments, 25 processing 
centres, 17 states, and 240 cities.
---
## Dashboard Pages

### Page 1: Performance Analysis
Three side-by-side bar charts showing % IN TAT broken down by:
- Department Name (18 departments, ranging from 9.56% to 95.89%)
- Processing Centre (25 centres, most hitting 97-100%)
- Age Group (0-5, 6-10, 11-15, 16-20, 20+ years)

Below that: a stacked hourly bar chart tracking sample volume across 
all four journey stages simultaneously:
- Samples Dept to Result Entry
- Samples Result Entry to Approval  
- Samples SRA to Dept Received
- Samples Reg to SRA Received

Peak load sits at 12:00-13:00 with 21K+ samples per stage. The 03:00-04:00 
slot has the lowest volume at 144 samples. That gap of 21,512% between peak 
and trough is not a rounding error — it tells you exactly where to focus 
staffing conversations.

### Page 2: Detailed Drill-Through Table
A fully filterable transaction-level table showing individual test records 
with SIN number, patient name, test name, department, booking date, city, 
and state. Filters on the right panel: Age Group, City, Department, 
Processing Centre, State.

This page answers the question lab managers ask after seeing the summary: 
"Which specific tests are failing?" You can filter to any combination of 
dimensions and get to individual records in two clicks.

### Page 3: AI Narrative Insights
An auto-generated natural language summary of key findings across all 
dimensions, toggled between KPI view, Visual view, and Detailed view.

Key findings surfaced automatically:
- Top 29 cities all hit 100% IN TAT. Across 240 cities, range was 
  35.14% to 100%
- IMMUNOASSAYS had the highest % IN TAT at 95.89%, 902.87% higher than 
  GENERAL EXAMINATION at 9.56%
- Across 18 departments, % IN TAT ranged from 9.56% to 95.89%
- PUDUCHERRY and ARUNACHAL PRADESH tied for highest State % IN TAT at 
  100%. MADHYA PRADESH had the lowest at 81.56%

This page was built using Power BI's AI-powered narrative visual, which 
generates plain-language summaries directly from the data model without 
manual text entry.

### Page 4: Summary KPIs
High-level operational snapshot:
- 262K Total Tests
- 2K Tests Rejected  
- 91.63% IN TAT overall
- 240K Tests IN TAT

State-level % IN TAT bar chart and Department % IN TAT breakdown. 
Six dynamic slicers: Booking Date, SIN No, City, Doctor Name, 
Department Name, Delivery Date. Gender toggle: Female / Male / 
Other / Transgender.
---
## Data Model

Star schema built in Power Query before any DAX measures were written.

**Fact Table:** Test transactions
- Test ID, Registration DateTime, SLA TAT, In TAT Flag
- Stage timestamps (Registration, SRA Receive, Dept Receive, 
  Result Entry, Approval)

**Dimension Tables:**
- Dim Lab / Processing Centre
- Dim Department
- Dim Test Type
- Dim Date (dedicated, for time intelligence)
- Dim Geography (State, City)
- Dim Age Group

Single-direction filtering throughout. All slicers interact dynamically 
across all pages.

Data quality issues handled before modeling: missing timestamps flagged, 
SLA mapping validated against BRD definitions, N/A strings resolved 
in Power Query.
---
## Key DAX Measures

```dax
% IN TAT = 
DIVIDE(
    CALCULATE(COUNTROWS(FactTests), FactTests[InTATFlag] = "In TAT"),
    COUNTROWS(FactTests),
    0
)

Tests OUT TAT = 
CALCULATE(COUNTROWS(FactTests), FactTests[InTATFlag] = "Out TAT")

Total Tests = COUNTROWS(FactTests)
```
---
## What the Data Actually Shows

The GENERAL EXAMINATION department at 9.56% IN TAT is the clearest 
finding in this dataset. It is not a small gap from the next department 
down. It is 902% below IMMUNOASSAYS. That kind of spread within a single 
lab does not come from test complexity alone. It points to workflow 
design, staffing allocation, or SLA definitions that need revisiting.

The hourly journey analysis confirms the bottleneck sits between 
Department Receive and Result Entry, not at registration where most 
people assume the delay is.
---
## Tools

- Power BI Desktop
- Power Query (data cleaning, model prep, stage calculations)
- DAX (KPI measures, % IN TAT, time intelligence)
- SQL (initial data validation)
- Power BI AI Narrative Visual (Page 3 auto-insights)
- PowerPoint (custom dashboard background design)
---
## Business Requirement Document

Full BRD available in the `/BRD` folder. Includes scope definition, 
KPI specifications, data model design, stakeholder list, risk register, 
and success criteria. The dashboard was built to spec against this document.
---
## Files
<img width="1648" height="883" alt="Screenshot 2026-06-22 at 11 22 00 AM" src="https://github.com/user-attachments/assets/281fb92a-165a-45fd-89f7-5046c2322119" />
<img width="1648" height="887" alt="Screenshot 2026-06-22 at 11 22 40 AM" src="https://github.com/user-attachments/assets/841ead70-67d1-4f66-a763-495b793a4d16" />
<img width="1648" height="887" alt="Screenshot 2026-06-22 at 11 23 12 AM" src="https://github.com/user-attachments/assets/315d038d-e1d9-4f55-958a-c689c607e453" />
<img width="1648" height="887" alt="Screenshot 2026-06-22 at 11 23 36 AM" src="https://github.com/user-attachments/assets/7dad3071-ebdc-4dbe-b97f-04d65aea6dae" />
---
## About

Data and reporting analyst with 3+ years building validated pipelines 
and BI dashboards across client and institutional environments. Focused 
on healthcare analytics, operational reporting, and translating complex 
data into decisions non-technical stakeholders can act on.

LinkedIn: linkedin.com/in/aish0830  
GitHub: github.com/Aishwaryakadam-30
