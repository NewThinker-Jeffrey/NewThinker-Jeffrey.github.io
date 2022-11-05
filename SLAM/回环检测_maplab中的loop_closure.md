[TOC]




## maplab 中的 loop closure

https://github.com/ethz-asl/maplab/wiki/Understanding-loop-closure

maplab 中的 loop closure 大致分一下几步

- descriptor projection
- kNN search
- Covisibility filtering 共视聚类
- PnP RANSAC 结构一致性检验

### descriptor projection

descriptor projection: 
高维二进制描述子 -->  projected descriptor 低维欧氏空间的向量 
图像（keypoint - descriptor 列表） --->  Projected Image (keypoint - projected descriptor 列表)

投影矩阵通过 PCA 或 LRT[^1] 训练；

[^1]: Keypoint Design and Evaluation for Place Recognition in 2D Lidar Maps

maplab中，freak 描述子是512 bits，brisk描述子是384bits（见[代码](https://github.com/ethz-asl/maplab/blob/618a0e0154d0c6e6cd84b5ac827140b5a9b84cb0/algorithms/loopclosure/loopclosure-common/include/loopclosure-common/types.h#L22));
projected descriptor 默认是 10维的 (float型)，对应 10*32 bits.

低维欧氏空间方便 kNN 搜索；二进制空间 kNN 搜索存在固有问题；见[^2]
> the projection vastly increases the efficiency of the k-nearest neighbor (kNN) search at minimal loss of precision [29]. More importantly, the projection enables the use of high-quality kNN algorithms for real-valued vectors [15, 38, 40, 5] and thus avoids the problems of kNN searches in binary spaces [50].

[^2]: Get Out of My Lab: Large-scale, Real-Time Visual-Inertial Localization



### kNN search

#### 字典 (index_interface_) 和数据库 (database)

字典存储 projected descriptor 列表用于检索，每个 descriptor 在列表中有一个 index;
数据库包含了所有 keyframe  以及其中的 keypoint 的信息； descriptor_index_to_keypoint_id_ 用于记录字典中 descriptor_index_ 到数据库中 keypoint_id 的映射（每个keypoint对应一个descriptor）；数据库中存储了 keyframe_id 和 ProjectedImage 组成的 pair，用于 retrieve keyframe and landmark to makeup a Match. 一个 Match 是指 query image 中的一个 keypoint 与数据库(地图) 中某 keyframe 中一个 keypoint 的配对（数据库中的keypoint又都对应于一个 landmark）；

####  inverted multi index

字典中检索 word 使用了 [inverted multi index](http://sites.skoltech.ru/app/data/uploads/sites/25/2014/12/TPAMI14.pdf) 方法 ，通过附件中一个实习生的ppt 也可以快速了解这一方法。
maplab 代码中其实支持多种检索方法，包括 inverted index， kd-tree,  inverted multi index,   inverted multi index 及其变种 inverted-multi-product-quantization-index（此变种理论上能提升算法运算速度，但从附件 ppt 中的实验数据来看实际重定位效果会下降）。
默认使用 inverted multi index 。

#### candidate match

kNN search，在字典中为 query image 的每个 keypoint 找到 k 个最相似的 projected descriptor ，字典中的每个 projected descriptor 对应地图数据库中的 某个关键帧中的一个keypoint ，也有对应landmark；
字典中的一个 projected descriptor 可以转化为 一个 candidate match，前提是对应的关键帧与待查询图像 **不属于同一数据集或者时间间隔足够长** (同一数据集时间相近的帧自然会很相似，它们之间的 loop 没有意义)

### Covisibility filtering 共视过滤

作用：
为尽可能减少后续  RANSAC 的 outlier 数量 （从而提升 RANSAC 的效率），把一些不太可能同时观测到的 match 组合去掉。

地图中的 keyframe 与 landmark 根据可视性可构成一个二分图 G；
通过 kNN search 找到的 candidate matchs， 每个 match 涉及到一个 map keyframe，和一个 landmark；所有这些被涉及到的 keyframe 和 landmark 构成 G的一个子图 G_c；
共视聚类就是找出子图 G_c 的所有连通 components ；然后选择包含 match 最多的一个 component，这其中的 match 将用于后续的 RANSAC。

maplab 共视过滤的代码流程：
- 所有candidate matches 按它们所涉及的 map keyframe 分组，筛选出得分（包含match数量）排 前 (1/4)  的keyframe ；
- 对剩余的match按共视性分组（component）： 
    - 来自同一个 Keyframe 的所有 match 划分到一个 component;
    - 直接或间接观测过同一个 landmark （仅限被recall 的landmark） 的多个 Keyframe 中的所有 match 划分到一个 component;
- 留下包含 match 数量最多的 component

### 结构一致性检验

PnP RANSAC。
地图中的 landmark 位置已知，所以用 4 个match 就可以 P3P 反算 query image 对应的 camera pose;
用 RANSAC 筛选 inliner，再看 inliner 数量 和 比率是否达到阈值来判定是否找到 loop；
如果调用参数中指定了要输出 loop edge，就在包含 match 数量最多的 vertex  与query vertex 之间建立 loop edge（相对位姿约束）；



