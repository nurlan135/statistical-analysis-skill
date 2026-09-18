---
name: statistical-analysis
description: >
  Statistical analysis service. Trigger conditions: (1) Upload a data file,
  (2) Say "statistical analysis" / "analyze my data" / "run the data",
  (3) Mention a specific statistical method (t-test / regression / SEM, etc.).

  **Core principle**: Proactively diagnose like a top-tier statistical consultant — don't just execute the method the user requests, ensure the method selection is correct.
---

# Statistical Analysis Service

## Core Philosophy: Diagnosis Before Analysis

Handle messy real-world tables first, then diagnose data and assumptions.

```
User request → Ingest (csv/xlsx/docx) → Data profile → Assumption checks → Intelligent method selection → Execute → Triplet output (table + figure + paragraph)
```

---

## Workflow Overview

```
User request
    |
    v
+-------------------------------------+
|  Step 0: Data Profile (required for  |
|          all paths)                  |
|  - Sample size, variable types,      |
|    missing patterns                  |
|  - Distribution characteristics,     |
|    outlier detection                 |
|  - Completed within 30 seconds,      |
|    no confirmation needed            |
+-------------------------------------+
    |
    v
Assess complexity -> Select path
    |
    |-- Quick path -> Assumption self-check -> Execute -> Triplet output
    |-- Light path -> Assumption self-check -> Confirm variables -> Execute -> Triplet output
    +-- Full path  -> Four stages (incl. assumption checks) -> Triplet output
```

---

## Step 0: Data Profile

**Must be executed before all analyses**. Output format:

```markdown
## Data Profile

| Metric | Value |
|--------|-------|
| Sample size | N = 3248 |
| Variables | 94 (continuous: 60, categorical: 34) |
| Missing rate | Overall 2.3%, highest: Variable X (15.2%) |
| Outliers | Variable Y has 12 (> 3SD) |

### Key Variable Distributions
| Variable | M (SD) | Skewness | Kurtosis | Normality |
|----------|--------|----------|----------|-----------|
| DV_score | 3.45 (1.23) | 0.34 | -0.12 | Pass |
| IV_score | 2.89 (0.98) | 1.45 | 3.21 | Right-skewed |

### Data Alerts
- Warning: IV_score is right-skewed; parametric tests should be used with caution
- Warning: Variable X has 15% missing; recommend checking MCAR/MAR
- OK: Sample size is sufficient for all standard analyses
```

**Execution code** (runs automatically, not displayed to the user):

```python
import pandas as pd
import numpy as np
from scipy import stats

# pip install: scipy statsmodels pingouin matplotlib seaborn scikit-learn python-docx
df = pd.read_excel("data.xlsx")  # or pd.read_csv("data.csv")

def parse_num(x):
    # Handles decimal comma, nbsp, and '-'/'–'/'—'/'' as missing
    if x is None or (isinstance(x, float) and np.isnan(x)):
        return np.nan
    s = str(x).strip().replace('\xa0', '')
    if s in ('', '-', '–', '—', 'nan', 'None'):
        return np.nan
    return float(s.replace(',', '.'))

# Word tables: read via python-docx, never copy-paste
import docx
doc = docx.Document("report.docx")
for ti, t in enumerate(doc.tables):
    rows = [[c.text.strip() for c in r.cells] for r in t.rows]
    pd.DataFrame(rows[1:], columns=rows[0]).to_csv(
        f"table{ti+1}_raw.csv", index=False, encoding="utf-8-sig")

def data_profile(df, target_vars=None):
    """Generate data profile; target_vars are key variables mentioned by the user"""
    vars_to_check = target_vars or df.select_dtypes(include=[np.number]).columns[:10]

    profile = {}
    for var in vars_to_check:
        col = df[var].dropna()
        n = len(col)
        # Normality test: Shapiro-Wilk for n<50, skewness+kurtosis for n>=50
        skew, kurt = col.skew(), col.kurtosis()
        if n < 50:
            _, p_norm = stats.shapiro(col)
            is_normal = p_norm > .05
        else:
            is_normal = abs(skew) < 2 and abs(kurt) < 7

        profile[var] = {
            'M': col.mean(), 'SD': col.std(),
            'missing': df[var].isna().sum(),
            'missing_pct': df[var].isna().mean() * 100,
            'skew': skew, 'kurt': kurt,
            'is_normal': is_normal,
            'outliers_3sd': ((col - col.mean()).abs() > 3 * col.std()).sum()
        }
    return profile

def format_p(p):
    if p < .001:
        return "< .001"
    return f"= {p:.3f}"
```

---

## Non-tidy / Hierarchical Tables (mandatory check)

Official statistics tables are often NOT tidy data. See `references/official-tables.md`
for the full checklist. Summary: (1) drop total/aggregate rows, (2) analyse leaf-level
units only, (3) flag non-independence — parent and child double-count the same output,
so paired t/Wilcoxon p-values are descriptive, not strict inference. State this caveat
in the result paragraph. With small N (< 20 groups) prefer effect sizes + CIs over
binary significance claims.

---

## Complexity Assessment and Path Selection

| Complexity | Analysis Type | Path | Confirmations |
|------------|---------------|------|---------------|
| **Simple** | Descriptive statistics, t-test, chi-square, correlation, reliability/validity, ICC | Quick | 0 |
| **Moderate** | Regression, ANOVA, moderation, mediation, ROC/AUC, survival analysis | Light | 1 |
| **Complex** | SEM/CFA, HLM, IRT, meta-analysis, RI-CLPM, propensity score matching | Full | 3-4 |
| **Planning** | Sample size calculation / Power Analysis (no data, parameters only) | Dedicated | 1 |

---

## Quick Path

Data profile → assumption self-check → execute → triplet output.

Check normality and homogeneity (Levene) before t-tests. If assumptions fail,
automatically switch to the non-parametric alternative (e.g. Mann-Whitney U)
and tell the user why:

```markdown
> Note: Variable X failed normality (Shapiro-Wilk p = .003). Switched to Mann-Whitney U test.
```

Method details: see `references/methods-index.md`. Code patterns: see `references/code-patterns.md`.

---

## Multiple Comparisons + Small-N + Nominal/Real (mandatory)

- 3+ group/sector comparisons → report Holm-adjusted p-values alongside raw p
  (statsmodels `multipletests`, method='holm').
- Small N (< 20 groups or n < 30): always report effect size + 95% CI next to p;
  state achieved power is low and treat p as exploratory.
- Monetary values across years are nominal unless a deflator/CPI is documented — say so
  explicitly (e.g. "nominal +5.4%; real change unknown without deflator") and never
  present nominal growth as real.

---

## Light Path

Data profile → assumption self-check → confirm variable roles → execute → triplet output.

Show a confirmation table with Y, X, moderator, controls plus distribution and
assumption warnings (VIF, skewness, sample size). Recommend fixes (log transform,
robust SE HC3) before proceeding.

---

## Full Path (complex analysis)

Work in four confirmed stages — see `references/full-workflow.md` for output templates:
(1) data profile + cleaning plan, (2) cleaning + assumption checks,
(3) analysis plan + sample adequacy, (4) execute → triplet output.
Pause for confirmation at each stage. R code runs via `docker/` environment.

---

## Power Analysis Path

No data file needed. Ask for: method, expected effect size (default medium),
alpha (.05), power (.80), groups, one/two-tailed. Then compute required N.
Quick reference (per group unless noted): t-test medium d=0.5 → 64;
ANOVA 3-group medium f=0.25 → 159; correlation medium r=.30 → 85;
regression 3-IV medium f²=.15 → 77.

---

## Required Output Triplet

Every analysis must produce all three. Table formats: see `references/table-formats.md`.
Report template: see `assets/report-template.md`.

1. **APA table** (Markdown; Excel on request): stats 2 decimals, p 3 decimals (`< .001`),
   stars *p<.05 **p<.01 ***p<.001, effect sizes, 95% CI.
2. **Figure** (PNG, dpi=300): t-test → grouped bars with error bars; correlation → heatmap;
   regression → coefficient forest plot; moderation → simple slopes; mediation → path diagram;
   ANOVA → boxplot; ROC → ROC curve; survival → Kaplan-Meier curve.
3. **Result paragraph** (English; Azerbaijani on request): ready to paste into a manuscript. Templates:
   - t-test: "An independent samples t-test revealed a significant difference in {DV} between {g1} (M = .., SD = ..) and {g2} (M = .., SD = ..), t(df) = .., p .., Cohen's d = ... The effect size was {small/medium/large}."
   - Correlation: "Pearson correlation showed {X} was significantly {positively/negatively} correlated with {Y}, r(df) = .., p ...."
   - Regression: report R², F, ΔR², ΔF, β per step.
   - Mediation: indirect effect + 95% bootstrap CI, direct effect, full/partial mediation.
   - Moderation: interaction B/SE/p + simple slopes at ±1SD.

Figure setup: `matplotlib.use('Agg')`, dpi=300, `savefig.bbox='tight'`, seaborn style `white`.

---

## Missing Data Strategy

| Missing rate | Strategy |
|-------------|----------|
| < 5% | listwise deletion |
| 5–20% | multiple imputation (MICE), report sensitivity |
| > 20% | check MCAR first, then decide |

Always alert the user when missingness exceeds 5%.

---

## Method–Library Quick Reference

| Method | Library |
|--------|---------|
| descriptives | pandas `describe()` |
| t-test, chi-square, correlation | scipy |
| regression, ANOVA interaction | statsmodels |
| ANOVA, reliability (Cronbach α), ICC | pingouin |
| ROC/AUC | sklearn |
| survival | lifelines |
| SEM/HLM/IRT/meta (Python impossible or user asks for R) | R via docker |

Only use R when the user explicitly requests it or Python cannot do the method.
