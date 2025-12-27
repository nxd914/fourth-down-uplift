# Fourth-Down Decision Uplift in the NFL

## Overview
This project estimates the win-probability impact of fourth-down decisions using causal uplift modeling. Rather than predicting play outcomes, we evaluate counterfactual decisions — asking when teams should go for it instead of kicking, and how much win probability is lost when they do not.

## Methodology
- Play-by-play data from recent NFL seasons
- Separate outcome models for GO vs KICK decisions
- Propensity modeling to adjust for selection bias
- Bootstrap confidence intervals for uplift estimates
- Policy rule: GO when lower confidence bound of uplift is positive

## Key Findings
- Conservative fourth-down decisions leave measurable win probability on the table
- A small number of corrected decisions yield outsized gains
- Effects vary significantly by field position and distance

## Repository Structure
- `notebooks/`: analysis and visualization
- `src/`: reusable modeling and policy code
- `figures/`: generated plots

## Disclaimer
This analysis is observational and does not claim causal certainty beyond modeled assumptions.
