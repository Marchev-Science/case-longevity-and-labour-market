# Case: Longevity and the Labour Market

People in Europe live longer than ever before — but do they live longer *in good health*? And what does that mean for who will work, who will retire, who will need care, and who will fill the concert halls, hiking clubs and evening classes?

This case asks you to build a quantitative bridge between **demography** and the **labour market**: to model and forecast how longevity and healthy longevity translate into the potential supply of labour, how the demand for labour evolves, where the two may drift apart, and what the time spent *outside* the labour force — in poor health, or in healthy retirement — implies for the economy.

The case is **multilayered**: it is organised in levels of increasing depth. Solve the levels in order. A team that completes the core levels has solved the case; a team that goes further has more to show and more to say. The final levels are deliberately open — they reward curiosity, not checklist completion.

> **A guiding idea.** The *average* life expectancy tells you about one person. Multiplied across a population, it becomes a *total* stock of expected person-years — a quantity that can be compared with jobs, costs and consumption. This shift from *average* to *total* measures is the conceptual engine of the whole case. See [concepts.md](concepts.md) before you start.

---

## Scope

**Countries** (all analyses cover all eight):

| | |
|---|---|
| Bulgaria (BG) | Poland (PL) |
| Czechia (CZ) | Romania (RO) |
| Germany (DE) | Norway (NO) |
| France (FR) | Switzerland (CH) |

The set deliberately mixes Eastern and Western Europe, EU and non-EU (EFTA) countries, and very different demographic and welfare regimes. Cross-country comparison is part of the analysis, not an afterthought.

**Sex disaggregation.** Demographic indicators and the labour market are strongly shaped by sex: women live longer, men work longer in some countries, retirement rules differ, health trajectories differ. **All tasks must be solved separately for men and women wherever the data permits.** Some economic series (job vacancies, sectoral costs, consumption) are not published by sex — there you must decide, justify and document how to handle it (model totals, apportion by a reasoned key, or argue why disaggregation is not meaningful). Your reasoning is part of the solution.

**Main data source:** Eurostat. Extracting, cleaning and structuring the data yourselves is an explicit part of the case — see [data-guide.md](data-guide.md). Where Eurostat does not carry a needed indicator (notably retirement legislation), finding a credible complementary source is part of the task.

---

## The levels

### Level 0 — Data foundation

Build a clean, reproducible dataset: for each country and each sex, time series of the indicators needed below, extracted from Eurostat (and complementary sources where unavoidable), with documented provenance. Everything later stands on this. A solution whose data step cannot be re-run is a fragile solution.

### Level 1 — Labour supply

1.1. Model and forecast the **average life expectancy**.

1.2. Model and forecast the **average healthy life expectancy** (healthy life years).

1.3. Model and forecast the **population size**.

1.4. Combine them: from the forecasts of average life expectancy and average healthy life expectancy on one hand, and population size on the other, forecast the **total life expectancy** and the **total healthy life expectancy** (in person-years). The total healthy life expectancy is an estimate of the **potential labour supply**.

### Level 2 — Labour demand

2.1. Model and forecast the **number of jobs** = number of employed + number of job vacancies.

2.2. Model and forecast the **required length of service for retirement** (and the statutory retirement age). This is partly a *legal-institutional* indicator: Eurostat does not publish it as a dataset, so you will need sources such as MISSOC comparative tables, OECD *Pensions at a Glance*, or national legislation — and you will need to turn legal rules (which differ by sex and birth cohort, and change over time) into usable time series.

2.3. Combine them: from the forecasts of the number of jobs and the required length of service, forecast the **potential demand for labour** (in person-years).

### Level 3 — Balance

3. Model and forecast the **relationship between potential labour supply and potential labour demand**, and on this basis identify possible **imbalances in the labour market** — by country, by sex, and over time. Where do shortages loom? Where surpluses? Do men's and women's markets diverge?

*A team that has reached this point has a complete solution to the case.*

### Level 4 — The cost of unhealthy years

4. The difference between average life expectancy and average healthy life expectancy is the **average life expectancy in poor health**; the corresponding difference between the totals is the **total life expectancy in poor health** (in person-years). These years put pressure on the health and social systems. Model and forecast the relationship between the total life expectancy in poor health and the **costs of the "Human health and social work" sector** (NACE Rev. 2 section Q).

### Level 5 — The healthy retirement dividend

5. The difference between average healthy life expectancy and the retirement age is the **average healthy life expectancy after retirement**; the corresponding total is the **total healthy life expectancy after retirement** (in person-years). These years generate demand for services for older adults — hobby clubs, sport, cultural events, excursions, adult education and more. Model and forecast the relationship between this total and the **consumption of services** in the sectors "Accommodation and food service" (NACE I), "Education" (NACE P) and "Arts, entertainment and recreation" (NACE R) — or, on the consumption side, the corresponding COICOP purposes (restaurants & hotels; education; recreation & culture).

### Level 6 — Open horizons

If you have come this far, the case becomes yours. Possible directions — pick, combine, or invent your own:

- **Longevity → labour force → macroeconomy.** Link healthy lifespan and labour supply to growth, productivity or other macroeconomic indicators, and/or to societal indicators (quality of life, well-being).
- **Longevity → consumption and innovation.** How do longer, healthier lives reshape consumption structure and demand for innovation?
- **Innovation → longevity.** Reverse the arrow: does innovation (e.g. health R&D, technology adoption) measurably extend life and healthy life?
- **Retirement legislation in depth.** A comparative legal analysis of retirement rules by country and sex, yielding a sharper assessment of the effective workforce than statutory headline ages allow.

There is no prescribed method anywhere in this case. Regressions, time-series models, state-space models, Bayesian approaches, machine learning, simulation — anything goes, provided you can defend it, validate it, and explain it. Two well-defended competing models are worth more than one undefended "best" model.

---

## What a solution looks like

There is no fixed template. A convincing solution typically contains:

- a **reproducible data pipeline** (raw extraction → cleaned datasets), with sources documented;
- **models with stated assumptions**, validated out of sample where the data allow, with uncertainty made visible (forecast intervals, scenarios or sensitivity analysis);
- **results by country and by sex**, with cross-country comparison;
- an honest **discussion of limitations** — short series, structural breaks, missing disaggregation, heroic assumptions;
- a **narrative**: what did you find, and why should anyone care?

How you split this between notebooks, scripts, reports and presentations is up to you.

## This repository is read-only

This repository contains the **case description only**. Do not commit your solution here — no raw data, no notebooks, no code, no outputs. Create your **own repository (or repositories)** for the team's work, link back to this case from its README, and choose your own license for it.

A structure that has served teams well (purely a suggestion — reorganise freely):

```
<your-team-repo>/
├── README.md            ← what you did, how to reproduce it, link to the case
├── data/
│   ├── raw/             ← as downloaded, never edited by hand
│   └── processed/       ← cleaned, analysis-ready
├── notebooks/           ← exploration and modeling
├── src/                 ← reusable extraction/processing code
├── outputs/             ← figures, tables, forecasts
└── docs/                ← report, presentation, definitions file
```

## Companion documents

- [concepts.md](concepts.md) — what the key terms mean and how the average→total logic works. Read this first.
- [data-guide.md](data-guide.md) — where exactly the data lives on Eurostat and how to get it.
- [team-and-practices.md](team-and-practices.md) — a proposed division of roles and recommended working practices. Proposals, not prescriptions.

## License

This case is released under the [MIT License](LICENSE). Use it, fork it, build on it.
