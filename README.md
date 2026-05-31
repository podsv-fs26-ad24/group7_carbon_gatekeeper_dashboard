# Carbon Gatekeeper Dashboard

An interactive, web-based decision-support tool that links planned business travel to annual CO₂ budgets. The dashboard analyses planned itineraries against historical travel records to answer three operational questions:

- What is the current CO₂ budget status and compliance across all Business Units?
- Which planned routes have viable lower-carbon transport alternatives?
- How much CO₂ can be saved by shifting to those alternatives?

A central **"Apply alternatives"** toggle recomputes the entire dashboard — KPIs, Business Unit gauges, bar charts, geographic map, and reduction-levers table — to quantify projected savings, turning emissions data into actionable, point-of-planning guidance.

---

## Project Organisation

The project follows a structured visualization product development process. Here is what matters most for getting oriented:

**The app**

The dashboard entry point is `deployment/app.py`. This is the file you run with Streamlit.

**The data**

| File | Path | Description |
|:-----|:-----|:------------|
| `traveldata-export_clean.xlsx` | `data_acquisition/data_clean/` | Historical travel records and CO₂ budgets — required to run the dashboard |
| `input_data.xlsx` | `data_acquisition/input_data/` | Planned trips for analysis — upload alongside the historical file |
| `traveldata-export.xlsx` | `data_acquisition/raw/` | Original raw data, for reference only |

**Exploratory analysis**

`eda/eda_travel_data.ipynb` contains the full exploratory data analysis of the historical dataset.

**Documentation**

All project documentation lives in `docs/` as Quarto (`.qmd`) files:

| File | Content |
|:-----|:--------|
| `project_charta.qmd` | Project goals, stakeholders, and visualization concept |
| `data_report.qmd` | Data sources, structure, and quality |
| `viz_design_report.qmd` | Visual encoding decisions and dashboard design |
| `evaluation.qmd` | Usability evaluation and findings |
| `deployment.qmd` | Deployment setup and architecture |

A pre-rendered version of the documentation is available in `docs/build/` — open `docs/build/index.html` in a browser to view it without building.

---

## Tech Stack

- **[Streamlit](https://streamlit.io/)** — interactive web application framework
- **[Plotly](https://plotly.com/python/)** — interactive charts and geographic maps
- **[Pandas](https://pandas.pydata.org/)** — data manipulation and processing
- **[uv](https://docs.astral.sh/uv/)** — Python environment and package management
- **[Quarto](https://quarto.org/)** — project documentation

---

## Getting Started

### Prerequisites

Make sure [uv](https://docs.astral.sh/uv/getting-started/installation/) is installed.

### Setup

Clone the repository and create the Python environment:

```bash
git clone https://github.com/podsv-fs26-ad24/ad24-7-fancyproject.git
cd ad24-7-fancyproject
uv sync
```

### Environment Variables

Copy the provided template and rename it to `.env`:

```bash
cp .env.template .env
```

Edit the `.env` file if needed to configure any environment-specific variables.

### Run the App

```bash
uv run streamlit run app.py
```

The dashboard will open automatically in your browser, or can be accessed at `http://localhost:8501`.

---

## Input Data

The dashboard requires two Excel files, both located in `data_acquisition/data_clean/`. Upload them via the sidebar when the app starts.

| File | Location | Role | Required |
|:-----|:---------|:-----|:--------:|
| `traveldata-export_clean.xlsx` | `data_acquisition/data_clean/` | Historical travel records including CO₂ baselines per route and annual CO₂ budgets per Business Unit | ✅ Yes |
| `input_data.xlsx` (`input_data2.xlsx` – `input_data4.xlsx`) | `data_acquisition/data_clean/` | Planned trips to be analysed against the historical data | ✅ Yes |

The historical reference file is required for the dashboard to function. Without it, no data can be loaded. The planned trips file enables route comparison, alternative transport recommendations, and CO₂ savings calculations.

---

## Usage

Once the app is running:

1. **Upload the historical reference file** (`traveldata-export_clean.xlsx`) in the sidebar.
2. **Upload a planned trips file** (e.g. `input_data2.xlsx`) in the sidebar.
3. **Explore the dashboard** — it is organised into four sections following an Overview → Insights → Action narrative:

| Section | Description |
|:--------|:------------|
| **KPI Overview** | Total CO₂ emissions, budget utilisation, reduction potential, and number of analysed trips |
| **BU Budget Monitor** | Gauge indicators and bar charts comparing actual emissions against CO₂ budgets per Business Unit |
| **Geographic Distribution** | Connection map showing travel routes by transport mode, globally and by region |
| **Reduction Levers** | Ranked list of routes with the highest CO₂ saving potential, including modal alternatives and estimated savings |

4. **Click "Apply alternatives"** to activate the optimised scenario. The dashboard shifts applicable flights to lower-CO₂ alternatives and recomputes all KPIs, gauges, and charts to show projected savings.
5. **Export data** from the Route Deep-Dive section for reporting or further analysis.

---

## Exploratory Data Analysis

A standalone EDA notebook is available at `eda/eda_travel_data.ipynb`. It covers data structure and quality, univariate and multivariate analysis, temporal trends, geographic patterns, and CO₂ budget comparisons. Run it with:

```bash
uv run jupyter notebook eda/eda_travel_data.ipynb
```

---

## Documentation

The full project documentation — including the Project Charta, Data Report, Visualization Design Report, Evaluation, and Deployment — is built with Quarto and located in the `docs/` folder.

To build and preview the documentation locally:

```bash
cd docs
uv run quarto preview
```

To render the full documentation website:

```bash
cd docs
uv run quarto render
```

The rendered site is output to `docs/build/`. Open `docs/build/index.html` in a browser to view it.

---

## Team

| Name | Role |
|:-----|:-----|
| Michelle Linares M. | Project coordination, visualization design |
| Domenik Bächler | Data analysis and processing |
| Dario Filippone | Dashboard development |
| Ajna Binaki | User analysis and evaluation |

---

## License

This project is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).
