# Medicare Advantage Star Rating Analysis

An R-based analysis of Medicare Advantage (MA) plan star ratings, market share, and enrollment patterns from 2010–2015, using OLS regression and regression discontinuity (RD) designs.

---

## Project Structure

Path leading up to Homework 3

```
econ470/
       ├── a0/
          ├── work
              ├── hwk3/
              ├── ma-data/
              └── hcris-data/
```

Open Demand Directory Orientation within the Homework 3 folder

```
hwk3/
├── data/
│   ├── data-2010.csv
│   ├── data-2011.csv
│   ├── data-2012.csv
│   ├── data-2013.csv
│   ├── data-2014.csv
│   └── data-2015.csv
├── submission1/
│   ├── problems.ipynb ← Main analysis notebook (R kernel)
│   ├── ma-data-2015.ipynb
│   ├── ...
│   ├── ma-data-2010.ipynb
│   ├── sarkar-s-hwk3-1.pdf
│   └── problems.qmd
├── submission2/
│   ├── problems.ipynb ← Main analysis notebook (R kernel)
│   ├── sarkar-s-hwk3-2.pdf
│   └── problems.qmd
├── submission3/
│   ├── problems.ipynb ← Main analysis notebook (R kernel)
│   ├── sarkar-s-hwk3-3.pdf
│   └── problems.qmd
├── functions-1.R/
└── readme.md/
```

> All CSV files are loaded from `../data/` relative to the notebook. The notebook uses an R kernel.

---

## Analysis Overview

The notebook is structured around 10 problems that progressively build from descriptive statistics to causal inference.

```
Raw CSVs (2010–2015)
       │
       ▼
  Data Merging & Cleaning
  bind_rows() + fix_type()
  + market_share = avg_enrollment / avg_enrolled
       │
       ├──► Problem 1 — Summary stats by year (star rating, enrollment, market share)
       │
       ├──► Problem 2 — Plans without a star rating (NA analysis)
       │
       ├──► Problem 3 — Bar charts of star rating distribution (2010, 2012, 2015)
       │
       ├──► Problem 4 — OLS: star rating dummies → market share (per year, 2010–2015)
       │
       ├──► Problem 5 — Raw rating = mean of 33 quality variables (2010 only)
       │                 + count of plans rounded up to each star threshold
       │
       ├──► Problem 6 — RD at 2.75 cutoff (2.5 vs 3★) and 3.25 cutoff (3 vs 3.5★)
       │                 bandwidth = ±0.125
       │
       ├──► Problem 7 — Sensitivity analysis: RD across bandwidths 0.10–0.15
       │                 with confidence intervals plotted
       │
       ├──► Problem 8 — RD scatter plots with fitted lines at both thresholds
       │
       ├──► Problem 9 — Covariate balance: HMO share & Part D share near cutoffs
       │                 (Love plots at 2.75 and 3.25 margins)
       │
       └──► Problem 10 — Interpretation & discussion
```

---

## Key Code Patterns

**Packages** — managed via `pacman::p_load()`:
```r
pacman::p_load(tidyverse, ggplot2, dplyr, lubridate, stringr,
               readxl, data.table, gdata, scales, knitr, kableExtra)
```

**Market share** is computed as:
```r
market_share = avg_enrollment / avg_enrolled
```

**Raw rating** (Problem 5) is the row-wise mean across 33 quality variables:
```r
raw_rating = mean(c_across(all_of(quality_vars)), na.rm = TRUE)
```

**OLS by year** (Problem 4) uses star rating dummies with `≤ 2.5★` as the reference group:
```r
lm(market_share ~ star_3 + star_35 + star_4 + star_45, data = ...)
```

**Regression discontinuity** (Problems 6–8) uses a local linear model:
```r
lm(market_share ~ score + treat + score_treat, data = window_subset)
# score       = raw_rating - cutoff
# treat       = (score >= 0)
# score_treat = score * treat   (interaction)
```
Two thresholds are analyzed: **2.75** (2.5 vs 3★) and **3.25** (3 vs 3.5★).

**Bandwidth sensitivity** (Problem 7) loops over windows from ±0.10 to ±0.15 and plots the treatment effect with 95% CIs.

---

## Requirements

- **R** ≥ 4.0 with an IRkernel (for Jupyter), or run directly in RStudio
- Packages: `tidyverse`, `ggplot2`, `dplyr`, `lubridate`, `stringr`, `readxl`, `data.table`, `gdata`, `scales`, `knitr`, `kableExtra`
- CSV data files placed in `../data/` relative to the notebook

Install all packages in one line:
```r
install.packages("pacman")
pacman::p_load(tidyverse, ggplot2, dplyr, lubridate, stringr,
               readxl, data.table, gdata, scales, knitr, kableExtra)
```
