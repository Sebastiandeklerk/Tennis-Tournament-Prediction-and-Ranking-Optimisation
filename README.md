# Applications of Machine Learning and Operations Research in Grand Slam, WTA and ATP Tennis Tournament Prediction and Ranking Optimisation

> MSc research project, University of Cape Town. **Work in progress**: this repository is updated as the project develops.

![Status](https://img.shields.io/badge/status-in%20progress-orange)
![Languages](https://img.shields.io/badge/R%20%7C%20Python-used-blue)
![Data](https://img.shields.io/badge/data-not%20yet%20shared-lightgrey)

## Overview

Professional tennis players must choose which tournaments to enter across a season. Each choice affects ranking points, fatigue, surface exposure and, ultimately, qualification for the **ATP Finals**. This project bridges predictive and prescriptive analytics:

1. **Predict** match and tournament outcomes with machine learning (ML).
2. **Optimise** the tournament schedule using operations research (OR).

### Research objective

To formulate, implement and validate an integrated ML and OR optimisation-based decision-support model for tournament selection in professional tennis. The model is intended to assist coaches, players and tennis professionals by acting as a tournament selection strategy, where the selected sequence of tournaments maximises a player's chances of qualifying for the ATP Finals, subject to predicted tournament outcomes.

The optimisation is formulated as a **Mixed-Integer Linear Program (MILP)** with **binary decision variables** representing the sequence of tournaments selected.


## Data

- **Source:** publicly available ATP match-level data from J. Sackmann's tennis data repository (2025).
- **Scope:** matches from 2000 to 2024, filtered to Grand Slam, Masters 1000, ATP 500 and ATP 250 events (the tournaments that award ATP ranking points).
- **Elo ratings:** computed using code adapted from the publicly available repository by Skoval (2025).
- **Availability:** the data is **not included** in this repository yet. See [`data/README.md`](data/README.md). It will be shared in due course.

A full variable list is in [`docs/data_dictionary.md`](docs/data_dictionary.md).

## Work completed so far

### 1. Exploratory data analysis
- **Missingness** was examined overall, by year and by player rank. Variables with more than 50% missingness (seed, entry, per-set game scores W3 to W5 and L3 to L5, tiebreak points) were dropped, as the information is recoverable from rankings, Elo and the final score.
- Missingness is driven mainly by **player rank rather than year**: it is below 1% for players ranked in the top 200 and rises sharply beyond rank 250. This motivates a **rank cutoff of 250** for model fitting.
- Serving statistics are more complete from 2016 onward, suggesting improved data collection.
- Descriptive statistics showed no obvious outliers. Aces, double faults, rankings and career matches are positively skewed.
- Categorical counts: hard courts make up about 54% of matches, clay 32%, grass 12% and carpet under 2%. About 86% of players are right-handed.
- A correlation analysis identified strongly correlated serving variables and a perfect correlation between serve % difference and return % difference (they are constructed from the same quantities, so only one is retained).

### 2. Feature engineering
- Reshaped winner/loser columns into **player/opponent** to avoid encoding the outcome in the column structure. Correlations between, for example, break points saved and faced drop markedly after this conversion.
- **Elo ratings** before each match (post-match Elo removed to prevent leakage).
- **Relative-difference features:** Elo, ATP rank, ranking points, serve %, ace rate, double-fault rate, 1st-serve-in %, break-points-saved %, and return %.
- Categorical encoding of surface, tournament level, round, and handedness.

### 3. Feature selection and data leakage
- **Lasso (L1) regularisation**, with the penalty chosen by cross-validation (minimising MSE), fitted on a complete-case, scaled dataset.
- **Random Forest variable importance**, fitted on both the full and complete-case training data. Elo, player rank and career matches played rank as the most important variables.
- **Leakage control:** same-match statistics (aces, serve metrics and so on) are not available before a match. They are replaced with per-player **rolling averages over the previous 5 (r5) and 20 (r20) matches**, ordered by tournament start date, with new relative-difference features derived from them.
- The rolling-average feature sets are being compared (rates vs sums vs means).

### 4. Train/test design
- **Temporal split** to respect the time ordering of the tour: training on 2000 to 2019, testing on 2020 to 2024.

## Getting started

> Instructions will be finalised once the code and data are released.

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

# Python
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r environment/requirements.txt

# R (from an R session)
# install.packages("renv"); renv::restore()
```

Place the source data in `data/raw/` (see [`data/README.md`](data/README.md)), then run the scripts in `R/` and `python/` in the numbered order.

## Tools

- **R / RStudio:** data cleaning, EDA, correlation analysis, Lasso, Random Forest variable importance.
- **Python:** machine learning models and MILP optimisation.


## Ethics

Ethical clearance was granted by the University of Cape Town Faculty Research Ethics Committee (Pre-screening Questionnaire outcome letter, 18 March 2026), before any work on this study began. The study uses publicly available, match-level data.

## Data and code attribution

- Match data: J. Sackmann, *tennis_atp* (2025). Please check and comply with the licence terms of the upstream repository.
- Elo rating code: adapted from Skoval (2025).

## Citation

If you use or build on this work, please cite:

```
<S. De Klerk> (2026). Applications of Machine Learning and Operations Research in
Tennis Tournament Prediction and Ranking Optimisation.
MSc thesis (in progress), University of Cape Town.
```

## Licence

Code: choose a licence (for example MIT). Data remains subject to its original source licence.

## Contact

<Sebastian De Klerk> · <sebadk1@gmail.com dklseb001@myuct.ac.za> · Supervisor: <Neil Watson>
