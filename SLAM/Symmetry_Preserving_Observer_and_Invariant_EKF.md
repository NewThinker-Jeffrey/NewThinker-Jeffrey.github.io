# Symmetry\_Preserving\_Observer\_and\_Invariant\_EKF
[toc]



---


# Symmetry Preserving Observer and Invariant-EKF
进行状态估计时，考虑系统内在的对称性（不变性）可以改善估计器的收敛性和一致性。

本文先介绍 Symmetry Preserving Observer，然后再介绍以此为理论基础发展起来的 Invariant EKF.



## 系统建模


过程方程：$\dot {\mathbf x} =  f(\mathbf x;\mathbf u) + M(\mathbf x)w$

观测方程： $\mathbf y = h(\mathbf x) \boxplus N(\mathbf x)v$



符号约定：

用  $\mathbb X$ 代表状态空间，维数为 $n$；

用  $\mathbb U$ 代表控制量所在的空间，维数为 $m$;

用  $\mathbb Y$ 代表观测空间，维数为 $p$;



### 过程
系统的演化过程可以用状态空间 $\mathbb X$ 中的一个向量场 $X\in\mathfrak X(\mathbb X)$ 描述，这个向量场可以是时变的，因此我们用$X_t$代表$t$时刻对应的向量场；

用 $\mathbf x$ 来表示系统的状态。假如$t$时刻的状态为 $\mathbf x_t\in \mathbb X$，那么$\mathbf x$此刻将沿着 $X_t(\mathbf x_t)$方向演化，即向量场 $X_t$ 在点 $\mathbf x_t$ 处的值就是$\mathbf x$在 $t$ 时刻的速度向量：

 $\dot {\mathbf x} = X_t(\mathbf x)$

> 如果向量场$X$是恒定的，不随时间变化，那 $\mathbf x$ 的演化轨迹就是向量场 $X$ 的一条积分曲线。

系统的演化过程通常会受噪声影响，所以向量场 $X_t$一般由确定部分和噪声部分组成，我们可以写作：

$\dot {\mathbf x} = X_t(\mathbf x) = f(\mathbf x;\mathbf u) + M(\mathbf x)w$

向量场 $f$ 代表过程的确定部分，其中$\mathbf u\in \mathbb U$ 是已知的输入（控制量）， $f$ 受 $\mathbf u$的影响；噪声项 $w\in \mathbb R^w$ ，它通过$M(\mathbf x)$映射到点 $\mathbf x$ 的切空间上，$M(\mathbf x)$ 的每一列是$\mathbf x$处的一个切向量；

以上各个量 都可以加上下标 $t$，代表它们可以是时变的。

---
### 观测
令$\mathbf y\in \mathbb Y$为观测量，它也分为确定部分和噪声部分：

$\mathbf y = h(\mathbf x;\mathbf u) \boxplus N(\mathbf x;\mathbf u)v$

其中，$N(\mathbf x;\mathbf u)$ 的每一列代表 $h(\mathbf x;\mathbf u)$ 处的一个切向量，它把观测噪声项 $v\in \mathbb R^v$ 映射到 $h(\mathbf x;\mathbf u)$的切空间上。

如果观测空间 $\mathbb Y$是个欧氏空间，我们可以把上面的 $\boxplus$ 换成 $+$。

---
### Pre-Observer
$F: \mathbb X\times \mathbb U\times \mathbb Y\rightarrow T\mathbb X, (\hat x, u, y)\mapsto (d/dt)\hat x=F(\hat x, u, y)$  称为一个 Pre-Observer，如果它满足：

$F(x,u,h(x,u))=f(x,u)$

即在初始无偏差、无噪声时 $F$ 能保持完美估计。

从定义上，Pre-Observer 并不保证收敛性：如果对于任意初始条件（与真值偏差在一定范围内），当$t\rightarrow \infty$时 $\hat x(t)\rightarrow x(t)$，那么这个 Pro-Observer 就是一个 (asymptotic) observer.

每个 Pre-Observer 都可以写为如下形式：

$F(\hat x,u,y) = f(\hat x,u) + \sum_{i=1}^n \mathcal L_i(\hat x,u,err_{(\hat x,u,y)})W_i(\hat x)$

其中，$\mathcal L_i$ 是关于 $(\hat x,u,err)$ 的函数，而观测误差 $err$ 本身也是 $(\hat x,u,y)$ 的函数；$W_i$ 是状态空间 $\mathbb X$ 上的一个标架场，$n$ 是状态空间维数；

> 任给一个 Pre-Observer $F$，我们有

> $\begin{aligned} F(\hat x,u,y) &= f(\hat x,u) + [F(\hat x,u,y)- f(\hat x,u)] \\
&= f(\hat x,u) + [F(\hat x,u,y)- F(\hat x,u,h(\hat x, u))] \\
&=f(\hat x,u) + \sum_{i=1}^n  [F^i(\hat x,u,y)- F^i(\hat x,u,h(\hat x, u))]W_i(\hat x)
\end{aligned}$

> 其中 $F^i$ 为$F$在标架场 $W$ 下的第 $i$ 个分量；

> 注意到  $y\mapsto err(\hat x,u,y)$ 是可逆的，我们可以用 $err$和 $(\hat x,u)$表示 $y$，因此可以令 $\mathcal L_i(\hat x,u,err) = F^i(\hat x,u,y(err,\hat x,u))- F^i(\hat x,u,h(\hat x, u))$

> 这样就可以把 $F(\hat x,u,y)$ 写成上述形式；

也可以写成增益矩阵形式：

$F(\hat x,u,y) = f(\hat x,u) +  W(\hat x) \bar {\mathcal L}(\hat x,u, err_{(\hat x,u,y)})err_{(\hat x,u,y)}$

其中 $W(\hat x)=[W_1(\hat x),W_2(\hat x)...W_n(\hat x)]$，$\bar {\mathcal L}$ 是个满足如下方程的 $n\times p$的增益矩阵：

$\bar {\mathcal L} \cdot err = \mathcal L=[\mathcal L_1,\mathcal L_2...\mathcal L_n]^T$

增益矩阵 $\bar {\mathcal L}$的每个 entry 都是$(\hat x,u, err)$的函数。

> 对于给定的 $(\hat x,u, err)$，满足方程 $\bar {\mathcal L}\cdot  err = \mathcal L$ 的矩阵 $\bar{\mathcal L}$ 有无穷多 ($n$ 个约束  vs  $n\times p$个 entries); 但能否找到一个全局的、随  $(\hat x,u, err)$ 光滑变化的矩阵函数  $\bar{\mathcal L}$?

> 如果不要求全局、或者不要求光滑，这都是没问题的；既要求全局又要求光滑，就需要证明了。

> 首先，我们可以找到一族局部的、光滑的 $ \{ {\bar {\mathcal L}^\alpha} \} $ ，使其定义域组成 $(\hat x,u, err)$空间的开覆盖；另外，若  $\bar{\mathcal L}^{\alpha_1}$和$\bar{\mathcal L}^{\alpha_2}$ 是两个局部的增益矩阵（在局部满足方程 $\bar {\mathcal L} \cdot err = \mathcal L$ ），则在他们的公共定义域上，$a\bar{\mathcal L}^{\alpha_1}+b\bar{\mathcal L}^{\alpha_2}$（其中 $a+b=1$）也是符合改方程的增益矩阵；因此，可以用 [单位分解定理(Partition of unity)](https://en.wikipedia.org/wiki/Partition_of_unity#Existence)  得到一个全局的增益矩阵函数 $\bar{\mathcal L}$;

---
## 不变性 和 Symmetry Preserving Observer
如果 $\mathbb X, \mathbb U, \mathbb Y$ 这几个空间在某种 transformation 下具有对称性（或不变性）——设所有这样的 transformation 组成的 transformation group 为 $G$—— 我们就可以利用这种对称性来设计 state observer。但首先，我们要描述这种不变性。

假设$G$作为 transformation group 可以（左）作用在 $\mathbb X, \mathbb U, \mathbb Y$ 这三个空间上，对应的作用函数分别记作$(\varphi, \psi, \rho)$，即$gx=\varphi_g(x), gu=\psi_g(x), gy=\rho_g(x)$。

系统的不变性可以概括为：

$\begin{aligned} \dot x=f(x,u) + M(x)w  \quad & \Rightarrow  \quad
g\dot x:=\dot{\overbrace {gx}} = f(gx,gu) + M(gx)w \\
y=h(x,u)\boxplus N(x,u)v \quad & \Rightarrow \quad gy=h(gx,gu) \boxplus  N(gx,gu)v\qquad \text{(equivariant)}
\end{aligned}$

$g$ 的作用相当于把 $\mathbb X, \mathbb U, \mathbb Y$ 三个空间中的点和切向量进行"平移"，上式是在说系统对这种平移具备不变性。

### 过程的不变性
$\dot x=f(x,u) + M(x)w  \qquad \Rightarrow  \qquad  g\dot x:=\dot{\overbrace {gx}} = f(gx,gu) + M(gx)w$

右边可以展开写作

 $\dot{\overbrace {\varphi_g(x)}} = (T\varphi_g)\dot x=f(\varphi_g(x),\psi_g(u)) + M(\varphi_g(x))w$

其中 $T\varphi_g$代表 $\varphi_g$ 的切映射，可以理解为 $\frac{\partial \varphi_g}{\partial x}$，$(T\varphi_g)\dot x=\frac{\partial \varphi_g}{\partial x} \frac {dx}{dt}$.

如果状态空间 $\mathbb X$ 是变换群 $G$ 本身，且群作用 $\varphi_g$定义为群乘法时，

* 如果 $\varphi_g$定义为左乘 $g$，即$\phi_g(x)=gx$，那么 $X_t,f$以及 $M$的每一列都是$G$上的左不变向量场；（$X_t(g)=X_t(ge)=T_e\lambda_g X_t(e)$）
* 如果 $\varphi_g$定义为右乘 $g^{-1}$，即$\phi_g(x)=xg^{-1}$，那么 $X_t,f$以及 $M$的每一列都是$G$上的右不变向量场（$X_t(g^{-1}) = X_t(eg^{-1})=T_e\rho_{g^{-1}} X_t(e)$）。

### 观测的不变性
$y=h(x,u)\boxplus N(x,u)v \qquad \Rightarrow \qquad gy=h(gx,gu) \boxplus  N(gx,gu)v$

右边可以展开写作

$\rho_g(y) = h(\varphi_g(x),\psi_g(u)) + N(\varphi_g(x),\psi_g(u))v$

### 不变观测误差（invariant output error）
当观测空间 $\mathbb Y$ 是欧氏空间时，通常 output error 被定义为 $err: \mathbb X\times \mathbb U\times \mathbb Y \to \mathbb R^p,\quad  err(\hat x, u, y)=\hat y-y=h(\hat x,u)-y$

但这样的误差定义一般不遵循系统的对称性，即通常 $g(h(\hat x,u)-y) \ne h(g\hat x,gu)-gy$。

一般，观测误差函数要满足：

* 误差与观测量有相同自由度：对于任意 $\hat x,u$ ，映射 $y\mapsto err(\hat x,u,y)$ 是可逆的；
* 与观测函数的一致性： $err(\hat x,u,h(\hat x,u))=0$

我们称额外还符合  $E(\hat x, u, y) = E(\varphi_g(x), \psi_g(u), \rho_g(y))$  的观测误差函数 $E$ 为不变观测误差；

### Pre-Observer 的不变性 （Symmetry Preserving Observer）
如果  $F$ 是个 Pre Observer，且

$\dot{\hat x}=F(\hat x, u, y) \quad \Rightarrow \quad g\dot{\hat x}:=\dot {\overbrace {g\hat x}} = F(g\hat x, gu, gy)$

就称 $F$ 是个可以保持不变性的 Pre Observer。右边可以展开写作

$\dot{\overbrace {\varphi_g(\hat x)}} = (T\varphi_g)\dot {\hat x}=F(\varphi_g(x),\psi_g(u), \rho_g(y))$

前面提到每个 Pre-Observer 都可以写为如下形式：

$F(\hat x,u,y) = f(\hat x,u) + \sum_{i=1}^n \mathcal L_i(\hat x,u,err_{(\hat x,u,y)})W_i(\hat x)$

如果系统具有不变性，且可以用某种 "完备的" G-不变量 $I(\hat x,u)$ 来表示 $\hat x,u$，以及有不变误差 $E$、不变标架场 $W_i$，那么保持不变性的 Pre Observer 则都可以写作如下形式：

$F(\hat x,u,y) = f(\hat x,u) + \sum_{i=1}^n \mathcal L_i(I(\hat x,u), E_{(\hat x,u,y)})W_i(\hat x)$

> 任给一个不变 Pre-Observer $F$，我们有

> $\begin{aligned} F(\hat x,u,y) &= f(\hat x,u) + \sum_{i=1}^n  [F^i(\hat x,u,y)- F^i(\hat x,u,h(\hat x, u))]W_i(\hat x)
\end{aligned}$

> $[F^i(\hat x,u,y)- F^i(\hat x,u,h(\hat x, u))]$ 依赖 $(\hat x,u,y)$ ，而$y$又可用观测误差 $E$和 $\hat x,u$ 表示，因此也可以说它依赖 $(\hat x,u,E)$ ；又由于它是个 $G$-不变量， 因此它可以写成 G-不变量 $I(\hat x,u)$ 和不变误差 $E$的函数，即可表示为 $\mathcal L_i(I(\hat x,u), E_{(\hat x,u,y)})$;

也可以写成增益矩阵形式：

$F(\hat x,u,y) = f(\hat x,u) +  W(\hat x) \bar {\mathcal L}(I(\hat x,u), E_{(\hat x,u,y)})E_{(\hat x,u,y)}$

其中 $W(\hat x)=[W_1(\hat x),W_2(\hat x)...W_n(\hat x)]$，$\bar {\mathcal L}$ 是个满足如下方程的 $n\times p$的增益矩阵：

$\bar {\mathcal L} E = \mathcal L=[\mathcal L_1,\mathcal L_2...\mathcal L_n]^T$

增益矩阵 $\bar {\mathcal L}$的每个 entry 都是$(I,E)$的函数。

---
### 完备不变量（函数）和不变标架场的构造
#### Moving frame $\gamma$
设李群$G$ 通过 $\phi$ 作用在 $\Sigma$ 上，$\phi: G\times \Sigma \rightarrow \Sigma$ ，对于任意 $g\in G$，$\phi_g: \xi \mapsto \phi_g(\xi):=\phi(g,\xi)$ 是 $\Sigma$ 上的微分同胚。设$G$ 的维数为 $r$，$\Sigma$的维数为 $s$。

考虑点$\xi\in \Sigma$，它在 $G$ 的作用下，保持不变的量是什么？正是它在$G$作用下的轨迹。

如果 $\Sigma$ 维数高于 $G$，$r\le s$，且对于任意 $\xi\in \Sigma$ ，$\phi(*,\xi):G\to \Sigma$  都是满秩的（单射），那么 $G$ 在 $\Sigma$ 上的轨迹就都是  $r$ 维的 "曲线" ；我们可以在每条轨迹上选择一个点作为 "代表"，得到轨迹的一个"横截面" $\sigma$；如果希望这个横截面是光滑的，那 $\sigma$ 通常只能是局部的，因为全局的光滑截面不一定存在。

假设 $\sigma$ 是一个光滑的局部截面， $\sigma$ 中所有点的轨迹记为 $\Sigma|\sigma$ (如果$\sigma$是全局截面那么 $\Sigma|\sigma=\Sigma$)，那么对于这些轨迹上的点  $\xi \in\Sigma|\sigma$ ，它在截面$\sigma$上的投影点是它的一个完备不变量。

对于  $\xi \in\Sigma|\sigma$ ，我们总能找到  $g\in G$ 使得它能通过群作用 $\phi$ 把$\xi$"拉"到截面 $\sigma$ 上，即 $\phi(g, \xi)=\sigma(p(\xi))$，其中 $p:\Sigma\to \Sigma/G$ 把$\Sigma$上的点映射为它对应的轨迹$p(\xi)$，$\sigma(p(\xi))$则代表 $\xi$ 所在的轨迹与轨迹截面 $\sigma$ 的交点。

依此，我们可以定义一个函数

$\gamma: \Sigma|\sigma\to G,\quad\xi\mapsto g=\gamma(\xi)\quad s.t.\quad \phi(\gamma(\xi),\xi)=\sigma(p(\xi))$

给定 $\xi\in \Sigma|\sigma$， $\gamma(\xi)$ 可以把 $\xi$ 拉到截面上，$\phi(\gamma(\xi),\xi)$ 为$\xi$在截面上的投影点，是$\xi$的一个完备不变量；

隐函数定理保证了存在这样一个（局部的）光滑函数  $\gamma(\xi)$ 使得 $\phi(\gamma(\xi),\xi)=\sigma(p(\xi))$；这个$\gamma$ 被称为 moving frame；

所选的的截面 $\sigma$决定了唯一的 moving frame $\gamma$，但反过来，如果给定 moving frame $\gamma$，也可以确定一个截面：对于截面上的点 $z\in \sigma$，我们有 $\phi(\gamma(z),z)=z$，从而  $\gamma(z)=e$，因此 $\gamma$ 的 kernel $\gamma(*)=e$也确定了截面 $\sigma$。

> 可以用纤维丛的语言来更准确地描述：

> 如果 $\Sigma$ 维数高于 $G$，$r\le s$，我们可以把 $\Sigma$ 看做一个以轨迹空间 $B=\Sigma/G$ 为底、结构群为 $G$的主纤维丛：$p: G\hookrightarrow \Sigma \to B(:=\Sigma/G)$.

> 每根纤维代表一条轨迹，微分同胚于 $G$；$G$ 对 $\Sigma$ 的作用（按照数学上主丛的符号习惯，一般假设这是个右作用）只会沿着纤维方向。

> 选定主丛的一个局部截面 $\sigma:U(\subset B)\to \Sigma$. （ $\forall x\in B,\quad p(\sigma(x))=x$）；

> 利用局部截面 $\sigma$，可以把每根纤维与$G$作等同：对于$x\in B$ 处的纤维  $p^{-1}(x)$，我们将截面上的点  $\sigma(x)$ 与$G$ 的单位元 $e$ 等同，再通过全局右作用$\phi$把此纤维与 $G$ 做等同：$\forall \xi\in p^{-1}(x), \quad \xi\mapsto g\quad s.t.\quad\phi_g(\xi) = \sigma(x) \quad \text{or equivalently } \quad \phi_{g^{-1}}(\sigma(x)) = \xi$  

> 于是我们可以得到一个映射 $\gamma: P|U \to G,\quad \xi\mapsto \gamma(\xi) \quad s.t.\quad\phi_{\gamma(\xi)}(\xi)=\sigma(p(\xi))$;

> 隐函数定理保证了存在局部的光滑函数 $\gamma: \Sigma|U \to G$ ,  使得   $\phi(\gamma(\xi),\xi)=\sigma(p(\xi))$，注意 $\sigma(p(\xi))$ 为 $\xi\in \Sigma$所在的纤维与局部截面 $\sigma$ 的交点；

#### 不变函数、不变坐标、不变标架场
 $\Sigma|\sigma$ 上在 $G$ 的作用下保持不变的函数，即 $J(\phi_g(\xi))=J(\xi)$ ，我们称之为（局部）G-不变函数；G-不变函数完全由其在截面 $\sigma$ 上的值决定；或者说，每个定义在截面 $\sigma$ 上的函数 $f_\sigma$ ，都能生成 $\Sigma|\sigma$ 上的一个G-不变函数 $f$:

$f(\xi) :=  f_{\sigma}(\sigma(p(\xi))) =  f_{\sigma}(\phi_{\gamma(\xi)}(\xi))$

如果我们为截面 $\sigma$ 选择一组坐标函数 $x^1_{\sigma},x^2_{\sigma}...x^{s-r}_{\sigma}$ ：在这组坐标函数下，$\sigma$ 上的点 $z$ 的坐标为 $(x^1_{\sigma}(z),x^2_{\sigma}(z)...x^{s-r}_{\sigma}(z))$ ；那截面上所有的函数 $f_\sigma$ 就都可以表示为这些坐标的函数；

> 坐标函数并不唯一，每组坐标函数对应一个 $\sigma\to \mathbb R^{s-r}$ 的微分同胚，有无穷多种选择，不同坐标函数之间只相差一个 $\mathbb R^{s-r}$ 上的微分同胚；

注意， $\sigma$ 上的坐标函数 $x^1_{\sigma},x^2_{\sigma}...x^{s-r}_{\sigma}$本身也可以分别生成  $\Sigma|\sigma$ 上的 $G$-不变函数 $x^1_b,x^2_b...x^{s-r}_b$，$x_b^i(\xi)=x_{\sigma}^i(\phi_{\gamma(\xi)}(\xi))$，这些函数可以作为 $\Sigma|\sigma$上的某种"不变坐标"：我们可以把 $\Sigma|\sigma$ 上的点也坐标化，且每个点的坐标分为两部分，一部分是沿着纤维方向的坐标（称为纤维坐标），另一部分是沿着截面方向的坐标（称为底坐标），这部分坐标是"不变坐标"。

具体来说，$\Sigma|\sigma$上截面附近的区域可以微分同胚到 $\sigma\times W$ 上（其中 $W$ 为$G$ 上单位元 $e$ 的一个开邻域），进而再映射到 $\mathbb R^n\times \mathfrak g$ 。纤维坐标定义在 $\mathfrak g$ 上（$\mathfrak g$ 上的点通过 $\exp$映射到 $W\subset G$），记为 $x^1_a,x^2_a...x^r_a$； 底坐标，即不变坐标则定义在 $\mathbb R^n$上， $G$-不变函数 $x^1_b,x^2_b...x^{s-r}_b$提供了一组这样的完备不变坐标（A complete set of s-r functionally independent invariants）。

坐标函数$\{y^1,y^2...y^r,  x^1,x^2...x^{s-r} \}$的微分 $\{W_i:=\frac{\partial}{\partial x^i}\}$ 可以在截面 $\sigma$ 上定义一个标架场，群作用 $\phi$又可以把这个标架场扩展到整个 $\Sigma|\sigma$：

 $W_i(\xi)=(T_\xi\phi_{\gamma(\xi)})^{-1}\frac{\partial}{\partial x^i}$ 

其中 $T_\xi\phi_{\gamma(\xi)}$代表 $\phi_{\gamma(\xi)}:\Sigma\to \Sigma$在 $\xi$ 处的切映射，它把 $\xi$ 处的切向量拉到截面上（它的逆则把截面上的向量映射为点 $\xi$ 处的向量）。

#### 一条关于 $\gamma$ 的性质
这里证明一条关于 $\gamma$ 的性质，这个在后面不变状态误差的定义等环节会涉及到。暂时可跳过。

若定义 $\eta(\xi_1,\xi_2) = \phi_{\gamma(\xi_1)}(\xi_2)$，那么 $\eta$ 是$G$-不变的，即

 $\eta(\phi_g(\xi_1),\phi_g(\xi_2)) = \eta(\xi_1,\xi_2)$

 $\phi_{\gamma(\phi_g(\xi_1))}(\phi_g(\xi_2)) = \phi_{\gamma(\xi_1)}(\xi_2)$

这条性质也可以描述为：$\phi_{\gamma(\phi_g(\xi))}(\phi_g(*)) = \phi_{\gamma(\xi)}(*)$

我们不妨先设群作用 $\phi$ 是左作用。

由于我们已假设 $\phi$ 是满秩的，$\phi(*,\xi): G\to \Sigma$ 是单射，且

* $\phi_{\gamma(\phi_g(\xi))}(\phi_g(\xi))=\phi_{\gamma(\xi)}(\xi)$，即 $\xi$ 在截面上的投影不会随着 $g$ 的作用变化；
* $\phi_{\gamma(\phi_g(\xi))}(\phi_g(\xi))=\phi_{\gamma(\phi_g(\xi))\cdot g}(\xi)$，  群作用的结合性质

所以 $\gamma(\xi)=\gamma(\phi_g(\xi))\cdot g$。

因此，$\phi_{\gamma(\phi_g(\xi))}(\phi_g(*)) = \phi_{\gamma(\phi_g(\xi))\cdot g}(*) = \phi_{\gamma(\xi)}(*)$.

> 如果 $\phi$ 是右作用，那么 $\gamma(\xi)=g\cdot \gamma(\phi_g(\xi))$。左或右不影响最后的结论。

---
### 完备不变量$I(\hat x,u)$ 和不变观测误差 $E(\hat x,u,y)$
首先，我们可以把状态空间 $\mathbb X$ 看做上节中的 $\Sigma$，$\varphi$看做上节的$\phi$；在 $\mathbb X$ 选择一个轨迹截面 $\sigma$，得到对应的moving frame  $\gamma$ ：$\varphi_{\gamma(x)}(x)=\sigma(p(x))$，生成一组完备不变坐标 $x^1_b,x^2_b...x^{n-r}_b$，$x_b^i(x)=x_{\sigma}^i(\varphi_{\gamma(x)}(x))$，和对应的不变标架场 $W_i(x)=(T_x\varphi_{\gamma(x)})^{-1}\frac{\partial}{\partial x^i}$；

> 注意我们要求 $\varphi$是满秩的群作用，但不对 $\psi$ 和 $\rho$ 作要求， $\psi$ 和 $\rho$ 甚至可以是平凡的作用。

> 只要 $\varphi$是满秩的作用，那么 $\varphi \times \psi\times \rho$ 就是满秩的作用：对任意 $\xi\in \mathbb X\times \mathbb U\times \mathbb Y$，$g\mapsto (\varphi \times \psi\times \rho)(g,\xi)$ 是 injective 的，因为单 $\mathbb X$ 上的分量已经是 injective 的。

$\mathbb X$上的 moving frame  $\gamma:\mathbb X\to G$  可以扩展到$\mathbb X\times \mathbb U \times \mathbb Y$ 上：

$\gamma: \mathbb X\times \mathbb U\times \mathbb Y\to G,\quad (x,u,y)\mapsto \gamma(x,u,y) := \gamma(x)$

此 moving frame 决定了 $\mathbb X\times \mathbb U\times \mathbb Y$ 上的截面 $\sigma\times \mathbb U\times \mathbb Y$ （可以验证$\sigma\times \mathbb U\times \mathbb Y$ 的点均属于不同的轨迹，无法通过乘一个 $g$ 使其中一个点变为另一个点）。

利用此 moving frame， $\mathbb X\times \mathbb U\times \mathbb Y$ 上的一种 G-不变量 即可表示为 

$\mathcal I(\hat x, u, y) = (\underbrace{\varphi_{\gamma(\hat x)}(\hat x), \quad \psi_{\gamma(\hat x)}(u)}_{I(\hat x,u)}, \quad \underbrace{\rho_{\gamma(\hat x)}(y)}_{J_h(\hat x,y)})$

注意，$\mathcal I(\hat x, u, y)$ 正是 $(\hat x, u, y)$在截面 $\sigma\times \mathbb U\times \mathbb Y$ 的投影。

容易验证：

* $I(\hat x,u)$ 是 $\mathbb X\times \mathbb U$ 上的不变量；$I(\hat x,u)$ 正是$(\hat x,u)$ 在截面 $\sigma\times \mathbb U$ 的投影。
* $E(\hat x, u, y) =J_h(\hat x, h(\hat x, u)) -  J_h(\hat x,y)$ 是一个不变观测误差。

> 注意，为了可读性，上面关于 $E$ 的定义中做了符号上的简化，其中出现了减号：如果$\mathbb Y$ 是欧氏空间，这没有问题；如果$\mathbb Y$ 是个流形，则需要写为

> $E(\hat x, u, y) = \mathcal Y(J_h(\hat x, h(\hat x, u))) -  \mathcal Y(J_h(\hat x,y))$

> 其中 $\mathcal Y$ 是 $\mathbb Y$上定义的一个坐标（$\mathbb Y\to \mathbb R^p$ 的一个局部微分同胚）；如果 $\mathcal Y$是局部定义的，那应该保证它在 $\rho_{\gamma(\hat x)}(y)$ 和 $\rho_{\gamma(\hat x)}(h(\hat x,u))$ 附近都有定义；

> 

> 在 $\mathbb U$ 上选择坐标 $\mathcal U$，$\mathbb Y$ 上选择坐标 $\mathcal Y$，那么 $\mathbb X\times \mathbb U\times \mathbb Y$ 上的一种不变坐标即可表示为  $\mathcal I(\hat x, u, y) = (x_\sigma(\varphi_{\gamma(\hat x)}(\hat x)), \quad \mathcal U( \psi_{\gamma(\hat x)}(u)),\quad \mathcal Y(\rho_{\gamma(\hat x)}(y)))$，

> 如果 $\mathbb U$ 上的坐标 $\mathcal U$是局部定义的，那应该保证它在 $\psi_{\gamma(\hat x)}(u)$ 附近有定义；

> 如果 $\mathbb Y$ 上的坐标 $\mathcal Y$是局部定义的，那应该保证它在 $\rho_{\gamma(\hat x)}(y)$  附近有定义；

每个不变观测误差 $\tilde E(\hat x,u,y)$ 都应该只是不变量  $I(\hat x,u)$  和 $J_h(\hat x,y)$ 的函数，而不依赖 $\hat x,u,y$在 G 作用下会变化的部分;  $J_h(\hat x,y)$ 又可以用 $\hat x, u, E$ 表示，因此每个不变观测误差 $\tilde E(\hat x,u,y)$ 都可以写为 $\hat x, u, E$ 的函数：

 $\tilde E(\hat x,u,y) = \mathcal L(I(\hat x,u), E(\hat x,u,y))$ 

注意给定 $I(\hat x,u)$ 时， $\tilde E$ 与 $E$ 之间的映射 $\mathcal L (I, *)$ 是可逆的 (误差定义的完备性)，且 $\mathcal L(I,0)=0$ （0误差要保持一致）；

---
### 不变状态误差
现在回到不变 Pre-Observer 的设计上，回顾一下不变 Pre-Observer 的形式：

$F(\hat x,u,y) = f(\hat x,u) + \sum_{i=1}^n \mathcal L_i(I(\hat x,u), E_{(\hat x,u,y)})W_i(\hat x)$

通过前面的介绍，我们已经知道该怎么设计不变量 $I(\hat x,u)$ 、不变标架场 $W_i$ 和不变观测误差$E$。剩下未解决的就是增益函数 $\mathcal L_i$ 的设计了。事实上我们并没有普遍适用的构造性方法来设计 $\mathcal L_i$ 并得到一个保证收敛的 估计器。

> We have no general constructive procedure to design the gain functions $\mathcal L_i$’s of theorem 1 in order to achieve systematic asymptotic convergence of towards for any non-linear system possessing symmetries.

做收敛分析时，一个关键环节是状态估计误差的定义。对于不变 Pre-Observer 的收敛性分析，我们使用 G-不变的状态估计误差。

其中一种 G-不变状态误差的定义为（用群$G$中的一个变换把 $x$拉到截面上后，再算误差）：

$\eta(x,\hat x) = \varphi_{\gamma(x)}(\hat x) - \varphi_{\gamma(x)}(x)$  

满足  $\eta(x,\hat x)=\eta(\varphi_g(x), \varphi_g(\hat x))$.

> 注意，为了可读性，上面的写法有一定的简化，其中出现了减号；如果状态空间$\mathbb X$ 是流形，无法定义减法，就把上式改为

> $\eta(x,\hat x) = x_b(\varphi_{\gamma(x)}(\hat x)) - x_b(\varphi_{\gamma(x)}(x))$

> 其中 $x_b$ 是之前定义的不变坐标，或者使用其他等价的不变坐标，转化为坐标后就可以做减法；

也可以等价地定义为 $\eta(\hat x, x) = \varphi_{\gamma(\hat x)}(x) - \varphi_{\gamma(\hat x)}(\hat x)$. （改为把 $\hat x$ 拉到截面上）

我们取第二种定义  $\eta:=\eta(\hat x, x)$  来分析：

1. $\eta:=\eta(\hat x, x)$ 是$G$-不变的：证明见前面介绍的 $\gamma$ 函数的性质。
2. 再考虑 误差的演化方程   $\frac{d}{dt}\eta$  :
   * 首先，可以证明  $\frac{d}{dt}\eta$ 也是 $G$-不变的（向量场）：注意观察下面两式的结构即可看出  $\frac{d}{dt}\eta$ 是 $G$-不变的，为了简化公式这里用了无噪声的情况，即假设 $y=h(x,u)$ 和 $\dot x=f(x,u)$。
$\begin{aligned} \frac{d}{dt}\eta(\hat x, x) &= \partial_1\eta.F(\hat x,u,h(x,u)) + \partial_2\eta.f(x,u)\\
\frac{d}{dt}\eta(\varphi_g(\hat x), \varphi_g(x)) &= \partial_1\eta.T\varphi_g.F(\hat x,u,h(x,u)) + \partial_2\eta.T\varphi_g.f(x,u)\\
&=\partial_1\eta.F(\varphi_g(\hat x),\psi_g(u),\rho_g(h(x,u))) + \partial_2\eta.f(\varphi_g(x),\psi_g(u))
 \end{aligned}$
   * 同时，注意到  $\frac{d}{dt}\eta$  是 $(x,\hat x, u)$ 的函数， $x$ 又可以用 $\eta$ 和 $\hat x$ 表示，所以   $\frac{d}{dt}\eta$  是 $(\eta, \hat x,u)$的函数；
   * 因此  $\frac{d}{dt}\eta$  可以写为不变量 $\eta$ 和 $I(\hat x,u)$的函数:

$\frac{d}{dt}\eta=\Upsilon(\eta, I(\hat x,u))$

3. 任意状态误差 $\tilde \eta$ 的定义都会依赖 $(x,\hat x)$ 或 $(x,\hat x, u)$， $x$ 又可以用 $\eta$ 和 $\hat x$ 表示，所以 $\tilde \eta$ 可以写作 $(\eta, \hat x,u)$ 的函数；如果 $\tilde \eta$ 是 G-不变的，那么它可写作 $\eta$ 和 $I(\hat x,u)$的函数：
$\tilde \eta(x,\hat x, u) = \mathcal F(I(x,u), \eta(\hat x,x))$
   * 注意给定 $I(\hat x,u)$ 时， $\tilde \eta$ 与 $\eta$之间的映射 $\mathcal F(I,*)$ 应该是可逆的 (误差定义的完备性)，且 $\mathcal F(I,0)=0$（0误差要保持一致）
   *   $\frac{d}{dt}\tilde \eta$ 也是 $G$-不变的（向量场），证明方法与上面   $\frac{d}{dt}\eta$ 的类似。

使用不变状态误差的一个好处是，可以降低误差演化方程对当前估计值 $\hat x$的依赖 。或者说是降低误差演化方程对真实状态 $x$ 的依赖，因为 $\hat x$ 可以用误差 $\eta$ 和 $x$表示。

> 比较理想的情况是，估计器 $F$的估计误差，不受真实状态轨迹$x_t$ 的起点  $x_0$ 影响；无论$x_t$ 起始于何处，$F$的估计误差都一样（有噪声时，误差的分布不变）。下节会再讨论到这种情况。

先回顾下常用的线性状态误差 $x\boxminus \hat x$ 的演化方程：

$\frac{d}{dt}(x\boxminus \hat x)=\dot x-\dot {\hat x} = f(x,u) - F(\hat x,u,h(x,u))$

上式对 $\hat x$ 和 $u$ 的 Jacobian 一般是满秩的，因此这个方程对 $(\hat x,u)$ 的依赖一般是全维度的 ，即$n+m$ 维；

再看不变状态误差$\eta$的演化方程

 $\frac{d}{dt}\eta=\Upsilon(\eta, I(\hat x,u))$ 

可以看出，通过使用不变状态误差 $\eta$ 代替常用的线性误差 $x\boxminus \hat x$，方程对 $(\hat x,u)$ 的依赖将仅限于不变量 $I(\hat x,u)$ （即截面）所在的 $n+m-r$ 个维度上 ($r\le n$)；

这反映出，使用不变状态误差可以降低演化方程对当前估计值 $\hat x$ 的依赖。

### 误差演化方程 $\frac{d}{dt}\eta$ 对 $\hat x$ 的依赖
但即便使用了不变误差 $\eta$，误差的演化方程依然对  $\hat x$ 有所依赖（依赖$\hat x$ 在截面 $\sigma$ 上的投影）；对 $\eta$ 和 $\hat x$  的依赖等价于对$\eta$ 和真实状态  $x$  的依赖，因为 $\hat x$ 可以用误差 $\eta$ 和 $x$表示。这就会导致 Pre Observer 的误差（分布）和收敛性仍然会受到真实状态轨迹 $x_t$的起点  $x_0$  影响。我们希望，无论$x_t$ 起始于何处，$F$的估计误差都一样（有噪声时，误差的分布不变）。

如果误差演化方程不受估计的状态轨迹 $\hat x_t$ 或 $x_t$ 的影响，即

$\frac{d}{dt}\eta=\Upsilon(\eta, I(\hat x,u))=\Upsilon(\eta, I(e,u))=\Upsilon(\eta, u)$

这样的误差 $\eta$ 称为 state trajectory independent [4\] 的，或者 autonomous [1\] 的，或者 intrinsic [2\] 的。这时误差的演化不依赖 $\hat x_t$ ，这与线性 Kalman filter 的情况就很相似，系统将具有很好的收敛性。

想让上式成立，要么 $\Upsilon$ 不依赖 $I$（这个假设没有很好的可操作性），要么 $(\hat x,u)$在截面 $\sigma \times \mathbb U$ 上的投影

 $I(\hat x,u)=(\varphi_{\gamma(\hat x)}(\hat x), \quad \psi_{\gamma(\hat x)}(u))$

只依赖$u$。这也意味着两点：

1. $\varphi_{\gamma(\hat x)}(\hat x)$是个常量。因此 $\mathbb X$ 上的截面 $\sigma$ 上只有一个孤立点，或者说 $\mathbb X$ 上只有一条轨迹，因此 $\mathbb X$ 可与 $G$等同，$\mathbb X=G$；所以不妨令 $\sigma = \{e\}$，$\varphi_{\gamma(\hat x)}(\hat x)=e$；此时
$\frac{d}{dt}\eta=\Upsilon(\eta, I(\hat x, u))=\Upsilon(\eta,   \quad \psi_{\gamma(\hat x)}(u))$ 
2. $\psi_{\gamma(\hat x)}(u)$ 不依赖 $\hat x$。而 $\gamma$ 又是满射，因此对所有 $g\in G$ 有 $\psi_g(u)=\psi_e(u)=u$，即 $\psi$是$G$在 $\mathbb U$上的平凡作用；此时，
$\frac{d}{dt}\eta=\Upsilon(\eta, I(\hat x, u))=\Upsilon(\eta,   \quad u)$ 
这两点假设将出现在下面的 Invariant EKF 中。

---
## Invariant EKF 
前面提到，不变误差$\eta$ 的演化方程对 $(\hat x,u)$ 的依赖仅限于不变量 $I(\hat x,u)$ 所在的 $n+m-r$ 个维度上 ($r\le n$)；

Invariant EKF，就是通过把状态空间 $\mathbb X$与变换群等同（从而$n=r$），使得这个依赖降低到 $m$ 维，此时 $I(\hat x, u)\equiv \psi_{\gamma(\hat x)}(u)$：

   * 如果群$G$在自身的作用 $\varphi_g$ 是左或右乘$g$，那么从 $\varphi_{\gamma(\hat x)}(\hat x)=e$ 可得${\gamma(\hat x)}=\hat x^{-1}$，$\psi_{\gamma(\hat x)}(u)=\psi_{\hat x^{-1}}(u)$;
   * 如果群$G$在自身的作用 $\varphi_g$ 是左或右乘$g^{-1}$，那么从 $\varphi_{\gamma(\hat x)}(\hat x)=e$ 可得${\gamma(\hat x)}=\hat x$，$\psi_{\gamma(\hat x)}(u)=\psi_{\hat x}(u)$;

[3\] 中介绍的 IEKF （连续时间+连续观测）就讨论了这种情况

* 假设$f(x,u)$ 和 $h(x,u)$ 在群 $G$ 的作用下具备不变性
* 假设过程噪声和观测噪声也具备不变性
* 不假设$G$在 $\mathbb U$上的作用 $\psi$ 是平凡的，从而误差演化方程依然对 $\hat x$ 存在依赖（通过依赖不变量$\hat I:=I(\hat x, u)\equiv \psi_{\gamma(\hat x)}(u)$）

将李群上的估计误差$\eta$ 通过指数映射转化为李代数上的误差 $\xi$ 并线性化后，可以得到误差演化的 jacobian $A$ 和 观测误差的 jacboian $C$ ；由于误差$\eta$ 的演化方程对 $\hat x$ 的依赖仅限于不变量$\hat I$，所以 $A$ 和 $C$ 都是只依赖$\hat I$的，从而协方差矩阵$P$的演化、卡尔曼增益矩阵 $K$都是只依赖$\hat I$ （注意噪声的不变性也很重要，否则 $\hat x$ 将通过噪声的协方差矩阵影响$P$ 和 $K$）。见 [3\] 中的 II.C。

根据变换群$G$作用的方式不同，作者把 IEKF 分为两种：

* 当群$G$在 $(\mathbb X=G,\mathbb U,\mathbb Y)$ 上的作用是左作用时，左不变的状态误差定义为 $\eta=x^{-1}\hat x$ ，这样的 IEKF 称为左不变 EKF (LIEKF)；
* 当群$G$在 $(\mathbb X=G,\mathbb U,\mathbb Y)$ 上的作用是右作用时，右不变的状态误差定义为 $\eta=\hat xx^{-1}$ ，这样的 IEKF 称为右不变 EKF (RIEKF)；

[2\] 中介绍的 IEKF （也是连续时间+连续观测）则额外假设了$G$在 $\mathbb U$上的作用 $\psi$ 是平凡的，控制量 $u$ 只依赖于时间 $t$，或者直接把 $u$ 写作 $t$；这样一来，不变量 $\hat I=t$，误差演化方程对状态轨迹 $\hat x_t$ 完全无依赖！这时系统与线性 Kalman Filter 就有很强的相似性： Jacobian ，协方差矩阵 $P$ 的演化和卡尔曼增益 $K$ 都不依赖 $\hat x$。这正是 IEKF 相比传统 EKF 的优势所在！

本文后面将着重介绍 "连续时间+离散观测" 的 IEKF。

由于常见的实际问题中，$G$在 $\mathbb U$上的作用 $\psi$ 一般是平凡的（这有利于去除误差演化方程对状态轨迹 $\hat x_t$ 的依赖），且控制量$u$不影响观测函数 $h$，从而 $f(x,u)$ 和 $h(x,u)$ 可分别写作 $f_u(x)$ 和 $h(x)$。后面也将默认这两点假设。

但要求过程、观测、以及两者的噪声都具备不变性，其实是个很强的要求。有些不变性要求可以适当放宽，但也有些不变性，如果不满足的话，IEKF 将损失一部分良好性质：

* 可以一定程度上放宽对系统演化方程 $f_u(x)$ 的不变性要求：只要 $f_u$ 满足一个稍微宽松些的条件$f_u(ab)=af_u(b)+f_u(a)b-af_u(Id)b$，误差 $\eta$ 的 propagation 过程 $\dot \eta_{prop}$ 就不依赖 $\hat x$，从而过程 jacobian 不依赖 $\hat x$;
   * 后面会解释到两种特例：如果使用 LIEKF，那么右不变的  $f_u(x)$ 将为计算带来便利；如果使用 RIEKF，那么左不变的  $f_u(x)$ 将为计算带来便利；
* 如果噪声不符合系统的不变性：比如噪声没有不变性、或者噪声的不变性(左/右)与系统的不变性相反，那么状态协方差 $P$ 和卡尔曼增益 $K$ 的计算将通过噪声的协方差矩阵对$\hat x$产生依赖；但好在噪声的协方差矩阵对系统的 stability 收敛性影响还是较为 轻微的（in a minor way）

> [4\] Theorem 3 + Theorem 4: 

> due to the error equation of the IEKF, the Riccati equation depends on the estimate **only through the matrices Qt and Nt** which affect stability in a **minor** way, as shown by Theorem 3.

* 如果观测函数$h(x)$没有不变性，观测误差的 jacobian 将依赖 $\hat x$：这个影响就比较大了。如果观测函数$h(x)$没有不变性，IEKF 依然可以解决 EKF 的能观性（Covariance 的一致性）问题，但收敛性就无法再被保证，因为观测误差的 jacobian 将对 $P$ 和 $K$ 产生比较显著的影响。

注意，在"连续时间+离散观测" 的 EKF 框架下，状态误差 $\eta$ 在 propagation 环节连续变化，在观测发生时会有个修正（跳变）。propagation 环节  $\eta$ 的演化用 $\dot \eta_{prop}$表示，严格来讲它跟之前提到的 $\dot \eta$ （连续观测框架下的估计误差的演化）有所区别，因为$\dot \eta$ 是耦合了连续的观测信息的，不仅仅是简单的 propagation 误差；但为了符号上的方便，下文依然用  $\dot \eta$ 来表示 $\dot \eta_{prop}$。

### 李群上的不变状态误差 $\eta$ 及其不依赖状态轨迹的条件
设 $\hat x$ 和 $x$ 是过程微分方程  $\dot x=f_u(x)$ 的两条轨迹。若状态空间 $\mathbb X=G$ 是个李群，那么我们有左、右不变误差如下：

$\eta^L=x^{-1}\hat x = (gx)^{-1} (g\hat x)$

$\eta^R=\hat x x^{-1} = (\hat xg) (xg)^{-1}$

如果希望如上定义的左、右不变误差的演化是不依赖轨迹的， $f_u$ 应该满足什么条件？

一个重要的结论，下面三个条件等价：

1. $\eta^L$ 的演化不依赖轨迹： $\dot \eta^L=g_u^L(\eta)$
2. $\eta^R$ 的演化不依赖轨迹： $\dot \eta^R=g_u^R(\eta)$
3. $f_u$ 满足 $f_u(ab)=f_u(a)b+af_u(b)-af_u(Id)b$

> 为了符号上的简化，这里出现了群元和切向量相乘，理解为对应群元左、右乘的切映射即可。

其中

$g_u^L(\eta)=f_u(\eta)- f_u(Id) \eta$

$g_u^R(\eta)=f_u(\eta)- \eta f_u(Id)$



我们这里证明 1 和 3 的等价性。先证 $1\Rightarrow 3$ ：

$\begin{aligned} \dot \eta^L &= (\dot{\overbrace{x^{-1}}}) \hat x + x^{-1}\dot{\hat x} \\
&= -x^{-1}\dot xx^{-1}\hat x + x^{-1}\dot{\hat x} \\
&= -x^{-1}f_u(x)\eta + x^{-1}f_u(\hat x) \\
&= -x^{-1}f_u(x)\eta + x^{-1}f_u(x\eta) \\
\end{aligned}$

上面第二个等号用到了 $\dot{\overbrace{x^{-1}}} = -x^{-1}\dot xx^{-1}$，这是因为：

$0=\dot e = \dot {\overbrace{xx^{-1}}}=x(\dot{\overbrace{x^{-1}}}) + \dot x x^{-1}\qquad \Rightarrow \qquad \dot{\overbrace{x^{-1}}} = -x^{-1}\dot xx^{-1}$

如果 $\dot \eta^L$ 不依赖轨迹，我们可以直接令 $x=Id$ 来计算 $\dot \eta^L$ ：

$\begin{aligned} \dot \eta^L &= -Id^{-1}f_u(Id)\eta + Id^{-1}f_u(Id\eta) \\
&= f_u(\eta) - f_u(Id)\eta = g^L_u(\eta)
\end{aligned}$

因此有：

$\begin{aligned} x\dot \eta^L &= -f_u(x)\eta + f_u(x\eta) \\
&= xf_u(\eta) - xf_u(Id)\eta =  xg^L_u(\eta) \\
\end{aligned}$

即 $f_u(x\eta) = f_u(x)\eta + xf_u(\eta) - xf_u(Id)\eta$；由于 $x,\eta$选取的任意性， $1\Rightarrow 3$ 得证。

反向 $3\Rightarrow 1$的证明按上述步骤反推回去即可。



#### Remark
为了使左、右不变误差不依赖状态轨迹，我们并不需要 $f_u$ 一定是左或右不变的，只要它满足  $f_u(ab)=f_u(a)b+af_u(b)-af_u(Id)b$ 即可，比如 $f_u$ 可以分解为两个左不变、右不变系统的和。

当然，如果 $f_u$ 是左或右不变的，那么上述条件会被自动满足：

* 若 $f_u$ 是左不变的，那么 $f_u(ab)=af_u(b),f_u(a)b=af_u(Id)b$;
* 若 $f_u$ 是右不变的，那么 $f_u(ab)=f_u(a)b,af_u(b)=af_u(Id)b$;

可见，IEKF 对系统演化方程 $f_u$ 的不变性要求（相比之前介绍的 Symmetry Preserving Observer）实际上有轻微放宽。

---
### 左、右不变观测
左不变观测的形式为： $y=x.d$ （可从 $h(x)$ 的左不变性质得来：$d:=h(e), x.d=h(x)=y$）

右不变观测的形式为： $y=x^{-1}.d$ （或 $y=d.x$）;

左不变观测误差定义为： $\hat x^{-1} y - \hat x^{-1} \hat y = \hat x^{-1} x.d - d = (\eta^L)^{-1}.d-d$

右不变观测误差定义为： $\hat x y - \hat x \hat y = \hat x x^{-1}.d - d = (\eta^R).d-d$

> 为了符号上的方便，这里暂时假设观测空间是欧氏空间，所以上面观测误差的定义中可以出现减号。如果观测空间是流形，需要先在 $d$ 附近定义一个局部的坐标，再对坐标用减号；不同的局部坐标定义了不同的不变观测误差，根据观测量的物理意义选择合适的局部坐标，比如如果观测空间是李群，可以借助李代数定义局部坐标。

可见，左、右不变观测误差可分别与左、右不变状态误差自然地联系起来。

所以，

* 如果观测模型（观测误差）是左不变的，必须使用左不变状态误差；
* 如果观测模型（观测误差）是右不变的，必须使用右不变状态误差；

---
### 过程噪声的引入和状态误差的线性化

本节假设过程噪声是左不变的，

$\dot x=f_u(x) + x.w$

其中噪声项$w$定义在李代数上，通过左乘 $x$ 作用到 $x$ 处。

> 如果假设噪声是右不变的，对应方程应写为 $\dot x=f_u(x)+w.x$，后续公式的推导也需跟着适配。

之前定义的不变状态误差 $\eta$ 是李群上的误差，不太方便操作，我们把它也转移到李代数上：

$\exp(\xi)=\eta$

我们需要得到李代数的误差 $\xi$ 的演化方程。

利用

$\dot {\hat x}=f_u(\hat x)$

$\dot x=f_u(x) + x.w$

可得

$\dot \eta=g_u(\eta) + \hat w\eta$

其中 $\hat w$ 是转化过的噪声，其具体形式后面会分左、右不变状态误差两种情况给出，$g_u$ 在不同误差下也有不同形式。

考虑到$\dot \eta \eta^{-1}\in \mathfrak g$，

$\dot \eta=(\dot \eta \eta^{-1})\eta= \frac{d\exp((\dot \eta \eta^{-1}) t)}{dt}\eta= \frac{d\exp((\dot \eta \eta^{-1}) t)\exp(\xi)}{dt}= \frac{d\exp(\xi + J_l^{-1}(\xi)(\dot \eta \eta^{-1})t)}{dt}$

因此

$\dot \xi=J_l^{-1}(\xi)(\dot \eta \eta^{-1})$

$\dot \eta \eta^{-1}$ 和  $\xi$ 都是小量时，略去二阶以上小量，得  $\dot \xi\approx \dot \eta \eta^{-1}$；

下面对左、右不变误差两种情况分别给出具体形式。

#### 左不变状态误差的处理
 $\eta = x^{-1}\hat x$

$\begin{aligned} \dot \eta &= (\dot{\overbrace{x^{-1}}}) \hat x + x^{-1}\dot{\hat x} \\
&= -x^{-1}\dot xx^{-1}\hat x + x^{-1}\dot{\hat x} \\
&= -x^{-1}(f_u(x)+x.w)\eta + x^{-1}f_u(\hat x) \\
&= -x^{-1}f_u(x)\eta - w.\eta + x^{-1}f_u(\hat x)\\
&= g_u(\eta) - w.\eta\\
&= g_u(\eta) + \hat w.\eta\\
\end{aligned}$

注意最后一行定义了$\hat w=-w$;

$\dot \eta \eta^{-1}=g_u(\eta)\eta^{-1} + \hat w= f_u(\eta)\eta^{-1} - f_u(Id) + \hat w$

> 对于左不变误差，$g_u(\eta)=f_u(\eta)- f_u(Id) \eta$

考虑 $f_u$ 的两个常见特例：

* 特例：若 $f_u$ 本身也是左不变的，那么$f_u(\eta)\eta^{-1}=\eta f_u(Id)\eta^{-1}$ ，于是
   * $\dot \eta \eta^{-1}= (Ad_{\exp(\xi)}-I_{\mathfrak g})f_u(Id) + \hat w = (e^{ad_{\xi}}-I_{\mathfrak g})f_u(Id) + \hat w$  
   * $\dot \xi=J_l^{-1}(\xi)(\dot \eta \eta^{-1})=J_l^{-1}(\xi).(e^{ad_{\xi}}-I_{\mathfrak g}).f_u(Id) + J_l^{-1}(\xi).\hat w$
   * 1st order approximation: 
      * $\dot \xi\approx \dot\eta\eta^{-1} = (e^{ad_{\xi}}-I_{\mathfrak g})f_u(Id) + \hat w \approx (ad_{\xi})f_u(Id)-  w=[\xi, f_u(Id)]- w$
      * $A_u=-ad_{f_u(Id)}$, $G=-I_{\mathfrak g}$
* 特例：若 $f_u$ 本身是右不变的，那么 $f_u(\eta)=f_u(Id)\eta$，  $g_u(\eta)=0$ ，于是
   * $\dot \eta \eta^{-1}=\hat w$
   * $\dot \xi=J_l^{-1}(\xi)(\dot \eta \eta^{-1})=J_l^{-1}(\xi).\hat w$
   * 1st order approximation: 
      * $\dot \xi\approx \dot\eta\eta^{-1} = \hat w=-w$
      * $A_u=0$,  $G=-I_{\mathfrak g}$

> 如果左不变误差定义为 $\hat x^{-1}x$ (instead of $x^{-1}\hat x$) ，那 $A_u$ 和 $G$ 与上面给出的相比都应再取负；

#### 右不变状态误差的处理
 $\eta = \hat xx^{-1}$

$\begin{aligned} \dot \eta &= \dot{\hat x}x^{-1} + \hat x(\dot{\overbrace{x^{-1}}})  \\
&= \dot{\hat x}x^{-1} - \hat xx^{-1}\dot xx^{-1} \\
&= f_u(\hat x)x^{-1} - \eta (f_u(x) + x.w) x^{-1} \\
&= f_u(\hat x)x^{-1} - \eta f_u(x)  x^{-1} - \eta.x.w. x^{-1} \\
&= f_u(\hat x)x^{-1} - \eta f_u(x)  x^{-1} - \hat x.w. {\hat x}^{-1} \eta \\
&= g_u(\eta) - Ad_{\hat x}(w)\eta\\
&= g_u(\eta) + \hat w.\eta\\
\end{aligned}$

注意最后一行定义了 $\hat w=-Ad_{\hat x}(w)$;

$\dot \eta \eta^{-1}=g_u(\eta)\eta^{-1} + \hat w=f_u(\eta)\eta^{-1}-\eta f_u(Id)\eta^{-1} + \hat w$

> 对于右不变误差，$g_u(\eta)=f_u(\eta)- \eta f_u(Id)$

考虑 $f_u$ 的两个常见特例：

* 特例：若 $f_u$ 本身也是右不变的，那么 $f_u(\eta)\eta^{-1}=f_u(Id)\eta\eta^{-1}=f_u(Id)$，于是
   * $\dot \eta \eta^{-1}= (I_{\mathfrak g}-Ad_{\exp(\xi)})f_u(Id) + \hat w= (I_{\mathfrak g}-e^{ad_{\xi}})f_u(Id) + \hat w$
   * $\dot \xi=J_l^{-1}(\xi)(\dot \eta \eta^{-1})=J_l^{-1}(\xi).(I_{\mathfrak g}-e^{ad_{\xi}}).f_u(Id) + J_l^{-1}(\xi).\hat w$
   * 1st order approximation: 
      * $\dot \xi\approx \dot\eta\eta^{-1} =(I_{\mathfrak g}-e^{ad_{\xi}})f_u(Id) + \hat w \approx -ad_{\xi}.f_u(Id) - Ad_{\hat x}w  = [f_u(Id), \xi] - Ad_{\hat x}w$
      * $A_u=ad_{f_u(Id)}$, $\hat G=-Ad_{\hat x}$
* 特例：若 $f_u$ 本身是左不变的，那么 $g_u(\eta)=0$，于是
   * $\dot \eta \eta^{-1}= \hat w$
   * $\dot \xi=J_l^{-1}(\xi)(\dot \eta \eta^{-1})=J_l^{-1}(\xi).\hat w$
   * 1st order approximation: 
      * $\dot \xi\approx \dot\eta\eta^{-1} = \hat w=-Ad_{\hat x}w$
      * $A_u=0$,  $\hat G=-Ad_{\hat x}$

> 如果右不变误差定义为 $x\hat x^{-1}$(instead of $\hat xx^{-1})$，那 $A_u$ 和 $\hat G$ 与上面给出的相比都应再取负；

#### Remark
可见，右不变 $f_u$ 使用 左不变误差（LIEKF）、左不变 $f_u$ 使用右不变误差（RIEKF），可以简化$A_u$，使其为0；对于其他情况，$A_u$ 是会依赖 $u$ 的。注意 IEKF 允许 $f_u$ 既非左不变又非右不变。

* 如果 $f_u$本身是左不变的，$x.u:=x.f_u(Id)=f_u(x)$，选用 RIEKF 时状态转移的计算会简单一些，因为 $A_u=0$;
* 如果  $f_u$本身是右不变的，$u.x:=f_u(Id).x=f_u(x)$，选用 LIEKF 时状态转移的计算会简单一些，因为 $A_u=0$;
* 但注意，到底选择 RIEKF 还是 LIEKF，由观测模型决定：左不变观测对应 LIEKF，右不变观测对应 RIEKF；如果观测本身不具备不变性，则可考虑按上面两条来选择更方便计算的 IEKF；
* 使用 RIEKF 时，系统演化方程$f_u$ 既可以是做不变的也可以是右不变的，甚至可以既非左不变又非右不变，只要它满足 $f_u(ab)=f_u(a)b+af_u(b)-af_u(Id)b$ 即可； LIEKF 同理。
* 由于本节噪声$w$的定义是左不变的 ($x.w$)，所以对于 RIEKF，$G$ 中会出现对状态轨迹 $\hat x$  的依赖；如果噪声$w$的定义是右不变的 ($w.x$)，那么 LIFEK 的 $G$ 中会出现对状态轨迹 $\hat x$  的依赖；过程噪声 $w$ 可以是受 $u$ 控制的 $w_u$，但不能受 $x$ 或 $\hat x$ 控制，否则，$\dot \xi$ 将依赖 $x$ 或 $\hat x$，就彻底破坏了误差演化不依赖状态轨迹的原则。

---
### 观测噪声的引入与观测的线性化
设 $G$ 通过 

 $\rho: G\times \mathbb Y\to \mathbb Y$ 

作用在 观测空间 $\mathbb Y$ 上；

观测函数为 

$h:=\rho^d:=\rho(*,d): G\to \mathbb Y,\quad g\mapsto h(g):=\rho(g,d)$

$d=h(e)$

令 $h_e':=T_eh:\mathfrak g\to T_{d}\mathbb Y$ 为单位元处的切映射，$h_e'$ 可写作 $\frac{\partial h}{\partial x}|_{x=e}$ ；如果 $\mathbb Y$ 是欧氏空间，$T_d\mathbb Y\equiv\mathbb Y$;



#### 左不变观测
左不变观测函数为 $h: G\to \mathbb Y,\quad x\mapsto x.d$

引入左不变噪声 $B$： $y=\rho(x,d\boxplus B)=x.(d\boxplus B)=(x.d)\boxplus (x.B)$  

> $B=Nv$，$v$是标准的高斯随机向量，$N$ 是$d\in \mathbb Y$ 处的一组列向量，把 $v$映射到$d\in \mathbb Y$ 处的切空间上

左不变观测误差定义为：(这里使用$\eta = \eta^L$ 为左不变状态误差)

$\begin{aligned} \tilde y_\xi=\hat x^{-1}y \boxminus \hat x^{-1}\hat y &=\hat x^{-1}y \boxminus  d \\
&=((\hat x^{-1}x.d)\boxplus (\hat x^{-1}x.B)) \boxminus d\\
&=((\eta^{-1}.d)\boxplus (\eta^{-1}.B)) \boxminus d\\
&=((\exp(-\xi).d)\boxplus (\exp(-\xi).B)) \boxminus d\\
\end{aligned}$

注意噪声 $B$ 和观测误差 $\tilde y_{\xi}$ 都是 $d\in \mathbb Y$ 处的切向量；

上式可以看出 $\tilde y_{\xi}$ 确实不依赖 $\hat x$，而只依赖误差 $\xi$；

将上式做线性化近似：

$\begin{aligned} \tilde y_\xi&=((\exp(-\xi).d)\boxplus (\exp(-\xi).B)) \boxminus d\\
&=(((e\boxminus \xi).d)\boxplus ((e\boxminus \xi).B)) \boxminus d\\
&\approx -\xi.d + B - \xi.B \\
&\approx -\xi.d + B \\
&= -h_e'.\xi +B
\end{aligned}$

其中 $\xi.d:=h_e'.\xi=T_eh.\xi=\frac{\partial h}{\partial x}(\xi)|_{x=e}$ ，也是 $d\in \mathbb Y$ 处的切向量；

另外上式第4行略去了噪声和误差 $\xi$ 的交叉二次项  $\xi.B:=\frac{\partial^2\rho}{\partial x\partial y}(\xi,B)|_{x=e,y=d}$。

可以看到，观测误差的 jacobian 为

 $H=-h_e'$

不变噪声项 $B$ （的协方差矩阵 $N$）也不依赖于 $\hat x$.

> 如果左不变误差定义为 $\hat x^{-1}x$ (instead of $x^{-1}\hat x$) ，那么$H$ 与现在相比将差一个负号，$H=h_e'$



如果再引入非左不变噪声 $V$: $y=(x.d)\boxplus (x.B+ V)$  

那么 $\hat x^{-1}y=(\hat x^{-1}x.d)\boxplus (\hat x^{-1}.(x.B+ V))=(\eta^{-1}.d)\boxplus (\eta^{-1}.B + \hat x^{-1}.V)$，

> 注意， $x.B$ 和 $V$ 同作为 $x.d$ 处的切向量，可以相加；

> 对于$\hat x^{-1}.(x.B+ V)$ ，由于它 实际上是 $T_{(\hat x.d)}\rho_{\hat x^{-1}}(x.B+V)$ 的简写，而$T_{(\hat x.d)}\rho_{\hat x^{-1}}$作为 $\rho_{\hat x^{-1}}$ 在$(\hat x.d)$处的切映射，它是线性的，因此有 $\hat x^{-1}.(x.B+ V) = \eta^{-1} B + \hat x^{-1}V$ ，

> $T_{(\hat x.d)}\rho_{\hat x^{-1}}$把$(\hat x.d)$处的切向量映射为 $d$ 处的切向量.

这时线性化后的观测误差为：

$\begin{aligned} \tilde y_\xi&=((\exp(-\xi).d)\boxplus (\exp(-\xi).B + \hat x^{-1}.V)) \boxminus d\\
&=(((e\boxminus \xi).d)\boxplus ((e\boxminus \xi).B + \hat x^{-1}.V))) \boxminus d\\
&\approx -\xi.d + B - \xi.B + \hat x^{-1}.V \\
&\approx -\xi.d + B + \hat x^{-1}.V\\
&= -h_e'.\xi +(B+ \hat V)
\end{aligned}$

可见，噪声项中多了一个依赖 $\hat x$ 的 $\hat V:=\hat x^{-1}.V$，这会使得噪声项 $(B+\hat V)$ 整体的协方差矩阵 $\hat N$ 是 $\hat x$ 的函数。



#### 右不变观测
右不变观测函数为 $h: G\to \mathbb Y,\quad x\mapsto x^{-1}.d$

引入右不变噪声 $V$： $y=\rho(x,d\boxplus V)=x^{-1}.(d\boxplus V)=x^{-1}.d\boxplus x^{-1}.V$  

> $V=Nv$

右不变观测误差定义为：(这里使用$\eta = \eta^R$ 为右不变状态误差)

$\begin{aligned} \tilde y_\xi=\hat xy \boxminus \hat x\hat y  &=\hat xy \boxminus d  \\
&=((\hat xx^{-1}.d)\boxplus (\hat xx^{-1}.V)) \boxminus d\\
&=((\eta.d)\boxplus (\eta.V)) \boxminus d\\
&=((\exp(\xi).d)\boxplus (\exp(\xi).V)) \boxminus d\\
\end{aligned}$

注意噪声 $V$ 和观测误差 $\tilde y_{\xi}$ 都是 $d\in \mathbb Y$ 处的切向量；

上式可以看出 $\tilde y_{\xi}$ 确实不依赖 $\hat x$，而只依赖误差 $\xi$；

将上式做线性化近似：

$\begin{aligned} \tilde y_\xi&=((\exp(\xi).d)\boxplus (\exp(\xi).V)) \boxminus d\\
&=(((e\boxplus \xi).d)\boxplus ((e\boxplus \xi).V)) \boxminus d\\
&\approx \xi.d + V + \xi.V \\
&\approx \xi.d + V \\
&= -h_e'.\xi +V
\end{aligned}$

其中 $\xi.d:=-h_e'.\xi=-T_eh.\xi=-\frac{\partial h}{\partial x}(\xi)|_{x=e}$ ，也是 $d\in \mathbb Y$ 处的切向量；

另外上式第4行略去了噪声和误差 $\xi$ 的交叉二次项  $\xi.V:=-\frac{\partial^2\rho}{\partial x\partial y}(\xi,V)|_{x=e,y=d}$。

可以看到，观测误差的 jacobian 为

 $H=-h_e'$

不变噪声项 $V$ （的协方差矩阵 $N$）也不依赖于 $\hat x$.

注意与左不变的公式的对比。由于观测函数 $h$ 的定义不同，对于左不变， $\xi.d=h_e'.\xi$; 对于右不变，则是 $\xi.d=-h_e'.\xi$，差个负号；但是，两种情况下，观测误差对 $\xi$ 的 jacobian 都正好写为 $-h_e'$。

> 如果右不变误差定义为 $x\hat x^{-1}$(instead of $\hat xx^{-1})$，那么$H$ 与现在相比将差一个负号，$H=h_e'$

如果再引入非右不变噪声 $B$: $y=(x^{-1}.d)\boxplus (x^{-1}.V+ B)$  

那么 $\hat xy=(\hat xx^{-1}.d)\boxplus (\hat x.(x^{-1}.V+ B))=(\eta.d)\boxplus (\eta.V + \hat x.B)$，

这时线性化后的观测误差为：

$\begin{aligned} \tilde y_\xi&=((\exp(\xi).d)\boxplus (\exp(\xi).V + \hat x.B)) \boxminus d\\
&=(((e\boxplus \xi).d)\boxplus ((e\boxplus \xi).V + \hat x.B))) \boxminus d\\
&\approx \xi.d + V + \xi.V + \hat x.B \\
&\approx \xi.d + V +\hat x.B\\
&= -h_e'.\xi +(V+ \hat B)
\end{aligned}$

可见，噪声项中多了一个依赖 $\hat x$ 的 $\hat B:=\hat x.B$，这会使得噪声项 $(V+\hat B)$ 整体的协方差矩阵 $\hat N$ 是 $\hat x$ 的函数。



#### 非不变观测
非不变观测是最一般的情况，此时观测方程为：

$y=h(x)+\mathbf n$

观测误差直接使用线性化误差即可。

对于左、右不变状态误差 $\eta^L,\eta^R$（分别对应李代数上的 $\xi^L,\xi^R$），观测方程的线性化分别如下：

$\begin{aligned} \tilde y_{\xi} = y-\hat y &=h(x) - h(\hat x) +\mathbf n\\
&=h(\hat x (\eta^L)^{-1})- h(\hat x) +\mathbf n\\
&=h(\hat x \exp(-\xi^L))- h(\hat x) +\mathbf n\\
&\approx -(h_{\hat x}'.T_el_{\hat x}).\xi^L + \mathbf n
\end{aligned}$

$\begin{aligned} \tilde y_{\xi} = y-\hat y &=h(x) - h(\hat x) +\mathbf n\\
&=h((\eta^R)^{-1}\hat x)- h(\hat x) +\mathbf n\\
&=h(\exp(-\xi^R)\hat x )- h(\hat x) +\mathbf n\\
&\approx -(h_{\hat x}'.T_er_{\hat x}).\xi^R + \mathbf n
\end{aligned}$

其中，$l_{\hat x},r_{\hat x}$ 分别代表左乘$\hat x$和右乘$\hat x$的函数，$T_el_{\hat x},T_er_{\hat x}$ 则分别代表它们在单位元处的切映射，它们负责把单位元处的切向量 $\xi$ 变换到 $\hat x$ 处。

> 如果 $\mathbb Y$ 不是欧氏空间，则需要在 $h(\hat x)$ 附近定义合适的局部坐标后再做上式中的加减法，注意局部坐标要与噪声项 $\mathbf n$ 兼容

可见，两种情况观测误差的 jacobian 都是依赖 $\hat x$ 的：

$\hat H^L=-(h_{\hat x}'.T_el_{\hat x})$

$\hat H^R=-(h_{\hat x}'.T_er_{\hat x})$

> 如果左、右不变误差分别定义为 $\hat x^{-1}x$ (instead of $x^{-1}\hat x$) 和 $x\hat x^{-1}$(instead of $\hat xx^{-1})$，那 $\hat H^L,\hat H^R$与现在相比将差个负号： i.e.  $\hat H^L=h_{\hat x}'.T_el_{\hat x}$，   $\hat H^R=h_{\hat x}'.T_er_{\hat x}$



### IEFK 更新公式
求出上述关键矩阵后，IEKF 的更新公式与标准 EKF 几乎完全相同，唯一的差别在与状态 $\hat x$ 的更新（李群上的更新）。

需要用到的矩阵有：

* $A_u$：状态误差 $\xi$ 演化的 jacobian
* $Q$: 状态噪声 $w$ 的协方差矩阵
* $G$ （或 $\hat G$）: 状态噪声的变换矩阵；带帽子的是依赖状态估计值 $\hat x$ 的量；
* $H$（或 $\hat H$）: 观测误差的 jacobian
*  $N$（或 $\hat N$）: 观测误差的 协方差矩阵

首先，通过求解 Riccati equation (Lyapunov equation) 获得  $t_n$ 时刻状态误差的先验协方差 $P_{t_n}$以及卡尔曼增益 $K$：

$\frac{d}{dt}P_t = A_{u_t}P_t + P_tA_{u_t}^T+\hat G_tQ_t\hat G_t^T$ 

$\begin{aligned} P_{t_n}&=P_{t_{n-1}}^+ + \int_{t_{n-1}}^{t_n}\frac{d}{dt}P_t\ dt \\
&=P_{t_{n-1}}^+ + \int_{t_{n-1}}^{t_n}(A_{u_t}P_t + P_tA_{u_t}^T+\hat G_tQ_t\hat G_t^T)\ dt  \\
&\approx (I+A_u\Delta t)P_{t_{n-1}}^+(I+A_u\Delta t)^T + \hat GQ\hat G^T\Delta t
\end{aligned}$

$S=\hat HP_{t_n}\hat H^T + \hat N$

$K=P_{t_n}\hat H^TS^{-1}$

$P_{t_n}^+=(I-K\hat H)P_{t_n}$

$\hat\xi^+ = K\tilde y_{\xi}$  (可以认为我们对误差$\xi$ 有个先验估计 $\hat \xi=0$ )

$\hat \eta^+=\exp(\hat \xi^+)$ (可以认为我们对误差$\eta$ 有个先验估计 $\hat \eta=e$ )

* LIEKF:   参考左不变 $\eta$ 的定义$x=\hat x\cdot \eta^{-1}$，更新公式应为
$\hat x_{t_n}^+=\hat x_{t_n}\cdot (\hat \eta^+)^{-1}=\hat x_{t_n}\cdot \exp(-K\tilde y_\xi)$
> [4\] 中给的是 $\hat x_{t_n}^+=\hat x_{t_n}\cdot \exp(K\tilde y_\xi)$，与上式的差异是括号里差个负号。 [4\] 中可能是错的？ 当左不变 $\eta$ 定义为 $x=\hat x\cdot \eta$ 时，才应该去掉括号中的负号，但 [4\] 中的左不变 $\eta$ 采用的定义是 $x=\hat x\cdot \eta^{-1}$。
* RIEKF:  参考右不变 $\eta$ 的定义$x=\eta^{-1}\cdot \hat x，$更新公式应为
$\hat x_{t_n}^+=(\hat \eta^+)^{-1}  \cdot  \hat x_{t_n}=\exp(-K\tilde y_\xi) \cdot  \hat x_{t_n}$
> [4\] 中给的是 $\hat x_{t_n}^+=\exp(K\tilde y_\xi)\cdot \hat x_{t_n}$，与上式的差异是括号里差个负号。 [4\] 中可能是错的？ 当右不变 $\eta$ 定义为 $x=\eta \cdot \hat x$ 时，才应该去掉括号中的负号，但 [4\] 中的右不变 $\eta$ 采用的定义是 $x=\eta^{-1}\cdot  \hat x$。


### IEKF 的使用

#### 判断 IEKF 是否适用

首先，确定系统的状态量 $x$、控制量 $u$、以及演化方程 $f_u(x)$；
然后，看是否可以在状态空间 $\mathbb X$ 上构造一个二元乘法运算，使得
1. $\mathbb X$ 在该乘法下构成李群 $G$ （满足结合律、存在单位元、存在逆元）
2. 且向量场 $f_u$ 是左或右不变向量场，或者满足 $f(ab)=f(a)b+af(b)-af(Id)b$

如果可以，那么可以使用 IEKF 。

##### 离散时间的情况


当 $f_u(x)$ 是左不变 $f_u(x)=xf_u(e)$ 或右不变 $f_u(x)=f_u(e)x$ 时，对应的离散时间状态转移方程也会有很简单的形式。
对于离散时间的 IEKF，上面的条件2对应：

2. 无噪声的状态转移方程可以写为以下两种形式之一
    - $x_{n}=x_{n-1}.\Gamma_u$ (对应左不变的 $f_u$， $\dot x=f_u(x)=x.f_u(e)$) 或 
    - $x_{n}=\Gamma_u.x_{n-1}$ (对应右不变的 $f_u$， $\dot x=f_u(x)=f_u(e).x$)

其中 $\Gamma_u\in G$ 是个依赖于 $u$ 的群元。一般把 $u$ 建模为李代数上的向量，且 $\Gamma_u=\exp(u)$，当然这并不是必须的.

> 暂不考虑一般形式（i.e. 既非左不变、又非右不变，但满足 $f(ab)=af(b)+f(a)b-af(e)b$) 的 $f_u$ 对应的离散时间版本。

再来看下离散时间版本的过程噪声。这里仅以 "左不变过程+右不变误差"为例。
对于左不变的过程，可以写成
$x_{n}=x_{n-1}.\Gamma_{(u,w)}=x_{n-1}.(\exp(w_u).\Gamma_{u})$
其中 $w$ 是原始噪声项，$w_u$ 是被 $u$ 变换过的左不变噪声项; 
当使用右不变误差 $\eta=x\hat x^{-1}$ 时 ，
$\begin{aligned}\eta_{n|n-1}&=x_{n}\hat x_{n|n-1}^{-1} \\
&=(x_{n-1}.\exp(w_u).\Gamma_{u})(\Gamma_{u}^{-1}\hat x_{n-1|n-1}^{-1}) \\
&=x_{n-1}.\exp(w_u).\hat x_{n-1|n-1}^{-1}\\
&=(x_{n-1}\hat x_{n-1|n-1}^{-1})(\hat x_{n-1|n-1}\exp(w_u).\hat x_{n-1|n-1}^{-1})\\
&=\eta_{n-1|n-1} \exp(Ad_{\hat x_{n-1|n-1}}w_u)
\end{aligned}$

换算成李代数上的误差 $\xi$，得
$\begin{aligned}\exp(\xi_{n|n-1})&=\exp(\xi_{n-1|n-1}) \exp(Ad_{x_{n-1|n-1}}w_u)
\end{aligned}$
略去关于误差 $\xi$ 和噪声 $w_u$ 的二阶小量（包括它们的交叉相乘项），得
$\xi_{n|n-1} \approx \xi_{n-1|n-1} + Ad_{x_{n-1|n-1}}w_u$
可见，$F_n=I_{\mathfrak g}$, $G_n=Ad_{x_{n-1|n-1}}.\frac{\partial w_u}{\partial w}|_{w=0}$
如果 $w_u=w$，那么 $G_n=Ad_{x_{n-1|n-1}}$ 。

#### LIEKF or RIEKF ?

为了选择合适的 IEKF，首先考察 观测函数  $y=h(x)$: 是否能构造一个 $G$ 在观测空间 $\mathbb Y$ 上的作用 $\rho$，使得在该作用下， $y=h(x)$ 是左或右不变的？

1. 如果可以，且 $y=h(x)$ 是左或右不变的，那么为了系统有更好的收敛性，我们应该分别选择 LIEKF 或 RIEKF；
    - 如果噪声（过程噪声和观测噪声）都和系统有一致的不变性，那是最理想的，这时系统的收敛性最好；
    - 如果噪声没有不变性，或其不变性与系统相反，那系统的收敛性会受到较轻微的影响
2. 如果不行（观测函数没有不变性），那么与传统EKF一样，此时系统的收敛性无法从理论上得到保证，可以相对随意地选择 LIEKF 或 RIEKF，或者通过实验效果来确定。如果只从计算的方便程度方面考虑:
    - 若 $f_u$ 是左不变的，那么选用 RIEKF 会方便 $A$ 和 $G$ 的计算；
    - 若 $f_u$ 是右不变的，那么选用 LIEKF 会方便 $A$ 和 $G$ 的计算；



### 一些注意事项


* 如果左、右不变误差分别定义为 $\hat x^{-1}x$ (instead of $x^{-1}\hat x$) 和 $x\hat x^{-1}$(instead of $\hat xx^{-1})$，那么对应的 $\xi$ 的定义将与文中所用的差一个负号，所以上述关于 $\xi$ 和 $\dot\xi$的相关公式、jacobian 等都要视情况取负号，比如上面的卡尔曼更新公式。



---


## todo: 
普通 observer 在 equilibrium points 的收敛性；symmetry preserving observer 在 permanent trajectories 的收敛性； [1\]

线性 KF 的收敛条件：[4\]; IEKF 的 收敛 [4\]



---




1. S. Bonnabel, Ph. Martin, and P. Rouchon, “Symmetry-preserving observers,” *IEEE Transactions on Automatic and Control*, vol. 53, no. 11, pp. 2514–2526, 2008.
2. Bonnabel, S. (2007, December). Left-invariant extended Kalman filter and attitude estimation. In 2007 46th IEEE Conference on Decision and Control (pp. 1027-1032). IEEE.
3. S. Bonnabel, Ph. Martin and E. Salaün, "Invariant Extended Kalman Filter: theory and application to a velocity-aided attitude estimation problem", 48th IEEE Conference on Decision and Control, pp. 1297–1304, 2009.
4. Barrau, A., & Bonnabel, S. (2016). The invariant extended Kalman filter as a stable observer. IEEE Transactions on Automatic Control, 62(4), 1797-1812.