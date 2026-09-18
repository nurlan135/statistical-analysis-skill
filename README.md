# 📊 stats-skill-for-ai-agents — Diagnosis Before Analysis

Turn your AI assistant into a statistical consultant that **profiles data and checks
assumptions first**, then selects the right method and delivers publish-ready results:
APA table + figure + result paragraph.

You don't need to know programming or statistics. Prepare your data — the skill
guides diagnosis, method choice, and reporting step by step.

---

## 💡 Why use this skill?

- **Diagnosis first** — spots missingness, skew, outliers, and non-tidy tables (totals,
  parent⊃child rows, decimal commas) before any test runs.
- **Right test, automatically** — assumptions decide parametric vs non-parametric;
  every fallback is stated, never silent.
- **Honest small-N** — effect size + 95% CI beside every p, Holm correction for
  families, exploratory label when power is low.
- **Publish-ready triplet** — APA table, dpi=300 figure, paste-into-manuscript
  paragraph (English + Azerbaijani).
- **SPSS alternative** — no menus, no licenses; works in any AI coding assistant.

---

## 🖥 Requirements

- An AI assistant that loads `SKILL.md` (opencode, Claude Code, or similar).
- Python 3.10+ with `pandas scipy statsmodels pingouin matplotlib seaborn scikit-learn`.
- RStudio (optional, only for advanced SEM/HLM/meta/IRT).

---

## 🚀 Getting started

1. **Copy the skill** into your assistant's skill folder:
   ```bash
   # opencode (project scope)
   cp SKILL.md references assets .opencode/skills/statistical-analysis/ -r

   # Claude Code (personal)
   cp -r . ~/.claude/skills/statistical-analysis/
   ```
2. **Prepare data** — CSV/Excel with clear headers, or a Word report with tables
   (`.docx` is parsed automatically, never copy-paste).
3. **Ask** — e.g. "compare 2025 vs 2024 by sector", "run regression of Y on X", "validate my scale".
4. **Receive the triplet** — table + figure + paragraph, ready for your paper.

---

## 📋 How it works

```
Upload data → Data profile → Assumption checks → Method selection → Execute → APA table + figure + paragraph
```

| Complexity | Path | Confirmations |
|------------|------|---------------|
| t-test, correlation, reliability, descriptives | Quick | 0 |
| Regression, ANOVA, moderation, mediation, ROC | Light | 1 (variable roles) |
| SEM, HLM, IRT, meta, panel models | Full | stage-by-stage |
| Sample-size planning (no data needed) | Power | 1 (parameters) |

---

## 🛠 What it covers

- **Data profile** — N, types, missingness, skew/kurtosis, Shapiro-Wilk, outliers.
- **Assumption gates** — Levene, VIF, sphericity; auto-fallback (Welch, HC3, Mann-Whitney, Wilcoxon) with a note.
- **Full method range** — t-tests, ANOVA, correlation, regression, moderation,
  mediation (bootstrap), reliability/ICC, ROC, survival, SEM, HLM, meta, IRT.
- **Official-table hygiene** — leaf-level rows only, non-independence caveat,
  nominal-vs-real money rule (`references/official-tables.md`).
- **Power analysis** — required N from effect size, α, power.

---

## 📁 Structure

```
SKILL.md                   # skill (root)
references/
  methods-index.md         # method selection guide
  code-patterns.md         # copy-paste Python patterns
  table-formats.md         # APA table templates
  full-workflow.md         # 4-stage complex-analysis workflow
  official-tables.md       # non-tidy table checklist
assets/report-template.md  # report skeleton (EN + AZ)
```

---

## 🤝 Support

- Open an issue describing your data + method + error text.

## 🔒 Privacy

The skill runs locally in your session. No data is stored or shared beyond it.

## 📄 License

MIT — use, fork, improve; contributions welcome via pull request.
