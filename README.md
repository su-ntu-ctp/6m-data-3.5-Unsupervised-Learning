# L05 — Unsupervised Learning

> *Sarah Chen's fifth week at NorthStar Retail. Marcus's question from L04: "Can we find natural clusters of customer behaviour WITHOUT labels?" This week she does.*
> By the end of this lesson you will know how to reduce high-dimensional data with PCA, group customers into segments with K-Means, and flag unusual customers with Isolation Forest — three techniques that cover 90% of unsupervised ML in industry.

---

## Before you start — environment setup

> **If this is your first time with this course, follow the [Setup Guide →](./setup.md)**.
>
> **Already set up from L01–L04?** Your `dsai-m3` environment has everything (`scikit-learn` includes PCA, KMeans, and IsolationForest).

---

## Where L05 fits

| Lesson | What you build | What you carry forward |
|---|---|---|
| **L03 — Supervised Foundations** | Logistic regression on labelled churn data | The preprocessing pipeline |
| **L04 — Trees & Ensembles** | Random Forest + Gradient Boosting | The Pipeline pattern extended |
| **L05 — Unsupervised Learning** *(you are here)* | PCA + K-Means + Isolation Forest — no labels needed | The toolkit for segmentation and anomaly detection |
| **L06 — Time Series** | Forecasting customer demand | The sequential-data lens |

**The narrative thread:** in L01–L04, Sarah always had a target column (`churned`). This week, she doesn't — and that's the point. She has to find STRUCTURE in the customer data without knowing what "right" looks like in advance.

---

## What you will be able to do by the end

- **Reduce** a high-dimensional dataset to 2 components with PCA — for visualisation AND as a preprocessing step
- **Cluster** customers into natural segments with K-Means, including a defensible choice for K using the elbow method and silhouette score
- **Detect** anomalous customers with Isolation Forest — the standard tool for fraud and outlier detection
- **Interpret** an unsupervised result and translate it into a stakeholder-readable summary ("we have four segments: high-value loyalists, price-sensitive new customers, …")
- **Recognise** when unsupervised methods are the right tool — and when they aren't

---

## How class time is structured (~3 hrs)

| Phase | Time | Format |
|---|---|---|
| Concepts + coding walkthrough | ~90 min | Instructor walks through the [**interactive key-concepts page →**](https://flexfengfeng.github.io/dsai-m3-l05-learner/) (PCA · K-Means · Isolation Forest) |
| Hands-on code-alongs | ~90 min | Three notebooks (~25–30 min each) — Core sections only |
| (Self-study after class) | self-paced | Each notebook has a 🟡 Extension section + the assignment |

---

## Your learning path

### Phase 1 — Before class: self-study (~30 min)

**Start here →** [**pre-class.md**](./pre-class.md)

- Run `notebooks/01_monday_morning.ipynb` (~15 min) — Marcus's brief, exploring the data without labels
- Watch two short videos (StatQuest on PCA + K-Means)
- Try three mini-exercises with sample answers

---

### Phase 2 — In class: hands-on (~3 hrs)

**Interactive walkthrough →** [**Key concepts page**](https://flexfengfeng.github.io/dsai-m3-l05-learner/) — the same visualisations the instructor uses in class (PCA · K-Means · Isolation Forest).

**Short reference & review →** [**lesson.md**](./lesson.md) (overview, key takeaways, technique-choice checklist, 9-question review, L06→L10 course map)

**Notebooks — run in order:**

| # | Notebook | Sarah's day | What you explore |
|---|---|---|---|
| 02 | [`02_pca.ipynb`](./notebooks/02_pca.ipynb) | Tuesday | PCA mechanics · 2D visualisation · variance-explained |
| 03 | [`03_kmeans.ipynb`](./notebooks/03_kmeans.ipynb) | Wednesday | K-Means · choosing K (elbow, silhouette) · interpreting clusters |
| 04 | [`04_isolation_forest.ipynb`](./notebooks/04_isolation_forest.ipynb) | Thursday | Anomaly detection · score interpretation · setting the contamination rate |

---

### Phase 3 — After class: assignment (self-paced)

**Assignment →** [`notebooks/assignment.ipynb`](./notebooks/assignment.ipynb)

Two parts: (A) e-commerce customer segmentation, (B) credit-card transaction anomaly detection.

**Further reading →** [**reference.md**](./reference.md)

---

## Core vs Optional — what this lesson teaches

**🟢 Core (taught in class):**
- PCA — dimensionality reduction + 2D visualisation
- K-Means clustering — segmentation + how to choose K
- Isolation Forest — anomaly detection

**🟡 Optional (self-study, not assessed):**
- Hierarchical clustering + dendrograms
- DBSCAN — density-based clustering
- t-SNE and UMAP — non-linear visualisation
- Z-score / IQR outlier math (also covered briefly in L02)

Optional material lives in [`notebooks/optional_extensions.ipynb`](./notebooks/optional_extensions.ipynb).

---

## File map

```
README.md                              ← You are here
setup.md                               ← One-time environment setup
pre-class.md                           ← Phase 1: 75-min self-study guide
lesson.md                              ← Short reference: overview, takeaways, technique-choice checklist, review Q&A, course map
reference.md                           ← Phase 3: Further reading + glossary
environment.yml                        ← Conda environment spec
docs/
  index.html                           ← Interactive key-concepts page (GitHub Pages)
notebooks/
  data/
    northstar_customers.csv            ← Same customers as L03/L04, label dropped
  01_monday_morning.ipynb              ← Pre-class hook: explore without labels
  02_pca.ipynb                         ← Part 1: PCA (Tuesday)
  03_kmeans.ipynb                      ← Part 2: K-Means (Wednesday)
  04_isolation_forest.ipynb            ← Part 3: Isolation Forest (Thursday)
  assignment.ipynb                     ← After class: segmentation + fraud
  optional_extensions.ipynb            ← 🟡 Hierarchical · DBSCAN · t-SNE · UMAP
```
