# Storm and Quiet-Time Identification

Selects geomagnetic storm events and storm-free quiet periods from a SYM-H storm catalog. The notebook produces:

- **100 storms**, stratified so the number of storms in the following 60 days matches the full catalog's distribution.
- **100 quiet windows**, each 60 continuous days with no storm activity, starting on unique UTC days.
- **The full catalog** with 60-day follow-up statistics added.

## Files

| File | Description |
|---|---|
| `storm_identification.ipynb` | Main notebook |
| `storm_list.csv` | Input storm catalog |
| `storm_list_with_60d_columns.csv` | Full catalog with follow-up columns |
| `selected_storms_100.csv` | Sampled storms |
| `selected_quiet_windows_100.csv` | Sampled quiet windows (`window_start`, `window_end`, `gap_id`) |

All CSVs are comma-delimited, and times are UTC.


## Method

**Follow-up statistics.** For each storm, the notebook counts storms whose main phase starts within the next 60 days, in the interval (t, t + 60 d]. It adds `n_storms_next_60d`, `mean_symh_next_60d` (mean minimum SYM-H of those storms), and per-classification counts `n_next_60d_<type>`. Storms within 60 days of the end of the catalog are excluded from sampling, since their windows are truncated.

**Storm sample.** A proportional stratified sample on `n_storms_next_60d`, with storms drawn at random within each follower count.

**Quiet windows.** Storm intervals (`Initial Phase Start` to `Recovery Phase End`) are merged where they overlap, and the gaps between them are the quiet periods. Each quiet window is 60 days lying entirely inside one gap. Windows may overlap each other.

## Usage

```bash
pip install pandas numpy matplotlib tqdm
jupyter notebook storm_identification.ipynb
```

Run all cells in order. Random draws are seeded (`np.random.default_rng(random_seed)`), so results are reproducible.

## Caveats

- The 60-day follow-up windows of the selected storms can overlap each other.
- Only a few quiet gaps are 60 days or longer, so the quiet windows cluster near solar minimum (mostly 2008–2009 and 2018–2020). Comparisons between storms and quiet windows are confounded with solar cycle phase.

