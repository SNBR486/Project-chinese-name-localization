# Too Local or Luxuriously Foreign?
## Chinese-Name Localization and Online Attention in China (Class Project)

This repository contains the data pipeline and replication code for my Class
empirical project **“Too Local or Luxuriously Foreign? The Economic Impact of
Chinese-Name Localization on Multinationals in China.”**

The project studies whether giving multinational firms more localized **Chinese
brand names** increases consumer attention, measured by Baidu Index search
data, for:

- **Supermarkets / warehouse clubs** (Costco, Sam’s Club, ALDI, Carrefour, etc.)
- **Automotive brands** (Polestar, Zeekr, AITO, Genesis, Avatr, etc.)

The core design is a **staggered Difference-in-Differences / event-study**
using brand–city–week panels from **2016–2025**, plus a Borusyak–Jaravel–Spiess
style model-based counterfactual estimator.

---

## Repository structure

```text
root/
├── 2016-2025/              # Supermarket / warehouse-club panel
│   ├── sum/                # Raw weekly Baidu Index exports (brand × city × week)
│   ├── panel_sum_ready.csv # Cleaned long panel, one row per brand–city–week
│   ├── panel_with_events.csv
│   ├── panel_async_ready.csv
│   ├── SDID_panel_async_ready.csv
│   ├── SDID_panel_with_events.csv
│   ├── localization_events_supermarkets_cn_eventlog.csv
│   ├── appendix_A_summary_stats.csv
│   ├── appendix_A_summary_stats_twfe.csv
│   └── DiD.ipynb           # TWFE / SDID event-study for supermarkets
│
├── car_2016-2025/          # Automotive brand panel
│   ├── sum/                # Raw weekly Baidu Index exports (brand × city × week)
│   ├── panel_sum_ready.csv
│   ├── panel_with_events.csv
│   ├── SDID_panel_async_ready.csv
│   ├── SDID_panel_with_events.csv
│   ├── localization_events_auto_cn_eventlog.csv
│   ├── appendix_A_summary_stats.csv
│   ├── appendix_A_summary_stats_twfe.csv
│   └── SDiD.ipynb          # TWFE / SDID event-study for automakers
│
├── 6a.png … 6f.png         # Final figures used in the paper (main TWFE & BJS plots)
├── Ding_Chen_Too_Local_or_Luxuriously_Foreign.pdf   # Final paper (PDF)
└── README.md               # This file
```

### Key CSV files

For each domain (`2016-2025` and `car_2016-2025`):

- `sum/`  
  Raw Baidu Index downloads (one file per brand / per city), directly scraped
  from the Baidu Index web interface. These are the lowest-level inputs and are
  *not* used directly in regressions.

- `panel_sum_ready.csv`  
  Cleaned and stacked brand–city–week panel:
  - `week_monday` — week start date  
  - `brand_slug`, `brand_zh` — English + Chinese brand identifiers  
  - `city_abbr`, `city_name` — city identifiers  
  - `index` — raw Baidu Search Index  
  - `index_log`, `index_z` — log and standardized index  

- `localization_events_*_cn_eventlog.csv`  
  Hand-coded **event log** of naming events and market entries:
  - brand, city (where relevant)  
  - `event_date` — first official adoption of localized Chinese name  
  - `event_type` — e.g. `zh_name_adopt`, `market_entry`  
  - notes and documentation source (Xinhua / corporate website / encyclopedias)

- `panel_with_events.csv`  
  `panel_sum_ready` merged with the event log, plus event-time variables:
  - `rel_week` — weeks relative to event  
  - `abs_rel` — absolute distance to event  
  - `rw_-20` … `rw_+20` — event-time dummies used in the TWFE binned
    event-study.

  This is the main input to the **TWFE** specifications.

- `panel_async_ready.csv` / `SDID_panel_async_ready.csv`  
  Staggered DID / SDID estimation sample:
  - balanced event-time window  
  - keeps only units with complete pre-event coverage  
  - outcome `y` = baseline-anchored log index, as defined in the paper.  
  These are the inputs to the Borusyak–Jaravel–Spiess style counterfactual
  estimator.

- `SDID_panel_with_events.csv`  
  Alternative estimation panel for SDID / imputation-based event studies.

- `appendix_A_summary_stats*.csv`  
  Pre-computed summary statistics (N, mean, std, min, max) used to build
  **Appendix B** in the paper.

---

## Notebooks

The analysis is driven by two Jupyter notebooks:

- `2016-2025/DiD.ipynb`  
  - builds supermarket TWFE event-study plots  
  - runs clustered and wild-cluster inference  
  - constructs the BJS-style counterfactual and gap plots.

- `car_2016-2025/SDiD.ipynb`  
  - repeats the pipeline for automotive brands  
  - generates the main figures (6a–6f) used in the paper.

Each notebook is self-contained: it reads the prepared CSVs in its own folder
and writes figures to the repo root.

---

## Environment

The notebooks assume a basic Python scientific stack, e.g.:

- `python >= 3.10`
- `pandas`
- `numpy`
- `matplotlib`
- `statsmodels`
- `scipy`

A minimal setup using `pip`:

```bash
pip install pandas numpy matplotlib statsmodels scipy
```

Then, from the repository root:

```bash
cd 2016-2025
jupyter notebook DiD.ipynb

cd ../car_2016-2025
jupyter notebook SDiD.ipynb
```

---

## Replication summary

1. **Data preparation**  
   - Start from `sum/` raw exports.  
   - Build `panel_sum_ready.csv` with one row per brand–city–week.  
   - Merge with the hand-coded event logs to obtain `panel_with_events.csv`.

2. **TWFE event study**  
   - Use `panel_with_events.csv` in `DiD.ipynb` / `SDiD.ipynb`.  
   - Estimate binned event-study models with unit and week fixed effects.  
   - Plot dynamic treatment effects by event time and domain.

3. **Model-based counterfactual (BJS-style)**  
   - Use `SDID_panel_async_ready.csv` to fit untreated-only splines.  
   - Construct baseline predictions and event-time gaps for each brand.  
   - Aggregate gaps to produce the figures 6a–6f in the paper.

---

## Citation

If you use this code or data, please cite the term paper:

> Ding, C. (2025). *Too Local or Luxuriously Foreign? The Economic Impact of
> Chinese-Name Localization on Multinationals in China.* class project paper,
> Stony Brook University.
