Keyframe-based visual–inertial odometry using nonlinear optimization
https://www.doc.ic.ac.uk/~sleutene/publications/ijrr2014_revision_1.pdf

[TOC]

OKVIS 最核心的特点，是以丢失部分 landmark 观测信息 为代价，最大限度保持了信息矩阵的稀疏性。
下面简述其 前端 和 边缘化过程

## 前端

### 特征点

**特征点检测**： a customized multi-scale SSE-optimized **Harris corner** detector followed by **BRISK descriptor** extraction.
**描述子方向**： BRISK would allow automatic orientation detection—**however**, better matching results are obtained by extracting descriptors oriented **along the gravity** direction that is projected into the image.
**特征点在图片中的分布**： The detection scheme **favors a uniform keypoint distribution** in the image by gradually **suppressing corners with weaker corner response close to a stronger corner**. 

### Feature tracking

**stage 1, 3D-2D matching step**: Given the current pose prediction (IMU积分获得), all landmarks that should be visible are consideredfor brute-force descriptor matching. 
**stage 2, outlier rejection** (consists of two steps): 
- use the uncertain pose predictions in order to perform a Mahalanobis test in image coordinates: 利用 IMU积分获得的、带协方差的位姿，可以预测 landmark 投影到图像中的位置和协方差，实际特征点的位置与此预测位置之差对应的马氏距离可用于剔除一部分外点；
- Second, an absolute pose RANSAC provided in OpenGV (Kneip and Furgale, 2014) is applied

**stage 3, 2D-2D matching** (to associate keypoints without 3D landmark correspondences)
- **brute-force matching** first
- **triangulation** to initialize landmark positions.  triangulation **across stereo image pairs** and **between the current and any previous frame**. Only triangulations with sufficiently low depth uncertainty are labeled to be initialized—the rest will be treated as 2D measurements in subsequent matching.
- Finally, a **relative RANSAC** step (Kneip and Furgale, 2014) is performed **between the current frame and the newest keyframe**. The respective pose
guess is furthermore used for bootstrapping in the very beginning.

### 关键帧筛选
the frame is inserted as keyframe if:
- the hull of projected and matched landmarks covers less than some percentage of the image (we use around 50%)
- or the ratio of matched versus detected keypoints is small (below around 20%)

### 滑窗组成

可概括为 temporal window  + old keyframes window, 即
a temporal window of the $S$ most recent frames including the current frame + a number of $M$ keyframes that may have been taken far in the past. 

temporal window 中的帧也可能包含了一些新产生的关键帧，这些新关键帧还暂未被滑入old keyframes window。（比如 Fig. 7中的 $x_T^5$）

## 边缘化，稀疏性的保持

OKVIS 的边缘花费了很多心思在保持信息矩阵的稀疏性上。

每时刻的状态量被划分为两部分，pose 和 Speed/bias； pose 部分与视觉 landmark 存在因子连接，Speed/bias 只在相邻时刻的状态量间存在因子连接；

- （Fig. 7）OKVIS 会首先 marginalize 掉 所有 $M$个 keyframes  的 Speed/bias，以降低优化变量的维数。这一部会在 所有 keyframe 对应的 **pose 变量** 之间引入 fill-in；但 old keyframes window 中 keyframe 的数量 $M$ 不大，这些 fill-in 并无多大影响；

- （Fig. 8）当一帧新 frame $x^c_T$ (current frame, index c) 到来时，如果 temporal window 中最老的一帧 $x^{c−S}_T$ 不是关键帧，则丢掉这一帧的所有 landmark 观测（这里发生了信息丢失，但避免了对 landmark 做边缘化而引入的大量 fill-in），再把该帧的所有状态量（包括 pose 和 Speed/bias）边缘化掉；

- （Fig. 9）当一帧新 frame $x^c_T$ (current frame, index c) 到来时，如果 temporal window 中最老的一帧 $x^{c−S}_T$ 是关键帧，则需要把这个新关键帧移入到 old keyframes window 中；但在移入之前，为了保持 old keyframes window  的 size 不变，还需要移除最老的关键帧和一些 landmark：
    -  边缘化掉 能被最老的关键帧观测到、但不能被即将移入的新关键帧和 temporal window 观测到的 landmark，这一步可能会在 所有 老关键帧 之间 fill-in，但这没关系，因为这些老关键帧 之间在此前已经 fill-in了。
    - 丢掉 最老关键帧中的剩余 landmark 观测（能被 temporal window 观测到的 landmark，还不能被边缘化，以免引入old keyframe 与 temporal frame之间的fill-in），这一步也会带来些信息丢失
    -  边缘化掉最老关键帧对应的所有变量，包括 pose 和 Speed/bias
    - 将新关键帧移入 old keyframes window  并边缘化掉 其 Speed/bias： old keyframes window 中的关键帧都只保留 pose 变量

OKVIS 中，以丢失部分 landmark观测信息 为代价，最大限度保持了信息矩阵的稀疏性。

### First Estimate Jacobian

此外，OKVIS 在边缘化过程中，还对剩余变量（与被边缘化变量存在因子连接的变量）进行了 FEJ 处理，即固定其在后续优化中的线性化点，以 minimizing erroneous accumulation of information. 

注意，如果相机与 IMU 的外参也包含在优化变量中，由于所有的 landmark 观测因子都会连接到这个外参变量上，所以，当第一次发生 landmark 被边缘化时，外参变量就被 固定线性化点了。
