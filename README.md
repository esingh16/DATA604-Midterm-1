# Technical Report: Systematic Evaluation of k-Nearest Neighbors on Fashion MNIST

## Section 1: Hyperparameter Influence on kNN Performance

1.1 Introduction
        This section evaluates how the number of neighbors (k) and training samples per class (N) impact the classification of Fashion MNIST images. The study examines k ∈ {1, 2, 5, 10, 15, 20} and N ranging from 1000 to 6000. By isolating these variables, we aim to determine the optimal balance between model complexity and generalization, ensuring that the classifier effectively learns the underlying patterns of apparel categories without overfitting.

1.2 Methodology and Efficiency
        To minimize computational overhead, a "compute-once" strategy was implemented. For each increment of N, the 20-nearest neighbors for the test set were calculated using a single distance matrix. Since neighbors for any k < 20 are a subset of this primary set, all required results were extracted programmatically without redundant calculations. Data was partitioned using stratified random sampling with a fixed seed (42) to ensure balanced class representation and reproducibility across trials.



1.3 Defining Significance
* Practical Significance: Defined as an accuracy change > 1%. Any performance gain below this threshold is considered marginal, as the increased computational cost of larger N or k does not justify the minimal improvement in predictive power.
* Statistical Significance: Observed through the stability of local accuracy across N. Consistent upward trends indicate that the hyperparameters are successfully capturing the true data distribution rather than local noise.

1.4 Discussion of Findings
        Global Accuracy followed a logarithmic trend, characterized by rapid gains as N increased to 3500, followed by a visible plateau. Smaller k values, specifically k=1 and k=5, consistently outperformed k=20. For example, at N=5500, k=5 achieved 86.28% global accuracy, while k=20 reached only 84.75%. This suggests that in high-dimensional pixel space, higher neighbor counts introduce noise from unrelated apparel categories. Locally, Class 1 (Trouser) and Class 7 (Sneaker) showed the highest stability, whereas Class 6 (Shirt) remained the primary challenge, dipping as low as 39.2% local accuracy for certain configurations due to structural overlap with t-shirts and coats.

1.5 Conclusion
        Optimal performance is achieved at k=5 with high N. The diminishing returns observed at N > 4500 suggest the model reaches a saturation point where additional data provides negligible practical significance relative to the time required for processing.

---

## Section 2: The Role of Data Indexing in Model Performance

2.1 Proposed Indexing Method
        To investigate the influence of data organization, we replaced randomized stratified sampling with a "Deterministic Sequential Selection" method. In this approach, we do not shuffle the pool of 70,000 images. Instead, for each of the 10 classes, we select the first N samples exactly as they appear in the raw dataset for training. This evaluates whether the algorithm’s performance is sensitive to the original order of the data, which could contain hidden biases or temporal clusters from the collection process.

2.2 Results and Observations
        Comparison between the randomized and sequential results reveals that Global Accuracy remained nearly identical. For the specific case of N=1000 and k=1, the sequential indexing yielded an accuracy of 81.55%, compared to 81.39% in the randomized trial. The difference of 0.16% falls significantly below our 1% threshold for practical significance, suggesting that the original data ingestion order does not contain significant localized bias.

2.3 Discussion of Outcomes
        The similarity between the two methods suggests that the Fashion MNIST dataset is highly homogeneous and well-mixed. Because the results did not diverge, we conclude that the pixel intensity vectors for each apparel category are consistent throughout the dataset. Any segment of size N appears sufficiently representative of the class variance, suggesting high reliability for the kNN algorithm regardless of whether the user implements a shuffle.

2.4 Conclusion
        While both methods produced comparable outcomes, the randomized split remains the "best" practice as it provides a theoretical safeguard against potential "bursts" of biased data in other datasets. Consequently, we utilize the randomized stratified indexing method for all subsequent evaluations.

---

## Section 3: Impact of the Distance Metric (L1 vs. L2 Norms)

3.1 Methodology
        This section examines the role of the distance metric by repeating the grid search using the L1 norm (Manhattan distance). The goal is to determine if calculating the absolute sum of pixel differences provides a more robust similarity measure than the straight-line Euclidean distance (L2). Given the 784-dimensional nature of the images, the choice of metric is critical for defining the "neighborhood" of each test sample.



3.2 Comparative Analysis of Results
        The L1 norm demonstrated a consistent improvement over the L2 norm across all configurations. For the optimal configuration of N=5500 and k=5, the L1 norm achieved a Global Accuracy of 86.79%, surpassing the 86.28% achieved by the L2 norm. Furthermore, the L1 metric helped stabilize the "difficult" Shirt category (Class 6), which rose to approximately 62.4% at N=5500, k=5, compared to 60.5% with the L2 metric.

3.3 Discussion
        The superior performance of the L1 norm is expected in high-dimensional feature spaces. In L2 distance, differences in pixel intensities are squared, which can disproportionately skew the distance if a few pixels vary wildly (outliers). By using the absolute sum (L1), the metric treats each pixel variation with linear importance, leading to a more stable representation of similarity for apparel images where small texture variations should not overwhelm the classification decision.

3.4 Conclusion
        Transitioning to the L1 norm provides a statistically significant improvement. When paired with high training density (N=6000) and k=5, the Manhattan distance represents the most effective configuration for the k-nearest neighbor algorithm on this dataset.

---

## Section 4: Comprehensive Project Summary and Conclusions

4.1 Synthesis of Experimental Results
        This study conducted a multi-dimensional evaluation of kNN on the Fashion MNIST dataset. By varying N, k, the selection method, and the distance metric, we identified the specific factors that govern classification success. Training set density (N) emerged as the most critical driver of accuracy, with Global Accuracy rising from 81% to over 86% as N increased.

4.2 Hyperparameter and Metric Optimality
        The L1 norm (Manhattan distance) proved superior to the L2 norm. The peak accuracy of 86.79% at N=5500, k=5 proves that linear differences are more effective for pixel-based comparison than squared differences. Regarding neighbor counts, k=5 provided the best balance; k=1 was too sensitive to noise, while k=20 was too broad to capture specific class boundaries.



4.3 Robustness and Limitations
        The dataset's homogeneity was confirmed by the sequential indexing test. However, a major limitation remains: the algorithm’s struggle with Class 6 (Shirt). Even with optimal settings, this class rarely exceeds 65% accuracy. This highlights a fundamental weakness of kNN when using raw pixels: it cannot distinguish between objects with similar outlines but different structural details.

4.4 Final Conclusion
        The "Gold Standard" for this project is N=6000, k=5, using the L1 distance metric. This configuration provides a robust baseline for apparel classification, balancing practical computational costs with high statistical stability.

---

## Section 5: References

1. Zalando Research. Fashion-MNIST Dataset. Kaggle. Available at: https://www.kaggle.com/datasets/zalando-research/fashionmnist
2. Wikipedia. K-nearest neighbors algorithm. Available at: https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm
3. GeeksforGeeks. K-Nearest Neighbours in Machine Learning. Available at: https://www.geeksforgeeks.org/k-nearest-neighbours/
4. AWS SageMaker. K-Nearest Neighbors (k-NN) Algorithm. Available at: https://docs.aws.amazon.com/sagemaker/latest/dg/k-nearest-neighbors.html
5. Analytics Vidhya. Introduction to k-Nearest Neighbors. Available at: https://www.analyticsvidhya.com/blog/2018/03/introduction-k-neighbours-algorithm-clustering/

---

## Data Availability Statement
The complete numerical datasets and performance metrics for all experimental trials are hosted at: https://github.com/esingh16/DATA604-Midterm-1
