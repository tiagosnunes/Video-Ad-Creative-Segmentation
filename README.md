# Video Ad Creative Segmentation (K-Means)

Segmenting 513 mobile game video ad creatives into performance-based profiles using K-Means clustering — statistically validated with ANOVA, Kruskal-Wallis, Tukey post-hoc, and Chi-Square tests.

## Business Context

CreativeArc is a video ad production agency for mobile game user-acquisition campaigns (Meta Ads, Google UAC, TikTok Ads, Apple Search Ads). Despite a large historical library of creatives and performance data, the agency had no structured way to identify which creatives were actually working — making it hard to replicate success or catch underperformers early.

**Goal:** segment the creative portfolio by performance profile to guide production decisions, reduce creative waste, and give the team an objective, data-driven benchmark instead of relying on intuition.

*Note: the dataset is simulated for academic purposes. Conclusions demonstrate a valid, reusable methodology — not real operational findings.*

## Data

513 ad creatives, 6 performance metrics selected after correlation analysis (from an original 12):

| Metric | What it measures |
|---|---|
| Hooks Rate | % of users who watch past the first ~3 seconds |
| CTR | Click-through rate |
| CVR | Conversion rate (click → install) |
| IPM | Installs per 1,000 impressions (scale efficiency) |
| Retention Rate | % of users still active after a set period |
| GrossROAS | Revenue generated vs. campaign cost |

`Cost`, `Impressions`, `Clicks`, and `Installs` were dropped (correlation 0.89–1.00 — all measuring campaign budget, not creative quality). `Retention D1` was dropped for redundancy with `Retention Rate`. `eCPI` was dropped for weak correlation with creative performance (driven mostly by external auction/seasonality factors).

## Methodology

1. **Data cleaning** — removed <1% of rows with missing values; IQR filtering (factor 3.0) removed 9 statistically impossible records (e.g. CVR > 100%) while preserving genuine high-performing outliers
2. **Feature selection** — correlation analysis to eliminate redundant variables (see Data section)
3. **Standardization** — `StandardScaler` applied before clustering, since K-Means relies on Euclidean distance
4. **Cluster count** — Elbow Rule + Silhouette Score tested for k = 2–10
5. **K-Means** — final model: k=3, `random_state=42`, `n_init=20`
6. **Statistical validation** — ANOVA, Kruskal-Wallis, Tukey post-hoc (all six clustering variables), plus Chi-Square / Cramér's V against creative *type* — a variable *not* used to build the clusters, making it the most informative generalization check

### Choosing k = 3 over the statistically "best" k = 2

| k | Silhouette | Trade-off |
|---|---|---|
| 2 | 0.379 (highest) | Only separates "good vs. bad" — no middle tier, too simplistic for the business |
| **3** | **0.334** | **Best compromise: adds an interpretable mid-tier segment** |
| 4 | 0.297 | Less interpretable, diminishing returns |

A deliberate trade-off: a lower Silhouette score in exchange for a segmentation the marketing team can actually act on.

## Results — Three Performance Clusters

| Cluster | Size | Hooks Rate | CTR | CVR | IPM | Retention | GrossROAS |
|---|---|---|---|---|---|---|---|
| **Consistent** | 214 (43%) | 48.8 | 16.6 | 25.7 | 42.1 | 37.2 | 61.5 |
| **Low Performers** | 154 (31%) | 29.0 | 6.9 | 20.5 | 13.5 | 33.4 | 24.1 |
| **Premium** | 130 (26%) | 46.7 | 17.7 | 50.3 | 89.6 | 54.6 | 145.1 |

- **Premium (26%)** — best-in-class on every conversion and profitability metric; the benchmark tier for future production
- **Consistent (43%)** — solid, reliable mid-tier: good attention capture, moderate conversion and monetization
- **Low Performers (31%)** — weak across the board; candidates for review or format rework

All six metrics differ significantly across clusters (ANOVA and Kruskal-Wallis, p < 0.001; Tukey confirms significant pairwise differences between every cluster pair).

### The key finding: format doesn't predict performance

Every creative *type* (80% Gameplay, AI Intro, Demo, ASMR, etc.) appears in all three clusters. A Chi-Square test against creative type — the one variable *not* used to build the clusters — found only a **weak association** (χ² = 24.72, p = 0.075, Cramér's V = 0.158).

**Business implication:** creative *format* is not what drives performance — *execution quality* is (hook strength, message clarity, call-to-action). CreativeArc should optimize production around what makes Premium-tier creatives work, not around which format to pick.

## Limitations

- Simulated dataset — findings demonstrate methodology, not real operational conclusions
- ANOVA/Kruskal-Wallis validate the same variables used to build the clusters (partially circular by construction); the Chi-Square test against creative type is the more informative generalization check
- No cluster stability test performed (e.g. varying `random_state`, bootstrap resampling)
- No contextual variables (video duration, game genre, platform, ad spend) — a natural next step

## Tech Stack

- **Data handling:** `pandas`, `numpy`
- **Machine Learning:** `scikit-learn` (`StandardScaler`, `KMeans`, `silhouette_score`)
- **Statistics:** `scipy`, `statsmodels` (ANOVA, Kruskal-Wallis, Tukey HSD, Shapiro-Wilk, Levene, Chi-Square, Cramér's V)
- **Visualization:** `matplotlib`, `seaborn`, `plotly`
- **Environment:** Google Colab / Jupyter Notebook

## Repository Contents

- `Notebook_Colab.ipynb` — full analysis, from data cleaning to statistical validation
- `Relatório.pdf` — full project report with detailed methodology and results

## How to Run

1. Clone this repository
2. Open the notebook in Google Colab or Jupyter
3. Run all cells in order — the dataset is generated/loaded within the notebook

## Context

Individual project developed for the Postgraduate Programme in Analytics for Business, ISCTE Executive Education (2026).

## Author

Tiago Nunes
