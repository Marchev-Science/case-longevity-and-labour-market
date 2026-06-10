# Data guide

Extracting the data is part of the case, not an inconvenience before the case. This guide tells you *where to look* and *how to get it* — by hand through the website, or programmatically through the API. Both routes are legitimate; a team can mix them. It deliberately does not hand you ready files.

> **Where the data goes:** this case repository is read-only — downloaded data, code and outputs belong in your own team repository.

## 1. Route A — Downloading from the Eurostat website (no programming needed)

This route works for everyone and is also the best way to *understand* a dataset before automating anything.

**Step by step:**

1. Go to the **Eurostat data browser**: https://ec.europa.eu/eurostat/databrowser/
2. In the search box, type the **dataset code** from the tables below (e.g. `demo_mlexpec`) — searching by code is far more precise than searching by words. Open the dataset.
3. You will see a table with default selections. Open the **filter panel** (funnel icon / "Customise" pane) and set the dimensions:
   - **Geo**: tick Bulgaria, Czechia, Germany, France, Poland, Romania, Norway, Switzerland;
   - **Sex**: tick Males, Females (and Total, for checks);
   - **Time**: select all available years;
   - dataset-specific dimensions (e.g. **Age** in `demo_mlexpec`: start with "Less than 1 year" = at birth, and consider 65; **Unit**: usually "Year" or as appropriate).
4. Check the on-screen table looks sensible — this is where you catch a wrong unit or a missing country *before* it poisons your pipeline.
5. Click **Download** and choose a format:
   - *Spreadsheet (xlsx)* — convenient to inspect, less convenient to process;
   - *CSV* — best for analysis; prefer the "full dataset/SDMX-CSV" style export which keeps one observation per row with flags.
6. Save the file into your team repo under `data/raw/` **without editing it**, and record in a small log (a `data/raw/README.md` works): dataset code, filters used, download date, and the URL. Eurostat revises data — your log ties results to a vintage.

Two browser features worth knowing: the **"API" button** on every dataset page shows the exact API query reproducing your current filter selection (a perfect bridge to Route B), and the **flags legend** (`b` break, `p` provisional, `e` estimated) is shown under the table — keep the flags.

For exploring what exists, browse the **database tree** by theme: https://ec.europa.eu/eurostat/web/main/data/database (Population and social conditions → Demography; Health; Labour market; Economy and finance → National accounts).

## 2. Route B — Downloading via the API (reproducible, for the technically inclined)

A pipeline that downloads by dataset code beats a folder of hand-clicked files: it is reproducible, refreshable and reviewable.

### B1. The easy way: the `eurostat` Python package

```bash
pip install eurostat pandas
```

```python
import eurostat

# Whole dataset as a pandas DataFrame (one row per series, years as columns)
df = eurostat.get_data_df('demo_mlexpec')
print(df.head())

# Discover the dimensions and their codes first:
pars = eurostat.get_pars('demo_mlexpec')          # e.g. ['freq','unit','sex','age','geo']
print(eurostat.get_par_values('demo_mlexpec', 'age')[:10])

# Filtered download — only what you need:
my_filter = {
    'geo': ['BG','CZ','DE','FR','PL','RO','NO','CH'],
    'sex': ['M','F'],
    'age': ['Y_LT1'],          # life expectancy at birth; 'Y65' for age 65
}
le = eurostat.get_data_df('demo_mlexpec', filter_pars=my_filter)
le.to_csv('data/raw/demo_mlexpec_at_birth.csv', index=False)
```

The same pattern works for every dataset in this guide — just change the code and the filter:

```python
hly = eurostat.get_data_df('hlth_hlye',  filter_pars={'geo': my_filter['geo'], 'sex': ['M','F']})
pop = eurostat.get_data_df('demo_pjan',  filter_pars={'geo': my_filter['geo'], 'sex': ['M','F'], 'age': ['TOTAL']})
```

A useful habit: reshape to tidy/long format immediately after download, so all series share one schema:

```python
import pandas as pd

def tidy(df, value_name='value'):
    id_cols = [c for c in df.columns if not str(c).isdigit()]
    out = df.melt(id_vars=id_cols, var_name='year', value_name=value_name)
    out['year'] = out['year'].astype(int)
    return out.rename(columns={'geo\\TIME_PERIOD': 'geo'})
```

### B2. The raw way: the Statistics API directly (no special package)

Every dataset is reachable as JSON. The URL pattern:

```
https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/<DATASET>?format=JSON&lang=EN&<dim>=<code>&...
```

```python
import requests

url = "https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/demo_mlexpec"
params = {
    "format": "JSON", "lang": "EN",
    "sex": ["M", "F"], "age": "Y_LT1",
    "geo": ["BG","CZ","DE","FR","PL","RO","NO","CH"],
}
r = requests.get(url, params=params)
r.raise_for_status()
js = r.json()         # JSON-stat 2.0 — values in js['value'], dimensions in js['dimension']
```

JSON-stat takes a little decoding (the `pyjstat` package does it for you: `pyjstat.Dataset.read(r.text).write('dataframe')`). Remember the **"API" button** in the data browser writes this URL for you from your clicked filters. API documentation: https://ec.europa.eu/eurostat/web/user-guides/data-browser/api-data-access

### B3. R users

The CRAN [`eurostat`](https://cran.r-project.org/package=eurostat) package mirrors all of the above: `get_eurostat("demo_mlexpec", filters = list(geo = c("BG","CZ"), sex = c("M","F"), age = "Y_LT1"))` returns a tidy tibble directly.

**Geo codes** for the case countries: `BG`, `CZ`, `DE`, `FR`, `PL`, `RO`, `NO`, `CH`. Norway and Switzerland report to Eurostat as EFTA members — most, but not all, datasets cover them; checking coverage is your job, and gaps are findings, not failures.

## 3. Dataset map by level

Codes below are the natural starting points. Eurostat occasionally reorganises datasets; if a code has moved, the search box in the data browser will find its successor. Verifying and, where needed, replacing these codes is part of Level 0.

### Level 1 — Labour supply

| Indicator | Dataset (code) | Notes |
|---|---|---|
| Life expectancy by age and sex | `demo_mlexpec` | Long annual series; take LE at birth and consider other ages (e.g. 65). |
| Healthy life years by sex | `hlth_hlye` | Short series (~mid-2000s onward); GALI-based; methodology break ~2008. |
| Population on 1 January by sex | `demo_pjan` | By single age also available; `demo_pjangroup` for age groups. |
| Population projections (benchmark) | `proj_23np` (baseline) | Eurostat's own projections — useful to *compare against*, not to copy. |
| Mortality rates / life tables (optional, for own life-table work) | `demo_magec`, `demo_mlifetable` | Only if you want to build LE bottom-up. |

### Level 2 — Labour demand

| Indicator | Dataset (code) | Notes |
|---|---|---|
| Employment by sex and age | `lfsa_egan` (annual LFS) or `lfsi_emp_a` | LFS gives sex × age detail. |
| Employment by sex and NACE activity | `lfsa_egan2` | Needed if you apportion vacancies by sector. |
| Job vacancies / vacancy rate | `jvs_q_nace2` (quarterly, NACE), `jvs_a_rate_r2` (annual rate) | **Not by sex.** Coverage differs by country (some exclude small units or public sector; France's coverage is notably partial). Document comparability limits. |
| Statutory retirement age / required service | **not a Eurostat dataset** | Use MISSOC comparative tables (https://www.missoc.org/missoc-database/), OECD *Pensions at a Glance*, and national legislation. Build your own country × sex × year table; cite sources per cell. |
| Duration of working life (cross-check) | `lfsi_dwl_a` | Eurostat's estimate of expected working-life duration by sex — a good sanity check for your demand-side constructions. |

### Level 3 — Balance

No new data — this level lives on the outputs of Levels 1 and 2. Optionally add unemployment (`une_rt_a`, by sex) and labour-market slack (`lfsi_sla_a`) as observable correlates of the imbalances you forecast.

### Level 4 — Costs of poor-health years

| Indicator | Dataset (code) | Notes |
|---|---|---|
| Value added / compensation by NACE section (incl. Q) | `nama_10_a64` | National accounts; pick and justify your "cost" concept (output, value added, compensation of employees). |
| Employment by NACE section | `nama_10_a64_e` | Sectoral labour input, if useful. |
| Government expenditure by function (COFOG: health, social protection) | `gov_10a_exp` | Alternative cost lens. |
| Health expenditure (SHA 2011) | `hlth_sha11_hf` / `hlth_sha11_hp` | Health-accounts lens; check NO/CH coverage. |

### Level 5 — Consumption in HoReCa, education, culture

| Indicator | Dataset (code) | Notes |
|---|---|---|
| Household consumption by purpose (COICOP) | `nama_10_co3_p3` | Divisions CP09 (recreation & culture), CP10 (education), CP11 (restaurants & hotels). |
| Value added by NACE I, P, R | `nama_10_a64` | Production-side counterpart. |
| Household budget survey detail (optional) | `hbs_exp_t121` and related | More structure, less frequency. |
| Turnover in services (optional, higher frequency) | `sts_setu_a` | Short-term statistics, if you want denser series. |

### Level 6 — Open horizons (examples)

GDP and components `nama_10_gdp`; productivity `nama_10_lp_ulc`; R&D expenditure `rd_e_gerdtot`; patents `pat_ep_ntot`; quality-of-life indicators in the `ilc_*` family; whatever else your question demands.

## 4. Practical caveats (read before modeling)

1. **Units and flags.** Eurostat values carry flags (`b` break in series, `p` provisional, `e` estimated). Keep the flags through your pipeline; a break flag in `hlth_hlye` is information.
2. **Short series.** HLY (~20 annual points) and job vacancies (often from ~2008–2010) will not support complex models. Method must follow data.
3. **COVID shock.** 2020–2021 distorts mortality, employment and consumption simultaneously. Decide explicitly: dummy it, model it, or scenario it — silence is the only wrong option.
4. **Sex dimension.** Available in demographic and LFS datasets (`sex` = `M`, `F`, `T`); absent in vacancies, national accounts and consumption. Your apportionment choices must be written down.
5. **NSA/SA and frequencies.** Quarterly series (vacancies) come seasonally adjusted or not; annualise consciously. Mixing frequencies is fine if documented.
6. **Prices.** National-accounts values come in current prices and chain-linked volumes. For relationships over time, you almost certainly want volumes or deflated values — say which.
7. **Revisions.** Snapshot your raw downloads with a date stamp in your **team repository's** `data/raw/` (not in this case repository, which is read-only). Eurostat revises; your results should be tied to a retrievable vintage.
