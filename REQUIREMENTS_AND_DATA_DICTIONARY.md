# Requirements and Data Dictionary

**Project:** Green Olive Tree Executive Dashboard (Power BI)  |  **Author:** Felicia Amponsah  |  **Currency:** USD

## 1. Purpose and scope

Give leadership one view of 28 business initiatives (504 tasks, 8 operation lines) that answers three questions: are we **on time**, **on budget**, and **in control of risk**?

**In scope:** portfolio KPIs, budget vs actual by project, task status, overdue and high-risk work, a project-level task register (drill-through), desktop and mobile layouts.
**Out of scope:** forecasting, resource planning, live data refresh from source systems.

## 2. Stakeholders and requirements

| ID | Stakeholder | Priority | Need | Report page |
|---|---|---|---|---|
| R1 | Managing Director | High | One view of portfolio health, budget headroom and delivery risk | Executive Overview |
| R2 | Finance Controller | High | Budget vs actual, utilisation, early warning of overruns | Executive Overview |
| R3 | Operations Director | High | Overdue work and bottlenecks by initiative | Task Delivery & Risk |
| R4 | Quality & Compliance Manager | High | High-risk tasks and whether they are being mitigated | Task Delivery & Risk |
| R5 | Commercial & E-commerce leads | Medium | Progress of revenue-facing initiatives (wholesale, online, hospitality) | Overview + slicers |
| R6 | Project owners and team leads | Medium | Task owner, status, due date, priority and risk per initiative | Project Details |

**Acceptance criteria**
- Every requirement above maps to at least one page and one KPI or visual.
- A user can go from a portfolio KPI to the exact tasks behind it using page navigation and drill-through.
- Totals reconcile to the CSV: 28 projects, 504 tasks, $1.38M allocated, about $694K spent, 122 overdue tasks.

## 3. Data model

Source CSV, shaped in Power Query into a star schema.

| Table | Grain | Role |
|---|---|---|
| `DimProject` | One row per project | Project name and project budget |
| `FactTasks` | One row per task | Status, owner, dates, priority, risk, spend |
| `Date` | One row per day | Fiscal calendar, FY2024/25 |

`DimProject` filters `FactTasks` by project; the `Date` table supports the fiscal calendar.

## 4. Data dictionary (`Green_Olive_Tree_project_tasks_USD.csv`, 504 rows, 18 columns)

| Column | Type | Description |
|---|---|---|
| Company | Text | Business name (one value: The Green Olive Tree) |
| Currency | Text | Currency of all amounts (USD) |
| Project Name | Text | Initiative the task belongs to (28 distinct) |
| Task Name | Text | Short task title |
| Task Status | Text | Completed, In Progress, Not Started or Overdue |
| Assigned Team Member | Text | Person responsible (504 distinct, one per task) |
| Due Date | Date | Task due date (all tasks: 2024-08-06) |
| Completion Date | Date | Date finished; blank unless completed |
| Priority Level | Text | High, Medium or Low |
| Budget Allocated | Number | Approved budget for the whole project, repeated on each of its tasks |
| Budget Spent | Number | Actual cost for the whole project, repeated on each of its tasks |
| Resource Utilization | Number | Resource use for the project, in percent |
| Risk Level | Text | High, Medium or Low |
| Business Operation Line | Text | One of 8 operation lines the task is mapped to |
| Business Task | Text | Business-language description of the task |
| Line Owner | Text | Role responsible for the operation line |
| Task Budget Allocated | Number | Project budget split evenly across its tasks |
| Task Budget Spent | Number | Project spend split evenly across its tasks |

**Handling notes:** project-level budget columns repeat on every task row, so sum them from `DimProject` (one row per project) and never from `FactTasks`, or budgets are multiplied. Use the task-level budget columns for task-level views.

## 5. Report measures

Described by purpose; the exact DAX is in the PBIX.

| Measure | Meaning |
|---|---|
| Total Projects / Total Tasks | Count of projects and of tasks in the current filter |
| Total Budget / Total Actual Cost | Approved budget and spend, summed once per project |
| Budget Variance | Budget remaining (allocated minus actual) |
| Budget Utilization % | Actual cost as a share of allocated budget |
| Overdue Tasks | Tasks with status Overdue |
| High Risk Tasks | Tasks with Risk Level High |
| On-Time Completion % | Share of completed tasks finished on or before their due date |
| Budget Status Colour | Colour flag for projects approaching their budget ceiling |
| Selected Project | Project chosen for drill-through |

