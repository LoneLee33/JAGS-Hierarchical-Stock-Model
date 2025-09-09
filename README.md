# JAGS-Hierarchical-Stock-Model

## Overview

This repository packages my **Bayesian Inference** course project modeling the probability that a stock posts a positive return using a hierarchical Bayesian logistic model in JAGS. 

The workflow combines EDA with a nested sector→stock structure—market baseline μ, sector effects αₛ, and stock effects βₛⱼ—fit via **rjags** with multi-chain MCMC and standard diagnostics (trace/density, R-hat, ESS, ACF). The goal is decision-oriented: from posterior draws we identify the most likely best sector overall and, within each sector, the most likely best stock, while clearly documenting assumptions and convergence checks. 

Results are presented as posterior probability summaries and rankings—not single point estimates—to emphasize uncertainty-aware comparisons across sectors and stocks. 

The repo also includes supporting R Markdown notebooks on **MCMC basics**, **Metropolis sampling**, and **JAGS diagnostics** that build up to the main model.

> Originally developed from coursework; reorganized and documented here as a standalone reproducible repository.

---

## Repository structure

```
JAGS-Hierarchical-Stock-Model/
├─ HM8 MCMC/
│  └─ MCMC.Rmd                # Metropolis kernel & Monte Carlo integration (supporting demo)
├─ HW9/
│  └─ HW9.Rmd                 # Random-walk Metropolis sampler + diagnostics (supporting demo)
├─ HW10/
│  └─ HW10.Rmd                # Intro JAGS model & posterior summaries (supporting demo)
├─ Porject/
│  └─ 3303 Project.Rmd        # Main hierarchical stock logistic model in rjags
└─ README.md
```
 
---

## Requirements

- **R** (≥ 4.2 recommended)
- **RStudio** 
- R packages (install per file as needed):

  - Core: `tidyverse`, `dplyr`, `ggplot2`, `knitr`, `kableExtra`
  - MCMC & diagnostics: `coda`
  - JAGS interface: `rjags` (requires the **JAGS** system library)
  - Other helpers used in demos: `ggpcp`, `invgamma`, `matrixcalc`, `expm`, `tidyr`

Install in R:

```r
install.packages(c(
  "tidyverse","dplyr","ggplot2","knitr","kableExtra",
  "coda","rjags","ggpcp","invgamma","matrixcalc","expm","tidyr"
))
```

### Install JAGS (for `rjags`)
- **macOS**: `brew install jags`
- **Windows**: download the JAGS installer, then install `rjags` from R
- **Linux**: `sudo apt-get install jags` (or your distro’s package name)

---

## How to run

Open any `.Rmd` in RStudio and click **Knit** (PDF/HTML). Or render from R:

```r
rmarkdown::render("HW9/HW9.Rmd")             # Metropolis & diagnostics demo
rmarkdown::render("HW10/HW10.Rmd")           # Intro JAGS modeling demo
rmarkdown::render("HM8 MCMC/MCMC.Rmd")       # MC integration + Metropolis demo
rmarkdown::render("Porject/3303 Project.Rmd")# MAIN hierarchical stock model
```

---

## File-by-file overview

### HM8 MCMC — Metropolis & Monte Carlo integration (`HM8 MCMC/MCMC.Rmd`)
**Packages:** `tidyverse`, `ggpcp`, `invgamma` (plus `matrixcalc`, `expm` if used)

**Purpose:** Supporting demo to build intuition for **Monte Carlo** estimation and a manual **Metropolis** acceptance–rejection loop.

**Outputs:** Numeric approximations for target quantiles and a basic chain plot.

---

### HW9 — Random-walk Metropolis & diagnostics (`HW9/HW9.Rmd`)
**Packages:** `ggplot2`, `coda`

**Purpose:** Supporting demo of a **Random-Walk Metropolis** sampler on a 2-parameter target with **trace/density** plots, **ACF**, and burn-in handling.

**Outputs:** Trace/density plots for `theta1`, `theta2`, and diagnostic notes.

---

### HW10 — JAGS modeling & posterior summaries (`HW10/HW10.Rmd`)
**Packages:** `rjags`, `coda`, `dplyr`, `ggplot2`, `tidyr`

**Purpose:** Supporting demo showing a small **JAGS** model end-to-end (define model, run chains, summarize) with trace/density diagnostics for key parameters.

**Outputs:** `summary()` of posterior draws plus diagnostic plots.

---

### Main model — Hierarchical logistic in JAGS (`Porject/3303 Project.Rmd`)
**Packages:** `tidyverse`, `dplyr`, `ggplot2`, `kableExtra`, `knitr`, `rjags`, `coda`

**Data expected:** a CSV `dataset20.csv` with columns
- `sector` ∈ {1,…,5}
- `stock` ∈ {1,…,10} (index within sector)
- `flip` ∈ {0,1} (1 = positive return, 0 = negative)
- ~**1,500 rows** (≈ 30 periods × 5 sectors × 10 stocks)

**What it does:**
- **EDA** of positive/negative return rates by sector and stock (tables + labeled bar charts), with short comments on limitations/bias
- **Hierarchical Bayesian logistic model** for \(P(\text{positive return})\):
  - \( y_{sjt} \sim \text{Bernoulli}(\theta_{sj}) \), \( \text{logit}(\theta_{sj}) = \mu + \alpha_s + \beta_{sj} \)
  - Market-wide baseline \( \mu \), **sector effects** \( \alpha_s \), and **stock effects** \( \beta_{sj} \) (stock nested in sector)
  - **Weakly-informative priors** (e.g., Normal on location terms; half-Normal/Cauchy on scales)
- **Fitting with JAGS** (`rjags`):
  - Multiple chains with clear specs for iterations, burn-in, thinning
  - Diagnostics: trace/density overlays, **R-hat**, effective sample size, **ACF**
- **Decision-oriented summaries**:
  - Posterior probabilities that each **sector is “best”** (highest \( \theta \))
  - For each sector, posterior probability each **stock is “best”** within that sector
  - (Implementation: argmax tallies across MCMC draws)

**Reproduce:**
```r
rmarkdown::render("Porject/3303 Project.Rmd")
```
Make sure **JAGS** is installed and accessible to R (`rjags`).

---

## Reproducibility tips

- Set seeds for simulation/MCMC (`set.seed()` in R; `rng.seed` in JAGS if needed)
- Record package versions (`sessionInfo()`) in each run
- Cache expensive chunks with `knitr` caching when knitting PDFs

---

## Acknowledgments

- R & Bayesian ecosystems (R Markdown, coda, JAGS/rjags, ggplot2, tidyverse)
- Originally structured from Ohio State’s STAT 3303 materials
