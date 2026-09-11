# Reaction Time Statistical Modeling

An R-based statistical modeling project analyzing how demographic, behavioral, device, and environmental factors are associated with human reaction time.

## Project Overview

This project investigates variation in reaction time using survey and experimental data. The analysis examines demographic, behavioral, device, and environmental predictors such as age, sleep, input device, fatigue, visual acuity, exercise habits, stress, temperature, and Wi-Fi stability.

The workflow covers the full statistical modeling pipeline:

- data cleaning and feature engineering
- exploratory data analysis and correlation analysis
- nonparametric hypothesis testing
- linear and multiple regression
- interaction modeling
- best-subset variable selection
- nested-model comparison
- model diagnostics
- Box-Cox response transformation
- leverage, outlier, and influence analysis

Because the data are observational and several predictors are self-reported, results should be interpreted as associations rather than causal effects.

## Research Questions

1. Is reaction time associated with age?
2. Do click-based and tap-based devices have different reaction-time distributions?
3. Does the relationship between age and reaction time differ by device group?
4. Which behavioral and environmental predictors provide useful explanatory value?
5. Can a lower-dimensional model retain the explanatory performance of a larger model?
6. Can a response transformation improve regression assumptions?

## Data Preparation

Several raw variables are regrouped into analytically useful categories. For example, input devices are reduced to two groups:

$$
\text{InputDevice}=\begin{cases}
\text{Click-based}, & \text{controller, mouse, or keyboard}\\
\text{Tap-based}, & \text{touch screen or trackpad}
\end{cases}
$$

Similar recoding is applied to fatigue, noise, visual acuity, temperature, cautiousness, handedness, device family, and mobile-device status. Missing observations are removed from the complete-case multiple-regression dataset used for model selection and diagnostics.

## Statistical Methods

### 1. Correlation Analysis

For numerical predictors, pairwise Pearson correlation is examined using

$$
r_{XY}=\frac{\sum_{i=1}^{n}(x_i-\bar{x})(y_i-\bar{y})}
{\sqrt{\sum_{i=1}^{n}(x_i-\bar{x})^2}\sqrt{\sum_{i=1}^{n}(y_i-\bar{y})^2}}.
$$

The resulting correlation matrix is visualized to identify strong linear relationships and potential multicollinearity before fitting broader regression models.

### 2. Wilcoxon Rank-Sum Test

Normality was not supported for the two device-group samples, so the project uses a Wilcoxon rank-sum test rather than a two-sample t-test.

The hypotheses are

$$
H_0: F_{\text{click}}(x)=F_{\text{tap}}(x)
$$

versus

$$
H_A: F_{\text{click}}(x)\neq F_{\text{tap}}(x).
$$

The original analysis reported a p-value of approximately

$$
p \approx 0.0025,
$$

providing evidence that the reaction-time distributions differed between click-based and tap-based input devices.

### 3. Linear Regression

The baseline age model is

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\varepsilon_i,
$$

where $Y_i$ is reaction time and

$$
\varepsilon_i\sim N(0,\sigma^2)
$$

under the standard linear-model assumptions.

The additive age-and-device specification is

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\beta_2\text{Device}_i+\varepsilon_i.
$$

To test whether age behaves differently across device types, an interaction model is fitted:

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\beta_2\text{Device}_i
+\beta_3(\text{Age}_i\times\text{Device}_i)+\varepsilon_i.
$$

The coefficient $\beta_3$ measures whether the age slope changes by input-device group. In the original analysis, the interaction-term p-value was approximately

$$
p \approx 0.55,
$$

so the data did not provide evidence that the age slope differed across device groups.

### 4. Nested-Model F Tests

Nested specifications are compared through

$$
F=
\frac{(SSE_R-SSE_F)/(df_R-df_F)}{SSE_F/df_F},
$$

where $R$ denotes the reduced model and $F$ the fuller model.

For the age-only model versus the age-plus-device additive model, the original analysis reported

$$
p \approx 0.0077,
$$

supporting the inclusion of input-device group in addition to age.

### 5. Multiple Linear Regression

The full multiple-regression framework is

$$
Y_i=\beta_0+\sum_{j=1}^{p}\beta_jX_{ij}+\varepsilon_i,
$$

with candidate predictors spanning demographics, sleep, activity, stress, environment, visual acuity, device type, and related behavioral variables.

The final reduced specification used in the project is

$$
\begin{aligned}
\text{ReactionTime} =\;&\beta_0
+\beta_1\text{WiFiStable}
+\beta_2\text{VisualAcuity}
+\beta_3\text{InputDevice}\\
&+\beta_4\text{ExerciseHours}
+\beta_5\text{SportFrequency}
+\beta_6\text{Temperature}\\
&+\beta_7\text{Stress}
+\beta_8\text{Class}
+\varepsilon.
\end{aligned}
$$

The original reduced-vs-full nested-model comparison produced a p-value of approximately

$$
p \approx 0.73,
$$

indicating that the larger model did not provide a statistically significant improvement over the reduced specification.

## Model Selection

Best-subset regression is used as a screening tool. Candidate model sizes are compared using several criteria.

### Adjusted $R^2$

$$
\bar{R}^2
=1-(1-R^2)\frac{n-1}{n-p-1}.
$$

Unlike ordinary $R^2$, adjusted $R^2$ penalizes adding predictors that do not improve fit enough to justify their complexity.

### Mallows' $C_p$

A common form is

$$
C_p=\frac{SSE_p}{\hat{\sigma}^2}-(n-2p),
$$

where $p$ is the number of fitted parameters. Models with relatively small $C_p$ and $C_p$ near the parameter count are preferred.

### AIC

The implementation compares candidate subset sizes using

$$
AIC=n\log\left(\frac{RSS}{n}\right)+2k,
$$

where $k$ is the number of model parameters.

### BIC

Similarly,

$$
BIC=n\log\left(\frac{RSS}{n}\right)+k\log(n).
$$

BIC penalizes model complexity more strongly than AIC as sample size increases.

## Model Diagnostics

### Multicollinearity: Variance Inflation Factor

For predictor $X_j$,

$$
VIF_j=\frac{1}{1-R_j^2},
$$

where $R_j^2$ comes from regressing $X_j$ on the remaining predictors. Large VIF values indicate that a predictor is highly explained by other predictors.

### Heteroskedasticity: Breusch-Pagan Test

The Breusch-Pagan test evaluates

$$
H_0:\operatorname{Var}(\varepsilon_i)=\sigma^2
$$

against a variance structure related to the predictors. The original reduced model produced a p-value of approximately

$$
p \approx 0.050,
$$

which was treated as borderline evidence of variance instability.

### Residual Normality: Shapiro-Wilk Test

The original untransformed reduced model had approximately

$$
p \approx 0.009,
$$

providing evidence against residual normality.

### Residual Autocorrelation: Durbin-Watson Test

The Durbin-Watson statistic is

$$
DW=
\frac{\sum_{t=2}^{n}(e_t-e_{t-1})^2}
{\sum_{t=1}^{n}e_t^2}.
$$

Values near 2 are consistent with little first-order residual autocorrelation. The original analysis did not find evidence of residual autocorrelation.

## Box-Cox Transformation

To improve regression assumptions, the project applies the Box-Cox family

$$
y^{(\lambda)}=
\begin{cases}
\dfrac{y^\lambda-1}{\lambda}, & \lambda\neq 0,\\[6pt]
\log y, & \lambda=0.
\end{cases}
$$

The original analysis used

$$
\lambda=-0.5,
$$

so the transformed response is

$$
y^{(-0.5)}=\frac{y^{-0.5}-1}{-0.5}.
$$

After transformation, the original analysis reported Breusch-Pagan and Shapiro-Wilk p-values above 0.05, indicating less evidence against constant variance and residual normality.

## Influence Diagnostics

Three complementary diagnostics are used.

### Leverage

High-leverage observations are screened using

$$
h_{ii}>\frac{2p}{n},
$$

where $p$ is the number of fitted coefficients.

### Studentized Residuals

Studentized residuals are

$$
t_i=\frac{e_i}{s_{(i)}\sqrt{1-h_{ii}}},
$$

and are compared with a Bonferroni-adjusted cutoff.

### Cook's Distance

Cook's distance summarizes the influence of observation $i$ on the fitted coefficient vector:

$$
D_i=\frac{e_i^2}{p\,MSE}
\frac{h_{ii}}{(1-h_{ii})^2}.
$$

The project flags observations with

$$
D_i\ge 1
$$

as potentially influential.

## Nonlinearity Check

A quadratic exercise-hours term is added to test whether the relationship appears nonlinear:

$$
Y=\beta_0+\beta_1X+\beta_2X^2+\cdots+\varepsilon.
$$

The transformed linear model and quadratic extension are compared with a nested F test. The original analysis reported

$$
p \approx 0.37,
$$

so the quadratic term did not provide evidence of a meaningful improvement.

## Evaluation Strategy

This project is explanatory rather than a machine-learning prediction benchmark, so evaluation focuses on statistical validity and parsimony instead of train/test prediction error.

The main evaluation criteria are:

- hypothesis-test p-values for group and interaction effects
- nested-model F tests for incremental explanatory value
- adjusted $R^2$, Mallows' $C_p$, AIC, and BIC for model selection
- VIF for multicollinearity
- Breusch-Pagan test for variance stability
- Shapiro-Wilk test for residual normality
- Durbin-Watson test for autocorrelation
- leverage, studentized residuals, and Cook's distance for influence

## Main Results

The original project results support four main conclusions:

1. **Input device matters:** click-based and tap-based reaction-time distributions differed significantly ($p\approx0.0025$).
2. **Device adds explanatory value beyond age:** adding device to the age-only model improved fit ($p\approx0.0077$).
3. **No supported age-by-device interaction:** the interaction term was not significant ($p\approx0.55$).
4. **A reduced model was sufficient:** the full model did not significantly outperform the reduced specification ($p\approx0.73$), while the Box-Cox transformation improved the residual diagnostics reported in the original analysis.

## Repository Structure

```text
.
├── README.md
├── reaction_time_analysis.Rmd
├── Survey.csv
└── Statistical Modelling_Final_Project1.pdf
```

## Tools

R · tidyverse · ggplot2 · car · lmtest · MASS · leaps · corrplot

## Reproducibility

Clone the repository:

```bash
git clone https://github.com/ziqixu22/reaction-time-statistical-modeling.git
cd reaction-time-statistical-modeling
```

Then open `reaction_time_analysis.Rmd` in RStudio and knit the document. The repository also includes a GitHub Actions workflow for automated reproducibility checks.

## Notes

This project was originally developed as a statistical-modeling course project and later reorganized for reproducibility and portfolio presentation. Numerical findings above are reported from the original project analysis; they should not be interpreted as causal estimates.
