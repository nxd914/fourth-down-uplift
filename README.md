# Fourth-Down Decision-Making via Causal Uplift Modeling

## Overview

Coaches facing fourth-down decisions must choose between **going for it**, **kicking a field goal**, or **punting**, often under extreme uncertainty. While traditional analytics estimate expected points or conversion probabilities, they do not directly answer the causal question:

> *How would win probability change if a different decision were taken in the same game state?*

This project applies **causal uplift modeling** to NFL fourth-down decisions to estimate the **counterfactual win probability impact** of going for it versus conservative alternatives. Using historical play-by-play data, we identify situations where conservative coaching decisions systematically reduce expected win probability and quantify how much is left on the table.

---

## Data

The analysis uses NFL play-by-play data filtered to fourth-down situations. Each observation represents a unique fourth-down decision and includes:

* `yardline_100`: yards from the opponent’s end zone
* `ydstogo`: yards required for a first down
* `score_differential`: offensive team score minus defensive team score
* `game_seconds_remaining`: seconds remaining in the game
* `qtr`: quarter
* `posteam_timeouts_remaining`, `defteam_timeouts_remaining`
* `action`: observed decision (`GO`, `PUNT`, `FG`)
* `wp`: observed win probability at the time of the decision

The outcome variable is **win probability**, rather than yards or points, ensuring decisions are evaluated in terms of game-level success.

---

## Methodology

### 1. Counterfactual Outcome Modeling

We model expected win probability under two counterfactual actions:

* **GO**: the team attempts to convert the fourth down
* **KICK**: the team chooses a conservative option (punt or field goal)

Separate supervised learning models are trained to estimate:

[
\mathbb{E}[WP \mid X, \text{GO}] \quad \text{and} \quad \mathbb{E}[WP \mid X, \text{KICK}]
]

where ( X ) represents the observed game state.

Gradient-boosted regression models are used due to their ability to capture nonlinear interactions between field position, time, score, and distance.

---

### 2. Uplift Definition

The **uplift** from going for it is defined as:

[
\text{Uplift}(X) = \mathbb{E}[WP \mid X, \text{GO}] - \mathbb{E}[WP \mid X, \text{KICK}]
]

* **Positive uplift** indicates that going for it improves expected win probability.
* **Negative uplift** indicates that a conservative decision is preferable.

This framing directly answers the causal question: *What is the expected change in win probability if the decision were different, holding the game state fixed?*

---

### 3. Uncertainty Estimation

Because uplift is a difference of two estimated quantities, uncertainty is explicitly modeled using **game-level bootstrap resampling**.

For each fourth-down situation:

* Games are resampled with replacement
* Uplift is recomputed across bootstrap samples
* A confidence interval ([ \text{uplift}*{lo}, \text{uplift}*{hi} ]) is constructed

This avoids overconfidence and preserves dependence structure within games.

---

## Policy Evaluation

### Policy Rule

We evaluate a simple, conservative decision rule:

[
\textbf{GO if } \text{uplift}_{lo}(X) > 0; \quad \textbf{otherwise retain the original decision}
]

* The **lower bound** of the uplift interval is used as a safety filter
* This ensures recommendations are robust to estimation noise

---

### Counterfactual Policy Simulation

For each decision:

* If the policy recommends GO, the expected uplift is added to observed win probability
* Otherwise, win probability is unchanged

[
WP_{\text{policy}} =
\begin{cases}
WP + \text{uplift}_{mean}, & \text{if policy recommends GO} \
WP, & \text{otherwise}
\end{cases}
]

The difference ( WP_{\text{policy}} - WP ) represents the expected win probability gained by following the policy.

---

## Results

* The policy overturns a **substantial fraction** of conservative coaching decisions.
* Aggregate uplift corresponds to **~26.5 expected wins** across the evaluated dataset.
* Gains are largest when overturning **punts**, with fewer but meaningful field-goal reversals.
* Teams differ significantly in how much win probability they leave on the table, even after accounting for opportunity volume.

Team-level summaries report:

* Number of corrected decisions
* Total wins added
* Average win probability gained per decision
* Wins added per 10 corrected decisions

---

## Interpretation

This analysis does **not** claim:

* That every fourth-down should be aggressive
* That win probability can be perfectly predicted
* That a single model replaces coaching judgment

It **does** show:

* Certain conservative decisions are consistently negative in expectation
* These mistakes persist even when accounting for uncertainty
* A simple, cautious policy can recover meaningful win probability without increasing downside risk

---

## Limitations

* Observational data cannot eliminate all confounding
* Models estimate expectation, not guaranteed outcomes
* Results depend on historical coaching behavior and league context

These limitations are mitigated—but not eliminated—by uncertainty-aware policy design.

---

## Conclusion

By reframing fourth-down decisions as a **causal uplift problem**, this project moves beyond descriptive analytics and into counterfactual evaluation. The results suggest that a small number of conservative decisions have outsized effects on win probability, and that disciplined policy corrections can yield tangible competitive advantages.

---

## Reproducibility

All analysis is contained in a single Jupyter notebook and can be reproduced end-to-end using the provided code and data pipeline.

---

## Keywords

Causal inference · Uplift modeling · Sports analytics · Counterfactuals · Win probability · Decision science
