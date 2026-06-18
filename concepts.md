# Concepts and key terms

This document explains the concepts the case is built on. None of it is exotic, but the case combines ideas from demography, labour economics and national accounts, and the combination only works if the terms are used precisely.

## 1. Life expectancy (LE)

**Life expectancy at birth** is the average number of years a newborn would live if exposed, throughout life, to the age-specific mortality rates observed in a given year. It is a *period* measure: a summary of *current* mortality conditions, not a prophecy about any actual cohort. It is computed from a **life table** and is published by sex and by age (life expectancy at age 65 is just as legitimate a starting point as at birth — think about which is more relevant for which task).

Key properties for this case:

- Long, reliable annual series exist for all eight countries.
- Women's LE exceeds men's everywhere, but the gap varies enormously across countries — compare Bulgaria with Norway.
- Series contain shocks (notably 2020–2021) that any forecasting model must confront rather than ignore.

## 2. Healthy life expectancy / Healthy Life Years (HLY)

**Healthy life years** (also called disability-free life expectancy) is the average number of years a person is expected to live *without limitation in usual activities*. Eurostat computes it with the **Sullivan method**: the life table is combined with age-specific prevalence of activity limitation taken from the EU-SILC survey question known as **GALI** (Global Activity Limitation Instrument: "For at least the past six months, have you been limited because of a health problem in activities people usually do?").

Key properties:

- HLY is a *survey-based* measure: it inherits sampling noise, cultural differences in how people report limitation, and methodology changes. There is a known break around 2008 when GALI wording was harmonised.
- The series are **short** (roughly from the mid-2000s). This is a real modeling constraint: a 20-observation annual series will not support a heavily parameterised model. Choosing methods appropriate to short series — and being honest about uncertainty — is part of the case.
- Counterintuitive patterns are normal: women live longer but often report *more* years in poor health; some countries with modest LE have high HLY shares. Do not "correct" such patterns away — investigate them.

## 3. From average to total: person-years

The case's central move is to convert *per-person averages* into *population totals*:

> **Total life expectancy** (of a population, in a given year) ≈ life expectancy × population size — a stock of expected **person-years** of life embodied in the current population.
>
> **Total healthy life expectancy** ≈ healthy life expectancy × population size — a stock of expected **healthy person-years**.

Two remarks, both intentional openings for interpretation:

- The naive product (LE at birth × total population) is the simplest defensible estimate, and it is acceptable. But you may refine it: use age-specific remaining life expectancy weighted by the population age structure, restrict to working ages, use LE at 65 for retirement-related totals, etc. **State what you compute and why.** Different refinements answer slightly different questions.
- Person-years are the common currency that makes supply, demand, costs and consumption comparable. A job held for a year absorbs one person-year; a year lived in poor health demands care for one person-year.

Derived quantities used in the case:

| Quantity | Definition |
|---|---|
| LE in poor health | LE − HLY |
| Total LE in poor health | total LE − total HLY |
| Healthy LE after retirement | HLY − retirement age (when positive; think about what a negative value means) |
| Total healthy LE after retirement | total HLY − total person-years up to retirement |

## 4. Potential labour supply

The **total healthy life expectancy** is used here as an upper-bound estimate of the **potential labour supply**: the stock of healthy person-years the population could, in principle, devote to work. It is *potential*, not actual — it abstracts from participation rates, education years, childcare, preferences. Part of the analytical fun in Levels 3 and 6 is asking how much of the potential is, or could be, realised — and whether the gap differs by sex and country.

## 5. Number of jobs and potential labour demand

**Number of jobs = number of employed + number of job vacancies.** Employment counts filled positions; vacancies count unfilled but actively offered ones. Together they approximate how much labour the economy *wants*.

Caveats you must handle:

- Employment is published by sex; **vacancies are not** (a vacancy has no sex). Decide and document how to produce sex-specific labour demand — e.g. apportion vacancies by the sex structure of employment in the corresponding sector, or analyse demand totals against sex-specific supply.
- Vacancy statistics have country-specific coverage quirks (see the data guide). Comparability across countries is imperfect; say so.

**Potential labour demand** in person-years combines the number of jobs with the **required length of service for retirement**: roughly, how many person-years of work the economy demands of a person before releasing them into retirement, multiplied across the jobs the economy offers. The exact operationalisation is left open deliberately — defend yours.

## 6. Retirement age and required length of service

Two distinct legal parameters:

- **Statutory retirement age** — the age at which a standard old-age pension can be claimed.
- **Required length of service (contributory period)** — the number of years of insured work required for a full pension.

Both differ **by country, by sex, and by birth cohort**, and both are moving targets: many countries legislate gradual increases years in advance, sometimes linked to life expectancy itself (a delicious feedback loop worth noticing). These are *institutional* data: they come from legislation, not from statistical surveys, so building a clean time series of them is a small research project in itself — and a legitimate part of the case.

## 7. Sector costs and consumption

Two complementary lenses from official statistics:

- **Production side (NACE Rev. 2)** — economic activity classified by sector. Relevant sections: **Q** Human health and social work activities; **I** Accommodation and food service activities; **P** Education; **R** Arts, entertainment and recreation. National accounts give output, value added, and compensation of employees by NACE section — usable proxies for the "costs" of a sector. Government expenditure statistics (COFOG: health, social protection) and health accounts (SHA) offer alternative cost measures. Choosing the cost concept is part of the modeling decision.
- **Consumption side (COICOP)** — household final consumption classified by purpose. Relevant divisions: **CP09** Recreation and culture, **CP10** Education, **CP11** Restaurants and hotels.

Neither sector costs nor consumption are published by sex. As with vacancies: decide, justify, document.

## 8. Modeling and forecasting — what is expected conceptually

"Model and forecast" means: choose a model class consciously, fit it, check it (residuals, out-of-sample performance where the series length allows), produce forecasts **with uncertainty** (intervals or scenarios), and interpret. The case does not prescribe horizons or methods; it does expect that:

- forecasts of *derived* quantities (totals, differences, ratios) are consistent with the forecasts of their ingredients;
- relationships between variables (Levels 3–5) are modeled, not just plotted — though plotting first is always wise;
- correlation is not waved through as causation, especially in Level 6.

## 9. Models as simulation engines: scenarios
A fitted model has two uses. The first is the obvious one — produce a forecast. The second, and the one this case leans on, is to serve as a simulation engine: a machine you can feed alternative assumptions and watch the consequences unfold.
Concretely, once you can forecast life expectancy, healthy life expectancy, population, jobs and retirement rules, you can ask questions the raw data cannot answer:

What if healthy life expectancy improves twice as fast as the trend? Re-run the supply side and see how potential labour supply shifts.
What if the retirement age rises by two years over a decade? Re-run the demand side and the balance.
What if population decline accelerates through migration? Watch it propagate into totals, costs and consumption.

A scenario is just a coherent set of such assumptions. A baseline scenario (trends continue) gives the reference; alternative scenarios (optimistic, pessimistic, policy-driven) bracket the uncertainty and expose which conclusions are robust and which hinge on a single fragile assumption. This is why it pays to build models that are easy to re-run with changed inputs, rather than one-off calculations: the higher levels of the case are, in large part, the lower-level models interrogated under new scenarios.

## 10. One warning about identities

Several quantities in this case are connected by accounting identities (totals = averages × population; poor health = LE − HLY). When you later "model the relationship" between two quantities, ask yourself which part of the relationship is *mechanical* (built in by construction) and which part is *behavioural/economic* (genuinely informative). Confusing the two is the single easiest way to produce an impressive-looking but empty result.
