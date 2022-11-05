
[TOC]

## Improving Image-Based Localization by Active Correspondence Search

paper: 
https://www.graphics.rwth-aachen.de/media/papers/sattler_eccv12_preprint_1.pdf

code: 
https://www.graphics.rwth-aachen.de/software/image-localization/
https://www.graphics.rwth-aachen.de/media/resource_files/ACG_Localizer_v_1_2_2.zip

### 问题描述

我们用 SfM 重建得到的 3D 点云来表示一个 scene （场景，或地图），每个 3D point 在建图数据中可能被观测过多次，所以会关联多个 descriptor。

如何 高效地 在 query image 中的数千个 feature 和数百万个 scene point 之间搜索匹配，是主要的难点。

除了搜索的高效性，我们还要保证搜索到的 matches 的质量。因为当获取到足够的 2D feature 到 3D scene point （坐标在地图中已知）的 matches 之后，还要用 RANSAC 估计当前的相机位姿，而 RANSAC 所需的迭代次数又依赖于这些 matches 的质量（inlier 比率）。

一帧 query image 中的 feature 数量要远远少于场景中3D点的数量，所以 3D-to-2D 的 matching 要更高效一些，但其匹配质量较差 （why, need more clarification）；
反过来，2D-to-3D 的 matching 搜索量虽然比较大，比较慢，但能产生较高质量的 match （why, need more clarification）；


### 2D-to-3D match

对于 query image 中的每个 feature $f$，我们在它所属的 word 内存储的所有 3D 点中，找出 2 个与 $f$ 描述子最相似的点  $p_1, p_2$ 。
如果 $|f − p_1|_2=|f − p_2|_2 <0.7$，即通过了 **ratio test**，那么我们就认为找到了一个匹配  $(f, p1, |f − p_1|_2)$  。其中 $|f − p_1|_2$ 代表  $f$ 和 $p_1$ 的描述子的距离。
能通过 **ratio test**，说明匹配到 descriptor 有很好的区分性。

> For every feature $f$, we search through all points stored in its word and find its two nearest neighbors, i.e., the points $p_1, p_2$ with the most similar descriptors. 
> A correspondence $(f, p1, |f − p_1|_2)$ is established if the SIFT **ratio test** $|f − p_1|_2=|f − p_2|_2 <0.7$ is passed. Here, $|f − p_1|_2$ is the descriptor distance between the descriptors of $f$ and $p_1$.

### 3D-to-2D match

3D-to-2D match 的思路与 2D-to-3D 类似。具体见论文中的 Algorithm 1。

有点小区别是，2D-to-3D match 时，为了限制搜索空间的大小，我们使用相对 细粒度的字典（fine vocabulary）；而 3D-to-2D match 时则希望能考虑到更多可能的 match，所以需要用粗粒度的字典（coarser vocabulary）；而 coarser vocabulary 可以通过 vocabulary tree 来实现：需要 fine vocabulary 时将叶节点作为word，需要 coarser vocabulary时中间某一层的节点作为word；

> While we require a rather fine vocabulary for 2D-to-3D matching to limit the search space, we need to use a coarser vocabulary for the 3D-to-2D search step to guarantee that enough features are considered. A key observation is that information about a coarser vocabulary can be obtained without additional cost by using a vocabulary tree [14, 21] to perform the initial 2D-to-3D matching. 
> Using a coarser level in the tree has the additional benefit of recovering matches lost due to quantization effects 

另外，论文中  3D-to-2D match 的 ratio test 阈值是 0.6，不同于 2D-to-3D 时的 0.7；这个阈值越低，最优匹配与次优匹配的 descriptor distance 之比越显著。 
3D-to-2D 时选用更低（即更严格）的阈值，因为图片中的 feature 数量较少，其描述子集合在描述子空间中分布相对稀疏，最优匹配与次优匹配的差异会较大，所以可以用更严格的 ratio test 阈值 0.6；
而2D-to-3D 时，地图中 3D point 数量极多，其描述子集合在描述子空间中分布很稠密，最优匹配与次优匹配的差异可能也没有那么明显，所以用稍微宽松的 ratio test 阈值 0.7；

### Prioritized Search

Prioritized Search 的核心思想是，利用某些先验知识，优先遍历高概率存在 match 的 3D point 或 2D feature 。

3D-to-2D 时，可以用共视信息来更新各 point 的优先度。
> For 3D-to-2D matching, Li et al. propose a prioritization scheme purely based on co-visibility of 3D points: When a match for a point $p$ is established, the priorities of all other points visible together with $p$ in one database image are increased.

2D-to-3D 时，根据 feature 所在的 word 中包含的 descriptor 数量多少 来确定各 feature 的优先度。feature 所在的 word 中包含的 descriptor 数量越少，说明这个 word 的区分度较高，为其设置越高的优先度，且在该 word 中搜索这个 feature 的最近邻所需的计算量也越少；
> For 2D-to-3D matching, Sattler et al. propose a strategy purely based on appearance. They assign both points and features to a set of visual words. The cost of processing a feature depends on the number of descriptor distance computations needed to find its nearest neighbor. This number of computations is proportional to the number of points assigned to the visual word of the feature. Features are then evaluated in order of increasing cost, preferring words with only few points and thus highly distinctive appearance.

### Active Correspondence Search

每完成一个 feature 的 2D-to-3D match 后，将本次 match 到的 3D point $p$ 附近的其他 3D 点（作为 3D-to-2D Active Search）添加到优先队列中；
新增的 Active Search 任务插入到优先队列中的什么位置呢？
有 direct, afterwards, combined 三种策略，见论文中的 Fig. 3。我们这里只介绍 combined 策略。

需要注意的是，无论是 2D-to-3D 为一个 feature 找匹配，还是 3D-to-2D 为一个 point 找匹配，每一次这样的 search 操作都最终转化为在一个 word 中搜索最近 descriptor 的问题。根据 所涉及的 word 中包含的 descriptor 数量，可以统一为 2D-to-3D、3D-to-2D 的搜索任务分配优先度，所涉及 word 中包含 descriptor 数量越少的  point/feature 的优先度越高。这正是上述的 combined 策略。

### Incorporating Additional Visibility Information

共视图可表达为一个二分图，第一组节点是 camera，第二组节点是 3D points;

#### Filtering 3D Points 过滤掉一些 3D 点的 Active Search

利用共视信息可以过滤掉 Active Search 中的一些 3D 点：$p$ 附近的点不一定都能跟 $p$ 共视，因为这些点即便空间距离近，但也可能是从完全不同的角度观测的。因此，并非为所有临近点进行 Active Search ，而只为 与 $p$ 有"直接共视（在共视图中相距两条边，即被同一个camera观测过）"的点被加入Active Search列表；见 Fig. 4(a),(b)

#### A RANSAC Pre-Filter

在进行 RANSAC 定位之前，先预过滤掉一些不可能被共视的 match 组合，可以提升 matches 的 inlier ratio， 从而提升 RANSAC 的效率。方法如下：
已经找到的所有 matches (features in query image to 3D points) 所涉及到的所有 3D points，以及与这些3D points "直接相连"的 dataset camera，组成了共视图 $G$ 的一个子图 $G_C$。位于 $G_C$ 中的不同 connected component 中的3D point被认为不会被共视；我们只保留包含3D points 数量最多的 connected component，只用与这部分 3D points 关联的 matches 进行RANSAC计算，

见 Fig. 4(c).

#### Using Camera Sets

By merging cameras we try to find a better, more continuous approximation of point visibility.

上述"直接共视"、"直接相连"的条件比较强，也可以考虑放宽这个条件：
把地图中的所有 Cameras 划分成多个 Camera Sets，每个 Camera Set 是指位置、视角相差不多的一组 camera.  然后以Camera Set（而不是Camera） 和 3D points 重新构建公示图。
见 green points in Fig. 4(b), Fig. 4(d).
