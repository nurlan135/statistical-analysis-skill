# 📊 statistical-analysis-skill — Diagnosis Before Analysis

Turn your AI assistant into a statistical consultant that **profiles data and checks
assumptions first**, then selects the right method and delivers publish-ready results.

## ✨ Features

- **Word (.docx) ingestion** via `python-docx` — no more copy-paste
- **Non-tidy table checklist** (`references/official-tables.md`): total rows, parent⊃child hierarchy, zero rows, decimal commas, dash-as-missing
- **Holm-adjusted p-values** for 3+ comparisons, mandatory effect size + CI at small N
- **Nominal vs real** money rule for cross-year comparisons

## 🚀 Install

Copy `SKILL.md` + `references/` + `assets/` into your agent's skill folder:

```bash
# opencode (project scope)
cp SKILL.md references assets .opencode/skills/statistical-analysis/ -r

# claude code (personal)
cp -r . ~/.claude/skills/statistical-analysis/
```

R methods (SEM/HLM/meta) need Docker: see `docker/README.md`.

## 📋 How it works

```
Upload data → Data profile → Assumption checks → Method selection → Execute → APA table + figure + paragraph
```

| Complexity | Path | Confirmations |
|------------|------|---------------|
| t-test, correlation, reliability | Quick | 0 |
| Regression, ANOVA, mediation | Light | 1 |
| SEM, HLM, IRT, meta | Full | stage-by-stage |
| Power analysis | Dedicated | 1 |

## 📁 Structure

```
SKILL.md                   # skill (root)
references/
  methods-index.md         # method selection guide
  code-patterns.md         # copy-paste Python patterns
  table-formats.md         # APA table templates
  full-workflow.md         # 4-stage complex-analysis workflow
  official-tables.md       # non-tidy table checklist
docker/                    # R environment (lavaan, lme4, metafor)
assets/report-template.md  # report skeleton
```

## 📄 License

MIT
