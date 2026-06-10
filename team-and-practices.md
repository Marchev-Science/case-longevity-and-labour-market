# Team and working practices

Everything in this document is a **proposal, not a prescription**. The case is designed for a small team (around five people) with mixed backgrounds — economics, statistics, computer science, social sciences, law — and that mix is a strength precisely because the case has demographic, economic, computational and institutional layers. Organise yourselves however suits your people; just organise yourselves *deliberately*.

## A proposed division of roles

One workable configuration for a team of five:

| Role | What they own | Natural background |
|---|---|---|
| **Coordinator / integrator** | Keeps the levels moving in order, merges the parts into one coherent story, guards consistency of definitions across the team, owns the final narrative. | Anyone with an overview instinct; often the most senior, not necessarily. |
| **Data engineer** | The Level 0 pipeline in the **team's own repository**: extraction from Eurostat (by website and/or API), cleaning, the data folder structure, flags and vintages, reproducibility. | Programming-comfortable member (the website route in the data guide needs no programming at all). |
| **Demography modeler** | Levels 1 and 4–5 ingredients: LE, HLY, population models and forecasts; the average→total constructions. | Statistics / quantitative member. |
| **Labour & economy modeler** | Levels 2–3 and the economic side of 4–5: jobs, vacancies, demand construction, balance modeling, sector costs and consumption links. | Economics / quantitative member. |
| **Analyst / storyteller** | Cross-country comparison, the institutional research (retirement legislation, MISSOC/OECD digging), visualisation, limitations chapter, presentation. | Social sciences / law / communication strengths. |

Sensible deviations from this scheme:

- **Pair the modelers** rather than splitting supply/demand — two heads on every model, alternating driver and reviewer.
- **Rotate the data role** after Level 0 is stable, so the pipeline knowledge does not live in one head.
- **Split the institutional research** (retirement rules for 8 countries × 2 sexes × time) across everyone — it parallelises perfectly, one or two countries each.
- A team of strong generalists can ignore roles entirely and swarm level by level. That works too — if someone still owns integration.

The only configuration that reliably fails: five people working on five disconnected pieces that meet for the first time the night before they are due to fit together. Integrate early, integrate often.

## On working with AI assistants

Use them — for boilerplate code, for API syntax, for debugging, for first-draft text, for rubber-duck discussions of model choice. But:

- **Verify every dataset code, every number, every legal claim** an assistant produces against the primary source. Assistants confabulate plausible Eurostat codes and plausible pension rules with equal confidence.
- **Own the interpretation.** A model an assistant fitted but nobody on the team can explain is a liability in front of any audience.
- Keep prompts/sessions that materially shaped the solution noted somewhere — it is honest, and it is interesting methodology in its own right.

## Recommended practices

Recommendations, not rules. Each one has a reason; if you have a better reason to deviate, deviate and write the reason down.

**Reproducibility**

- Raw data is sacred: files in your team repo's `data/raw/` are never edited by hand. Every transformation lives in code (or, for website downloads, in a written log of filters and dates).
- One command (or one clearly documented sequence) should rebuild `data/processed/` from `data/raw/`, and outputs from processed data.
- Pin your environment (requirements.txt / renv / environment.yml) so the pipeline runs on more than one laptop.

**Version control**

- Commit early, commit small, write messages a teammate can understand a week later.
- Branch per workstream if it helps; never let two people edit the same notebook simultaneously without a plan.
- Notebooks are for exploration; once code stabilises, promote it into `src/` as functions.

**Modeling hygiene**

- Plot before you model. Always.
- Hold out the last observations for validation where series length allows; report out-of-sample, not just in-sample, fit.
- Prefer two simple, defensible models you can compare over one elaborate model you cannot explain. Model comparison *is* analysis.
- Forecasts without uncertainty are opinions with axes. Produce intervals, scenarios or sensitivity analyses.
- Keep a shared definitions file (what exactly is "total HLY" in *this* team's solution?) so Level 3 doesn't discover that supply and demand were computed on incompatible conventions.

**Teamwork**

- Short, regular sync-ups beat long, rare meetings.
- Define "done" for each level before starting it.
- Disagreements about method are valuable — resolve them with a quick empirical test where possible, and record the verdict.
- Leave time to assemble the story. Results that exist but cannot be told do not exist.

**Honesty**

- The limitations section is not an apology; it is the part that proves you understood what you did. Short HLY series, non-sex-disaggregated vacancies, mechanical components in identities, heroic apportionments — name them yourselves before anyone else has to.
