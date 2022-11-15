
# COLMAP-SfM: Structure-from-Motion Revisited


-----

2 Review of Structure-from-Motion

Incremental SfM is a sequential processing pipeline with an iterative reconstruction component (Fig. 2). It commonly starts with feature extraction and matching, followed by geometric verification. 

**scene graph**： 包含了所有通过几何验证的图像匹配对。

The resulting **scene graph** serves as the foundation for the reconstruction stage, which seeds the model with a carefully selected **two-view** reconstruction （SFM从一个 **two-view** reconstruction开始）, before incrementally registering new images, triangulating scene points, filtering outliers, and refining the reconstruction using bundle adjustment (BA). 

-----

4 Contributions

4.1. Scene Graph Augmentation

前面提到 Scene Graph 是指所有通过几何验证的图像匹配对 image-pair。
Scene Graph Augmentation，会为每个 valid pair 打上标签(model type)，标签有三种： general, panoramic, planar 。某个 image pair 该对应哪个标签，由几何验证的结果决定。这些标签会辅助 SfM 初始化过程中选择更优的 register 顺序。

对每个 image pair 做多模型几何验证 （a multi-model geometric verification strategy），分别 RANSAC 计算：
- 基础矩阵(Fundamental，对应普通3D点到3D 点的映射)
- 单应性矩阵(Homography，对应平面点到平面点的映射) 和
- 本征矩阵 (Essential, 对应普通3D点到3D 点的映射，但要求 image pair 中的图像都是标定过的求本征矩阵) 

即共三种模型。每种模型的 inliner 特征数量分别记为 $N_F, N_H, N_E$。
- 有些网络图像的 border 部分会有水印、时间戳、帧号等文字信息 (Furthermore, a frequent problem in Internet photos are watermarks, timestamps, and frames (WTFs))，对于具有相同 WTF 的图像，这些 WTF 会导致一些无用的 2D-2D match，干扰几何验证的有效性。剔除这类图像的办法是：
    - 利用两幅图像的 borders 中的特征点 RANSAC 计算一个相似变换 similarity transformation，设 inlier 数量为 $N_S$，如果 $N_S/N_F > \epsilon_{SF}$ 或 $N_S/N_E > \epsilon_{SE}$，说明两幅图像的 border 很相似，我们就把这个 pair 记作 WTF ，不再把它加入 scene graph. 
- general : 如果 $N_H/N_F < \epsilon_{HF}$，说明 image pair 观测到的这些 keypoints 对应的 3D 点大多不在同一平面附近，这是一般的情况；这类 image pair 预标记为 general；we assume a moving camera in a general scene if $N_H/N_F < \epsilon_{HF}$.
- 如果 image pair 中的两幅图都是标定过的，且 $N_E/N_F > \epsilon_{EF}$ , 我们认为标定参数是正确的；
- 被预标记为 general、且正确标定过的 image pair，
    - 我们分解其本征矩阵（得到相对旋转和up to scale 的相对平移），
    - 并对 inline match 做三角化，记录各 inlier 的 triangulation angle 中值 $\alpha_m$；
    - 用 $\alpha_m$ 来区分 image pair 间的相对运动是纯旋转 (panoramic, 全景) 还是平面运动 (planar)
        - ?? 待从代码中确认： 如果中值视差角 $\alpha_m$ 接近 image pair  之间的相对旋转角，说明 image pair 间的相对运动是纯旋转（或视差主要由旋转贡献）；


初始图像对倾向于选择非全景的、校准过的图像对。The model type is leveraged to seed the reconstruction only from **non-panoramic** and **preferably calibrated** image pairs.

利用增强过的 scene graph  可以使得我们：
- 高效找到最优的初始化过程 efficiently find an optimal initialization for a robust reconstruction process. 
- 避免对纯旋转的图像对做三角化 we do not triangulate from panoramic image pairs to **avoid degenerate points** and thereby improve robustness of triangulation and subsequent image registrations

-----

4.2. Next Best View Selection

如何选取一帧图像，使其作为下一帧注册的图像最合适？
我们希望这样的图像中
- 能匹配到已三角化的map point 的 keypoint 数
- 这些匹配点在图像中的分布也较广

点的数量越多，分布越均匀，则该图像的得分越高。
文中的实现方式为，把图像分别划分成 $2\times 2, 4\times 4...,2^l\times 2^l, ...$ 的不同尺度的网格，第 $l$ 层的每个包含有匹配点的网格将为本帧图像贡献 $2^l$ 的得分 ，把各层的网格得分加起来就是本帧图像的总得分。示例见 Figure 3.

-----

4.3. Robust and Efficient Triangulation

以往的三角化存在的问题：
- 如果直接用 image pair 来执行三角化，由于 image pair 由外观匹配得来，两帧 image 外观的高相似度高通常源于它们的相对位置较近，即它们之间的基线通常较短；而基线短就导致  triangulation angle 比较小，影响三角化精度；可以把多个 image pair 串联起来，把其中的 feature 延伸成一个跨越多帧图像的 feature track，这样可以获得更大的  triangulation angle 。
- 对 outlier 污染不够鲁棒。尤其对于 feature track， 一个 track 中通常存在大比例的 outlier 污染；

COLMAP 对三角化的把控：
1. triangulation angle $\alpha$ (三角化点到两个摄像头的光心连线的夹角) 要足够大 , 见 式(3)
2. 三角化的深度是正值
3. 三角化点的重投影误差小于阈值

-----

4.4. Bundle Adjustment

Parameterization. 
- employ the *Cauchy function* as the robust loss function
- For problems up to a few hundred cameras, we use a sparse direct solver
- and for larger problems, we rely on PCG.

Filtering.

BA 后，过滤错误的（即重投影误差过大的） observation  以及错误的 image （Cameras with an abnormal field of view or a large distortion coefficient magnitude are considered incorrectly estimated and filtered after global BA.）

过滤错误 image 的原理： 相机的内参在BA中参与优化。（只有焦距等参数被优化，光心点按照经验被固定为图像中心点 ，Since principal point calibration is an illposed problem [15], we keep it fixed at the image center for uncalibrated cameras）


Re-Triangulation.

第一次BA完成后，为了重建的完整性，再执行重新三角化，以尝试救回 BA 前因初始位姿不准而导致的未被成功三角化的 landmark 。



Iterative Refinement.

做两次甚至更多次 BA->Filtering -> Re-Triangulation -> BA...;
为什么第一次BA不够？Due to drift or the pre-BA RT, usually a significant portion of the observations in BA are outliers and subsequently filtered.
通常，第二次 BA 就能显著改善。
我们可以迭代地进行多次 BA


-----

4.5. Redundant View Mining

image 按共视比例分组，每个 image 在组内有个局部位姿，然后整组 image 对外有一个全局 pose  $G_r$， BA 时只优化 $G_r$ （每组 image 只有一个待优化的pose） ，减少了 BA 优化过程中自由参数的数量。

-----

其他 SfM 库：

COLMAP 论文中经常拿来对比的：  Bundler,  VisualSfM

-----

另一篇速读博文：
https://blog.csdn.net/weixin_44120025/article/details/123769174


