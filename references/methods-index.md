# Methods Index

Selection rules with assumptions, fallbacks, and reporting lines. Code lives in
`code-patterns.md`; table shells in `table-formats.md`.

## 0. Path routing (do this first)

| Your question | Path | Confirmations |
|---|---|---|
| Describe / compare 2 groups / correlate / reliability | Quick | 0 — run |
| Predict / compare 3+ groups / moderate / mediate / classify / survive | Light | 1 — variable roles |
| Latent / nested / IRT / meta / panel dynamics | Full | stage-by-stage (`full-workflow.md`) |
| How many subjects do I need? | Power | 1 — parameters |

## 1. Descriptives

Report M (SD) + skew + kurt for continuous; n (%) for categorical.
Normality: Shapiro-Wilk if n < 50, else |skew| < 2 and |kurt| < 7.
Skewed → median [IQR] alongside mean, and prefer non-parametric downstream.

## 2. Two-group differences

```
2 groups?
├── independent + normal + equal variance (Levene) → Student t
├── independent + normal + unequal variance        → Welch t (default ⭐)
├── independent + non-normal                       → Mann-Whitney U
└── paired: differences normal → paired t, else Wilcoxon
```

Always: Cohen's d (0.2 / 0.5 / 0.8) or rank-biserial; 95% CI of the difference.

## 3. Three-plus groups

One-way ANOVA (Levene ok) → Tukey HSD; unequal variance → Welch ANOVA + Games-Howell;
non-normal → Kruskal-Wallis + Dunn. Report F(df1, df2), p (Holm across the family),
η². Repeated measures → check sphericity (Mauchly), else Greenhouse-Geisser; or
Friedman. Sectors in one official table are NOT independent — mark p descriptive.

## 4. Correlation

| Pair | Use |
|---|---|
| continuous–continuous, linear + normal | Pearson r |
| monotonic / ordinal / skewed | Spearman ρ (Kendall τ if n < 30) |
| nominal–nominal | Cramér's V; 2×2 → φ |

|r| ≈ .10 small, .30 medium, .50 large. Holm-adjust matrices; heatmap figure.

## 5. Regression

DV continuous → OLS with HC3 robust SE (default). DV binary → logistic;
count → Poisson, overdispersed → negative binomial; ordinal → ordinal logit.
Checklist: linearity (residual plot), VIF < 10, homoscedasticity (else HC3/WLS),
no autocorrelation (Durbin-Watson for time series). Nested models → ΔR²/ΔF (or
LRT); non-nested → AIC/BIC. Report R², F, B (SE, β, p, p_adj, 95% CI) per step.
Effect: f² = R²/(1−R²): .02 / .15 / .35.

## 6. Moderation & mediation

- Moderation: center X and W, add XW, OLS-HC3; if interaction p < .05 → simple
  slopes at ±1SD (+ simple-slopes plot).
- Mediation: bootstrap indirect a×b (5000, 95% percentile CI). CI excludes zero →
  significant; c′ n.s. → full, else partial mediation.
- Complex / latent paths → SEM instead.

## 7. Reliability & validity

α (or ω) > .70 acceptable, > .80 good; drop items with CITC < .30. Scale
validation: EFA (KMO > .60, Bartlett p < .05) → CFA (CFI > .90, RMSEA < .08).
Raters: categorical → κ (Cohen/Fleiss); continuous → ICC(3,1); methods → Bland-Altman.

## 8. Classification (ROC)

AUC .70–.80 acceptable, .80–.90 good, > .90 excellent. Cutoff via Youden's J.
Report AUC + 95% CI, sensitivity, specificity, curve figure.

## 9. Survival

KM curve → log-rank → Cox (check PH via Schoenfeld, p > .05). Report HR + 95% CI.
Median follow-up always stated.

## 10. Latent / nested / meta / longitudinal (Full path)

| Structure | Method | Gate |
|---|---|---|
| Latent + paths | SEM/CFA (semopy; lavaan .R if complex) | N > 200; fit: χ²(df), RMSEA [90% CI], CFI, SRMR |
| Nested (ICC > .05) | HLM, null → intercept → slope | ≥ 30 groups preferred |
| Studies as units | Meta, random-effects default | Q, I² (25/50/75), τ², Egger + funnel |
| Items as units | IRT: Rasch/2PL/3PL/GRM | b ∈ [−3, 3], a ∈ [0.5, 2.5] |
| Waves as units | LGM for trajectories; RI-CLPM for causality | separate trait vs state |

## 11. Power quick reference (α = .05, power = .80, two-sided)

t-test d = 0.5 → 64/group · ANOVA-3 f = 0.25 → 159 total · r = .30 → 85 ·
regression-3IV f² = .15 → 77 · χ²-2×2 w = .3 → 88 · SEM → N > 200 · HLM → 30×30.

## 12. Cross-cutting rules

Holm for every 3+ family · effect + CI beside every p · small N = exploratory ·
nominal ≠ real · non-independent rows = descriptive p · Welch/HC3 by default.
