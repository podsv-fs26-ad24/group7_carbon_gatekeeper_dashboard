# Project Overview

This document is the single source of truth for the **Carbon Gatekeeper Dashboard**. It provides a high-level orientation for anyone onboarding to the project or seeking a concise understanding of what the dashboard does, who it serves, how it is built, and where it currently stands. For phase-specific detail, see the [Project Charta](project_charta.qmd), [Data Report](data_report.qmd), [Visualization Design Report](viz_design_report.qmd), and [Evaluation Report](evaluation.qmd).

---

## 1. Executive Summary

The **Carbon Gatekeeper Dashboard** is an interactive, web-based decision-support tool that links planned business travel directly to annual CO₂ budgets, moving the organization from retrospective emissions reporting to tactical, point-of-planning guidance. It is built for sustainability and travel management stakeholders who need to monitor budget compliance across Business Units, identify lower-carbon alternatives for planned routes, and quantify the savings achievable by shifting transport modes. The tool is delivered as a high-fidelity Streamlit prototype, accessible through a standard web browser with no additional software.

---

## 2. Core Objectives & Value Proposition

The dashboard addresses a central organizational gap: travel emissions are typically reviewed *after* the fact, when reduction opportunities have already passed. The Carbon Gatekeeper closes that gap by analyzing planned itineraries against historical route data **before** trips are taken. It is designed to answer three core questions:

- **What is the current CO₂ budget status and compliance across all Business Units (BUs)?**
- **Which specific routes in our planned travel have viable, lower-carbon alternatives based on historical data?**
- **How much CO₂ can we save by actively shifting to these recommended transport modes?**

The value proposition rests on three qualitative objectives that run through every design decision:

- **Actionability** — The tool automatically scans user-provided trip lists, surfaces routes with the largest aggregate saving potential, and lets users apply a mode-shift scenario in a single click.
- **Transparency** — A clear, hierarchical view of emissions lets users distinguish performance between Business Units and inspect the route-level evidence behind every recommendation.
- **Accessibility** — Stakeholders reach the tool directly through a web browser via a Streamlit deployment, without manual data pulls from separate systems.

**Out of scope:** predictive forecasting of future travel demand, full-footprint accounting beyond business travel, and automated enforcement (the tool recommends and alerts but does not block bookings).

---

## 3. Target Audience / Personas

The dashboard serves four stakeholder roles, each with a distinct relationship to the data:

- **Sustainability Manager** *(primary user, very high interest)* — Responsible for company-wide CO₂ monitoring and sustainability reporting. Needs to answer, at any moment, whether the company is within budget and, if not, why. The dashboard replaces manual evaluations and underpins reporting to senior management and external stakeholders.
- **Travel Manager** *(very high interest)* — Owns operational coordination of all business travel and policy enforcement. Uses the dashboard as a daily tool to spot policy violations — for example, flights booked where a viable alternative connection existed. The transport-mode comparison is the core feature for this role.
- **Finance / Controlling** *(medium interest)* — Tracks travel-related spend and budget adherence across departments. Focuses on cost KPIs (total spend, cost per trip, per department, per transport mode); CO₂ data is secondary unless emission-related fees become budget-relevant.
- **Management / Corporate** *(high interest)* — Senior decision-makers who set CO₂ reduction goals and budget thresholds. They require a clean, top-down summary — current budget consumption, overall spend, and trend direction — and value the transport-mode comparison for strategic decisions such as mandatory rail policies below a distance threshold. They need clear indicators, not analysis tools.

These roles are further grounded in two literature-based personas, **Daniel Schmid** and **Anna Meier**, documented in the Project Charta.

---

## 4. High-Level Architecture & Tech Stack

The dashboard is a **single-page Streamlit application** built entirely in Python, with a persistent left sidebar for inputs and a fixed-width main canvas (max 1400 px).

**Technology stack:**

- **Python 3** — implementation language
- **Streamlit** — web app framework, layout, and session state management
- **Plotly** (`graph_objects`) — all interactive charts and the geographic connection map
- **pandas** — data loading, route aggregation, and scenario computation
- **NumPy** — distance calculations (haversine) and emission-factor fallback
- **openpyxl** — Excel I/O

**Data flow.** The pipeline runs from a raw export to two processed tables consumed by the app:

1. `traveldata-export.xlsx` (raw export, ~25,500 records) → manual cleaning in Excel plus a derived 2026 CO₂-budget assumption →
2. `traveldata-export_clean.xlsx` — the **historical reference file** loaded into the app (`travel_data` 25,527 × 20, `data_dictionary`, `budget_2026`). This establishes route averages and per-BU budgets.
3. `input_data.xlsx` — the **planned-trips file** uploaded via the Streamlit sidebar, defining the scenario being analyzed.

At runtime, the user uploads the required historical reference file and an optional planned-trips file. Planned trips need only `business_unit`, `transport_mode`, `departure_iata`, and `arrival_iata`; coordinates, distance, and CO₂ are **enriched on the fly** from the historical reference. All calculations use a user-selectable CO₂ metric — **CO₂e RFI2 (t)** or **CO₂e RFI2.7 (t)** — so the dashboard adapts to whichever radiative-forcing standard the organization reports against.

**Reproducibility & deployment.** Source is maintained in the [GitHub repository](https://github.com/podsv-fs26-ad24/ad24-7-fancyproject) on `main`. The documentation is a Quarto project deployed to GitHub Pages via a GitHub Actions workflow, using frozen (`_freeze`) computation results so the runner needs no Python environment.

---

## 5. Key Features & Modules

The dashboard is organized into four thematic sections following an **overview → insight → action** narrative, stacked vertically with implicit scroll-based navigation:

- **Overview KPIs** — Top-level cards showing total CO₂ emissions, budget utilization, reduction potential, and number of analyzed trips, accompanied by a traffic-light status banner. A metadata strip states source file, period, trip count, scenario, reference, and method up front.
- **BU Performance (Gauges & Bar Chart)** — Four per-BU gauges using green / yellow / red zones (under, approaching, over budget), paired with a dual-encoding horizontal bar chart where each bar shows current CO₂ (solid) and additional saving potential (hatched) against the budget (dotted reference line). Plain-language banners verbalize status (e.g., *"BU1 on track (57% used)"*) for the Management persona.
- **Geographic Distribution (Connection Map)** — A great-circle connection map where line width scales with route CO₂ and color encodes transport mode, with a region selector (World, Europe, Americas, Asia) for re-projection.
- **Reduction Levers (Alternatives Table & Route Deep-Dive)** — A ranked table of routes with viable alternatives, detailing flight count, average flight CO₂, average alternative CO₂, saving in tonnes, and saving percentage. Selecting a row opens a route-level deep-dive (map inset, CO₂-vs-duration combo chart, and per-mode comparison cards) that turns the ranked list into an evidence-based recommendation.

**Central interaction — "Apply alternatives" toggle.** A single button switches the dashboard into an *optimised scenario*, recomputing the KPIs, banner, BU gauges, bar chart, and map under the assumption that every flight with a greener alternative has been shifted. A reset button restores the *as-planned* view. Supporting interactions include the CO₂-metric switch, the region selector, row-level drill-down, hover details-on-demand, and a collapsed *Detail data and export* expander for raw row-level data.

---

## 6. Current Project State & Next Steps

**Currently functional.** The prototype is **high-fidelity** — a fully interactive web implementation rendered from real data, with all four thematic sections, all interactions (scenario toggle, drill-down, region selector, hover), responsive Streamlit layout, and data export in place. A systematic evaluation (heuristic, task-based, and insight-based methods, assessed against the Charta's success criteria) found that the dashboard supports key analytical tasks, communicates sustainability insights clearly, provides actionable reduction recommendations, and offers transparent, exportable outputs. The reduction-lever functionality generated the strongest analytical value during testing.

Based on these results, the team's checkpoint decision was to **proceed to deployment with minor refinements**.

**Known limitations.** The geographic map can become visually dense with globally distributed, high-density route networks; less experienced users occasionally needed additional orientation; and some participants wanted more contextual explanation of the recommendation logic. The evaluation itself was prototype-based with a limited sample, did not simulate real organizational deployment, and did not fully test performance on very large datasets.

**Planned refinements & next steps (future work):**

- Additional interaction guidance for complex visualizations
- Enhanced filtering options for geographic route analysis
- Improved contextual explanations for recommendation logic
- Further accessibility and responsiveness refinements
- The **Deployment** phase (`deployment.qmd`), including data-refresh needs, access control, and any additional requirements surfaced during evaluation

> **Note on assumptions.** The 2026 CO₂ budgets are derived rather than supplied: the team continued the historical reduction trajectory of roughly −10 % per year (2020–2025), setting each BU's 2026 budget to *2025 × 0.9*. These figures (BU1 207.5 t, BU2 211.5 t, BU3 240.9 t, BU4 181.2 t; total 841.1 t) are the reference used by every gauge, banner, and KPI.
