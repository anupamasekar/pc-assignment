# Playing with a scene point cloud

Accompanying notebook has the python code for each task with comments added.
Visualization outputs are not saved in the notebook due to size constraints. The corresponding 2D renders are included below. Run the notebook with `visualize=True` in all functions to turn on interactive visualization.

### Q1
In the given point cloud, there is a shoe kept on the floor. The task is to detect the floor and re-orient the floor (and hence the entire point cloud) on the XZ plane with the centre of the floor lying on the origin i.e. the equation of the plane passing through the floor should be y = 0.

**Solution:**

Raw pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/raw-pcd.png)

1. Since floor is planar, we can identify the floor in the scene by segmenting out the plane(s). Assuming the floor is the largest plane in the scene, we can use RANSAC algorithm to get the equation of this plane and the points in the point cloud that lie on the plane. Open3D provides support for plane segmentation using RANSAC. After this step, we get the plane segmented as below.

Segmented plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/segmented-plane.png)

2. Next we align the plane center with the origin of the coordinate system by translating the point cloud appropriately.
3. Then we get the normal vector of the plane from the plane equation obtained in step 1. Since there are two opposite directions for normal, we pick the one pointing towards center of the scene.
4. Now we compute the rotation matrix to transform the normal vector and align it with positive y axis.
5. By applying this rotation on the scene point cloud, we re-orient it such that the floor is now perpendicular to the y axis as show below. 

 Re-oriented pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/reoriented-pcd.png)

Plane segmentation can fail to detect the floor if there are multiple similar sized planes in the scene, due to walls or other planar objects. In such cases we can iteratively run RANSAC to segment the planes and choose the plane that is most likely to be the floor based on some criterion.

### Q2
Since the 3D representation involved over here is a point cloud, the overall scene looks kind of pointy and perforated. Convert this point cloud to some other representation where the scene looks more continuous with a smooth surface.

**Solution**

1. To represent the scene as a mesh with smooth surfaces we need to do surface reconstruction. Point clouds can be very noisy which could affect the surface reconstruction process and normal computation. So we first clean up the point cloud by removing statistical outliers.

Clean pcd:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/clean-pcd.png)

2. Now we estimate the point normals using points in the local neighbourhood. Open3D provides an implementation for this as well. Then an additional step is performed to orient the normals in the same direction to be consistent.
3. Open3D supports Poisson surface reconstruction, and using this we create a triangle mesh from the cleaned point cloud.

Meshified:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/meshified.png)

Surface reconstruction can be challenging when the point cloud is too noisy or too sparse. Noise filtering can help, and the parameters can be further tuned depending on the point density and amount of noise.

### Q3
Add a unit test case to check and verify your solution. For this, add some random transformations to an input point cloud and then pass it through your algorithm to test it.

**Solution**

1. We start by creating a point cloud on the XZ plane.

XZ plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/init-plane.png)

2. We then apply some random rotation (R1) about X and Z axis, and some random translation (T1).

Random transform:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/random-transform-plane.png)

3. Run the re-orientation algorithm from Q1 to get the R2 and T2 used for re-orienting the point cloud. If the algorithm is correct, then it should reverse the translation and rotation that has been applied in step 1 and return the XZ plane.

Re-oriented plane:

![](https://github.com/anupamasekar/pc-assignment/blob/main/data/reoriented-plane.png)

4. We can verify this by checking that `R1 * R2 = I` and `T1 + T2 = 0`. Alternatively we can also check if the re-oriented plane matches the original XZ plane.

`R1 * R2 = `
```
[[ 1.00000000e+00 -3.97491755e-16 -7.45206385e-17]
 [ 3.63890903e-16  1.00000000e+00 -1.07006905e-16]
 [-4.17507078e-18  2.92120513e-17  1.00000000e+00]]
```
`T1 + T2 = `
```
[ 1.38777878e-16  5.55111512e-16 -1.11022302e-15]
```

Note that in step 2, we cannot apply rotation about Y axis as the algorithm only aligns the floor perpendicular to Y axis but allows any rotation about Y axis.
