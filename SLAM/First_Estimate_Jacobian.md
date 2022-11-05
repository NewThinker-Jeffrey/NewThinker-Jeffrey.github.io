[TOC]

## FEJ 简介

FEJ: First Estimate Jacobian

论文连接： https://www-users.cse.umn.edu/~stergios/papers/iser08-fej.pdf

标准 EKF 中，存在一个状态量同时有多个线性化点的现象，不同的观测在不同的线性化点处计算了observation jacobian。而这种同时使用多个线性化点的现象，可能会给系统带来错误的、本不存在的可观性。

使用FEJ的方法，可以避免这种线性化点的不一致性。


### EKF 中单一状态量可能对应多个线性化点

标准EKF框架中，在 $k$ 时刻，我们有状态量 $x_k$ 和 观测量 $y_k$，观测模型为 $y_k = h(x_k) + n_k$。$y_k$ 为我们估计 $x_k$ 提供了信息。
但其实 后续时刻，比如 $k+1$ 时刻的观测量 $y_{k+1}$ ，也在继续为估计 $x_k$ 提供信息。因为根据状态转移模型 $x_{k+1}=f(x_k) + v_k $，我们有
 $y_{k+1} = h(x_{k+1}) + n_{k+1} = h[f(x_k) + v_k] + n_{k+1} \approx h[f(x_k)] +  (h'v_k + n_{k+1}) $
而这就给出了 $x_k$ 和 $y_{k+1} $ 之间的（一阶近似）观测模型，其中 $h' $ 是在 $f(x_k)$ 附近某点处求得的观测函数 $h$ 的 Jacobian。
依此类推，$k+i$ 时刻的观测值，都会为估计 $x_k$ 提供新的信息。
下面我们解释为什么说在标准 EKF 中 $y_k$ 和 $y_{k+1}$ 是在不同的线性化点处计算的 observation jacobian （with respect to the state at time $k$）。
在标准 EKF 中， $k$ 时刻的观测模型被近似为 $y_k \approx h(x_{k|k-1}) + h'(x_{k|k-1}) (x_k - x_{k|k-1}) + n_k$，针对观测量 $y_k$ 我们在 $x_{k|k-1}$ 处计算了 它相对于 $k$ 时刻状态量的 observatoin jacobian ($J_{k|k}=h'(x_{k|k-1})$);
$k+1$ 时刻，观测模型被近似为 
$y_{k+1} \approx h(x_{k+1|k}) + h'(x_{k+1|k}) (x_{k+1} - x_{k+1|k}) + n_{k+1} $
而同时 $x_{k+1}$ 在标准 EKF 的 propagation 中被近似为 
$x_{k+1} = x_{k+1|k} + (x_{k+1} - x_{k+1|k}) \approx f(x_{k|k}) + f'(x_{k|k})(x_k - x_{k|k-1})$
即 $x_{k+1} - x_{k+1|k} \approx f'(x_{k|k})(x_k - x_{k|k-1})$，代回$k+1$ 时刻的近似观测模型中，有
$y_{k+1} \approx h(x_{k+1|k}) + h'(x_{k+1|k}) f'(x_{k|k}) (x_k - x_{k|k-1}) + n_{k+1} $
即，针对观测量 $y_{k+1}$ ，我们在 $x_{k|k}$ 处计算了 它相对于 $k$ 时刻状态量的 observatoin jacobian （$J_{k+1|k}=h'(x_{k+1|k}) f'(x_{k|k})$）;
可见，对于观测量 $y_{k}$ 和  $y_{k+1}$ ，我们在不同的点处 （分别是 $x_{k|k-1}$ 和 $x_{k|k}$） 计算了它们相对于 $k$ 时刻状态量的observatoin jacobian 。而这本质上是因为 $k$ 时刻的观测 jacobian 和 $k\rightarrow k+1$ 时刻的propagation jacobian 是在不同点处计算的。
正是这一点，可能会为系统引入实际中并不存在的能观性。后面我们会用一个超级简单的例子来解释，为什么同时为同一状态量使用多个不同的线性化点会带来能观性问题。现在，我们先来看看，通过什么方法能消除这种线性化点的不一致性。

### 解决方法： FEJ-EKF

其实做法很简单，我们只要想办法保证 $k$ 时刻的观测 jacobian 和 $k\rightarrow k+1$ 时刻的propagation jacobian 是在同一点计算的。所以，我们直接把 $k\rightarrow k+1$ 时刻的propagation jacobian 也在  $x_{k|k-1}$ （而不是 $x_{k|k}$ ） 处计算就可以了。

FEJ-EKF 的流程
Given $x_{k|k-1}, x_{k|k}, P_{k|k-1}, P_{k|k}$
- state propagation: $x_{k+1|k} = f(x_{k|k})$
- covariance propagation
    - standard EKF (for contrast):  $P_{k+1|k} = f'(x_{k|k})P_{k|k}f'(x_{k|k})^T + Q_{k+1}$
    - FEJ-EKF: $P_{k+1|k} = f'(x_{k|k-1})P_{k|k}f'(x_{k|k-1})^T + Q_{k+1}$
- obervatoin jacobian: $H_{k+1} = h'(x_{k+1|k})$
- compute $K$ as usual EKF;
- updaet state: $x_{k+1|k+1} = x_{k+1|k} + K(h(x_{k+1|k}) - y) $
- update posterior covariance  $P_{k+1|k+1} $ as usual EKF.


另外，在普通的滤波问题中，如果我们使用 Iterated EKF，并且在每一个时刻 $k$ 的 update 环节，我们都迭代多次直至收敛，收敛条件为最后一次迭代后  $x_{k|k-1} = x_{k|k}$，那么就可以保证，在最后一次迭代后，$k$ 时刻的观测 jacobian 和 $k\rightarrow k+1$ 时刻的 propagation jacobian 是于同一点处计算的。
但 FEJ 的论文中，section 2 (SLAM Observability Analysis) 的最后，也明确提到一句：
even if the Iterated EKF is employed for state estimation, the same, erroneous, observability properties will arise, since the landmark position estimates will generally differ at different time steps.
然而这是因为SLAM问题比较特殊，普通的滤波问题中，不同时刻没有共享的不变状态量，而 SLAM 问题中，Landmark 的位置 $p_{L}$ 是贯穿整个滤波过程的不变状态量。它不同于机器人的位置，机器人的位置在每个时刻$k$，会产生一个新的状态量 $p_{Rk}$，普通滤波问题中的状态量也多是如此。
针对 landmark ，当它第一次被添加到问题中来时，为其选择一个线性化点（一般选在第一次对 landmark 的估计值），之后保持这个线性化点不变。其实每一个状态量都应是如此，在第一次被估计后，就固定线性化点。注意 $p_{Rk}$ 和 $p_{Rk+1}$ 理应看作两个状态量，虽然他们都代表机器人位置，但却是不同时刻的位置。

### 多线性化点对能观性的影响举例

同一状态量对不同的观测量用了不同的线性化点，会产生什么影响？
单纯针对这一问题，可以考虑下面的简单例子：

二维平面上有个静止的机器人，设它的真实位置为 $s^*=(x^*,y^*)$，真实值不可知；假设我们对机器人的位置已经有一个估计值，记为 $\hat s=(\hat x_0, \hat y_0)$；

坐标系原点处，有A,B两台雷达，都可以测量机器人到原点的距离（不测角度），观测模型分别为 
$h_a(x,y)=\sqrt{x^2+y^2} + v_a$
$h_b(x,y)=\sqrt{x^2+y^2} + v_b$
其中 $v_a, v_b$ 是高斯噪声项，方差分别为$\sigma_a^2,\sigma_b^2$；

假如这两台雷达各做了一次测量，得到观测值 $r_a, r_b$；
下面我们根据这些观测值来更新我们对机器人位置的估计。
我们尝试把估计值更新为 $s+\delta s =  (\hat x_0+\delta x, \hat y_0+\delta y)$， 其中$\delta s = (\delta x,\delta y)$ 为状态更新量；
根据A雷达的观测，更新量 $\delta s$ 对应的 likelyhood 为：
$\begin{aligned} &  & exp [ -\frac{1}{2\sigma_a^2} |h_a(\hat s+\delta s) - r_a |^2] \\ \approx & & exp [ -\frac{1}{2\sigma_a^2}|h_a(\hat s) + J \delta s - r_a |^2] \\ \propto & & exp [-\frac{1}{2} (\delta s-\mu_a)^T(J^TJ/\sigma_a^2)(\delta s-\mu_a)]\end{aligned}$
(此处省略了归一化系数，其中 $\mu_a$ 依赖于 $\hat s$, $r_a$ 和 $J$)
关于 $J$，我们一般会选择 $J=h_a'(\hat s)$。在我们的模型中，$J$ 是 $(1\times 2)$ 的矩阵，信息矩阵 $(J^TJ/\sigma_a^2)$ 是 $(2\times 2)$ 的、秩为1的矩阵；

类似的，根据B雷达的观测，更新量 $\delta s$ 对应的 likelyhood 为：
$exp [-\frac{1}{2} (\delta s-\mu_b)^T(J^TJ/\sigma_b^2)(\delta s-\mu_b)]$
注意，我们这个例子中为了简单，两个观测量的观测模型 $h_a$ 和 $h_b$ 处噪声项外完全相同，它们在同一个线性化点处正好有相同的 Jacobian，所以都用 $J$ 表示即可；如果观测模型不一致，这里应该区分 $J_a,J_b$。

把上面二个 likelyhood 乘起来，可得 $\delta s$ 对应的 joint likelyhood 为：
$exp [-\frac{1}{2} (\delta s-\mu_{ab})^T(J^TJ/\sigma_a^2 + J^TJ/\sigma_b^2)(\delta s-\mu_{ab})]$
(注意新引入的 $\mu_{ab}$，它依赖于$\mu_a$, $\mu_b$ 和 $J$)
todo: 配图： likelyhood a $\times$  likelyhood b =  joint likelyhood

不难看出，上式中，信息矩阵  $(J^TJ/\sigma_a^2 + J^TJ/\sigma_b^2)$ 的秩依然为1 （因为 $J$ 只有一行）；
此时，系统的能观性矩阵为 $\left[\begin{matrix}J\\ J\end{matrix}\right]$，它的秩显然为1， 而状态空间是2维的，所以有一个方向不可观；


而我们现在要讨论的问题，就是假如我们在为两个观测量计算 Jacobian 时选择了不同的线性化点，会发生什么。
比如，计算A雷达的 Jacobian 时依然选择 $s_a$ 作为线性化点，得到 $J_{s_a}$；B选择  $s_b$ 作为线性化点得到 $J_{s_b}$；那么此时，上面的 joint likelyhood 就变为：
$exp [-\frac{1}{2} (\delta s-\mu_{ab})^T(J^TJ/\sigma_a^2 + J^TJ/\sigma_b^2)(\delta s-\mu_{ab})]$
todo: 配图： likelyhood a $\times$  likelyhood b =  joint likelyhood

能观性矩阵变成  $\left[\begin{matrix}J_{s_a}\\ J_{s_b}\end{matrix}\right]$；
而通常 $J_{s_a}\ne J_{s_b}$且二者一般会是线性无关的，因此能观性矩阵的秩就变成了2，于是状态空间上的所有方向都变得可观了。

我们这里构造的例子中，存在刻意的简化。比如两个观测函数除了噪声项外完全一样，遂导致采取一致的线性化时它们只能提供相同的能观方向。实际问题中，即便多个观测量的观测函数没有如此相像，它们的能观方向也可能都限制在特定的几个方向上；这种情况下，如果为不同的观测量在不同的线性化点计算Jacobian，就可能会引入错误的能观方向。

另外，FEJ 论文中有稍微复杂一点点的 2D SLAM 例子，下面的链接中有更复杂一点的视觉SLAM例子（但相对于实际的SLAM问题依然算简单）：
https://docs.openvins.com/fej.html

