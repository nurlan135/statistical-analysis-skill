# APA Table Standards

## General Rules

- Statistics: 2 decimals. p-values: 3 decimals, never `p = .000` — use `< .001`.
- Leading zero omitted for values bounded by 1 (r, p, β, α, AUC, ICC).
- Stars: *p < .05, **p < .01, ***p < .001.
- Every table ends with *Note.* explaining abbreviations, coding, and N.
- 3+ comparisons in one family → add Holm-adjusted p column (`p_adj`,
  statsmodels `multipletests`, method='holm').
- Effect size + 95% CI mandatory next to every p; at small N (< 30) add
  "power limited, exploratory" to the note.
- Cross-year money tables → add "Values nominal; real change unknown without
  deflator" to the note.

## Table 1: Continuous Descriptives

| Variable | M | SD | Min | Max | Skew | Kurt |
|----------|---:|---:|---:|---:|---:|---:|
| Variable 1 | 3.45 | 1.23 | 1.00 | 7.00 | 0.12 | -0.34 |
| Variable 2 | 4.56 | 0.98 | 1.50 | 6.50 | -0.23 | 0.56 |

*Note.* N = 500. M = mean; SD = standard deviation; Skew = skewness; Kurt = kurtosis.

## Table 2: Categorical Descriptives

| Variable | n | % |
|----------|---:|---:|
| Gender | | |
| Male | 245 | 49.0 |
| Female | 255 | 51.0 |
| Education | | |
| High school | 120 | 24.0 |
| Bachelor's | 280 | 56.0 |
| Master's+ | 100 | 20.0 |

*Note.* N = 500.

## Table 3: Correlation Matrix

| Variable | 1 | 2 | 3 | 4 |
|----------|---:|---:|---:|---:|
| 1. Variable A | — | | | |
| 2. Variable B | .45*** | — | | |
| 3. Variable C | .23** | .34*** | — | |
| 4. Variable D | -.12 | .56*** | .28** | — |

*Note.* N = 500. Pearson r. *p < .05. **p < .01. ***p < .001 (Holm-adjusted within matrix).

## Table 4: Regression

| Variable | B | SE | β | t | p | p_adj | 95% CI |
|----------|---:|---:|---:|---:|---:|---:|---:|
| (Constant) | 2.34 | 0.45 | | 5.20 | <.001 | <.001 | [1.46, 3.22] |
| Predictor X | 0.45 | 0.08 | .34 | 5.63 | <.001 | <.001 | [0.29, 0.61] |
| Age | -0.02 | 0.01 | -.08 | -2.00 | .046 | .092 | [-0.04, 0.00] |

*Note.* N = 500. R² = .23. Robust SE (HC3). p_adj = Holm.

## Table 5: Hierarchical Regression

| Variable | Step 1 | | Step 2 | |
|----------|---:|---:|---:|---:|
| | β | p | β | p |
| Control A | .12 | .034 | .10 | .065 |
| Predictor X | | | .34 | <.001 |
| R² | .05 | | .17 | |
| ΔR² | | | .12*** | |

*Note.* N = 500. ***p < .001 for ΔR² (ΔF test).

## Table 6: Moderation

| Variable | B | SE | t | p | 95% CI |
|----------|---:|---:|---:|---:|---:|
| X | 0.34 | 0.08 | 4.25 | <.001 | [0.18, 0.50] |
| M | 0.28 | 0.09 | 3.11 | .002 | [0.10, 0.46] |
| X × M | 0.15 | 0.05 | 3.00 | .003 | [0.05, 0.25] |

**Simple slopes:**

| Level | B | SE | t | p |
|-------|---:|---:|---:|---:|
| Low (-1 SD) | 0.19 | 0.10 | 1.90 | .058 |
| High (+1 SD) | 0.49 | 0.09 | 5.44 | <.001 |

*Note.* N = 500. R² = .28. Predictors centered.

## Table 7: Mediation (bootstrap)

| Path | B | SE | 95% CI |
|------|---:|---:|---|
| Total (c) | 0.45 | 0.08 | [0.29, 0.61] |
| Direct (c') | 0.28 | 0.09 | [0.10, 0.46] |
| Indirect (a×b) | 0.17 | 0.05 | [0.08, 0.28] |

*Note.* N = 500. 5000 bootstrap samples, percentile CI. CI excludes zero → significant indirect effect (partial mediation).

## Table 8: Sector / Group Comparison (official tables)

| Sector | 2025 | 2024 | Growth % | p | p_adj |
|--------|---:|---:|---:|---:|---:|
| Food | 17888.70 | 10262.60 | +74.31 | <.001 | <.001 |
| Energy | 6940.30 | 4510.70 | +53.86 | .012 | .048 |
| Chemicals | 18708.80 | 19860.10 | -5.80 | .310 | .620 |

*Note.* Leaf-level sectors only (totals and parent aggregates excluded, zero rows dropped).
Rows are not independent samples — p-values descriptive; Holm-adjusted. Values nominal.

## Table 9: Reliability

| Subscale | Items | M | SD | α | CITC range |
|----------|------:|---:|---:|---:|---|
| Subscale A | 5 | 3.45 | 0.89 | .85 | .52–.71 |
| **Total** | **15** | **3.53** | **0.72** | **.91** | — |

*Note.* N = 500. α = Cronbach's alpha; CITC = corrected item-total correlation.

## Table 10: ROC

| Index | Value | 95% CI |
|-------|------:|---|
| AUC | .85 | [.80, .90] |
| Cutoff (Youden) | 12.50 | — |
| Sensitivity | .82 | [.76, .87] |
| Specificity | .78 | [.72, .84] |

*Note.* N = 500 (250 cases, 250 controls).

## Table 11: Cox Regression

| Variable | HR | 95% CI | p |
|----------|---:|---|---:|
| Age (per year) | 1.03 | [1.01, 1.05] | .008 |
| Treatment (new vs standard) | 0.58 | [0.43, 0.78] | <.001 |

*Note.* N = 500. HR = hazard ratio. Median follow-up 24 months.

## Table 12: Power / Sample Size

| Analysis | Effect | α | Power | Required N |
|----------|---|---:|---:|---:|
| Independent t-test | d = 0.50 | .05 | .80 | 64/group |
| One-way ANOVA (3 groups) | f = 0.25 | .05 | .80 | 159 total |
| Pearson correlation | r = .30 | .05 | .80 | 85 total |
| Multiple regression (3 IVs) | f² = .15 | .05 | .80 | 77 total |

*Note.* Two-sided tests.

## p-value Quick Reference

| Raw | Print |
|-----|---|
| .0234 | = .023 |
| .0004 | < .001 |
| .050 | = .050 (marginal — state explicitly, never "trend toward significance") |

## Bilingual Notes (optional)

Result paragraphs and table notes can be issued in Azerbaijani on request.
Template: "*Qeyd.* N = 500. ..." — same rules, translated abbreviations
(O = orta, SS = standart sapma — define on first use).
