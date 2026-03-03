# DanioVision Swim Analysis (Multi-Plate)

A Jupyter notebook for analysing zebrafish locomotor activity data exported from DanioVision. Supports single or multi-plate experiments, light/dark protocols, and 48-well plate layouts.

![Category breakdown example](images/example_category_breakdown.png?v=2)

## Run it online
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/grantrichter04/daniovision-swim-analysis-multiplate/blob/main/zebrafish_analysis.ipynb)

## What it does

- Loads one or more DanioVision `.xls` / `.xlsx` exports (auto-detects the UTF-16 TSV format DanioVision actually produces)
- Lets you assign wells to experimental groups and define light/dark periods
- Produces three analyses for both **distance** and **duration**:
  - **Per-well scatter plots** with group means ± 95% CI (t-distribution, not z — honest intervals for small n)
  - **Time-series traces** across periods, with light-period shading
  - **Category breakdown** (large / small / inactive) for Overall, Light, and Dark phases — computed per-fish then group-averaged, so high-activity outliers don't skew proportions
- Exports results to Excel with per-well data, time-series summaries, and category breakdowns

## Quick start

### Requirements

Python 3.8+ with Jupyter. The first cell installs all dependencies:

```
matplotlib, ipywidgets, pandas, numpy, scipy, openpyxl, xlrd
```

### Running

1. Open `zebrafish_analysis.ipynb` in Jupyter Notebook, JupyterLab, or Google Colab
2. Run **Cell 1** (Setup) — installs packages and defines shared helpers
3. Run **Cell 2** (Load data) — paste a file path or upload, then define groups and light periods
4. Run **Cell 3** (Distance) and/or **Cell 4** (Duration) to generate plots and download results

### Loading data

You can either paste file path(s) into the text field (one per line for multi-plate), or use the upload widget. Multi-plate files are auto-prefixed `P1:`, `P2:`, etc. so well IDs stay unique across plates.

### Defining groups

After loading, a copy-paste-ready well grid appears. Paste the relevant wells into each group's text area. Trailing commas are included so you can concatenate rows freely.

### Light/dark periods

Enter period numbers as ranges: e.g. `3-7, 13-17`. Pre-filled with a common protocol. Leave blank to skip the Light/Dark split entirely.

## DanioVision data format

DanioVision exports `.xls` files that are actually **UTF-16 tab-separated text** (not Excel binary). The notebook handles this automatically. Each row is one well × one time period, with columns:

| Column | Description |
|--------|-------------|
| `aname` | Well ID, e.g. `A01-1` (arena suffix is always `-1` for single-arena plates) |
| `pn` | Period number (1, 2, 3, …) |
| `start`, `end` | Period start/end time in seconds |
| `lardist` | Distance (mm) from large movements |
| `smldist` | Distance (mm) from small movements |
| `inadist` | Distance (mm) during inactivity (usually drift — often excluded) |
| `lardur` | Duration (sec) spent in large movement |
| `smldur` | Duration (sec) spent in small movement |
| `inadur` | Duration (sec) spent inactive |

The thresholds between inactive, small, and large movement are set in the DanioVision protocol (`inasmlthr` and `smllarthr` columns in the export).

## Analysis notes

- **95% CI** uses the t-distribution rather than z = 1.96. At typical zebrafish group sizes (8–16 wells), this gives wider, more honest intervals.
- **Category percentages** are computed per-fish (each fish's own large/small/inactive proportion), then averaged at the group level. This prevents high-activity fish from dominating the group proportion.
- **Period exclusion** is shared between distance and duration analyses. Pre-filled with `1, 2` to exclude typical acclimation periods.
- **Multi-plate** support auto-prefixes well names (`P1:A01-1`, `P2:A01-1`) so wells from different plates don't collide.

## 48-well plate layout

```
     1      2      3      4      5      6      7      8
A  A01-1  A02-1  A03-1  A04-1  A05-1  A06-1  A07-1  A08-1
B  B01-1  B02-1  B03-1  B04-1  B05-1  B06-1  B07-1  B08-1
C  C01-1  C02-1  C03-1  C04-1  C05-1  C06-1  C07-1  C08-1
D  D01-1  D02-1  D03-1  D04-1  D05-1  D06-1  D07-1  D08-1
E  E01-1  E02-1  E03-1  E04-1  E05-1  E06-1  E07-1  E08-1
F  F01-1  F02-1  F03-1  F04-1  F05-1  F06-1  F07-1  F08-1
```
