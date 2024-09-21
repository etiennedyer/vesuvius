## Work I've been doing on the [Vesuvius Challenge](https://scrollprize.org/)

I'm developing a sheet stitching algorithm for the [ThaumatoAnakalyptor](https://github.com/schillij95/ThaumatoAnakalyptor) pipeline, working from a Bayesian perspective, where we estimate the probability of a stitch being correct given the stitches in the layers that come before and after it, weighed by how confident we are in those guesses.

The TA pipeline (broadly) works as follows: 
1. Create a point cloud of the entire scroll
2. Use the Mask3D deep learning environment to create sheet segments
3. Stitch the sheet segments together
4. Reconstruct the full papyrus
5. Flatten the papyrus
6. Texture the papyrus
7. Detect the ink

The current bottleneck is 3. They were previously using a Random Walk algorithm to generate potential stitches, but this was very computationally demanding, and are now looking for more efficient solutions. (What they currently have implemented is, I believe, similar to the original algorithm Dr. Seales and his team were using.) They model the scroll as a connected graph (where nodes represent sheet segments), and try locally figuring out the winding angle of the edge connecting each sheet.

My impression is that trying to solve this locally is really difficult. I'm working on another solution, which uses information about the entire length of the scroll to solve connectivity for a given area.

The premises I'm working from are that 1) different parts of a scroll are easily discernable at different points in the scan, and 2) the images change very little from scan-to-scan, so it should be easy to stitch edges together "vertically." The idea is that we might be able to improve our guesses for hard-to-discern parts by comparing them to the same area where it is easy to discern. \
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison1.png" width=400 height=400>
<img src="https://etiennedyer.github.io/assets/vesuvius/comparison2.png" width=400 height=400> \
*The encircled area is squished together and hard to discern in the first image, but clearly separated in the second*

This is the outline of my strategy:
1. Convert each image to a point cloud
2. Use a culstering or segmentation algorithm to delineate high-density areas (I've tried DBSCAN with some success).
3. Trace the outline of the cluster to form a 2D graph for each layer, representing the outline of the scroll. Edges are weighed by confidence in their accuracy
4. Compare each layer to its neighbours and modify the graph such that it resembles neighbours' high-accuracy areas
5. Stitch all layers together vertically to create a 3D mesh

This corresponds to steps 1-3 in the TA pipeline, I'd use all the same steps afterwards, which seem to be working well.
I'm currently on step 2, I'm looking at different segmentation algorithms and trying to figure out which makes the most sense.
For step 3, I'm trying to figure out how to only trace the inside face of the scroll. In terms of connecting the edges, I think a model based on distance vs the angle of the edge makes a lot of sense, where node_i is connected to node_{i+1} based on which node scores the highest on some combination of (distance - angle), prioritizing nodes which are along the expected path of the graph.

I've uploaded my work [here](segment.md). \
<img src="https://etiennedyer.github.io/assets/vesuvius/dbscan.png" width=400 height=400>
