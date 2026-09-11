# Reaction Time Statistical Modeling

An R-based statistical modeling project analyzing how demographic, behavioral, device, and environmental factors are associated with human reaction time.

## At a Glance

| Item | Summary |
|---|---|
| Goal | Explain variation in human reaction time and identify a parsimonious statistical model. |
| Data | Survey + experimental reaction-time observations with demographic, behavioral, environmental, and device variables. |
| Main methods | Wilcoxon rank-sum, multiple linear regression, interaction modeling, best-subset selection, nested F-tests, Box-Cox transformation. |
| Evaluation | Adjusted $R^2$, Mallows' $C_p$, AIC/BIC, VIF, Breusch-Pagan, Shapiro-Wilk, Durbin-Watson, influence diagnostics. |
| Main result | Device type was associated with reaction time; the age-by-device interaction was not supported; a reduced model retained similar explanatory performance. |

## Project Flow

```text
Raw survey / experiment data
        |
        v
Cleaning + categorical recoding
        |
        v
EDA + correlation analysis
        |
        v
Device-group hypothesis test
        |
        v
Age / device regression models
        |
        v
Best-subset model selection
        |
        v
Diagnostics + Box-Cox transformation
        |
        v
Reduced interpretable model + conclusions
```

## Project Overview

This project investigates variation in reaction time using survey and experimental data. The analysis examines demographic, behavioral, device, and environmental predictors such as age, sleep, input device, fatigue, visual acuity, exercise habits, stress, temperature, and Wi-Fi stability.

The workflow covers the full statistical modeling pipeline: data cleaning, exploratory analysis, nonparametric testing, regression, variable selection, diagnostics, transformation, and influence analysis. Because the data are observational and several predictors are self-reported, results should be interpreted as associations rather than causal effects.

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

### 2. Wilcoxon Rank-Sum Test

Normality was not supported for the two device-group samples, so the project uses a Wilcoxon rank-sum test rather than a two-sample t-test.

$$
H_0: F_{\text{click}}(x)=F_{\text{tap}}(x),
\qquad
H_A: F_{\text{click}}(x)\neq F_{\text{tap}}(x).
$$

The original analysis reported

$$
p\approx0.0025,
$$

providing evidence that the reaction-time distributions differed between click-based and tap-based devices.

### 3. Linear Regression and Interaction

The baseline age model is

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\varepsilon_i.
$$

The additive model is

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\beta_2\text{Device}_i+\varepsilon_i.
$$

The interaction model is

$$
Y_i=\beta_0+\beta_1\text{Age}_i+\beta_2\text{Device}_i
+\beta_3(\text{Age}_i\times\text{Device}_i)+\varepsilon_i.
$$

The interaction-term p-value was approximately

$$
p\approx0.55,
$$

so the data did not support different age slopes across device groups.

### 4. Nested-Model F Tests

Nested specifications are compared through

$$
F=\frac{(SSE_R-SSE_F)/(df_R-df_F)}{SSE_F/df_F}.
$$

For age-only versus age + device, the original analysis reported

$$
p\approx0.0077,
$$

supporting inclusion of device type beyond age alone.

### 5. Multiple Linear Regression

The general model is

$$
Y_i=\beta_0+\sum_{j=1}^{p}\beta_jX_{ij}+\varepsilon_i.
$$

The reduced specification used in the project is

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

The reduced-vs-full comparison produced

$$
p\approx0.73,
$$

so the larger model did not provide a statistically significant improvement over the reduced model.

## Model Selection

The project compares candidate models using several criteria.

$$
\bar{R}^2=1-(1-R^2)\frac{n-1}{n-p-1}
$$

$$
C_p=\frac{SSE_p}{\hat{\sigma}^2}-(n-2p)
$$

$$
AIC=n\log\left(\frac{RSS}{n}\right)+2k
$$

$$
BIC=n\log\left(\frac{RSS}{n}\right)+k\log(n)
$$

The purpose is to balance goodness of fit against model complexity rather than simply maximize ordinary $R^2$.

## Model Diagnostics

### Multicollinearity

$$
VIF_j=\frac{1}{1-R_j^2}.
$$

### Heteroskedasticity

The Breusch-Pagan test evaluates whether

$$
\operatorname{Var}(\varepsilon_i)=\sigma^2.
$$

The reduced model produced approximately

$$
p\approx0.050,
$$

which was treated as borderline evidence of variance instability.

### Residual Normality

The original untransformed reduced model had Shapiro-Wilk

$$
p\approx0.009,
$$

providing evidence against residual normality.

### Residual Autocorrelation

The Durbin-Watson statistic is

$$
DW=\frac{\sum_{t=2}^{n}(e_t-e_{t-1})^2}{\sum_{t=1}^{n}e_t^2}.
$$

Values near 2 are consistent with little first-order residual autocorrelation. The original analysis did not find evidence of residual autocorrelation.

## Box-Cox Transformation

The project applies

$$
y^{(\lambda)}=\begin{cases}
\dfrac{y^\lambda-1}{\lambda}, & \lambda\neq0,\\[6pt]
\log y, & \lambda=0.
\end{cases}
$$

with

$$
\lambda=-0.5.
$$

After transformation, the original analysis reported Breusch-Pagan and Shapiro-Wilk p-values above 0.05, indicating less evidence against constant variance and residual normality.

## Influence Diagnostics

High leverage is screened using

$$
h_{ii}>\frac{2p}{n}.
$$

Studentized residuals are

$$
t_i=\frac{e_i}{s_{(i)}\sqrt{1-h_{ii}}},
$$

and Cook's distance is

$$
D_i=\frac{e_i^2}{p\,MSE}\frac{h_{ii}}{(1-h_{ii})^2}.
$$

The project flags observations with $D_i\ge1$ as potentially influential.

## Nonlinearity Check

A quadratic exercise-hours term is added:

$$
Y=\beta_0+\beta_1X+\beta_2X^2+\cdots+\varepsilon.
$$

The original comparison reported

$$
p\approx0.37,
$$

so the quadratic term did not materially improve the model.

## Evaluation Summary

| Question | Evidence | Interpretation |
|---|---:|---|
| Click vs. tap reaction-time distributions differ? | $p\approx0.0025$ | Yes, statistically significant difference. |
| Does device add value beyond age? | $p\approx0.0077$ | Yes, additive model improves fit. |
| Does age slope differ by device? | $p\approx0.55$ | No supported interaction. |
| Does full model beat reduced model? | $p\approx0.73$ | No; reduced model is more parsimonious. |
| Are untransformed residuals normal? | $p\approx0.009$ | Evidence against normality. |
| Does quadratic exercise effect help? | $p\approx0.37$ | No supported improvement. |

## Main Conclusion

The project shows that input-device type is associated with reaction-time differences, while the age-by-device interaction is not supported. A reduced multiple-regression model retains similar explanatory performance to the full model, and Box-Cox transformation improves the residual diagnostics reported in the original analysis.

## Interview Talking Points

A concise way to explain the project:

> I started with a broad set of demographic, behavioral, environmental, and device variables and asked which factors were associated with reaction time. Because the device groups were not normally distributed, I first used a Wilcoxon rank-sum test. I then fitted nested regression models to separate the effects of age, device, and their interaction. For the broader model I used best-subset selection and compared adjusted $R^2$, $C_p$, AIC, and BIC. The reduced model performed similarly to the full model, but residual diagnostics showed non-normality and borderline heteroskedasticity, so I used a Box-Cox transformation. The main lesson was that model selection is not just about fit—it also requires diagnostics, parsimony, and interpretable assumptions.

Potential follow-up questions to be ready for: why Wilcoxon instead of a t-test; why a reduced model is preferable when the F-test is not significant; what Box-Cox changes; what VIF measures; and why association does not imply causation.

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

```bash
git clone https://github.com/ziqixu22/reaction-time-statistical-modeling.git
cd reaction-time-statistical-modeling
```

Open `reaction_time_analysis.Rmd` in RStudio and knit the document. The repository also includes a GitHub Actions workflow for automated reproducibility checks.

## Notes

This project was originally developed as a statistical-modeling course project and later reorganized for reproducibility and portfolio presentation. Numerical findings above are reported from the original project analysis and should not be interpreted as causal estimates.