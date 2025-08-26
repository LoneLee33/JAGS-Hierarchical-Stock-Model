# STAT\_3303‑Bayesian\_Inference

Course homework and final project materials for **STAT 3303 – Bayesian Analysis and Statistical Decision Making** (Ohio State University). This repo contains runnable **R Markdown** files for homeworks **HW9**, **HW10**, an **MCMC practice** sheet, and a **final project** that fits a hierarchical logistic model in **JAGS**. Each folder is self‑contained with code that re‑creates figures and tables.

---

## Repository structure

```
STAT_3303-Bayesian_Inference/
├─ HM8 MCMC/
│  └─ MCMC.Rmd                # Manual Metropolis, Monte Carlo integration examples
├─ HW9/
│  └─ HW9.Rmd                 # Metropolis sampler, diagnostics, posterior plots
├─ HW10/
│  └─ HW10.Rmd                # JAGS model (cats PCV example): priors, MCMC, summaries
├─ Porject/
│  └─ 3303 Project.Rmd        # Final project: hierarchical logistic model in rjags
└─ README.md
```

---

## Requirements

* **R** (≥ 4.2 recommended)
* **RStudio**
* R packages used across the repo (install per file as needed):

  * Core: `tidyverse`, `dplyr`, `ggplot2`, `knitr`, `kableExtra`
  * MCMC & diagnostics: `coda`
  * JAGS interface: `rjags` (requires **JAGS** system library)
  * Others used in exercises: `ggpcp`, `invgamma`, `matrixcalc`, `expm`, `tidyr`

Install in R:

```
install.packages(c("tidyverse","dplyr","ggplot2","knitr","kableExtra","coda","rjags","ggpcp","invgamma","matrixcalc","expm","tidyr"))
```

### Install JAGS (for `rjags`)

* **macOS**: `brew install jags`
* **Windows**: download the installer from the JAGS website, then install `rjags` from R.
* **Linux**: use your package manager, e.g., `sudo apt-get install jags`.

---

## How to run

Open any `.Rmd` in RStudio and click **Knit** (PDF/HTML). Or run from R:

```
rmarkdown::render("HW9/HW9.Rmd")
rmarkdown::render("HW10/HW10.Rmd")
rmarkdown::render("HM8 MCMC/MCMC.Rmd")
rmarkdown::render("Porject/3303 Project.Rmd")
```

---

## File‑by‑file overview

### HM8 MCMC — Manual Metropolis & MC integration (`HM8 MCMC/MCMC.Rmd`)

Packages: `tidyverse`, `ggpcp`, `invgamma` (plus math helpers: `matrixcalc`, `expm` if used).

What it does:

* Demonstrates **Monte Carlo** and **Riemann** methods to approximate quantiles (e.g., find `c` where a CDF ≈ 0.99).
* Implements a **Metropolis** kernel step‑by‑step (proposal, acceptance ratio, accept/reject loop) and plots the simulated chain.

Outputs you should see:

* Numeric approximations of quantiles and a basic chain plot.

### HW9 — Random‑walk Metropolis & diagnostics (`HW9/HW9.Rmd`)

Packages: `ggplot2`, `coda`.

What it does:

* Implements a **Random‑Walk Metropolis** sampler for a 2‑parameter target.
* Plots **trace** and **density** for each parameter; overlays sample means.
* Visual diagnostics: **bivariate trace**, **ACF** plots; burn‑in handling.

Outputs you should see:

* Trace/density plots for `theta1`, `theta2`.
* Acceptance/burn‑in notes and autocorrelation checks.

### HW10 — JAGS modeling & posterior summaries (`HW10/HW10.Rmd`)

Packages: `rjags`, `coda`, `dplyr`, `ggplot2`, `tidyr`.

What it does:

* Specifies a **JAGS** model (animal PCV example) with parameters such as breed effects `alpha_i`, and coefficients for **dose** and **disease type**.
* Runs MCMC via `rjags`, then extracts draws to **trace** and **density** plots for key parameters (e.g., `mu_alpha`, `mu_beta`, `sigma_alpha`, `sigma_beta`).
* Provides written answers for sub‑parts (e.g., interpretation of parameters).

Outputs you should see:

* `summary(fit.samples)` in the console and diagnostic plots per parameter.

### Final project — Hierarchical logistic in JAGS (`Porject/3303 Project.Rmd`)

**Packages:** `tidyverse`, `dplyr`, `ggplot2`, `kableExtra`, `knitr`, `rjags`, `coda`.

**Data:** expects a CSV named `dataset20.csv` with columns

* `sector` ∈ {1,…,5}
* `stock` ∈ {1,…,10} (index within sector)
* `flip` ∈ {0,1} where 1 = positive return, 0 = negative return
* 30 periods × 5 sectors × 10 stocks ≈ **1,500 rows**

**What the project implements (per the final brief):**

* **EDA** of the binary outcomes by sector and stock (tables and labeled bar charts), including **limitations/bias** discussion.
* A **hierarchical Bayesian logistic model** for the probability a stock has a positive return in a period:

  * Observation level: `y_{s,j,t} ~ Bernoulli(θ_{s,j})` with `logit(θ_{s,j}) = μ + α_s + β_{s,j}`
  * **Market-wide mean** `μ`, **sector effects** `α_s`, and **stock effects** `β_{s,j}` (stock nested within sector)
  * **Weakly-informative priors** (e.g., Normal(0, 5) on `μ`, Normal(0, σ\_α) on `α_s`, Normal(0, σ\_β) on `β_{s,j}`; Half‑Normal/Half‑Cauchy on scale parameters) and **conditional independence** as appropriate
* **Model fitting with JAGS** via `rjags`:

  * Specify initial values; run multiple chains; record iterations, burn‑in, and thinning
  * **Convergence diagnostics**: trace plots, density overlays, **R‑hat** / Gelman–Rubin, effective sample size, and ACF
* **Posterior reporting and decisions** in the problem context:

  * Parameter interpretation for an analyst audience (define symbols and scales)
  * **Posterior probabilities that each sector is “best”** (highest `θ` among sectors)
  * **Posterior probabilities that each stock is “best” within its sector** (highest `θ` within sector)
  * Implementation tip: compute these by counting the argmax across MCMC draws

To reproduce the project locally:

```r
rmarkdown::render("Porject/3303 Project.Rmd")
```

Make sure **JAGS** is installed and accessible to R (`rjags`).

---

## Reproducibility tips

* Set seeds for simulation/MCMC (`set.seed()` in R; `rng.seed` in JAGS if needed).
* Record package versions at the end of each assignment (`sessionInfo()`).
* Cache expensive chunks with `knitr` caching when knitting PDFs.

---

## Reference

* OSU STAT 3303 instructions and materials.
