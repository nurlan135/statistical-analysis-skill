# Analysis Report Template

Fill top to bottom. Every analysis section ships the triplet: table + figure + paragraph.
Delete sections that do not apply. Azerbaijani version follows the same skeleton —
see §7.

## 1. Data Profile

| Metric | Value |
|--------|-------|
| Source | [file + sheet/table] |
| Sample size | N = [valid / original] |
| Variables | [total (continuous: X, categorical: Y)] |
| Missing | [overall %; highest variable + treatment] |
| Normality | [pass / variable + fallback used] |

> Assumption notes (e.g. "Variable X failed Shapiro p = .003 → Mann-Whitney U").

## 2. Descriptives

[Table 1: continuous — M, SD, skew, kurt]
[Table 2: categorical — n, %]

One-sentence read: [e.g. "Scores centered near midpoint with mild right skew."]

## 3. [Analysis: e.g. Group Comparison]

### 3.1 Method

[Test + why: assumptions met/failed, fallback, corrections (Holm), SE type (HC3).]

### 3.2 Results

[Table: estimates, 95% CI, p, p_adj, effect size]
[Figure: method-matched plot, dpi=300]

### 3.3 Result paragraph (paste-ready)

[2–5 sentences: test, numbers (M/SD or B/CI), p, effect size, plain-language verdict.]

## 4. [Next Analysis]

[Same 3.1–3.3 skeleton.]

## 5. Sector Tables (if official statistics)

[Table 8 format: leaf sectors only. Mandatory note: totals/parents excluded,
rows non-independent → p descriptive; values nominal unless deflated.]

## 6. Methods Note

- Software: Python [libs] [+ R via Docker if used]
- α = .05, two-sided; Holm adjustment within each family
- Effect sizes with 95% CI throughout; small-N results marked exploratory

### Symbols

M, SD, SE, CI, B, β, R², ΔR², d, r, HR, AUC, α — define any non-obvious one.

---

## 7. Azərbaycan şablonu (eyni skelet)

### 7.1 Məlumat profili

| Göstərici | Dəyər |
|-----------|-------|
| Mənbə | [fayl + cədvəl] |
| Həcm | N = [yararlı / ilkin] |
| Dəyişənlər | [cəmi (kəmiyyət: X, kateqorik: Y)] |
| Çatışmayan | [ümumi %; ən yüksək dəyişən + üsul] |
| Normallıq | [keçdi / dəyişən + alternativ test] |

### 7.2 Nəticə paraqrafı (məqaləyə hazır)

[2–5 cümlə: test, rəqəmlər, p, effekt ölçüsü, sadə dildə hökm.]

---

*Report generated: [date] · Skill: statistical-analysis-skill*
