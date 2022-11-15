
# ORB-SLAM 论文笔记

-----

ORB-SLAM-1 主要针对单目。
Monocular SLAM 

-----

map point

每个 map point 除了有3D坐标外，还有：

- 一个法向量（a patch normal），这个法向量是地图中该 map point的已知观测向量的平均：Each map point has associated a patch normal $n$, which is the mean vector of the viewing directions (the rays that join the point with the keyframe camera centers).
    - 后续找 3D-2D match 时，这个法向量可用于提前剔除掉一些来自偏僻视角（观测角度大于45°）的观测，这部分观测通常可信度较低；
- 多个 descriptor 和一个 representative descriptor ：
    - 每个 map points 被多个 keyframe 观测，所以有多个 descriptor; 
    - 但为了简化后续的 3D-2D match，我们会为每个 map point 计算一个 representative descriptor （到所有descriptor的距离之和最小），一般用D表示），然后只用 D 、而不是尝试所有 descriptor 去搜索 3D-2D match；(To **speed up data association** each map point stores a representative descriptor D, whose distance is least with respect to all associated descriptors)
- 有效观测距离 $d_{max}, d_{min}$:
    - $d_{max}, d_{min}$ 分别对应map point的最远可观测距离和最近可观测距离。观测距离的远近决定了该 map point 被观测时对应的 feature point 的不同尺度。理论上$d_{max}, d_{min}$ 分别对应最小和最大的特征点尺度；(In addition each map point stores the maximum dmax and minimum distance dmin at which it can be observed according to the scale and distance at which it has been observed.)

-----

共视图

ORB-SLAM 中的共视图，与 Active Correspondence Search中的共视图并不一样。

ORB-SLAM 中的共视图，顶点 都是 keyframe，边 edge 是 keyframe 的共视关系，Edge 的权重是两个 keyframe共同观测到的 map point 的数量（当两个keyframe共同观测到的map points 的数量超过一个阈值时，才会在他们之间添加 edge）;

Active Correspondence Search中的共视图，则是一个二分图，keyframe 和 map point 都是 顶点。edge 只存在于 一个 keyframe 和一个map point 之间，代表该 keyframe可以观测到 该 map point，且edge无权重。

-----

 III.B   Efficient and refined ORB matching

回环检测或重定位时，只在词汇树的同一个二级节点中搜索两幅图像间可能的ORB匹配： 设 query image 中某个 feature $f$ 的 descritptor 所在的二级节点为 N，那么另一个 image 中，只有 descritptor 同样落入节点 N 的那些 feature 才会被尝试与 $f$ 进行匹配；

to compute a set of ORB correspondences between two images, we compare **only ORB that belong to the same node at the second level of the vocabulary tree**.  

And this initial correspondences can be refined by an **orientation consistency test** (多个相互一致的 feature match 之间，应该有相似的旋转方向)；

-----

 III.C   Covisibility Consistency
  
covisibility consistency test 取代类似于 DBoW2 中的 temporal consistency test.

temporal consistency test： the system waits to obtain a number of consecutive temporally consistent matches to finally accept a loop closure

covisibility consistency test: two consecutive keyframe matches are considered consistent if there exist an edge in the covisibility graph linking them

-----


Tracking 步骤：

1. 当前 frame 的ORB 特征提取
2. 找出部分 3d-2d match，并初步估计当前帧的 pose
    - case 1: Track from previous frame (if the previous frame itself is tracked), previous frame 中每个绑定过 map point 的 feature 在新图像的局部 进行 2D-2D match，再转化为 3D-2D match，然后 ransac PnP 估计 pose 并做几何校验；
    - case 2: Track from scratch, i.e. Relocalisation (if the previous frame is NOT tracked)， 用 BoW 检索地图中的相似keyframe，每个 keyframe 再与当前 frame 做 III.B 中的 ORB matching，看能否找到可通过 ransac PnP 几何校验的 keyframe；
3. Track the Local Map: 在上一步基础上，从 Local Map 中找出尽可能多的 3D-2D match：把 Local Map 的map point投影到当前 frame中，再在投影点$x$附近搜索与 该map point匹配的 2d feature.
    - Local Map 决定了需要对哪些 3d 点做 3d-2d match ；Local Map 由两类 keyframes 组成
        - 利用上一步中找到的部分 match，可以确定哪些 keyframe 与当前 frame 有共视的 map points，这些keyframe组成集合 $\mathcal K_1$（the set of keyframes K1, that share map points with the current frame ）
        -  $\mathcal K_1$  在共视图中的邻居 keyframe 组成 $\mathcal K_1$ (with neighbors to the keyframes K1 in the covisibility graph)
    - 3D-2D match:
        - map point 的投影点 $x$ 超出当前图像范围时，跳过该 map point ；
        - map point 到 当前 frame 的观测角大于 45°时（计算观测角需要用到前面提到的 map point 的法向量），跳过该 map point；
        -  map point 到当前 frame 的 观测距离超出该 map point 的 $[d_{min}, d_{max} ]$ 范围时，跳过该 map point；
        - 根据 map point 到当前 frame 的 观测距离，预测 feature point 的尺度；然后只在当前 frame 中尺度相符的 feature 中进行搜索，进一步缩小搜索范围；
        - 在投影点 $x$ 附近范围内，找出尺度相符、且与 map point 的 representative descriptor D 最相似的 feature point；
4. Final pose optimization: 根据上一步找出的 3D-2D match 和上上步初步估计的当前帧 pose，通过 g2o 最小化重投影来计算当前 frame 的最优 pose 估计；
5. 判断是否添加当前 frame 为新的 keyframe.


> 个人看法： 
第 2 步中找 3d-2d match 的作用有两个，一是用于初步估计当前帧的 pose，而是辅助第3步确定keyframe 集合$\mathcal K_1$；
如果有 imu 或其他 odomtry 辅助，第 2 步中的 3d-2d match 是否就不再必要？有了  imu 或其他 odomtry ，第2步中可以通过积分直接得到一个初始 pose；而第3步中，也可直接选最新的若干帧 keyframe 组成 $\mathcal K_1$；

------

LOCAL MAPPING：

有新的关键帧加入时。
1. (A) 把新的关键帧转化为 bag of word vector 供后续 loop closer 使用；
2. (B) Triangulate new map points:   新的 keyframe 加入后，一些之前未被有效三角化的 feature match 可能又可以被三角化了。这一步就是搜索这些新的可被三角化的feature match ，并据此生成新的 map point。
    - 确定在哪些 keyframe 中搜索 match：只在与当前新 keyframe 共视数最多的 N 个keyframe 中搜索待三角化的 match (we triangulate points with **the N neighbor keyframes in the covisibility graph** that share most points). 
    - 在当前的新 keyframe 与其他 老 keyframe 之间，搜索 free ORB 间的 match ：For each free ORB in the current keyframe we search a match to other free feature in other keyframe just comparing descriptors. 
    - 限制搜索范围（只在另一图像中与query ORB落入相同二级node的ORB特征点中搜索最佳匹配）： only compare ORB that belong to the same node at the second level in the vocabulary.
    - 剔除不符合对极约束的 match: and discard those matches that do not fulfill the epipolar constraint. 
    - 三角化有足够视差 (1°) 的 match: Once a pair of ORB have been matched, they are triangulated. If the map point parallax is less than one degree it is discarded. 
    - 通过两个 keyframe 做完初始三角化后，该 map point 可以被 project 到其他 keyframe 中（并据此尝试在这些 keyframe 中找 3d-2d match ？）
3. (C) Update the local area:  
    - 对于新建的 map point ： 一个新的 map point 自被创建后，会在接下来的 3 个 keyframe 中被检验，如果通不过检验则会被 discard。被检验的指标包括两点：
        - 该 map point 实际被观测的次数与它被投影到图片上后理应被观测到的次数之比，应超过 0.25 （The tracking must find the point in more than the 25% of the frames in which it is predicted to be found）
        - map point 被创建后如果又已经有了 keyframe，则该 map point 至少应被 3 个keyframe 观测到 (If more than one keyframe has passed from map point creation, it must be seen in at least three keyframes.)
    - 对于已有的和通过上一步检验的map point ：
        - 在各 keyframe 中对这些 map point 有了新的观测，所以要根据这些观测来更新共视图（可能有新的edge 被建立或者某些 edge 的权重被增加）；
        - 同时，所有有了新观测的 map point 的 representative descriptor D 会被更新；In addition we recompute the representative descriptor D for each point that have new measurements.
4. (D) Local BA:  做 local BA，并根据 BA 后的结果更新 map point 的信息
    - local BA 涉及到的变量包括:
        - 新加入的关键帧 (the currently processed keyframe)
        - 共视图中与新关键帧相连接的所有老关键帧 (all the keyframes connected to it in the covisibility graph )
        - 上述关键帧所能观测到的 map points (and all the map points seen by these keyframes.)
    - 更新 map point 的信息（After the optimization, for each map point）:
        - recompute the normal $n$
        - and scale invariance distances $d_{min}$ and $d_max$
5. (E) Prune redundant keyframes:  discard all those keyframes whose 90% of the map points have been seen in at least **other three keyframes** in **the same or finer scale**.

------

LOOP CLOSER

设最新加入的 keyframe 为 $K_i$，
1. (A) Loop detection
    - 计算 loop 的相似度得分的下限 the lowest score $s_{min}$：计算共视图中所有与 $K_i$ 相连的 keyframes 与  $K_i$ 间的 BoW 相似度，取其中最低值作为 $s_{min}$
    - 去掉 database 中所有与  $K_i$ 的 BoW 相似度得分低于 $s_{min}$ 的 keyframe（这部分keyframe不太可能是loop），以及共视图中与  $K_i$ 相连的 keyframe（这部分 keyframe 产生的 loop 没有实用价值）
    - 为了提升鲁棒性，我们必须找到 3 个相连、且满足共视一致性的 loop candidates （three consecutive loop candidates that are covisibility consistent）
    - 如果存在能通过共视检验（the covisibility consistency test）的 loop candidate keyframes ，则进入下一步计算相似变换的环节
2. (B) Compute the similarity transformation:  this computation also serves as **geometrical validation** of the loop . 单目 SLAM 中，全局地图可能在 7 个自由度 (6DoF pose + 1 DoF scale) 上发生漂移；为了实现闭环，我们就需要计算 $K_i$ 与loop keyframe $K_l$ 间的 7 DoF 相似变换。如果1(A) 中找到了多个 备选的 loop keyframes，则对于每个备选的 loop keyframe   $K_l$：
    - 根据 III.B 中的方法，找  $K_i$  与 $K_l$ 间的 candidate 2D-2D match；由于 $K_i$  与 $K_l$ 种的很多特征点都已有对应的 map point，所以我们进而可以得到一组 candidate 3D-3D match;  每个 3D-3D match 在 $K_i$  与 $K_l$ 系下分别对应一个 3D 点坐标，因此我们得到一个 3D 点对；
    - 利用这些来自 $K_i$  与 $K_l$ 系的 3D 点对， 可以用 ransac 求解一个 相似变换 $S_{il}$；
    - 如果找到了相似变换 $S_{il}$ 且它有足够多的 ransac inlier, 则这个 loop 被接受（不再检查剩下的 备选 loop frame）
3. (C) Synchronization with local mapping thread: 与 local mapping 线程同步，等待 local mapping 线程完成当前操作。
    - local mapping 线程 和 loop closer 线程都会对地图中的map points、keyframe 的pose等信息做出修改，所以二者需要同步
4. (D) Loop fusion:  fuse duplicated map points ( 类似于 maplab 中的 合并 landmark) and insert new edges in the covisibility map that will attach the loop closure.
    - 首先，用 2 中计算的 $S_{il}$ 以及  $K_l$ 的 pose $T_{lw}$  来反算 $K_i$ 修正后的 的 pose $T_{iw}$，并通过邻居 keyframe 间的相对位姿关系 把这个位姿修正传递给  $K_i$ 在共视图中的相邻 keyframes ; (At first the current keyframe pose $T_{iw}$ is corrected with the similarity transformation $S_{il}$ and this correction is propagated to all the neighbors of $K_i$, concatenating transformations, so that both sides of the loop get aligned)
    - 把 loop frame   $K_l$ 及其邻居 keyframe 观察到的 map points 投影到  $K_i$ 及其邻居 keyframe 中，然后再利用 Tracking 环节查找 3D-2D match 的方法来找对应，并将找到的 3D-2D match  转化为 3D-3D match；
    - 上一步找到的 3D-3D match，以及2中计算 相似变换 $S_{il}$ 时的 inlier 3D-3D pair，都将被合并为 1 个 point (All those map points matched, and those that were inliers in the computation of Sil are fused)
    - 对于上述 point fusion 过程中影响到的所有 keyframes，他们的共视关系需要根据 fuse 后的 point 来更新，即它们在共视图中的 edge 需要重新计算；
5. (E) Covisibility pose graph optimization： 基于共视图做位姿图优化 (a pose graph optimization over the covisibility graph), 为后续的 global BA 提供一个较好的初值 (compute an initial solution so that we facilitate the convergence of the optimization)。根据论文，大致环节如下，具体怎么实现还要看代码。
    - 共视图的 edge 转化为两个 keyframe 间的一个相似变换 (similarity transformations) 约束；
    - 所有 edge 做位姿图优化 （the error in the loop closure edges will be distributed along the graph）
    - 注意被优化的是每个 edge 对应的相似变换，这样可以修正尺度漂移 (The pose graph optimization is applied over similarity transformations to correct the scale drift)。
6. (F) Global BA:  第3步中，我们同步了 local mapping 线程使其暂停工作； Global BA 结束后，再次同步 local mapping 线程，使 local mapping 重新开始工作；
