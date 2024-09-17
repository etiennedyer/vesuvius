## Work I've been doing on the [Vesuvius Challenge]()

I'm developping a sheet stitching algorithm similar to [ThaumatoAnakalyptor](https://github.com/schillij95/ThaumatoAnakalyptor), but working from a Bayesian perspective, where we estimate the probability of a stitch being correct given the stitches in the layers that come before and after it, weighed by how confident we are in those guesses.

The premises I'm working from are that 1) different parts of a scroll are easily discernable at different points in the scan, and 2) the images change very little from scan-to-scan, so it should be easy to stitch edges together "vertically." The big idea is that we might be able to improve our guesses for hard-to-discern parts by comparing them to the same area where it is easy to discern. \
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison1.png" width=400 height=400>
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison2.png" width=400 height=400> \
*The encircled area is squised together and hard to discern in the first image, but clearly separated in the second*

Broadly, these are the steps:
1. Convert each image to a point cloud
2. DBSCAN each point cloud into several thousand clusters, each cluster weighed by estimated accuracy
3. Compare each layer to its neighbours and modify clusters such that they resemble neighbours' high-accuracy areas
4. Stitch all layers together vertically to create a 3D mesh
5. Surface reconstruction (??)
6. Ink detection (!?)

I'm currently on step 2, I have the points split into clusters but I have to figure out how to weigh them.
I've uploaded my work [here](segment.md).
<img src="https://etiennedyer.github.io/assets/vesuvius/dbscan.png" width=400 height=400>
