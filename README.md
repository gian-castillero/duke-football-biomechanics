# Duke Football Biomechanics: Cross-Instrument Performance & Asymmetry Analysis

A statistical consulting project conducted for Duke Athletics, investigating whether biomechanical performance measurements across four sports science instruments are associated with one another — and whether left/right muscular asymmetries in one part of the body predict reduced performance elsewhere.

## Overview

Duke Football's sports science staff collects physiometric data from athletes using four biomechanical instruments over the course of a competitive season. Each instrument measures a different aspect of physical performance in isolation. This project asks a more integrated question: **do these measurements talk to each other?**

Two research questions are addressed:

1. **Cross-instrument performance:** Can performance metrics from three instruments predict performance on a fourth?
2. **Asymmetry effects:** Do left/right muscular imbalances measured on one instrument predict reduced performance on another?

Answering these questions helps coaches and athletic trainers identify which exercises and body systems serve as indicators of overall athletic readiness — and which imbalances may signal broader performance or injury risk beyond a single test.

## The Four Instruments

| Instrument | What It Measures | Key Variable Selected |
|------------|-----------------|----------------------|
| **Forcedeck** | Lower body explosiveness and neuromuscular readiness via vertical jump | RSI-modified (Reactive Strength Index) |
| **Forceframe** | Isometric joint strength — hips (adduction/abduction) and shoulders (internal/external rotation) | Inner Right Max Force (hips), Outer Right Max Force (shoulders) |
| **Nordbord** | Hamstring strength via Nordic curl | Right Max Force |
| **Perch** | Lower body power and velocity during weighted squat | Max Peak Power |

Each athlete was measured on each instrument multiple times over the course of the data collection period. Measurements were averaged per athlete to produce one observation per person per variable.

## Dataset

Four de-identified datasets provided by Duke Athletics, one per instrument:

```
data/
├── forcedeck.xlsx
├── forceframe.xlsx
├── nordbord.xlsx
└── perch.xlsx
```

Athletes are identified by anonymized ID only. Body weight was available in both datasets (kg in forcedeck, lbs in perch) and harmonized into a single variable. All variables were standardized to mean 0 and variance 1 prior to modeling to prevent disproportionate influence from scale differences.

## Methods

### Data Cleaning & Variable Selection

For each instrument, a single representative performance variable was selected based on domain relevance. Forceframe presented a complication: athletes performed multiple test types, most completed by fewer than 30 players. Analysis was restricted to the two most-completed tests — Hip AD/AB at 60° (70 athletes) and Shoulder IR/ER custom setup (83 athletes). The right-side metric was used consistently across all instruments, under the assumption that it is the dominant side for the majority of athletes.

Asymmetry percentages were computed using the formula:

$$\text{Asymmetry \%} = \left|\frac{\text{Left} - \text{Right}}{\max(\text{Left}, \text{Right})}\right| \times 100$$

This formula was already applied by the forcedeck software for its own metrics and was applied consistently to forceframe and nordbord.

Variance distributions across repeated athlete measurements were inspected to confirm that averaging was appropriate — all variables showed low within-athlete variance except Outer Right Max Force, which was noted.

### Performance Models

Ten linear regression models were fit — one with each of the five performance variables as the response and the remaining four as predictors, plus body weight as a covariate in every model.

### Asymmetry Models

A second set of five linear regression models used all available asymmetry percentages (from instruments other than the one being predicted) as predictors of each performance outcome, again controlling for body weight.

All models were evaluated at α = 0.05. Full diagnostic plots (residuals vs. fitted, Q-Q, scale-location, Cook's distance) are included in the appendix of the written report.

## Results

### Performance Models

**Max Peak Power from perch emerged as a consistent cross-instrument predictor**, showing statistically significant positive associations with three of the four other instruments:

| Response Variable | Predictor | Estimate | p-value |
|------------------|-----------|----------|---------|
| RSI-modified (forcedeck) | Max Peak Power | +0.338 | 0.009 |
| Outer Right Max Force — Shoulder (forceframe) | Max Peak Power | +0.319 | 0.018 |
| Right Max Force (nordbord) | Max Peak Power | +0.301 | 0.045 |

No other cross-instrument predictor reached significance. This suggests that the weighted squat (perch) may serve as a particularly informative indicator of overall athletic readiness — athletes who generate higher peak power in a squat also show greater explosiveness in vertical jumps, more shoulder extension force, and stronger hamstrings.

### Asymmetry Models

Only one significant association was found across all asymmetry models:

| Response Variable | Asymmetry Predictor | Estimate | p-value |
|------------------|---------------------|----------|---------|
| Inner Right Max Force — Hip (forceframe) | Max Force Asymmetry % (nordbord) | −0.040 | 0.045 |

A one percentage point increase in hamstring asymmetry (nordbord) is associated with a decrease in hip adduction force (forceframe), holding all else constant. This negative relationship between hamstring imbalance and hip force capacity may indicate a biomechanical trade-off between these two systems — a finding relevant to both injury prevention and targeted strength training decisions.

## Key Findings

- **The perch squat is the most connected instrument:** Max Peak Power was the only metric significantly associated with performance on multiple other instruments, making it a candidate indicator for overall lower-body athletic readiness.
- **Asymmetric hamstrings predict reduced hip force:** The sole significant asymmetry finding connects nordbord (hamstrings) to forceframe (hips), suggesting that imbalances in the posterior chain may extend their effect beyond the hamstrings themselves.
- **Most instruments measure relatively independently:** The majority of cross-instrument and asymmetry associations were not statistically significant, indicating that each instrument captures aspects of performance that are largely distinct from one another.

## Limitations

- Player position, injury history, and training load were not available, all of which could confound the observed relationships.
- Results reflect associations between instrument measurements, not on-field performance outcomes.
- Playing time differences (starters vs. reserves) affect fatigue levels and were not controlled for.
- A hierarchical modeling approach (athletes nested within positions) could provide more generalizable estimates and is a natural next step for this analysis.

## Tech Stack

- R
- `tidyverse` (data wrangling and visualization)
- `tidymodels` (linear regression)
- `patchwork` (multi-panel plots)
- `knitr` / `rmarkdown` / `quarto` (report generation)

## Repository Structure

```
duke-football-biomechanics/
├── data/
│   ├── forcedeck.xlsx
│   ├── forceframe.xlsx
│   ├── nordbord.xlsx
│   └── perch.xlsx
├── written-report.Rmd        # Full analysis and results
├── written-report.pdf        # Rendered report
├── Case_Study_Report.qmd     # Quarto version of the report
├── report.Rmd                # Working analysis file
├── report.pdf                # Rendered working report
└── project.Rproj             # RStudio project file
```

## How to Run

```r
install.packages(c("tidyverse", "tidymodels", "patchwork", "knitr", "readxl"))
```

Open `project.Rproj` in RStudio and knit `written-report.Rmd` to reproduce the full analysis. All four `.xlsx` data files must be present in the `data/` directory.

## Authors

Lisa Zuo, Gian Castillero, Jack Nowacek
