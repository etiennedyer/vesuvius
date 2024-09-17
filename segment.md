```python
import numpy as np
from scipy.spatial import cKDTree
from skimage import io
import networkx as nx
from itertools import combinations
import matplotlib.pyplot as plt
from PIL import Image
from sklearn.cluster import DBSCAN
import os

def generate_point_cloud(tif_file, threshold_percentage, sample_rate):
    with Image.open(tif_file) as img:
        image_array = np.array(img)

    # Calculate threshold based on the maximum value in the image
    max_value = np.max(image_array)
    threshold = int(max_value * threshold_percentage)

    # Get coordinates of points above the threshold
    y, x = np.where(image_array > threshold)
    
    # Sample points to reduce dataset size if needed
    points = np.column_stack((x[::sample_rate], y[::sample_rate]))

    # Visualize the point cloud
    plt.figure(figsize=(30, 30), dpi=300)
    plt.scatter(points[:, 0], points[:, 1], s=0.1, color='grey', alpha=0.5)
    plt.title(f"High-Def Point Cloud for {os.path.basename(tif_file)}\n"
              f"Threshold: {threshold} ({threshold_percentage:.1%}), Points: {len(points)}")
    plt.gca().invert_yaxis()
    plt.axis('equal')
    plt.show()

    return points

file_path = 'data/vesuvius_segment/10022.tif'
points = generate_point_cloud(file_path, 0.55, 1)

```


    
![png](segment_files/segment_0_0.png)
    



```python
from sklearn import metrics
from sklearn.cluster import DBSCAN

db = DBSCAN(eps=6, min_samples=10).fit(points)
labels = db.labels_

# Number of clusters in labels, ignoring noise if present.
n_clusters_ = len(set(labels)) - (1 if -1 in labels else 0)
n_noise_ = list(labels).count(-1)

print("Estimated number of clusters: %d" % n_clusters_)
print("Estimated number of noise points: %d" % n_noise_)

unique_labels = set(labels)
core_samples_mask = np.zeros_like(labels, dtype=bool)
core_samples_mask[db.core_sample_indices_] = True

plt.figure(figsize=(20, 20), dpi=300)
colors = [plt.cm.Spectral(each) for each in np.linspace(0, 1, len(unique_labels))]
for k, col in zip(unique_labels, colors):
    if k == -1:
        # Black used for noise.
        col = [0, 0, 0, 1]

    class_member_mask = labels == k

    xy = points[class_member_mask & core_samples_mask]
    plt.plot(
        xy[:, 0],
        xy[:, 1],
        "o",
        markerfacecolor=tuple(col),
        markeredgecolor="none",
        markersize=1,
    )

    xy = points[class_member_mask & ~core_samples_mask]
    plt.plot(
        xy[:, 0],
        xy[:, 1],
        "o",
        markerfacecolor=tuple(col),
        markeredgecolor="none",
        markersize=1,
    )

plt.title(f"Estimated number of clusters: {n_clusters_}")
```

    Estimated number of clusters: 26547
    Estimated number of noise points: 174230





    Text(0.5, 1.0, 'Estimated number of clusters: 26547')




    
![png](segment_files/segment_1_2.png)
    

