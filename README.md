# The AI Disconnect: Hype vs. Investment Across 12 Economies

A data science project testing whether public AI hype (search interest) predicts where real AI capital goes — and whether five years closes any gap between them. Every figure traces to a named public source; where the data ran out, countries were dropped rather than estimated.

**Headline finding:** the US posts one of the lowest AI search-interest scores in the set and by far the highest investment intensity. China and India post the two highest hype scores and sit mid-pack on capital. A fitted trend (not an assumed one) puts the hype-vs-investment rank correlation at the same level today (ρ=+0.45) as in a 2030 extrapolation — the relationship neither tightens nor breaks down over five years.

📄 **[Read the full write-up with all 6 charts →](BLOG.md)**

## Data sources    


| Metric | Source | Vintage |
|---|---|---|
| GDP (current US$) | [World Bank](https://data.worldbank.org/indicator/NY.GDP.MKTP.CD) — `NY.GDP.MKTP.CD` | 2024 |
| Population, total | [World Bank](https://data.worldbank.org/indicator/SP.POP.TOTL) — `SP.POP.TOTL` | 2024 |
| AI public interest | Google Trends — "ChatGPT" search interest by country | trailing 12mo |
| Private AI investment | [Stanford HAI AI Index 2025](https://hai.stanford.edu/assets/files/hai_ai-index-report-2025_chapter4_final.pdf) Fig 4.3.8/4.3.9, [2026](https://hai.stanford.edu/assets/files/ai_index_report_2026_chapter_4_economy.pdf) Fig 4.2.8/4.2.12 | 2022–2025 |
| AI Preparedness Index | IMF AIPI Dashboard, 174 economies | 2023 |
| China AI industry size (context only) | CAICT/MIIT via China Daily/Xinhua | 2025 |

Investment figures are read from the primary chapter PDFs, not summaries. The two Stanford HAI vintages cross-validate: for every country, (cumulative 2013–2025 − cumulative 2013–2024) matches the separately reported 2025 single-year figure to within rounding.

## What this measures — and what it doesn't

Private AI investment is **capital committed**, not revenue and not market size. No public country-level index for AI revenue exists; this project doesn't claim one. It shows where money is going, not where value has been captured.

**12 countries, not 15.** Brazil and Spain appear in no investment table in either Stanford HAI report; the Netherlands has a 2024 figure but never appears in either cumulative table. Rather than estimate them, they're dropped. Of the 12, **9** have a matched 2024/2025 pair and carry into the forecast; the US and China additionally have a 2022 pre-boom figure.

## Method

- **Clustering** — `StandardScaler` → `KMeans` (k=3), grouping by hype/investment behaviour. Benchmarked in the notebook appendix against RobustScaler, PowerTransformer, and QuantileTransformer — StandardScaler held up against every alternative (RobustScaler returns identical cluster membership; StandardScaler scores highest on silhouette).
- **Forecast** — projects the **annual flow** forward and accumulates onto the stock (a cumulative total can only rise by new capital added each year — applying a growth rate to the stock directly overstates it by 3×+).
- **Decay** — each country's real 2024→2025 growth fades exponentially toward Stanford HAI's stated long-run trend (~29.2%/yr, from "13x growth since 2014").
- **Decay constant** — `k` is *ordered* by IMF AI preparedness rather than hand-picked; bounds remain a modelling choice, ordering is sourced. This is parameterisation, not validation — it becomes testable once more annual flow data is published.
- **Cross-check** — a second, independent log-linear regression fit directly to real annual points (not the decay model) is used to test whether hype-vs-investment correlation actually changes over the forecast window. It doesn't (ρ stays +0.45).

## Key findings

- **Percentage growth flatters small bases.** India's +253% in 2025 is real and the fastest in the set — and amounts to $2.9B of new capital, less than China's $3.1B at +34%, and 1.6% of what the US added.
- **China is compounding, not surging.** Its 2025 growth sat 4.3pp above trend, so its projected path is near-identical under every decay assumption (1.09× spread).
- **India is the open question.** Its growth sat 223pp above trend, so the decay assumption dominates — a 25.5× spread in the 2030 projection ($17B–$435B). Whether that jump was structural (multi-year commitments already booked) or one-off deal timing is the largest unresolved variable here — a question about intent and capacity that no decay model settles alone.

## Repo structure

```
Hype_vs_Value_AI_Investment.ipynb   # full analysis, executed with outputs
BLOG.md                             # long-form write-up with all 6 charts
charts/                             # the 6 charts, standalone PNGs
requirements.txt
```

## Caveats

Twelve countries is a small, self-selected sample. Investment ≠ revenue ≠ value captured. AIPI is 2023 vintage against 2024/2025 investment data — fine for structural readiness, which moves slowly, but not concurrent. The $435B India scenario is the arithmetic ceiling of "253% never fades," not a forecast. Compute capacity, energy, chip supply, and regulation all shape real-world AI capability and are not modeled here — a natural next layer, to be built with the same sourcing discipline as everything above.

---
*Raj Majumder*
