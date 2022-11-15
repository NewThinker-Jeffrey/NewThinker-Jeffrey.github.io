
# ORB-SLAM2 论文笔记

-----

ORB-SLAM-2 除支持单目配置外，还支持 RGB-D、Stereo 相机；

ORB-SLAM-2 论文中主要讨论了 对 RGB-D、Stereo 相机的处理，包括  Fig 2 的框架图也是针对这些Stereo cases。单目的处理仍参考 ORB-SLAM-1。
> 注意 Fig 2 的 Loop Detection 方框中，有一项是 "Compute SE3"，从这里可以看出这个 Fig 是针对的 stereo case; 因为对于单目 case，这里应该是  "Compute Sim3" 而不是 SE3。
> Fig 2 的注解中也提到，"ORB-SLAM2 also works with a monocular input as in [1]"，而文献 [1] 就是ORB-SLAM-1。

-----

地图

与 ORB-SLAM-1 相比，地图中除了有共视图Covisibility Graph外，还维护了该 Graph 的一个最小生成树 (minimum spanning tree)，这个最小生成树连接了Graph中的所有 keyframe。（在ORB-SLAM-3中，这个最小生成树也被称为 Essential-graph）

该生成树的主要作用是把 full BA 产生的 correction 快速传递到 新 keyframe 上（full BA 优化过程中新产生的 keyframe）。

-----

ORB 特征点分类

前面提到，ORB-SLAM-2 除支持单目配置外，还支持 RGB-D、Stereo 相机；
为支持这些不同的配置，ORB 特征点将被分为两类:
- monocular keypoints，普通特征点，不能直接获得深度相机；只能通过多视角来三角化，不能提供尺度信息，只能为估计旋转和（尺度未知的）平移提供信息；
- stereo keypoints，可通过Stereo相机或RGB-D相机直接获得深度的keypoint；这部分特征点又按观测距离远近分为两类：
    - close stereo keypoints: 深度小于40倍基线长度的；通过一帧观测即可较准确地三角化并确定深度，并提供尺度、平移、旋转信息；
    - far stereo keypoints: 深度大于40倍基线长度的；需要更充分的观测视角才能三角化，能提供准确的旋转信息，但对尺度、平移只能提供很弱的信息；

-----


- Monocular keypoints 用两个坐标表达 $(u_L,v_L)$；投影函数 $\pi_m$ 用于把光心系下的3维空间点坐标投影为2维图像坐标;
- Stereo keypoints 用三个坐标表达 $(u_L,v_L,u_R)$。其中 $(u_L,v_L)$ 代表特征点在左图像中的坐标，$u_R$ 代表该特征点在右图像中的横坐标（通过 patch correlation 确定特征带你在右图像中的匹配坐标）；投影函数 $\pi_s$ 用于把光心系下的3维空间点坐标投影为3维图像坐标（2维的左图像坐标+1维的右图像坐标）;

$\pi_m$  和  $\pi_s$ 的具体形式见论文中的式(3). 在 BA 中计算Monocular keypoints 和Stereo keypoints 的重投影误差时，就是分别运用$\pi_m$  和  $\pi_s$ 来计算投影点。

> 对于 $RGB-D$ 相机的特征点，通过定义一个假想的右相机来确定 $u_R$ （见论文中的(1)式，把结构光发射器和红外接受器之间的距离定义为基线，再由深度反算视差）。这样就可以统一处理 RGB-D 和 stereo 相机了（In this way, features from stereo and RGB-D input are handled equally by the rest of the system）。

-----

B. System Bootstrapping 系统初始化

单目slam中，我们需要跑一个 SfM 来初始化最开始的若干帧 keyframes 和 map points；

但对于 stereo 或 RGB-D slam，我们可以直接把第一帧作为第一个 keyframe，并把此帧所能观测到的 stereo keypoints 作为最初的 map points; (At system startup we create a keyframe with the first frame, set its pose to the origin, and create an initial map from all stereo keypoints.)

-----

BA：

- motion-only BA in the Tracking thread： 只优化 camera 位姿来最小化 matched 3D points 的重投影误差；
- local BA in the local mapping thread: 
    - 待优化的变量包括 ：
        - a set of covisible keyframes $\mathcal K_L$
        - and all points seen in those keyframes $\mathcal P_L$
    - 其他能观测 $\mathcal P_L$ 但不属于  $\mathcal K_L$ 的 keyframes 组成的集合 $\mathcal K_F$，它们对 $\mathcal P_L$ 的观测依然会为 BA 贡献约束，但优化过程中它们的位姿会固定；
- full BA  (检测到 loop 后在第4个线程中执行): 可看做 local BA 的特例，所有 keyframe  和 map point都参与更新。唯一的一点小区别是我们需要固定初始 keyframe 的 pose 以消除 SLAM 系统的规范自由度；

-----

Loop closing  

Loop closing  分两步： loop detection and validation, 以及 loop correction (位姿图优化)。
注意，在单目SLAM中，因为尺度的不可观性，在 geometric validation 和 pose-graph optimization 中我们都不得不使用 Sim3 来表达相对变换；
而 Stereo 或 RGB-D SLAM 的情形下，尺度是可观的，可以直接用 SE3 来表达相对变换；

-----

full BA after loop closing

ORB-SLAM2 中，在执行完 loop closing 的 pose-graph optimization 后，我们会再进行一次 full BA 来得到 the optimal solution. 这个 full BA比较耗时，会在一个单独的线程中执行。如果 full BA 过程中产生了新的 frame 甚至新的 loop，怎么办？
- 如果 full BA 执行过程中，又有新的 loop 产生，我们就停掉当前的 full BA，处理新来的 loop , 新 loop 处理完后会再启动一次 full BA；(If a new loop is detected while the optimization is running, we abort the optimization and proceed to close the loop, which will launch the full BA optimization again)
- When the full BA finishes, we need to merge the updated subset of keyframes and points optimized by the full BA, with the non-updated keyframes and points that where inserted while the optimization was running. This is done by:
    - 通过地图中的最小生成树把 full BA 产生的 correction 快速传递给新的 keyframe （propagating the correction of updated keyframes (i.e. the transformation from the non-optimized to the optimized pose) to non-updated keyframes **through the spanning tree**）
    - 新的 map pionts 根据其参考帧的修正后的 pose 来更新 (Non-updated points are transformed according to the correction applied to their reference keyframe)

-----

E. Keyframe Insertion


对于 RGB-D 或 stereo SLAM 的情况，我们可以多一条筛选关键帧的条件：
如果当前帧中跟踪到的近点小于 $\tau_t$、检测到的近点大于$\tau_c$ 时，把当前帧添加为关键帧。if the number of tracked **close points** drops below $\tau_t$ and the frame could create at least $\tau_c$ new close stereo points, the system will insert a new keyframe.
> We empirically found that $\tau_t=100$ and $\tau_c=70$ works well in all our experiments.


这个筛选条件的合理性在于：在远景较多的室外场景下，想要实现高质量的tracking，就需要有足够多的 "近点" 来保证对平移的有效估计。


-----


F. Localization Mode

Purpose: for lightweight long-term localization in well mapped areas.
在定位模式下，local mapping 和 loop closing 线程不工作，tracking 线程则使用两种类型的 matches 来定位 camera:

- visual odometry matches : 当前帧与前一帧（中可三角化的 3D points）之间的 ORB matches （Visual odometry matches are matches between ORB in the current frame and **3D points created in the previous frame from the stereo/depth information**），可以对当前帧做相对定位；这些 matches 使得定位对无地图区域有了一些鲁棒性，但只依赖这部分 match 会导致定位缓慢漂移；
- map point matches (relocalization): 这部分 match 保证了有地图区域不漂移
