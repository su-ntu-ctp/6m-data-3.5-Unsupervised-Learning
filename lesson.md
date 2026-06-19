# Lesson — L05 Unsupervised Learning

> **Chapter 5 of the NorthStar Retail story.** *Sarah Chen · Customer Experience Analyst · February 2023.*
> Marcus signed off on the churn model on Friday, then asked: *"Can you find natural CLUSTERS of customer behaviour — without labels? And while you're at it, give us a watch list of the weird ones."*
> Sarah opens the same CSV she has used for two weeks, but this time she drops the `churned` column. She has Friday to ship segments and a list.

This document is a **short reference** — the lesson itself is taught in the notebooks. Read it for orientation before class, then come back to it for the takeaways, the technique-choice checklist, the review questions, and the course map.

---

## How L05 is taught

| Stage | Where to go |
|---|---|
| **Pre-class** | `pre-class.md` + `notebooks/01_monday_morning.ipynb` |
| **In-class — Part 1: PCA** | `notebooks/02_pca.ipynb` |
| **In-class — Part 2: K-Means** | `notebooks/03_kmeans.ipynb` |
| **In-class — Part 3: Isolation Forest** | `notebooks/04_isolation_forest.ipynb` |
| **Self-study** | `notebooks/assignment.ipynb` + `notebooks/optional_extensions.ipynb` |
| **Reference & review** | This document |

The notebooks are the spine. Run them in order. Come back here for the consolidated takeaways and the review questions.

---

## Overview

For four weeks Sarah has worked with a target column. This week there isn't one — and Marcus's brief is two things at once: *find groups* and *find oddballs*. The week's three Parts are a coherent arc. **PCA** compresses 10+ features into a handful of variance-rich axes — both for the Friday slide and as preprocessing for everything that follows. **K-Means** finds segments she can name, with the elbow plot and silhouette score arguing about K and business judgement picking the winner. **Isolation Forest** flags the ~5% of customers who do not fit any pattern — the watch list for the customer success team. Same dataset, same preprocessing pipeline as L03/L04; what changes is that there is no "right answer" to score against.

---

## Key takeaways

1. **Unsupervised learning is three jobs, not one.** Dimensionality reduction, clustering, and anomaly detection cover roughly 90% of real unsupervised work. Pick the job before you pick the algorithm.
2. **Scale before any distance-based method.** PCA maximises variance and K-Means minimises squared distance — both are scale-sensitive. A feature measured in pounds will dominate a feature measured in proportions unless you standardise first.
3. **PCA reads variance, not meaning.** `explained_variance_ratio_` tells you how much signal each component carries; `components_` tells you which original features drive it. Naming a PC ("engagement", "satisfaction-vs-dissatisfaction") is a human step done by inspecting the loadings.
4. **K-Means will not tell you K — and the elbow plot often won't either.** Compute inertia and silhouette across K = 2…10, but expect smooth curves on real data. The defensible answer combines the metrics with what the business can actually act on.
5. **K-Means assumes spherical, similarly-sized clusters.** If reality is stringy, density-varying, or has one huge group and a few tiny ones, K-Means will force-fit it. A 12-customer cluster next to three 2,500-customer clusters is usually an anomaly cluster, not a segment.
6. **Cluster labels are arbitrary until you profile them.** The output of `fit_predict` is integers. The deliverable is *named segments* — built by grouping by cluster, comparing each cluster's feature means to the global mean, and writing one sentence per cluster.
7. **Isolation Forest's `contamination` only sets the threshold.** The continuous `score_samples()` output is independent of it — you can re-threshold after fitting without re-training. Treat `contamination` as an operational dial, not a model parameter.
8. **An anomaly is a starting point, not a verdict.** The model surfaces unusual feature *combinations*; a human reads the row and decides whether it is fraud, a data error, or just an interesting customer. Always inspect the raw features of the top-scored points before sending the list anywhere.

---

## Choosing an unsupervised technique — a checklist

Before you reach for PCA, K-Means, or Isolation Forest, run the question through this three-step check:

1. **What is the deliverable — a picture, a label per row, or a watch list?** A 2D picture for a stakeholder is PCA. A segment assignment per customer is K-Means (or DBSCAN if the shapes look non-globular). A ranked list of "weird" rows is Isolation Forest. The deliverable picks the algorithm; do not start the other way around.
2. **Is the preprocessing the same one you would use for a supervised model?** Median-impute, standard-scale, one-hot encode — exactly as in L03. The same leakage rule applies: fit your imputer and scaler on training data only, never on the full dataset. In supervised learning a leaky pipeline at least betrays itself through inflated test scores; in unsupervised learning there is no held-out test set to raise that alarm, so a leakage mistake goes completely undetected — which is why the discipline matters *more* here, not less.
3. **Can you defend the choice with both a metric and a sentence?** "K=4 because silhouette peaks there" is half an answer. "K=4 because silhouette and elbow both support K = 3-4, and the marketing team can run campaigns against four segments but not seven" is the answer Marcus actually accepts.

Skip any of these and you will ship a result that is mathematically defensible but operationally useless — or one the stakeholder cannot read.

---

## Check your understanding

Work through these after finishing the three Part notebooks. Attempt each question on your own first.

### Part 1 — PCA

**Q1 — Why scale first?** A colleague runs PCA directly on the raw NorthStar features and finds that PC1 is almost entirely driven by `avg_monthly_spend`. What happened, and what should they do?

> **Sample answer:** PCA maximises variance, and `avg_monthly_spend` (measured in pounds) has a far bigger numeric range than features like `returns_ratio` (0-1) or `support_tickets_quarter` (0-10). Without scaling, its raw variance swamps everything else, so PC1 just tracks spend. Fix: `StandardScaler` before `PCA`. Every feature then contributes on equal footing, and PC1 reflects the strongest *joint* pattern rather than the loudest column.

**Q2 — Reading the scree plot.** The first two PCs together explain 22% of total variance on the NorthStar data; you need 6-8 components to reach 80%. What does this tell you about the data, and what does it imply for the 2D plot you are about to put in front of Marcus?

> **Sample answer:** The features are mostly independent — variance is spread across many directions rather than concentrated in one or two. PCA cannot compress aggressively here. The 2D plot is still useful for visualisation, but you should be honest that it captures only ~22% of the signal; the clustering you do downstream uses all 17 dimensions, not just the two on the screen.

**Q3 — Naming a component.** PC1 has strong positive loadings on `tenure_months`, `total_purchases`, and `avg_monthly_spend`. What is PC1 about, and how would you describe it to a non-technical stakeholder?

> **Sample answer:** PC1 has captured a "customer engagement" or "activity level" axis — customers high on PC1 have stayed longer, bought more, and spent more. To a stakeholder: "PC1 ranks customers from least to most engaged with NorthStar." This is the human naming step: the algorithm gives coefficients; you read them and supply the label.

### Part 2 — K-Means

**Q4 — When the elbow is smooth.** You plot inertia vs K from 2 to 10 and the curve has no obvious bend. Silhouette score peaks weakly at K=2. What do you ship?

> **Sample answer:** A smooth elbow plus a weak silhouette peak means the data does not have strong natural clusters under K-Means' spherical assumption. Two honest options: (a) pick K from business needs — e.g., K=4 because marketing can run four campaigns — and be explicit that you chose for actionability, not statistical separation; (b) try a different algorithm (DBSCAN for density-based, hierarchical for nested structure). Do not pretend the elbow showed something it did not.

**Q5 — A tiny cluster.** K-Means with K=4 returns three clusters of ~2,500 customers and one with 12. What is the most likely interpretation?

> **Sample answer:** The 12-customer cluster is an anomaly group — a handful of customers very different from everyone else, isolated by K-Means because they cannot be assigned to any of the natural three. Two options: report those 12 as a "watch list" alongside the three real segments, or drop them as outliers and re-fit K-Means with K=3. Either way, do not present "four equally important segments" — that misrepresents the result.

**Q6 — Naming the clusters.** After fitting K=4, how do you turn the integer labels into something Marcus can act on?

> **Sample answer:** Group the data by cluster and compute each feature's mean per cluster; compare to the global mean (or standardise to Z-scores for a heatmap). Each cluster has a profile — high on some features, low on others. Write one sentence per cluster naming the segment from that profile: e.g., "Cluster 0: long tenure, high spend, low returns — Loyal Premium." The deliverable is the names plus the profile table, not the integers.

### Part 3 — Isolation Forest

**Q7 — What `contamination` controls.** You fit Isolation Forest with `contamination=0.05`, then decide 5% is too aggressive — you only want to surface ~2%. Do you have to refit?

> **Sample answer:** No. `contamination` only sets the threshold between the +1 and -1 labels; the continuous output from `score_samples()` does not depend on it. Keep the fitted model, take `score_samples`, and pick a stricter cutoff (e.g., the 2nd-percentile score). This is why `contamination` is an operational dial, not a model parameter.

**Q8 — Why a flagged row was flagged.** The top-scored anomaly has tenure = 70 months, 0 purchases this quarter, and 7 support tickets. Is this a real anomaly, and what do you do with it?

> **Sample answer:** Yes — long-tenured customers who suddenly stopped engaging and started complaining are genuinely unusual, and the model has surfaced a feature *combination* no single Z-score would catch. The action is not "label as fraud" — it is "send to customer success for a personal call." The model surfaces; humans investigate. Always report the diverging features alongside the flag so the recipient knows where to start.

**Q9 — Anomalies vs segments.** After fitting both K-Means (K=4) and Isolation Forest, you find anomalies are spread across all four clusters, not concentrated in one. What does this confirm?

> **Sample answer:** Anomaly detection and clustering are answering different questions. Clusters are dense regions; anomalies are sparse edge points — and edge points exist on the boundary of every cluster, not just one. If anomalies were concentrated in a single cluster, that cluster would itself be an "outlier group" and you would re-examine your K. Spread anomalies confirm the two methods are complementary: segments for the marketing team, watch list for customer success.

---

## Where L05 fits in the course

L05 is the first lesson without labels — and the techniques recur whenever the target column is missing, noisy, or arrives late.

| Lesson | How L05 shows up |
|---|---|
| **L06 — Time Series** | Anomaly detection on residuals (forecast minus actual) reuses Isolation Forest. Seasonal decomposition is the time-series analogue of PCA — finding a small number of axes that explain most variance. |
| **L07 — Neural Networks** | Autoencoders generalise PCA to non-linear compression, and a reconstruction error is an anomaly score. The "unlabelled" mindset of L05 is what makes self-supervised pre-training make sense. |
| **L08 — Computer Vision** | Image embeddings live in hundreds of dimensions — PCA (or t-SNE/UMAP) is the standard way to see them. Defect detection on a production line is Isolation Forest with image features. |
| **L09 — NLP & Embeddings** | Document embeddings are clustered with K-Means to discover topics without labelled categories. Near-duplicate detection is anomaly detection in embedding space. |
| **L10 — Transformers & GenAI** | Semantic search over an LLM index is fundamentally a distance computation in a learned space — the same metric intuitions you used for K-Means apply. Quality-checking generations against a reference set uses anomaly scoring. |

---

> *"OK. The segments are clear, the watch list is in the customer success team's inbox. But our sales are seasonal — every December we spike, every February we crash. Can you forecast next quarter's revenue?"* — Marcus, after Sarah's Friday presentation.
>
> That question — *can I predict what comes next when the data has a time order?* — is the engine of **L06 (Time Series Forecasting)**.
