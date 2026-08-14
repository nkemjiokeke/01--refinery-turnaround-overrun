# Data Dictionary — Refinery Turnaround Work Orders (Simulated)

**File:** `refinery_turnaround_workorders_simulated.csv`
**Grain:** one row = one **work order (job package)** inside a turnaround event.
**Size:** 900 work orders across 4 turnaround events, 2021–2024.
**⚠️ Simulated data.** Generated to mirror the real structure and behaviour of refinery turnaround work-order registers. It is *not* from any real plant. See `SOURCES.md`.

> This is a **representative extract** — a real major turnaround can contain 3,000–10,000 work orders. 900 rows across four events is enough to benchmark and find drivers without being unwieldy.

## Columns

| Column | Type | Description |
|---|---|---|
| `work_order_id` | text | Unique job-package ID (WO-00001 …). Primary key. |
| `turnaround_id` | text | The turnaround event this job belongs to (e.g. `TAR-2022-FCC`). Use to group/benchmark events. |
| `unit` | text | Process unit under turnaround (Crude Distillation, FCC, Hydrocracker, Coker). |
| `discipline` | text | Craft doing the work (Mechanical Rotating/Static, Piping, Valves, Inspection/NDT, Instrumentation, Electrical, Scaffolding, Insulation, Civil, Catalyst Handling). |
| `job_type` | text | Inspection, Repair, Replacement, Cleaning, Overhaul. |
| `work_category` | text | **Key field.** `Planned` = in the frozen scope; `Discovery` = extra work found once equipment was opened; `Emergent` = unplanned work added mid-execution. |
| `on_critical_path` | Y/N | Whether the job sits on the critical path (its slip delays the whole turnaround). |
| `contractor` | text | Crew responsible (In-House + four contractors). One is a deliberately weaker performer — you should find it, not be told it. |
| `planned_start` / `planned_finish` | date | Baseline schedule dates. |
| `actual_start` / `actual_finish` | date | As-executed dates. |
| `planned_hours` | number | Estimated labour hours (the estimate). |
| `actual_hours` | number | Actual labour hours burned. |
| `crew_size` | int | Workers assigned. |
| `planned_cost` | number ($) | Estimated cost = planned labour + planned material. |
| `actual_cost` | number ($) | Actual cost = actual labour + actual material. |
| `material_cost_planned` / `material_cost_actual` | number ($) | Material portion, budget vs actual. |
| `material_ready_on_time` | Y/N | Was material staged before the job was due to start. |
| `rework_flag` | Y/N | Whether the job required rework (quality miss). |
| `safety_standdown_hours` | number | Hours lost to safety stand-downs on that job. |
| `primary_delay_reason` | text | Dominant reason a job overran: On Plan, Scope Growth / Discovery, Material Delay, Rework / Quality, Safety Stand-down, Labor Productivity, Permit / Isolation Wait, Inspection Hold. |

## Relationships / how to model it
- It's a single fact table at work-order grain. You can build small **dimension** lookups from it: `dim_event` (turnaround_id, unit, year), `dim_contractor`, `dim_discipline`.
- Derive: `hours_variance = actual_hours − planned_hours`, `cost_variance = actual_cost − planned_cost`, `performance_factor = actual_hours / planned_hours`, `schedule_slip_days = actual_finish − planned_finish`.

## Known, intentional signal (do not quote in your README — go find it yourself)
The data was built so that a real analyst *can* discover: (a) most of the overrun is concentrated in a few drivers, (b) one contractor underperforms, (c) discovery/emergent work behaves very differently from planned work, and (d) material readiness matters. Your job is to prove or disprove each with the numbers.
