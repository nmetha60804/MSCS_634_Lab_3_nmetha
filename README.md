# MSCS 634 Lab 3: K-Means and K-Medoids Clustering

This lab explores clustering techniques using the Wine dataset from `sklearn`. It applies K-Means and K-Medoids with three clusters, evaluates the results using the Silhouette Score and Adjusted Rand Index (ARI), and visualizes the clusters with a two-dimensional PCA projection.

## Run the notebook

Open `nmetha_lab3.ipynb` in VS Code and select a Python environment containing:

```bash
python -m pip install numpy pandas matplotlib scikit-learn ipykernel nbformat nbclient
```

Run all cells from top to bottom. The notebook reports the Silhouette Score and Adjusted Rand Index, displays PCA-based side-by-side cluster plots, and prints a results comparison.

## Reproducibility

The notebook uses `random_state=42`, three clusters, standardized features, and a deterministic K-Medoids implementation included in the notebook.

## Analysis and Conclusion

### Dataset observations

The Wine dataset had 178 samples and 13 features. It contains three classes with the following distribution:

| Class | Count |
|---:|---:|
| 0 | 59 |
| 1 | 71 |
| 2 | 48 |

There were no missing values or exact duplicate feature rows, so no imputation or duplicate removal was needed. I used the 1.5 x IQR rule to screen for potential outliers. It flagged 17 rows, with flags in malic acid (3), ash (3), alkalinity of ash (4), magnesium (4), proanthocyanins (2), color intensity (4), and hue (1). I retained these rows because an IQR flag is a screening signal, not evidence that a measurement is invalid; removing valid observations could distort the clusters. The features have very different ranges. For example, `hue` has values below 2, while `proline` ranges from 278 to 1680. Because of this, I used z-score standardization before clustering. After scaling, the feature means were approximately 0 and the standard deviations were approximately 1.0028. The small difference from 1 is because pandas displays the sample standard deviation, while `StandardScaler` uses the population standard deviation.

### Clustering results

| Algorithm | Silhouette Score | Adjusted Rand Index |
|---|---:|---:|
| K-Means | 0.2849 | 0.8975 |
| K-Medoids | 0.2676 | 0.7411 |

K-Means produced the higher Silhouette Score, so its clusters were slightly better-defined and more separated. Its ARI was also higher, which means its clusters matched the actual wine classes more closely. Based on both metrics, K-Means performed better for this dataset.

The K-Medoids results were still meaningful, but the scores were lower. The three medoids selected were observations with indices 35, 106, and 148. Since medoids are actual rows from the dataset, they can be interpreted as representative wine samples rather than calculated average points.

K-Means represents each cluster with an arithmetic mean, so it is efficient and works well for compact, roughly spherical clusters. K-Medoids represents each cluster with an actual observation and is generally more robust to outliers, although it can be more computationally expensive.

The PCA figure is only a two-dimensional view of the standardized 13-feature dataset. The Silhouette Score and ARI were calculated using all 13 standardized features, so I gave the metric results more weight than the visual appearance of the plots. In the projection, both methods show broad groups in the upper-left, lower-center, and right-hand regions. Their assignments differ around the group boundaries. Cluster numbers and colors are assigned independently by each algorithm, so matching colors across the panels do not imply matching cluster labels. The centroid and medoid markers show each method's representative points in this shared projection.

## Challenges and Decisions

- The value `random_state=42` was used to make the random initialization reproducible. The value is arbitrary but ensures that the results can be repeated.
- K-Medoids was implemented directly in the notebook using pairwise Euclidean distances. This avoids requiring the additional `scikit-learn-extra` package while showing how medoids are selected from actual observations.

## Troubleshooting

- **The notebook would not run:** I checked that VS Code was using the same Python environment where the packages were installed by selecting the correct kernel.
- **The clustering results changed between runs:** I kept `random_state=42` so the initial cluster representatives were selected reproducibly.


Overall, this lab helped me understand that the best clustering method depends on the data and the evaluation metric. For this Wine dataset, K-Means was the better choice because it had both the higher Silhouette Score and the higher ARI. K-Medoids would still be a useful choice when robustness to outliers and actual representative observations are more important.
