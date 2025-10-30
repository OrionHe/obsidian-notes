`cv::solvePnP` 是 OpenCV 中一个非常常用的函数，用于**姿态估计 (Pose Estimation)**。它的核心任务是：

👉 已知一组三维点（物体在世界坐标系或物体坐标系中的坐标）和它们在图像上的二维投影点（像素坐标），再加上相机的内参[[Camera]]，求解相机相对于物体的 **旋转矩阵 R** 和 **平移向量 t**。

换句话说：

* 输入：3D 点、对应的 2D 图像点、相机内参（和畸变系数）。
* 输出：相机位姿，即物体坐标系 → 相机坐标系的变换。

---

### 1. 数学模型

相机成像可由 **针孔相机模型**表示：

$$
s \begin{bmatrix} u \\ v \\ 1 \end{bmatrix} = K \cdot \big[ R \ | \ t \big] \cdot \begin{bmatrix} X \\ Y \\ Z \\ 1 \end{bmatrix}
$$

其中：

* $(X, Y, Z)$：物体点在世界/物体坐标系下的坐标。
* $(u, v)$：点在图像平面上的像素坐标。
* $K$：相机内参矩阵（包含焦距 fx, fy 和主点 cx, cy）。
* $R, t$：旋转和平移，描述物体到相机的刚体变换。
* $s$：尺度因子（深度）。

`solvePnP` 就是通过最小化重投影误差来估计 $R, t$：

$$
\text{minimize} \quad \sum_i \| (u_i, v_i) - \pi(K [R|t] X_i) \|^2
$$

其中 $\pi$ 表示投影函数。

---

### 2. 函数接口

在 OpenCV (C++ 版) 中：

```cpp
bool cv::solvePnP(
    InputArray objectPoints,   // 3D点: Nx3 (float/double)
    InputArray imagePoints,    // 2D点: Nx2 (float/double)
    InputArray cameraMatrix,   // 相机内参 3x3
    InputArray distCoeffs,     // 畸变参数
    OutputArray rvec,          // 旋转向量 (Rodrigues形式)
    OutputArray tvec,          // 平移向量
    bool useExtrinsicGuess = false,
    int flags = SOLVEPNP_ITERATIVE
);
```

---

### 3. 常用解法 (`flags`)

OpenCV 提供了多种 PnP 求解算法，可以通过 `flags` 参数选择：

* `SOLVEPNP_ITERATIVE`
  **默认方法**，基于 Levenberg-Marquardt 最小化重投影误差。
  要求至少 6 对点。
* `SOLVEPNP_P3P`
  经典的 P3P 算法，需要 **3 对点**，返回可能的 4 个解。
* `SOLVEPNP_AP3P`
  更稳定的 P3P 改进算法。
* `SOLVEPNP_EPNP`
  高效 PnP，复杂度低，适合点数较多时。至少 4 对点。
* `SOLVEPNP_IPPE` / `SOLVEPNP_IPPE_SQUARE`
  适合平面点集的特殊情况。
* `SOLVEPNP_DLS`
  Direct Least Squares 方法，适合点较少但对精度有要求时。
* `SOLVEPNP_UPNP`
  无需已知相机内参的近似算法（不常用）。

---

### 4. 输出结果

* `rvec`：旋转向量，采用 **Rodrigues** 表示法，可以用 `cv::Rodrigues` 转为 3x3 旋转矩阵。
* `tvec`：平移向量，表示相机在物体坐标系下的位置。

得到 $(R, t)$ 后，可以：

* 构造相机位姿矩阵 $[R|t]$。
* 进一步计算相机坐标系和世界坐标系之间的变换关系。

---

### 5. 典型应用

* **Aruco码/AprilTag** 定位：识别到角点后，利用 solvePnP 估计相机位姿。
* **手眼标定**：相机和机械臂的坐标系转换。
* **3D 物体定位**：已知物体 CAD 模型关键点，通过 solvePnP 确定物体在相机坐标系中的位置。
* **AR 增强现实**：把虚拟物体叠加到现实场景中。

---
