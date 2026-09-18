# Full Path Workflow

For SEM/CFA, HLM, IRT, meta-analysis, RI-CLPM, PSM. Four stages, pause for
confirmation after each.

```
Stage 1: profile + cleaning plan  →  CONFIRM
Stage 2: execute cleaning         →  CONFIRM
Stage 3: analysis plan + adequacy →  CONFIRM
Stage 4: execute → triplet output
```

If the user requests changes mid-flight, show original-vs-new table and re-confirm
before redoing downstream stages.

## Stage 1: Cleaning plan

```markdown
## Data Overview
- N original = ... · variables = ... (continuous X, categorical Y)

## Cleaning Plan
| Variable | Missing n (%) | Treatment |
|----------|---------------|-----------|
| ... | ... | [delete / MICE / FIML / retain] |

| Check | Result | Action |
|-------|--------|--------|
| Outliers (> 3SD) | ... | [retain / winsorize / drop + why] |
| Nesting (ICC) | ... | [single-level / HLM if > .05] |
| Leaf-level only | ... | [totals + parents dropped] |

Expected N final = ... (retention ...%)
---
⏸ Confirm cleaning plan
```

## Stage 2: Cleaning results

```markdown
## Cleaning Results
- N: ... → ... (retention ...%)
- Done: [1. ... 2. ...]
- Saved: data_cleaned.csv
---
⏸ Confirm cleaning results
```

## Stage 3: Analysis plan

```markdown
## Variable Roles
| Role | Variable | Notes |
|------|----------|-------|
| Latent / grouping / outcome / predictor | ... | ... |

## Plan
| # | Step | Method | Output |
|---|------|--------|--------|
| 1 | [e.g. CFA] | [semopy / lavaan .R] | [fit indices, diagram] |
| 2 | [e.g. SEM] | ... | [paths, comparison] |

## Adequacy
- [SEM: N > 200? HLM: ≥ 30 groups? Meta: k ≥ ...? IRT: n per item?]
- Figures/tables language: [EN / AZ] · APA format
---
⏸ Confirm analysis plan
```

## Stage 4: Execute

Python-first (semopy, statsmodels, factor_analyzer, pymare, lifelines).
Beyond Python's reach → emit a standalone `.R` script for RStudio
(lavaan / lme4 / metafor / mirt) with install instructions.

Checklist before closing: [ ] results table (.xlsx) · [ ] figure (dpi=300) ·
[ ] result paragraph · [ ] fit/heterogeneity indices reported ·
[ ] limitations stated (small N, non-independence, nominal values).
