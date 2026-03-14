# Fashion-MNIST k-NN Optimization Analysis
> **DATA 604 Midterm Project** > *A systematic study of hyperparameter sensitivity, indexing bias, and distance metrics.*

---

## 📋 Project Overview
This repository contains a comprehensive analysis of the **k-Nearest Neighbors (k-NN)** algorithm applied to the full **Fashion-MNIST** dataset (70,000 images). The project explores the boundaries of instance-based learning by evaluating how data volume, neighbor selection, and mathematical distance norms impact classification accuracy.



---

## 🔬 Experimental Methodology

The study was divided into three primary research questions:

### 1. Hyperparameter Tuning ($k$ and $N$)
We evaluated the trade-off between the number of training samples ($N$) and the number of neighbors ($k$).
- **Variables:** $N \in \{2000, 4000, 6000\}$ per class; $k \in \{5, 10\}$.
- **Finding:** Accuracy scales logarithmically with $N$. While $k=10$ provides smoother decision boundaries, $k=5$ often captured local class nuances more effectively.



### 2. Indexing and Sampling Bias
We investigated whether the physical storage order of the dataset introduced bias by comparing **Random Indexing** vs. **Sequential Indexing**.
- **Result:** The performance delta was $<0.2\%$, confirming the Fashion-MNIST dataset is naturally well-shuffled and homogeneous.

### 3. Metric Evaluation ($L_1$ vs. $L_2$)
We compared the **Euclidean ($L_2$)** norm against the **Manhattan ($L_1$)** norm.
- **Result:** The Manhattan distance provided a consistent **~1% accuracy boost**.
- **Insight:** $L_1$ is more robust in high-dimensional (784px) spaces as it avoids the "outlier exaggeration" caused by squaring differences in $L_2$.



---

## 📈 Summary of Findings (Synthesis)

### Local vs. Global Stability
While global accuracy reached a peak of **86.13%**, class-specific (local) accuracy remained remarkably polarized:
* **Highly Stable:** *Trousers* (Class 1) and *Bags* (Class 8) maintained $>95\%$ accuracy across all trials.
* **Persistent Ambiguity:** *Shirts* (Class 6) remained locked at $\sim 55\%-60\%$ accuracy.

### The "Visual Structure" Constraint
The performance plateau in the "Shirt" category is not a failure of hyperparameters but a result of the **Visual Structure** of the data. In a 784-dimensional vector space, the silhouettes of Shirts, T-shirts, and Coats overlap significantly. k-NN, being a distance-based classifier, struggles to find a clear manifold when class boundaries are visually blurred.



---

## 🛠️ Tech Stack
* **Language:** Python 3.x
* **Libraries:** `scikit-learn`, `numpy`, `pandas`, `matplotlib`, `seaborn`
* **Dataset:** Fashion-MNIST (Zalando Research)

---

## 🚀 Key Results Table

| Configuration | Metric | N (Total) | k | Global Accuracy |
| :--- | :--- | :--- | :--- | :--- |
| **Baseline** | Euclidean | 20,000 | 5 | 83.85% |
| **Optimized** | **Manhattan** | **60,000** | **5** | **86.13%** |

---
*Developed for DATA 604 - Spring 2026*
