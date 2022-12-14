
------

先看一个小引理：

$\mathbb R^m$的某个开集$U$上的$l$个线性无关的光滑切向量场$\{X_i\}_{1\le i\le}$，$l\lt m$，是否一定可以通过补充$U$上的$m-l$个光滑切向量场使其扩充为一个$U$上的光滑标架场？或者，等价的命题是，是否可以补充另一个光滑切向量场，使之与前$l$个给定的切向量场处处线性无关（这个命题成立的话，就可以逐一补全$m-l$个光滑切向量场）。



首先可以确定的是，对于任一点 $p\in U$，存在开子集$V$，$p\in V \subset U$，使得 $V$ 上存在这样的 $m-l$ 个额外的$C^\infty$光滑（甚至$C^\omega$解析的）切向量场：

考虑$p$点处的切空间$T_p(M)$。我们选择$m-l$个"常"切向量场 $X_{l+1},X_{l+2}...X_m$，使得它们在$p$点处对应的切向量$X_{l+1}(p),X_{l+2}(p)...X_m(p)\in T_p(M)$ 与 $\{X_i(p)\}_{1\le i\le}$ 一起构成$T_p(M)$的一个基底。$p$点处所有这$m$个切向量间的线性无关性，保证了它们的坐标组成的$m\times m$方阵的行列式非0。 而这个行列式其实定义了一个依赖于这$m$个向量场的从 $\mathbb R^m$  到 $\mathbb R$ 的连续函数，因此该行列式在$p$点处非0，则必在$p$的某个邻域$V$内非0。在这个行列式在$V$上非0，说明这$m$个向量场在$V$的某一点处都构成该点切空间的一个基底。

>【接下来这段论证存疑，但有了以上结论后，就已经够下文使用了】
由于每点 $p\in U$ 都存在这样的一个开邻域 $V$以及对应的一组额外的 $m-l$个光滑切向量场。再用单位分解把这些 $C_infty$ 的向量场加权后加在一起，就能组成$U$上的一组$m-l$个光滑切向量场。但单位分解只能保证合成的向量场的光滑性，并不能保证最终加权求和得到的$m-l$个向量场依然线性无关，能否加上什么约束保证最终构造出$m-l$个线性无关向量场呢？为简单起见，可以考虑不一次性添加$m-l$个向量场，而是逐一地添加新向量场，并用单位分解逐一构造全局的非退化向量场？也不一定可行。
另外，能否保证构造出$C^\omega$的向量场呢？大概率也不行。



------

Frobenius 定理和 Pfaff 方程组

将阐述Frobenius 定理如何从欧式空间的形式拓展到一般微分流形。

先看欧氏空间 $\mathbb R^m$ 上的版本。
问题：给定线性无关的光滑向量场$X_1,X_2...X_l$，是否 (局部地) 存在$m-l$个光滑函数$f_{l+1}, f_{l+2},...f_m$，使得，$X_if_k=0, \forall 1\le i\le, l+1\le k\le m$，且所有$f_{k}$的梯度$\nabla f_{k}$之间线性无关？

$X_if_k=0$ 其实是在说，$X_i$与$f_k$的梯度场$\nabla f_k$正交。
Frobenius 定理给了这个问题有解的充要条件，即向量场$X_1,X_2...X_l$要满足对合性 (involutivity)。注意对合性是针对"分布"的，它不依赖子向量场的选取，两组向量场如果生成同样的分布，那它们同时满足或不满足对合性。

> 欧氏空间上 $X_if_k=0$ 可以写成一个超定的一阶齐次线性偏微分方程组的形式: 超定是指，约束的数量多于未知函数的数量。
https://en.wikipedia.org/wiki/Frobenius_theorem_(differential_topology)#Introduction
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/7f2bfaf1618ce5fe35770890539bc516.png)



如果能找在局部找到 $m-l$个线性无关且与所有$X_i$正交的光滑向量场，就可以这些向量场做为梯度场生成$m-l$ 个符合条件的局部光滑函数。
之前已经论证过，在局部找到 $m-l$个与所有$X_i$线性无关的光滑向量场。但若要找到"垂直"的光滑向量场，则需要对所有$X_i$附加对合条件(involutivity)。
另外要注意，并非所有向量场都可以作为梯度场，只有无环的向量场（没有闭合的积分曲线）才可以。但只要向量场在某点$p$处非0，那么根据向量场直化定理，必然存在$p$的一个邻域使得向量场在此邻域内可直化，而可直化的向量场自然可以作为梯度场。我们这里已假设所述向量场在讨论的区域内无奇点，因此必然存在一个邻域使得所有向量场局部地可作为某函数的梯度场。
> 如果去掉光滑或连续可微条件，这样的$m-l$个"垂直"向量场是一定存在的，因为只需在每一点处，选定$m-l$个垂直向量即可。那么问题是，对合条件不满足时，具体是什么阻碍了这样的"垂直"向量场变得光滑 (或连续可微) 呢？

问题可以等价描述为：
给定线性无关的光滑向量场$X_1,X_2...X_l$，是否 (局部地) 存在另外$m-l$个线性无关的光滑向量场$X_{l+1}, X_{l+2},...X_m$，使得
- 它们都与原来的$l$个向量场$X_1,X_2...X_l$正交
- 局部来看，它们中的每一个都是某个光滑函数的梯度场  （这一条只是向量场局部无奇点的推论）

但梯度、垂直/正交这些概念只适用于配备了黎曼度量的流形，比如欧式空间上，并不适用于一般的微分流形。但这个结论可以适用于一般微分流形。
为向微分流形推广，要处理一些概念：
- 与正交向量(场)对应的概念是 Annihilator【此处术语待规范】，一个向量(场)$X$的Annihilator不再是一个与之"垂直"的向量(场)，而是一个对偶向量(场) $W$使得 $<W,X>=W(X)=0$，或叫做一个1-form。
> 反过来，给定流形上的一个向量场$X$和一个对偶向量场$W$，若$<W,X>=W(X)=0$，那么选定一个坐标图后，就可通过欧氏空间的度规张量把$W$转化为欧氏空间上的一个与$X$垂直的向量场（在欧氏空间中，对偶向量场与向量场可通过度规张量互相转化且一一对应）。在流形上找某个向量场的一个Annihilator对偶向量场，可以借助坐标图转化为在欧氏空间找一个垂直向量场。

- 如何在一般微分流形上找到"梯度场"的对应物？欧氏空间中，梯度场是一个向量场，但也可以看做一个对偶向量场或 1-form (通过用度规张量转化)。函数$f_k$的梯度场记作$\nabla f_k$，对应的1-form则记作 $df_k$。
> 关于这个转化后的 1-form 的几何意义：考虑函数$f_k$的一个 levle set 所组成的超曲面，levle surface。对levle surface上任一点处$p$处的任一切向量$X_p$，它与$p$点处的"梯度向量"垂直，因此，梯度场转化得来的1-form，$df_k$ 满足 $<df_k(p), X_p>=df_k(p)(X_p)=0$

- 欧氏空间中，给定一个局部无奇点的向量场 (因此局部可视作一个梯度场)，可以局部地通过路径积分得到一个函数。那微分流形上，给定一个局部无奇点的对偶向量场$X^*$，能否也生成一个函数$f_k$？
> 利用坐标图，可以把对偶向量场转化为欧氏空间的向量场，然后问题就转化回了从给定的梯度场生成一个函数。

利用这些概念的推广，可以重述上述问题如下：
给定线性无关的光滑向量场$X_1,X_2...X_l$，是否 (局部地) 存在一组光滑函数$f_{l+1}, f_{l+2},...f_m$，或一组**exact** 1-form $df_{l+1}, df_{l+2},...df_m$使得：
- 每个$df_k$ 可以 annihilate 掉所有 $X_i$，即$<df_k, X_i>=0$，（或等价地写为$X_if_k=0$），$\forall 1\le i\le, l+1\le k\le m$
- 所有$df_{k}$之间线性无关，$l+1\le k\le m$

Frobenius定理是说，当且仅当向量场$X_1,X_2...X_l$对合时，上述的一组 **exact** 1-form $df_{l+1},df_{l+2},...df_m$ 存在。
> 从几何角度来说，Frobenius定理证明了，当且仅当向量场$X_1,X_2...X_l$对合时，存在一个局部坐标图使得在此坐标图内，向量场$\{X_1,X_2...X_l\}$与 $\{\frac{\partial}{\partial x^1},\frac{\partial}{\partial x^2}...\frac{\partial}{\partial x^l}\}$张成相同的分布。这时，与坐标图对应的$dx_{l+1},dx_{l+2}...dx_{m}$就是一组符合条件的1-form 。

反过来，若给定一组线性无关的光滑 1-form $\{\omega_k\}_{l+1\le k \le m}$ ，是否（局部地）存在一组线性无关的、对合的光滑向量场 $\{X_i\}_{1\le i\le l}$，使得 $\langle w_{k}, X_i \rangle=w_{k}(X_i)=0$ ? 
或者等价地问，Pfaffian system 方程组 $\omega_k=0,l+1\le k \le m$ 是否完全可积？（在书上和wiki上可找到 方程组完全可积的定义）。
为了使结论成立，这些1-form需要满足什么条件？

来看一个引理：
若$X_1,X_2...X_l, X_{l+1},X_{l+2}...X_m$ 组成一个光滑标架场，$w_1,w_2...w_l, w_{l+1},w_{l+2}...w_m$ 是对应的对偶向量场（1-form），$w^i(X_j)=\delta^i_j$ 。那么下面两种说法等价（白国正和陈的书中都有证明）:
- $X_1,X_2...X_l$ 对合
- 对 $l+1\le k \le m$，$dw_k\equiv 0\ mod (w_{l+1}, w_{l+2}...w_m)$
> 这里的 $w_{l+1}, w_{l+2}...w_m$ 与前面提到的 $df_{l+1},df_{l+2},...df_m$是什么关系？
若$X_1,X_2...X_l$ 对合，按前述定理，存在一组 exact 1-form $df_{l+1},df_{l+2},...df_m$ 满足 $<df_k, X_i>=0$。可见，$\{df_k\}$都张成了$\{X_i\}_{1\le i\le}$的annihilator空间，换句话说，$\{df_k\}$构成了$\{X_i\}_{1\le i\le}$的annihilator空间的一组基。
而对于$l+1\le k \le m$，依$w_k$的定义，它可以annihilate掉所有$\{X_i\}_{1\le i\le}$，所以$w_k$可以用这组基展开：$w_k=\sum_{l+1\le r \le m}g_{rk} df_r$，其中$g_{rk}$是光滑函数。于是：
$dw_k=\sum_{l+1\le r \le m}dg_{rk}\wedge df_r$
前面提到的 $df_{l+1},df_{l+2},...df_m$都是exact form，但这里的 $w_{l+1}, w_{l+2}...w_m$ 并不一定。
> 这里还要注意，在欧氏空间中，假设 $w_j$  通过度规张量升降指标转化得来的向量场是 $Y_j$。$Y_j$和$X_j$都与对偶向量场$w_j$存在某种特殊关系，但$Y_j$与$X_j$一般并不是同一个向量场。 $Y_j$与所有${X_i}_{i\ne j}$正交，但$X_j$依定义是比较随意的，它不一定与${X_i}_{i\ne j}$正交。 $Y_j$与$X_j$二者之间的关系是 它们的向量内积$Y_j\cdot X_j=w_j(X_j)$为1。

若给定线性无关的 1-forms $w_{l+1}, w_{l+2},...w_m$，那么我们可以先补充1-forms $w_1,w_2...w_l$ 使得它们与原来的$m-l$个1-form组成一个"对偶"标架场（这可以做到，因为这等价于在坐标图对应的欧式空间中补全一组线性无关的梯度向量场）。再设$X_1,X_2...X_m$是与$w_1,w_2...w_m$ 相对偶的向量场。
那么，根据前述引理，如果对于$l+1\le k\le m$，$w_k$满足：
$dw_k\equiv 0\ mod (w_{l+1}, w_{l+2}...w_m)$
那么前$l$个向量场$X_1,X_2...X_l$ 必然是对合的。

【下面这段需要补充】
反过来，给定一组光滑 1-form $\omega_k, l+1\le k \le m $，是否（局部地）存在一组向量场 $X_i, 1\le i\le$，使得 $w_{k}(X_i)=0$ ? 
或者等价(?)地问，Pfaffian system 方程组 $\omega_k=0,l+1\le k \le m$ 是否完全可积？（在书上和wiki上可找到 方程组完全可积的定义）。

 $\omega_k=0,l+1\le k \le m$ 是否完全可积，等价于
存在一组向量场 $X_i, 1\le i\le$ 使得 $w_{k}(X_i)=0, l+1\le k \le m$  ，且这组向量场必然对合，从而 $w_k$满足书上所述的1-form形式的对合条件，即 $dw_k\equiv 0\ mod (w_{l+1}, w_{l+2}...w_m)$。

假如$w_k$满足$dw_k\equiv 0\ mod (w_{l+1}, w_{l+2}...w_m)$，则存在一组向量场 $X_i$是对合的？

如果方程组完全可积，在特定坐标图上，存在一组光滑标架场 $X_r$，使得 $w_k(X_i)=0, 1\le i \le$，且前$l$个向量场在各点处的向量所张成的子向量空间恰好是 $\frac{\partial}{\partial x^i},1\le i \le$张成的子空间，或者等价地说，前$l$个向量场满足对合性；


todo
如果光滑向量场$X_1,X_2...X_l$ (张成的分布) 是对合的，则存在一组补向量场（与原向量场一起构成标架场）$X_{l+1},X_{l+2}...X_m$ ，这组补向量场 (张成的分布) 也对合？

相关概念： 叶状结构(foliation或slicing)，叶片(leaves, slices) ；叶片、levle surface、积分流形的关系(等价？局部等价？）

-----

与偏微分方程组的关系
设$f_i: \mathbb R^n\rightarrow \mathbb R$，它的第$j$个分量函数记作$f_i^j: \mathbb R \rightarrow \mathbb R$。
若$x\in \mathbb R^n$，分量记为$x^j$。
$x=\left[x^1,x^2...x^n\right]^T, f(x)=\left[f_i^1(x), f_i^2(x)... f_i^n(x)\right]^T$。

我们先来看一类最简单的偏微分方程，比如$f_1$关于$x^1$的**单个**偏微分方程：$\frac{\partial f_1}{\partial x^1}=F_i^1$，其中$F_1^1: \mathbb R^n\rightarrow \mathbb R$ 是个已知函数。对于这样的微分方程，给定边界条件后，通过沿每条$x^1$线对$F_1^1$做积分即可求解出$f_1$。注意，在常微分方程中，边界条件是指未知函数在某点处的值，而对于本例中讨论的偏微分方程，边界条件是指与$x^1$轴垂直的超平面 (或一个$n-1$维超曲面) 上的值。
如果$F_1^1$是依赖于未知函数$f_1$的，即$\frac{\partial f_1}{\partial x^1}(x)=F_1^1(x, f_1(x))$，此时给定边界条件后，每条$x^1$线决定了一个常微分方程。而根据常微分方程解的存在和唯一性定理，可知每条$x^1$线都可以独立地局部求解。因此某种意义上讲，$f_1$也可以被局部（在某个子集内）求解。
> 但仅通过这一步论证，还不足以说明$f_1$能在$R^n$的一个**开子集**中有解。假设过点$(0, y)$的$x^1$线上，解区间为 $x^1\in(-\epsilon_y, \epsilon_y)$；由于超平面$x^1=0$上有无穷多个点$y$，就会有(不可数)无穷多个$(-\epsilon_y, \epsilon_y)$这样的区间，而这无穷多个的区间之交可能不再是一个非零区间，即不一定存在一个大于0的$\epsilon$使得所有$epsilon_y\le \epsilon$。 

同样得 ，考虑下述关于$x^1$偏微分方程组：
$\frac{\partial f_1}{\partial x^1}(x)=F_1^1(x, f_1(x), f_2(x)...f(n))$
$\frac{\partial f_2}{\partial x^1}(x)=F_2^1(x, f_1(x), f_2(x)...f(n))$
$...$
$\frac{\partial f_n}{\partial x^1}(x)=F_n^1(x, f_1(x), f_2(x)...f(n))$
给定边界条件后，每条$x^1$线上决定了一个常微分方程组。每条$x^1$线可以被独立地局部求解。
> 同样地，仅通过这一步论证，还无法说明方程组能在$R^n$的一个开集中有解。如果确实有“这样的方程组必在一个开集内有解”这样的结论，那要证明它可能需要用向量场直化定理？这里先跳过，以后再想。

由此可见，n个未知函数，在指定了边界条件后，只需再给定n个约束(n个偏微分方程组成的方程组)，就可以被求解。

如果共有n个未知函数，但约束却多于$n$个，这样的偏微分方程组就是"超定"的偏微分方程组。
超定的方程组中，不同的方程之间可能是存在矛盾的，因此超定方程组一般无解。
比如，考虑下述方程组，只有一个未知$C^2$函数$f_1$，但却有$n$个约束。
$\frac{\partial f_1}{\partial x^1}=F_1^1$
$\frac{\partial f_1}{\partial x^2}=F_1^2$
...
$\frac{\partial f_1}{\partial x^n}=F_1^n$
假如要求函数$f_1$是$C^2$的。如果函数 $F_1^1,F_1^2...F_1^n$是随意给定的，那方程组大概率无解。因为我们对这些函数有隐含的要求（二阶导求导顺序可交换性）：$\frac{\partial F_1^i}{\partial x^j}=\frac{\partial F_1^j}{\partial x^i}$。

有些特定的超定方程组，各方程之间并非完全独立时，方程组依然可能是有解的。
比如，Frobenius定理中我们遇到的方程组（见上面的wiki截图），它是超定的，但当所给向量场满足对合条件时，方程组有解而且有无穷多解。

从偏微分方程的角度，也可以解释为什么我们不一定能找到一组光滑（甚至只是$C^1$的）的向量场，使得它与给定的一个分布( 或给定的一组向量场) 处处正交。
考虑欧氏空间中，给定光滑向量场$X_1, X_2,...X_l$，是否存在一个光滑或连续可微的（局部无奇点的）向量场$X$，使得它与前$l$个向量场垂直？等价描述为，是否存在一个光滑函数$f$，它的梯度$\nabla f$与前$l$个向量场垂直。
为了让$\nabla f$与$X_1$垂直，我们要求 $\nabla f\cdot X_1=0$，写成分量形式即：
$X_1^1\frac{\partial f}{\partial x^1}+X_1^2\frac{\partial f}{\partial x^2}+...+X_1^n\frac{\partial f}{\partial x^n}=0$
这是一个偏微分方程。
类似，对每个$X_i$, 都生成一个这样的偏微分方程。
这样我们得到一个包含$l$个方程的方程组。但待求的函数（或梯度向量场）只有1个，因此这是个超定问题。待求的函数或向量场不一定存在。

如果只要求找到一个光滑或可微向量场与所给定的$l$个向量场线性无关，就是另外一回事了，前面也已说明这样的向量场是可以构造出来的。下面从偏微分方程组的角度来解释其存在性的合理性：补充待求的函数$f_1, f_2...f_l$（我们期待它们处处非0），并定义偏微分方程组如下：
$X_if=f_i$
$X_if=f_i$
...
$X_if=f_l$
这个方程组中包含了 $f$ 和 $f_1, f_2...f_l$ 共 $l+1$ 个未知函数，但约束只有$l$个，因此是可以存在无穷多解的。
这些解中，那些能使得  $f_1, f_2...f_l$ 处处非0 的解，对应的 $f$ 的梯度场就是一个与所给定的$l$个向量场线性无关的向量场。


再来考虑另一个有趣的问题。
微分几何中定义$l$个向量场 $X_1,X_2...X_l$张成的分布$D^l$是"完全可积"时，用的说法是：存在一个坐标图，使得在该坐标图下，每点处 $X_1,X_2...X_l$张成的切子空间，恰好是$\frac{\partial}{\partial x^1},\frac{\partial}{\partial x^2}...\frac{\partial}{\partial x^l}$张成的切子空间。
这样的说法看上去有点不够完美，那能否直截了当的说成：存在一个坐标图，使得在该坐标图下，每点处 $X_1=\frac{\partial}{\partial x^1},X_2=\frac{\partial}{\partial x^2}...X_l=\frac{\partial}{\partial x^l}$? 下面说明这个说法通常并 **不能成立**。
问题等价于：给定$\mathbb R^l$(或某个开集)上的一个光滑标架场 $\{X_i\}_{1\le i\le l}$，是否存在一个微分同胚$F: \mathbb R^l \rightarrow \mathbb R^l$，使得该映射对应的微分$F_*$，满足 $F_*X_i=\frac{\partial}{\partial x^i}$？ 即把 $\{X_i\}_{1\le i\le l}$分别映射为$l$个标准坐标场$\{\frac{\partial}{\partial x^i}\}_{1\le i\le n}$。
注意到: 函数$F$可以看做有$l$个分量函数$F_i: \mathbb R^l \rightarrow \mathbb R$; 在每点处，$F_*$对应一个雅克比矩阵; $F_*X_i$是$\mathbb R^l$中的一个向量场，它有$l$个分量。
因此，对于每个$i$， $F_*X_i=\frac{\partial}{\partial x^i}$ ，其实可以拆分成$l$个偏微分方程。
这样总共将有$l\times l$个偏微分方程。但我们待求解的函数$F_1, F_2...F_l$只有$l$个，所以这又是一个超定问题。它不一定有解。也即，不一定存在这样一个微分同胚$F$。

> 能否用类似地方法解释"向量场直化"的不唯一性呢？
是否存在微分同胚 $F$, 使得$F_*$ 把一个向量场映射成 $\frac{\partial}{\partial x^1}$?
微分同胚$F$ 对应 $l$个分量函数，$F_*$ 可由各分量函数的一阶偏导组成的$l\times l$矩阵表示，
$F_*X = \frac{\partial}{\partial x^1}$ 对应$l$个偏微分方程。
此方程组的解即是可以把向量场直化的微分同胚，我们知道这样的微分同胚并不唯一。
考虑到 共有 $l$ 个待求的分量函数，也正好有$l$个偏微分方程。为什么方程组的解不唯一呢？
因为边界条件不唯一！确定了边界条件，解就唯一了？思考，边界条件该是什么形式？
