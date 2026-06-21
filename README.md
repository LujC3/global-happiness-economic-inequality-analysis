# Does Economic Condition Predict Happiness? A Cross-Country Analysis (2015–2023)

A cross-country analysis testing whether a nation's economic condition — GDP per capita, poverty rate, and income inequality — meaningfully predicts how happy its people report being, using the World Happiness Report panel and a global poverty/inequality dataset.

## Why this question matters

GDP growth is the headline measure most governments, including the Philippines, use to track national progress. If GDP growth doesn't reliably translate into measurable wellbeing gains once a country passes a basic development threshold, that has direct implications for how budgets, social protection programs, and development plans get prioritised. This project tests that relationship directly rather than assuming it.

## Data sources

| Dataset | Source | Coverage |
|---|---|---|
| World Happiness Report (panel) | [jainaru/world-happiness-report-2024-yearly-updated](https://www.kaggle.com/datasets/jainaru/world-happiness-report-2024-yearly-updated) (Kaggle, originally Sustainable Development Solutions Network / Gallup World Poll) | 2005–2023, ~165 countries |
| Global Poverty & Economic Inequality | [hamnamunir/global-poverty-and-economic-inequality-2015-2024](https://www.kaggle.com/datasets/hamnamunir/global-poverty-and-economic-inequality-2015-2024) (Kaggle) | 2015–2024, 40 countries |

Merged on country name + year, giving **39 countries across 2015–2023** (338 country-year observations) after fixing three country-name mismatches (Turkey→Türkiye, UK→United Kingdom, USA→United States).

## ⚠️ Data quality finding (important — read before trusting any conclusion here)

While auditing the poverty/inequality dataset before merging, I found that it contains **multiple, randomly-varying rows for the same country and year** — between 5 and 79 separate rows per country-year, with no second key (province, survey wave, source) to explain the spread. For example, Bangladesh in 2017 has 40 rows where GDP per capita alone ranges from $2,555 to $3,134 and the poverty rate ranges from 9.6% to 37.4%. Real official statistics (World Bank, national statistical agencies) don't take this shape.

This is consistent with the dataset being **synthetic/simulated rather than sourced from real official statistics**. The notebook documents this finding with visuals (Section 2) and aggregates the duplicate rows by taking the mean per country-year so the merge is technically possible — but that fix doesn't repair the underlying data quality issue.

**Conclusion:** this notebook is a methodology demonstration of cross-country merge, correlation, and regression analysis with proper diagnostics — not policy-grade evidence. A real policy brief on this question would need to substitute official World Bank PovcalNet / World Development Indicators data (or PSA/PIDS data for a Philippines-specific version) for the poverty/inequality side of the merge.

## Method

1. Audited the poverty dataset for structural issues before using it (see above)
2. Aggregated to one row per country-year, fixed country-name mismatches, merged with the happiness panel
3. Computed Pearson correlations between happiness (Life Ladder) and 11 economic/social indicators
4. Built a multiple regression (Life Ladder ~ log GDP per capita + Gini coefficient + poverty rate), with standard errors clustered by country to account for the repeated-country panel structure
5. Checked Variance Inflation Factors to confirm the regression wasn't distorted by multicollinearity (a fuller model including HDI and life expectancy was tested and discarded — VIFs exceeded 80, since those indices are partly built from GDP and life expectancy)

## Key findings

- **GDP per capita (logged)** is the strongest economic correlate of happiness (r = 0.84) and remains statistically significant (p < 0.001) in the clustered regression, even after controlling for inequality and poverty
- **Gini coefficient** and **poverty rate** are not statistically significant predictors once GDP is accounted for in this sample — this does not prove inequality and poverty don't matter, only that their effect wasn't separable from GDP's effect in this dataset
- The regression explains ~70% of the variation in happiness across the sample (R² = 0.70)
- This is observational, cross-country data: it shows association, not causation. Reverse causality (happier, more stable societies may grow faster) and omitted variables (governance quality, health systems, social trust) remain plausible alternative explanations

## Limitations

1. Poverty/inequality data appears synthetic (see above) — treat all poverty/inequality-side conclusions as illustrative
2. Cross-country observational data cannot establish causation
3. The merged sample (39 countries) is constrained by which countries appear in both source datasets
4. Non-significance of Gini/poverty rate should be read as "not detected in this sample," not "proven to have no effect"

## Tech stack

Python · pandas · NumPy · statsmodels · scipy · matplotlib · seaborn

## Background note

My professional background is in law, nursing, and local government procurement, not statistics — but cross-country wellbeing analysis like this is exactly the kind of evidence base Philippine policymakers need when weighing growth-focused programs against welfare-focused ones. Flagging a synthetic dataset before drawing conclusions from it is the same instinct that makes a good case auditor: the source has to hold up before the argument built on it does.

---
*Part of a [data analytics portfolio](https://lujc3.github.io) combining a legal, nursing, and public-sector background with applied data analysis.*
