## Work I've been doing on the [Vesuvius Challenge]()

I'm developping a sheet stitching algorithm similar to [ThaumatoAnakalyptor](https://github.com/schillij95/ThaumatoAnakalyptor), but working from a Bayesian perspective, where we estimate the probability of a stitch being correct given the stitches in the layers that come before and after it, weighed by how confident we are in those guesses.

The premises I'm working from are that 1) different parts of a scroll are easily discernable at different points in the scan, and 2) the images change very little from scan-to-scan, so it should be easy to stitch edges together "vertically." The big idea is that we might be able to improve our guesses for hard-to-discern parts by comparing them to the same area where it is easy to discern. \
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison1.png" width=400 height=400>
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison2.png" width=400 height=400>
/
Broadly, these are the steps:
1. [Convert the scan to a point cloud.](pointcloud.md)
2. Convert this point cloud to a graph, where every edge recieves a probability value. At this point, every layer of the scan has a suggested winding path.
3. 

