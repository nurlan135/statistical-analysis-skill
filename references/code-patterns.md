# Code Patterns

Copy-paste Python templates for every path. Conventions used throughout:
`format_p()` for p-values, `dpi=300, bbox_inches='tight'` for figures,
`matplotlib.use('Agg')` for headless runs.

```python
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np
from scipy import stats

plt.style.use('seaborn-v0_8-white')
FIG = dict(dpi=300, bbox_inches='tight')

def format_p(p):
    if pd.isna(p):
        return '—'
    if p < .001:
        return '< .001'
    return f'= {p:.3f}'

# pip install: scipy statsmodels pingouin matplotlib seaborn scikit-learn python-docx lifelines
```

## Locale notes

- Azerbaijani/Turkish Latin characters (ə, ğ, ı, ş, ç) render fine with the
  default DejaVu Sans — no special font setup needed.
- Official tables use decimal commas and dashes for missing. Always parse via
  `parse_num()` below, never `float()` directly.

## Ingestion

```python
def parse_num(x):
    """Decimal comma, nbsp, and '-'/en-dash/em-dash/blank as missing."""
    if x is None or (isinstance(x, float) and np.isnan(x)):
        return np.nan
    s = str(x).strip().replace('\xa0', '')
    if s in ('', '-', '–', '—', 'nan', 'None'):
        return np.nan
    return float(s.replace(',', '.'))

df = pd.read_excel("data.xlsx")   # or pd.read_csv("data.csv")

# Word report tables -> raw CSVs, never copy-paste
import docx
doc = docx.Document("report.docx")
for ti, t in enumerate(doc.tables):
    rows = [[c.text.strip() for c in r.cells] for r in t.rows]
    pd.DataFrame(rows[1:], columns=rows[0]).to_csv(
        f"table{ti+1}_raw.csv", index=False, encoding="utf-8-sig")
```

## Data profile

```python
def profile(s):
    s = pd.to_numeric(s.map(parse_num) if s.dtype == object else s,
                      errors='coerce').dropna()
    n = len(s)
    skew, kurt = s.skew(), s.kurt()
    p_norm = stats.shapiro(s).pvalue if 3 <= n < 50 else np.nan
    normal = (p_norm > .05) if n < 50 else (abs(skew) < 2 and abs(kurt) < 7)
    return {'N': n, 'M': s.mean(), 'SD': s.std(ddof=1),
            'skew': skew, 'kurt': kurt,
            'normal': normal, 'p_norm': p_norm,
            'out3sd': int(((s - s.mean()).abs() > 3 * s.std(ddof=1)).sum())}
```

## Assumption checks

```python
# Normality + homogeneity before any t-test
stats.shapiro(group_a)                       # n < 50
stats.levene(group_a, group_b)               # equal variances?

# Multicollinearity before regression
from statsmodels.stats.outliers_influence import variance_inflation_factor
vif = [variance_inflation_factor(X.values, i) for i in range(X.shape[1])]
# VIF > 10 -> drop or merge predictors

# Sphericity (repeated measures ANOVA) via pingouin: pg.sphericity(df)
```

## Descriptives

```python
desc = df[vars].describe().T
desc['skew'] = df[vars].skew()
desc['kurt'] = df[vars].kurt()
df[var].value_counts(normalize=True).mul(100).round(1)   # categorical %
```

## Two-group comparison (auto fallback)

```python
ok_a, ok_b = profile(a)['normal'], profile(b)['normal']
equal_var = stats.levene(a, b).pvalue > .05
if ok_a and ok_b:
    res = stats.ttest_ind(a, b, equal_var=equal_var)      # or ttest_rel
    d = (a.mean() - b.mean()) / np.sqrt((a.var(ddof=1) + b.var(ddof=1)) / 2)
else:
    res = stats.mannwhitneyu(a, b, alternative='two-sided')  # or wilcoxon
    print("> Note: normality failed, switched to non-parametric test.")
```

## Correlation with p-values

```python
def corr_table(df, vars, method='pearson'):
    fn = stats.pearsonr if method == 'pearson' else stats.spearmanr
    r = pd.DataFrame(np.nan, index=vars, columns=vars)
    p = r.copy()
    for i in vars:
        for j in vars:
            x, y = df[i].dropna(), df[j].dropna()
            idx = df[i].notna() & df[j].notna()
            r.loc[i, j], p.loc[i, j] = fn(df.loc[idx, i], df.loc[idx, j])
    return r.round(2), p

# Heatmap
sns.heatmap(r, annot=True, fmt='.2f', cmap='RdBu_r', center=0, vmin=-1, vmax=1)
plt.savefig('corr_heatmap.png', **FIG)
```

## Regression (+ Holm for 3+ tests)

```python
import statsmodels.api as sm
from statsmodels.stats.multitest import multipletests

X = sm.add_constant(df[ivs])
model = sm.OLS(df[dv], X).fit(cov_type='HC3')   # robust SE by default
print(model.summary())

# Hierarchical: controls -> + predictors
m1 = sm.OLS(df[dv], sm.add_constant(df[controls])).fit()
m2 = sm.OLS(df[dv], sm.add_constant(df[controls + predictors])).fit()
r2_change = m2.rsquared - m1.rsquared

# Holm adjustment across a family of p-values
reject, p_adj, _, _ = multipletests(p_values, method='holm')
```

## Moderation (simple slopes)

```python
df['Xc'] = df['X'] - df['X'].mean()
df['Mc'] = df['M'] - df['M'].mean()
df['XM'] = df['Xc'] * df['Mc']
mod = sm.OLS(df['Y'], sm.add_constant(df[['Xc', 'Mc', 'XM']])).fit(cov_type='HC3')
b0, b1, b2, b3 = mod.params
for label, m in [('low -1SD', df['M'].mean() - df['M'].std()),
                 ('high +1SD', df['M'].mean() + df['M'].std())]:
    print(label, 'slope =', round(b1 + b3 * (m - df['M'].mean()), 3))
```

## Mediation (bootstrap)

```python
def bootstrap_mediation(df, X, M, Y, n_boot=5000, seed=42):
    rng = np.random.default_rng(seed)
    n = len(df)
    ind = []
    for _ in range(n_boot):
        s = df.sample(n, replace=True, random_state=rng.integers(1e9))
        a = sm.OLS(s[M], sm.add_constant(s[X])).fit().params[X]
        b = sm.OLS(s[Y], sm.add_constant(s[[X, M]])).fit().params[M]
        ind.append(a * b)
    ind = np.array(ind)
    lo, hi = np.percentile(ind, [2.5, 97.5])
    c_prime = sm.OLS(df[Y], sm.add_constant(df[[X, M]])).fit().params[X]
    return {'indirect': ind.mean(), 'ci': (lo, hi),
            'significant': not (lo <= 0 <= hi),
            'direct_c_prime': c_prime,
            'verdict': 'full mediation' if abs(c_prime) < 1e-9 or
                       stats.t.sf(abs(c_prime), n) > .05 else 'partial mediation'}
```

## Sector / group comparison (official tables)

```python
# Leaf-level rows only: drop totals, parents, zero rows first
leaf = t[~t['sector'].str.contains('total|cəmi', case=False)]
leaf = leaf[(leaf[['y25', 'y24']] != 0).any(axis=1)].dropna(subset=['y25', 'y24'])
d = leaf['y25'] - leaf['y24']
if stats.shapiro(d).pvalue > .05:
    print(stats.ttest_rel(leaf['y25'], leaf['y24']))
else:
    print(stats.wilcoxon(leaf['y25'], leaf['y24']))
leaf['growth_pct'] = (d / leaf['y24'].replace(0, np.nan) * 100).round(2)
# Caveat: rows are NOT independent samples -> p is descriptive; report Holm + CI.
```

## Reliability / ICC

```python
import pingouin as pg
pg.cronbach_alpha(data=df[items])          # Cronbach's α
pg.intraclass_corr(data=df_long, targets='t', raters='r', ratings='score')
```

## Figures (triplet standard)

```python
# Grouped bars with error bars (t-test)
means = df.groupby('group')['score'].agg(['mean', 'sem'])
means.plot.bar(y='mean', yerr='sem', capsize=4, rot=0)
plt.savefig('group_bars.png', **FIG)

# Growth bars, red/green split (sector comparison)
colors = ['#c0392b' if v < 0 else '#2471a3' for v in leaf['growth_pct']]
plt.barh(leaf['sector'], leaf['growth_pct'], color=colors)
plt.axvline(0, color='black', linewidth=0.8)
plt.savefig('growth.png', **FIG)

# Coefficient forest (regression)
coef = model.params.drop('const')
ci = model.conf_int().drop('const')
plt.errorbar(coef, coef.index, xerr=[coef - ci[0], ci[1] - coef], fmt='o')
plt.axvline(0, color='black', linewidth=0.8)
plt.savefig('forest.png', **FIG)
```

## Survival

```python
from lifelines import KaplanMeierFitter, CoxPHFitter
kmf = KaplanMeierFitter()
for g in df['group'].unique():
    m = df['group'] == g
    kmf.fit(df.loc[m, 'duration'], df.loc[m, 'event'], label=g)
    kmf.plot_survival_function()
plt.savefig('km_curve.png', **FIG)
CoxPHFitter().fit(df[['duration', 'event'] + covs],
                  duration_col='duration', event_col='event').print_summary()
```

## Export

```python
with pd.ExcelWriter('results.xlsx') as w:
    desc.to_excel(w, sheet_name='Descriptives')
    r.to_excel(w, sheet_name='Correlations')
```

## R (only when Python cannot)

Rule: try Python first, else emit a standalone `.R` script for RStudio
(lavaan / lme4 / metafor / mirt templates in `references/full-workflow.md`).
