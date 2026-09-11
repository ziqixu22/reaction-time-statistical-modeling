# Reaction Time Statistical Modeling

An R-based statistical modeling project analyzing how demographic, behavioral, device, and environmental factors are associated with human reaction time.

## Project Overview

This project investigates variation in reaction time using survey and experimental data. The analysis focuses on whether factors such as age, sleep, input device, fatigue, visual acuity, exercise habits, stress level, and environmental conditions are associated with reaction-time performance.

The workflow covers the full statistical modeling process:

- Data cleaning and feature engineering
- Exploratory data analysis
- Correlation analysis
- Nonparametric hypothesis testing
- Multiple linear regression
- Interaction modeling
- Best-subset variable selection
- Nested model comparison
- Multicollinearity diagnostics
- Residual diagnostics
- Box-Cox transformation
- Leverage, outlier, and influence analysis

## Research Questions

1. Is reaction time associated with age?
2. Do click-based and tap-based input devices show different reaction-time distributions?
3. Does the relationship between age and reaction time vary by input-device type?
4. Which behavioral and environmental variables provide the strongest explanatory value?
5. Can model assumptions be improved through response transformation?

## Methods

### Exploratory Data Analysis

The analysis examines reaction-time distributions, pairwise numeric relationships, categorical group differences, and correlation structure.

### Nonparametric Testing

A Wilcoxon rank-sum test is used to compare reaction-time distributions across input-device groups when normality assumptions are not satisfied.

### Regression Modeling

Several model structures are compared:

- Age-only regression
- Input-device-only model
- Additive age + device model
- Age × device interaction model

Nested F-tests are used to compare model specifications.

### Variable Selection

A broader multiple regression model is evaluated using best-subset regression and model-selection criteria including adjusted R², Mallows' Cp, AIC, and BIC.

### Model Diagnostics

Regression assumptions are evaluated using variance inflation factors, the Breusch-Pagan test, Shapiro-Wilk test, Durbin-Watson test, and residual plots. A Box-Cox transformation is applied to improve residual normality and variance stability. Influential observations are assessed using leverage, studentized residuals, and Cook's distance.

## Key Findings

The original analysis found evidence that reaction-time distributions differed between click-based and tap-based input-device groups. The age-by-device interaction was not statistically significant, suggesting that the age slope did not vary meaningfully across device groups. A reduced multiple regression model achieved similar explanatory performance to the full model while using fewer predictors, and the Box-Cox transformed model showed improved agreement with residual normality and constant-variance assumptions.

These results describe statistical associations and should not be interpreted as causal effects.

## Repository Structure

```text
.
├── README.md
├── reaction_time_analysis.Rmd
├── Survey.csv
└── Statistical Modelling_Final_Project1.pdf
```

## Tools

- R
- tidyverse
- ggplot2
- car
- lmtest
- MASS
- leaps
- corrplot

## How to Reproduce

Clone the repository:

```bash
git clone https://github.com/ziqixu22/Statistical-Modelling.git
cd Statistical-Modelling
```

Open `reaction_time_analysis.Rmd` in RStudio and knit the document. Make sure the required R packages are installed.

## Notes

This project was originally developed as part of a statistical modeling course project and later reorganized for reproducibility and portfolio presentation.
