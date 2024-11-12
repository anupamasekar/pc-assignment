# Playing with a scene point cloud

Accompanying notebook has the python code for each task with comments added.
Visualization outputs are not saved in the notebook due to size constraints. The corresponding 2D renders are included below. Run the notebook with `visualize=True` in all functions to turn on interactive visualization.

**Q1:**
In the given point cloud, there is a shoe kept on the floor. The task is to detect the floor and re-orient the floor (and hence the entire point cloud) on the XZ plane with the centre of the floor lying on the origin i.e. the equation of the plane passing through the floor should be y = 0.

**A:** 

1. Plane segmentation using RANSAC to detect the floor
2. Align plane center with origin, by translating the pcd
3. Align the plane normal with y axis, by rotating the pcd

Raw pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/raw-pcd.png)

Segmented plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/segmented-plane.png)

 Re-oriented pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/reoriented-pcd.png)

**Q2:**
Since the 3D representation involved over here is a point cloud, the overall scene looks kind of pointy and perforated. Convert this point cloud to some other representation where the scene looks more continuous with a smooth surface.

**A:**

1. Clean up the point cloud by removing outliers
2. Estimate normals and align them consistently
3. Poisson surface reconstruction to convert the point cloud to mesh representation

Clean pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/clean-pcd.png)

Meshified:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/meshified.png)

**Q3:**
Add a unit test case to check and verify your solution. For this, add some random transformations to an input point cloud and then pass it through your algorithm to test it.

**A:**

1. Create a point cloud on XZ plane
2. Apply rotation about X and Z axis, and some translation (R1, T1)
3. Run the Q1 algorithm to get the R and T used for re-orienting the point cloud (R2, T2)
4. Check R1 * R2 = I
5. Check T1 + T2 = 0
6. Alternatively one can check if the re-oriented plane matches the original XZ plane

XZ plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/init-plane.png)

Random transform:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/random-transform-plane.png)

Re-oriented plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/reoriented-plane.png)

**Additional comments**
1. Plane segmentation can fail to detect the floor if there are multiple similar sized planes in the scene, due to walls or other planar objects
2. In such cases we can iteratively use RANSAC to segment all the planes and using some criterion pick the plane that is likely to be the floor
3. Plane fitting and surface reconstruction can be challenging when the point cloud is too noisy or too sparse
4. Noise fitering and downsampling can help improve robustness to noise
