# Official / Non-tidy Tables Checklist

Government and agency reports usually ship **presentation tables**, not analysis-ready
datasets. Apply this checklist before any test.

## 1. Total / aggregate rows

Drop rows labelled `total`, `all`, `all industries`, etc. They are sums of the rows
below, not independent observations.

## 2. Hierarchical (parent ⊂ child) structure

Example: Manufacturing ⊃ Food, Chemicals. Parent value ≈ sum of children, so parent
and child rows double-count the same output. Rule: analyse **leaf-level units only**;
if a parent must be reported, report it descriptively, never inside the same test.

## 3. Zero rows

Rows with 0 in all periods (e.g. inactive sub-sectors like Beverages, Apparel) carry
no variance — exclude from tests, mention in a footnote.

## 4. Non-numeric cells

Dashes (`-`, `–`, `—`), blanks, and decimal commas (`,`). Parse with:

```python
def parse_num(x):
    import numpy as np
    if x is None or (isinstance(x, float) and np.isnan(x)):
        return np.nan
    s = str(x).strip().replace('\xa0', '')
    if s in ('', '-', '–', '—', 'nan', 'None'):
        return np.nan
    return float(s.replace(',', '.'))
```

## 5. Word documents

Read tables via `python-docx`, never copy-paste:

```python
import docx, pandas as pd
doc = docx.Document("report.docx")
for ti, t in enumerate(doc.tables):
    rows = [[c.text.strip() for c in r.cells] for r in t.rows]
    pd.DataFrame(rows[1:], columns=rows[0]).to_csv(
        f"table{ti+1}_raw.csv", index=False, encoding="utf-8-sig")
```

## 6. Non-independence caveat

Sectors/products in one official table are NOT independent samples (shared inputs,
shared price shocks). Paired t/Wilcoxon p-values across rows are therefore
**descriptive, not strict inference**. Always state this caveat and prefer
effect sizes + CIs, Holm-adjusted p-values, and a small-N disclaimer (< 20 groups).

## 7. Nominal vs real money

Values across years are nominal unless a deflator/CPI series is documented.
Report e.g. "nominal +5.4%; real change unknown without deflator" — never present
nominal growth as real growth.
