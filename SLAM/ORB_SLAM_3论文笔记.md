
# ORB-SLAM3 论文笔记

-----

ORB-SLAM-3 

新特性： IMU, Atlas

-----

III. SYSTEM OVERVIEW

与 ORB-SLAM-2  相比最显著的 novelties 在于

- Atlas, multi-map 多地图支持：Atlas 中包含一系列互不连通的 map，
    - 其中有一个 active map，是当前 tracking 线程所参考的地图，并会在local mapping 线程中持续更新；
    - 其余的是 non-active map （如果 当前 frame 的 tracking 失败，会尝试在包括 non-active map 在内的所有map 中， 找 relocalization，且会把 relocalization 涉及的 map 标记为 active map，具体见 tracking 线程的介绍）；

- Tracking thread ：与 ORB-SLAM-2 中类似，此线程主要负责 实时计算当前 frame 相对于 active map 的位姿（最小化重投影），并决定是否将当前帧插入为新的keyframe；ORB-SLAM-3 中的不同点在于：
    - 优化时可以加入惯性(IMU)残差:  In **visual-inertial** mode, the **body velocity and IMU biases** are estimated by including the **inertial residuals** in the optimization.
    - 直接 tracking 上一帧失败时，会在多地图中尝试重定位。
        - 如果重定位成功，就把被重定位到的 map 切换为新的 active map;  When tracking is lost, the tracking thread tries to relocalize the current frame in all the Atlas’ maps. If relocalized, tracking is resumed, switching the active map if needed.
        - 否则，若一段时间内总是 tracking 失败，则把当前的  active map 标记为 non-active，并初始化一个新的 map 作为 active map；   Otherwise, after a certain time, the active map is stored as non-active, and a new active map is initialized from scratch.
 
- Local mapping thread： 与 ORB-SLAM-2 中类似，Local mapping 线程 会 添加新的 keyframe 和 map point 到 active map，移除冗余的 keyframe，local BA 优化；ORB-SLAM-3 中的不同点在于：
    - 可以支持 visual-inertial BA
    - in the inertial case, the IMU parameters are initialized and refined by the mapping thread

- Loop and map merging thread ： ORB-SLAM-2 中的  loop closing  相当于只对 active map 进行；而 ORB-SLAM-3 中， 对于新加入 active map 的 keyframe ， 对 Atlas 中的所有 map 尝试  loop  (detects common regions between the active map and the whole Atlas at keyframe rate.)
    -  如果 loop 到的 区域还是在 active map 中，那这就是一次普通的  loop closing ，将执行  loop correction；
    - 否则，如果 loop 到的区域在其他地图中，则把该地图 merge 到  active map 中；

-----

IV. CAMERA MODEL

ORB-SLAM-3 的另一个改进是，把 camera 模型的接口 (projection and unprojection functions, Jacobian, etc.)   抽象了出来，以便灵活支持多种 camera 模型；基于新的接口，ORB-SLAM-3 中已经支持了 pin-hole 和  Kannala-Brandt fisheye model 。

以前的 ORB-SLAM 以及其他一些常见的计算机视觉算法中，通常会把 pin-hole 模型作为假设。然后把整张图片或者只把其中的特征点映射到 pin-hole 模型的理想成像平面上。但这只适用于摄像头 FOV 小于180°的情况，而对于 fisheye lenses, 它的 FOV 可能达到甚至超过 180°。此时 pin-hole 模型假设就不再成立。ORB-SLAM-3 中则允许更灵活的 camera 模型；

为了适配更灵活的camera model，原ORB-SLAM中的一些环节需要跟着调整：

-  A. Relocalization: PnP 求解问题。Tracking 失败后，ORB-SLAM 进行 relocalization 时需要求解 PnP。
    - 以前，老的 ORB-SLAM 直接使用 ePnP 算法进行求解，该算法默认使用了 calibrated pin-hole camera ；
    - 但 ORB-SLAM-3  中，为了让 PnP 算法不依赖具体的 camera model，选用了 Maximum Likelihood Perspective-n-Point algorithm (MLPnP) ，该算法只需要 camera 提供一个 unprojection function (passing from pixels to projection rays);

- B. Non-rectified Stereo SLAM  (ORB-SLAM-2 中只支持 rectified Stereo SLAM)
    - Most stereo SLAM systems assume that stereo frames are **rectified**, i.e. 
        - both images are transformed to pin-hole projections using the same focal length, 
        - with image planes co-planar, 
        - and are aligned with horizontal epipolar lines, such that a feature in one image can be easily matched by looking at the same row in the other image. 
    - However the assumption of rectified stereo images is **very restrictive and**, in many applications, is neither suitable nor feasible. e.g.
        - a divergent stereo pair, 
        - or a stereo fisheye camera would require severe **image cropping**, loosing the advantages of a large FOV.
    - For that reason, our system does not rely on image rectification, considering the stereo rig as two monocular cameras having:
        - 1) 有固定外参. a constant relative SE(3) transformation between them, and
        - 2) optionally 有交叉视野. optionally, a common image region that observes the same portion of the scene.


-----

V. VISUAL-INERTIAL SLAM

ORB-SLAM-3 在 VI-SLAM 的处理上，比前作 ORB-SLAM-VI 有了更多改进，主要体现在
- 更快、更准确的的初始化
- 支持 pin-hole 和 fisheye 两种 camera 模型 （前作ORB-SLAM-VI 只支持 pin-hole）

B. IMU Initialization
把 IMU Initialization 看做一个 MAP estimation problem，并分成三个阶段：

- 1)  Vision-only MAP Estimation: 实现一个 up to scale 的 SfM，估计各 frame 的 pose (up to scale);
- 2) Inertial-only MAP Estimation: 估计 尺度$s$， 重力对齐方向，IMU bias， 以及各 image 的 velocity;
    - 对于重力对齐方向，ORB-SLAM-3中将其表达为一个旋转，可以用李代数求导。3维的李代数上的扰动方向，与重力向量平行时，不会改变重力向量，这个方向的扰动是"无用"的；因此我们只需把李代数上的扰动限制在与重力方向(0,0,1)垂直的2维子空间上，见论文中的式(9)。可以与VINS中的处理方式做个对比，VINS将重力方向表达为一个向量，在2维球面上人工选择合适的2维坐标求导，无法使用李代数。
    - 为防止尺度变负，尺度的更新也做了特殊处理（乘以扰动的指数映射，代替直接加扰动），见式(10)
- 3) Visual-Inertial MAP Estimation:  VI 联合优化 . 初始化中的这个VI 联合优化环节与 figure 2a 中一般的 VI optimization 问题相比，特殊之处在于，初始化过程中可以认为 bias 不随时间变化


C. Tracking and Mapping

- Tracking:  solves a simplified visual-inertial optimization where **only the states of the last two frames** are optimized, while map points remain fixed.
- Local mapping: use **a sliding window of keyframes and their points** as optimizable variables, including also observations to these points from covisible keyframes but keeping their pose fixed.


D. Robustness to tracking loss

对于没有 IMU 的纯视觉 SLAM 或 VO，短暂的摄像头遮挡或快速运动都会导致视觉特征马上跟丢。
但如果加上 IMU 的辅助，就可以通过IMU预测当前frame的pose，并把map points 投影到该frame中并尝试在投影点附近搜索匹配。有 IMU 时，visually lost分为两个阶段处理：
- Short-term lost:  （视觉丢失不超过5s）
    - the current body state is estimated from IMU readings, and map points are projected in the estimated camera pose and searched for matches within a large image window. The resulting matches are included in visual-inertial optimization. 
    - In most cases this allows to recover visual tracking. Otherwise, after 5 seconds, we pass to the next stage.
- Long-term lost:  （视觉丢失超过5s）
    - A new visual-inertial map is initialized as explained above, and it becomes the active map;
  
如果IMU初始化后不到15s视觉就跟丢了，则对应的地图将被丢弃，已免在 Atlas 中积攒无用的地图。(If the system gets lost within 15 seconds after IMU initialization, the map is discarded. This prevents to accumulate inaccurate and meaningless maps.)

------

VI. MAP MERGING AND LOOP CLOSING

A. Place Recognition
过程：设 $K_a$ 代表需要搜索 loop 的 active keyframe （即query keyframe）.
- 1) DBoW2 candidate keyframes:  从 Atlas DBoW2 database 中找出 3 个与 $K_a$ 的词向量最相似（且与$K_a$不共视）的 candidate keyframe，每个这样的 candidate keyframe 我们记作 $K_m$;
- 2) Local window: 
    - 对于每个 candidate keyframe $K_m$，我们为它定义一个 local window，这个window  中包含了 $K_m$ 以及与他共视最好的那些keyframes，以及所有这些keyrame 观测到的map points; 
    - DBoW2 在 $K_a$  和这些  local window keyframes 之间找出一系列 2D-2D match；
    -  每个 2D-2D match 又可以转化 map points 间的 3D-3D match;
- 3) 3D aligning transformation:
   RANSAC 求$K_m$与 $K_a$ 的相对变换 $T_{am}$，对于单目情况$T_{am}\in Sim(3)$，对于stereo情况 $T_{am}\in SE(3)$；对于两种情况，在RANSAC的每一步中，我们都通过 Horn 算法 [77] 用 3 组  3D-3D matches 来求一个 hypothesis for $T_{am}$；
- 4) Guided matching refinement.
    - 把 candidate keyframe $K_m$ 所在的 local windown 中的所有 map points 投影到  active keyframe   $K_a$ 中，并尝试在 $K_a$ 中做 3D-2D match；
    - 反过来，再把  $K_a$ 中 的  map points 投影 local windown 中的所有 keyframes 中，尝试做 3D-2D match；
    - 把找到的所有匹配组合起来，最小化双向重投影误差，通过优化来 refine $T_{am} $;
    - 如果上一步优化结果的 inlier 超过阈值，则再重新进行一次Guided matching，只不过这次做 3D-2D match 时将用更小的 image search window.
- 5) Verification in three covisible keyframes:
    - 如果能找出两个与 $K_a$ 共视的 keyframe（加上$K_a$自己就共有3个keyframe），使得它们在 $K_m$ 的 local window 中 match 到的 map points数量都超过一个阈值，我们就认为 本次 loop 通过校验；否则，等待后续新的 keyframes 打来后再进一步判断 (To verify place recognition, we search in the active part of the map two keyframes covisible with $K_a$ where the number of matches with points in the local window (around $K_m$) is over a threshold. If they are not found, the validation is further tried with the new incoming keyframes, without requiring the bag-of-words to fire again)
    - The validation continues until 
        - three keyframes verify $T_{am}$, 代表成功 loop
        - or two consecutive new keyframes fail to verify it, 代表loop失败
- 6) VI Gravity direction verification. 有IMU时做重力方向校验
    - We further check whether the pitch and roll angles (of $T_{am}$) are below a threshold to definitively accept  the place recognition hypothesis. (用此校验，需假设各keyrame 的pitch roll不会有大变化)


B. Visual Map Merging
- 1) Welding window assembly:   
    - Welding window 是指 $K_a$ and its covisible keyframes, $K_m$ and its covisible keyframes, and all the map point observed by them.
    - Before their inclusion in the welding window, the keyframes and map points belonging to $M_a$ ($K_a$ 所在的 active map) are transformed by $T_{ma}$ to align them with respect to $M_m$ ($K_m$所在的map).
- 2) Merging maps:  Maps Ma and Mm are fused together to become the new active map.
    - active search: 为把重复的 map point 合并，我们把 $M_a$ 中 3D point  投影到 $M_m$ 的 keyframes 中来找3D-2D 匹配，并最终化为 3D-3D 的 map point 匹配；
    - 合并 map points: For each match, the point from $M_a$  is removed, and the point in Mm is kept accumulating all the observations of the removed point.
    - 更新 covisibility and essential graphs
- 3) Welding bundle adjustment:
    - local BA 优化 Welding window 中的 keyframes 和 map points; 
    - 其他能观测到 Welding window  map points 的关键帧也会添加到 local BA 问题中，提供更多观测约束，但这些关键帧的pose 会被 fix 而不会跟着更新（这也 fix 了此BA问题的规范自由度）；
- 4) Essential-graph optimization:  (均摊drift)
    - A pose-graph optimization is performed using the essential graph of the whole merged map, keeping fixed the keyframes in the welding area. 
    - This optimization propagates corrections from the welding window to the rest of the map


C. Visual-Inertial Map Merging:     Steps 1) and 3) for the visual only case above are modified to better exploit the inertial information.
- 1) VI welding window assembly:  相比上面 visual only case 中的1)，VI welding window中， $M_a$  到 $M_m$ 的对齐需要分两种情况
    - If the active map is mature, we apply the available $T_{ma} \in SE(3)$ to map $M_a$ before its inclusion in the welding window.
    - If the active map is not mature, we align Ma using the available $T_{ma} \in Sim(3)$
- 2) VI welding bundle adjustment:  相比上面 visual only case 中的3)， 额外的变化如下：
    - 待优化变量的扩充Poses, velocities and biases of keyframes $K_a$ and $K_m$ and their five last temporal keyframes are included as optimizable.
    - For $M_m$, the keyframe immediately before the local window is included but fixed, while for $M_a$ the similar keyframe is included but its pose remains optimizable.
    - All map points seen by the above mentioned keyframes are optimized, together with poses from $K_m$ and $K_a$ covisible keyframes. 

D. Loop Closing
- Loop closing correction algorithm is analogous to map merging, but in a situation where both keyframes matched by place recognition belong to the active map. 
- loop correction 之后 "可能" 还会再接一个 global BA (to find the MAP estimate after considering the loop closure mid-term and longterm matches)
    - 对于 visual-inertial case，为防止计算量过大，只有当 keyframe 总数小于一个阈值时，才做global BA;