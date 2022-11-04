
[TOC]

# Scaled_Unscented_Transform_变尺度无味变换中的一些技术性推导


# 1. 符号约定
- 粗体字母表示张量 (矢量和矩阵也都属于张量，矢量是一阶张量，矩阵是二阶张量)，或表示张量矩阵（元素都是同阶张量的矩阵）；
- 带下标的非粗体字母表示对应张量的某分量，如 $a_{ijk}$ 是张量$\boldsymbol{a}$ 的一个分量；
- 张量矩阵的元素用带括号的上标指定，且由于张量矩阵的元素是张量，所以元素也表示成粗体，比如若$\boldsymbol{A}$是一个张量矩阵，则其第$i$行第$j$列的元素表示为：$\boldsymbol{A^{(ij)}}$
- 不加粗的大写字母表示传统的矩阵，加粗的大写字母表示该矩阵的张量形式，如$M$是矩阵，$\boldsymbol{M}$是二阶张量；
- 如果两个张量直接连写在一起，表示两个张量的并积，如 $\boldsymbol{ab}$ 表示$\boldsymbol{a}$ 与 $\boldsymbol{b}$ 的并积，特殊地， $\boldsymbol{a^n}$ 表示$\boldsymbol{aaa...a}$ 即 $n$ 个 $\boldsymbol{a}$ 的并积；
- $\boldsymbol{a}\cdot\boldsymbol{b}$ 表示张量 $\boldsymbol{a}$ 与 $\boldsymbol{b}$ 的点积，即$\boldsymbol{a}$ 的最后基矢量与 $\boldsymbol{b}$  的第一基矢量进行缩并，展开式为： $\boldsymbol{r}=\boldsymbol{a}\cdot\boldsymbol{b}=a_{ijk}b_{lm}\boldsymbol{e}_{i}\boldsymbol{e}_{j}\boldsymbol{e}_{k}\cdot\boldsymbol{e}_{l}\boldsymbol{e}_{m}=a_{ijk}b_{lm}\boldsymbol{e}_{i}\boldsymbol{e}_{j}\delta_{kl}\boldsymbol{e}_{m}=a_{ijk}b_{km}\boldsymbol{e}_{i}\boldsymbol{e}_{j}\boldsymbol{e}_{m}=r_{ijm}\boldsymbol{e}_{i}\boldsymbol{e}_{j}\boldsymbol{e}_{m}$ ，$\boldsymbol{r}$ 的阶数等于$\boldsymbol{a}\boldsymbol{b}$的阶数减2，用爱因斯坦求和约定来表示分量间的关系式为 $r_{ijm}=a_{ijk}b_{km}$。传统矩阵的乘积就可以看做张量的点积，传统的矢量点积也是张量点积的一个特例；

为方便表示，再额外定义一些运算

- 张量的并联式多重点积，$\boldsymbol{a}*\boldsymbol{b}$ 表示张量 $\boldsymbol{a}$ 与 $\boldsymbol{b}$ 的并联式多重点积，以下简称多重点积（若不额外说明，文中提到的多重点积都是指并联式的），若$\boldsymbol{r}=\boldsymbol{a}*\boldsymbol{b}$ ，$\boldsymbol{a}$ 与 $\boldsymbol{b}$的阶数分别为$p$和$q$，其中$p\ge q$，则$\boldsymbol{r}$ 就是$\boldsymbol{a}$ 的最后$q$个基矢量与 $\boldsymbol{b}$ 的$q$个基矢量分别依次缩并的结果，$\boldsymbol{r}$ 的阶数为 $|p-q|$（特殊地，若$p=q$即$\boldsymbol{a}$与$\boldsymbol{b}$同阶，则多重点积的结果退化为一个标量，切此时满足交换率）。比如若$p=3,q=2$，则以爱因斯坦求和约定来表示分量间的关系式为 $r_{i}=a_{ijk}b_{jk}$。（如果是串联式的多重点积，则$r_{i}=a_{ijk}b_{kj}$，但这里我们更多的使用前述的并联式）；
- 张量矩阵的转置：设$\boldsymbol{A}$是一个张量矩阵，$\boldsymbol{B}=\boldsymbol{A}^T$，则$\boldsymbol{A^{(ij)}}=\boldsymbol{B^{(ji)}}$
- 张量矩阵的乘法：与传统矩阵乘法格式基本一样。举例，设张量矩阵 $\boldsymbol{C}=\boldsymbol{A}\boldsymbol{B}$，则$\boldsymbol{C^{(ij)}}=\sum_{k}{\boldsymbol{A^{(ik)}}\boldsymbol{B^{(kj)}}}$，需要强调的是，求和式中$\boldsymbol{A^{(ik)}}$与$\boldsymbol{B^{(kj)}}$的乘积指的是它们的并积。因此，若$\boldsymbol{A}$的元素是$m$阶张量，$\boldsymbol{B}$的元素是$n$阶张量，则它们的乘积$\boldsymbol{C}$的元素就是$(m+n)$阶张量。
- 若$\boldsymbol{A}$是一个张量矩阵，$\boldsymbol{b}$ 是一个张量，则$\boldsymbol{A}*\boldsymbol{b}$或$\boldsymbol{b}*\boldsymbol{A}$依然表示一个张量矩阵（或当$\boldsymbol{b}$与$\boldsymbol{A}$的元素同阶时，退化成传统标量矩阵），记为$\boldsymbol{C}$，则$\boldsymbol{C^{(ij)}}=\boldsymbol{A^{(ij)}}*\boldsymbol{b}$或$\boldsymbol{C^{(ij)}}=\boldsymbol{b}*\boldsymbol{A^{(ij)}}$


# 2. 标准正态分布的高阶中心矩

利用一元微积分可以算出0均值、方差为$\sigma^2$的单变量高斯分布的高阶矩：奇数阶矩为0，偶数阶比如$2k$阶矩为：

$(2k-1)\times (2k-3)...\times 3\times 1\times \sigma^{2k}$

为简化表示，定义 $(2k-1)!!=(2k-1)\times (2k-3)...\times 3\times 1=\frac{(2k-1)!}{2^k(k-1)!}$，则单变量高斯分布的$2k$阶矩为：

$(2k-1)!!\sigma^{2k}$

如果是标准高斯分布，$\sigma=1$，则$2k$阶矩为$(2k-1)!!$。

设$x_{1},x_{2},x_{3}...x_{m}$分别都是服从一维标准高斯分布的随机变量且两两之间相互独立，则它们的组合变量服从均值为0、协方差矩阵为$m\times m$单位阵$I$的标准联合正态分布，即 $$for\ i=1,2,3...m,i\neq j\begin{cases}E[x_i]=0\\E[x_i^2]=1\\E[x_ix_j]=0\\E[x_i^{2k-1}]=0\\E[x_i^{2k}]=(2k-1)!!\end{cases}\tag{1}$$


更高阶的矩，如$n$阶矩的分量可表示如下：
$$E[x_{i_1}x_{i_2}...x_{i_n}]\quad in\ which:\ 1\le i_1,i_2...i_n \le m$$
$\boldsymbol{x}$的分量可以重复出现多次，也可以不出现，比如可以有$i_1=i_2=1$。由此可以合并相同的分量写成幂形式，将高阶矩重新表示成：$$E[x_{i_1}x_{i_2}...x_{i_n}]=E[x_{1}^{\alpha_1}x_{2}^{\alpha_2}...x_{m}^{\alpha_m}]\\in\ which:\ n=\alpha_1+\alpha_2+....+\alpha_m,\quad 1\le i_1,i_2...i_n \le m$$
且由于不同的$x_i$之间相互独立，
$$E[x_{1}^{\alpha_1}x_{2}^{\alpha_2}...x_{m}^{\alpha_m}]=E[x_{1}^{\alpha_1}]E[x_{2}^{\alpha_2}]...E[x_{m}^{\alpha_m}]\\in\ which:\ n=\alpha_1+\alpha_2+....+\alpha_m$$
而每个$E[x_{i}^{\alpha_i}]$可由式(1)得到，由此可计算$n$阶中心矩的所有分量。只有每个$x_i$的指数都是偶数的n阶矩分量是非0的。


# 3. 一般正态分布的高阶中心矩

本节中需要张量表示法，会用到第1节中的一些运算符号。

设$\boldsymbol{y}=\{y_{1},y_{2},y_{3}...y_{m}\}^T$ 是均值为0、协方差矩阵为$m\times m$的方阵$P$的服从联合高斯分布的一组随机变量，$m\times m$的方阵$M$是$P$的一个平方根(e.g., lower-triangular Cholesky factorization)，满足$\boldsymbol{P}=\boldsymbol{M}\cdot\boldsymbol{M}^T$（$\sum_k{M_{ik}M_{jk}=P_{ij}}$），  则可以认为$\boldsymbol{y}$是由$\boldsymbol{x}$经矩阵$M$线性变换而来，即$\boldsymbol{y}=\boldsymbol{M}\cdot\boldsymbol{x}$（$y_i=\sum_j{M_{ij}y_j}$），其中$\boldsymbol{x}$服从上节提到的标准联合正态分布，其协方差矩阵是$I$。

> $$ E[\boldsymbol{y}]=E[\boldsymbol{M}\cdot\boldsymbol{x}]=\boldsymbol{M}\cdot E[\boldsymbol{x}]=0\\E[y_iy_j]=E[(\boldsymbol{M_i}\cdot\boldsymbol{x})(\boldsymbol{M_j}\cdot\boldsymbol{x})]=(\boldsymbol{M_i}\boldsymbol{M_j})*E[\boldsymbol{x}^2]=(\boldsymbol{M_i}\boldsymbol{M_j})*\boldsymbol{I}=\sum_k{M_{ik}M_{jk}}=P_{ij}\\therefore\ E[\boldsymbol{y}^2]=\boldsymbol{P} $$





我们来看$\boldsymbol{y}$的高阶矩，同样可以用两种形式写出来

$$E[y_{1}^{\alpha_1}y_{2}^{\alpha_2}...y_{m}^{\alpha_m}]=E[(\boldsymbol{M_1}\cdot\boldsymbol{x})^{\alpha_1}(\boldsymbol{M_2}\cdot\boldsymbol{x})^{\alpha_2}...(\boldsymbol{M_m}\cdot\boldsymbol{x})^{\alpha_m}]=\\
(\boldsymbol{M_1}^{\alpha_1}\boldsymbol{M_2}^{\alpha_2}...\boldsymbol{M_m}^{\alpha_m})*E[\boldsymbol{x}^n]\\
in\ which:\ n=\alpha_1+\alpha_2+....+\alpha_m$$

或

$$E[y_{i_1}y_{i_2}...y_{i_n}]=E[(\boldsymbol{M_{i_1}}\cdot\boldsymbol{x})(\boldsymbol{M_{i_2}}\cdot\boldsymbol{x})...(\boldsymbol{M_{i_n}}\cdot\boldsymbol{x})]=\\ (\boldsymbol{M_{i_1}}\boldsymbol{M_{i_2}}...\boldsymbol{M_{i_n}})*E[\boldsymbol{x}^n]\\in\ which:1\le i_1,i_2...i_n\le m;$$

将上式展开成分量求和形式即是：

$$(\boldsymbol{M_{i_1}}\boldsymbol{M_{i_2}}...\boldsymbol{M_{i_n}})*E[\boldsymbol{x}^n]=\\
\sum_{k_1=1}^{m}\sum_{k_2=1}^{m}...\sum_{k_n=1}^{m}{M_{i_1k_1}M_{i_2k_2}...M_{i_nk_n}E[x_{k_1}x_{k_2}...x_{k_n}]}\\in\ which:1\le i_1,i_2...i_n,k_1,k_2,...k_n\le m;$$

对于奇数阶矩，即$n$为奇数时，因为$E[\boldsymbol{x}^n]$等于0，所以所有的$E[y_{i_1}y_{i_2}...y_{i_n}]$必然是0，$E[\boldsymbol{y}^n]=0$；

偶数阶矩则稍微复杂，下面只分析偶数阶矩，即$n=2t\ (for\ t=1,2,3...)$。

我们只需要考虑$\boldsymbol{x}$的n阶矩中非0的分量，即$k_1,k_2,...k_n$中每个数字都出现了偶数次的分量。如$\boldsymbol{x}$的6阶矩的某个非零分量为$E[x_1x_1x_1x_1x_2x_2]$（即$k_1,k_2,...k_6$分别等于$1,1,1,1,2,2$），$k$下标中1出现了4次，2出现了两次，都是偶数次；

由于求和式的每一非0项中，每个$k$下标都会成对出现(准确得说是出现偶数次)，我们可以将每项中的$M_{i_1k_1},M_{i_2k_2},...M_{i_nk_n}$也进行两两分组，每一组中的两个数具有相同的$k$下标，（想一下：不同组的$k$下标一定不同吗？当然不是，有可能相同，后面会分析）

即：

$$\sum_{k_1=1}^{m}\sum_{k_2=1}^{m}...\sum_{k_n=1}^{m}{M_{i_1k_1}M_{i_2k_2}...M_{i_nk_n}E[x_{k_1}x_{k_2}...x_{k_n}]}=\\
\sum_{k_1=1}^{m}\sum_{k_2=1}^{m}...\sum_{k_n=1}^{m}{(M_{i_{l1}k_{l1}}M_{i_{l2}k_{l2}})(M_{i_{l3}k_{l3}}M_{i_{l4}k_{l4}})...(M_{i_{l(n-1)}k_{l(n-1)}}M_{i_{ln}k_{ln}})}E[(x_{k_{l1}}x_{k_{l2}})(x_{k_{l3}}x_{k_{l4}})...]$$

其中：$\boldsymbol{l}=(l1,l2..ln)$ 是 ($1,2..n$)的一个重排列，满足 ($k_{l1}=k_{l2}, k_{l3}=k_{l4}...$)

如果某一项的$M_{i_*k_*}$中，每个$k$下标都正好只出现2次，那么对该项中所有的$M_{i_*k_*}$进行两两分组的方式是唯一的，分完组后不同的组有不同的$k$下标，而且此时$E[(x_{k_{l1}}x_{k_{l2}})(x_{k_{l3}}x_{k_{l4}})...]=1$；

而如果某一项的$M_{i_*k_*}$中，某些$k$下标出现了4次、6次、8次等，这时该项内$M_{i_*k_*}$的分组方式就不唯一了。假如某个$k$下标$k_x$在该项中出现了$\alpha_x$次($\alpha_x$是偶数)，则该项中$k$下标等于$k_x$的共$\alpha_x$个$M_{i_*k_*}$ ，共有$(\alpha_x-1)!!$种两两分组的方式，而如果$k$下标共有$s$个不同的数字，分别为$(t_1,t_2...t_s)$，每个数字分别出现了$\alpha_1,\alpha_2...\alpha_s$次(都是偶数)，则此时两两分组的方式共有 $(\alpha_1-1)!!(\alpha_2-1)!!...(\alpha_s-1)!!$。巧的是，此时 $$E[(x_{k_{l1}}x_{k_{l2}})(x_{k_{l3}}x_{k_{l4}})...]=E[x_{t_1}^{\alpha_1}x_{t_2}^{\alpha_2}...x_{t_s}^{\alpha_s}]=(\alpha_1-1)!!(\alpha_2-1)!!...(\alpha_s-1)!!$$

因此，原求和式每一非0项中的$E[x_{k_1}x_{k_2}...x_{k_n}]$都恰好代表该项中的$M_{i_*k_*}$有多少种两两分组的方式！这也意味着，原求和式的所有非0项，正好遍历了**将$M_{i_*k_*}$按照$k$下标相同的原则两两分组相乘**的结果，即

$$\sum_{k_1=1}^{m}\sum_{k_2=1}^{m}...\sum_{k_n=1}^{m}{M_{i_1k_1}M_{i_2k_2}...M_{i_nk_n}E[x_{k_1}x_{k_2}...x_{k_n}]}=\\
\sum_{k_1=1}^{m}\sum_{k_2=1}^{m}...\sum_{k_n=1}^{m}\{ \sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}{(M_{i_{l1}k_{l1}}M_{i_{l2}k_{l2}})(M_{i_{l3}k_{l3}}M_{i_{l4}k_{l4}})...(M_{i_{l(n-1)}k_{l(n-1)}}M_{i_{ln}k_{ln}})}\}=\\
\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}\sum_{k_{l1}=k_{l2}=1}^{m}\sum_{k_{l3}=k_{l4}=1}^{m}...\sum_{k_{l(n-1)}=k_{ln}=1}^{m}{(M_{i_{l1}k_{l1}}M_{i_{l2}k_{l2}})(M_{i_{l3}k_{l3}}M_{i_{l4}k_{l4}})...(M_{i_{l(n-1)}k_{l(n-1)}}M_{i_{ln}k_{ln}})}=\\
\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}{(\sum_{k_{l1}=k_{l2}=1}^{m}{M_{i_{l1}k_{l1}}M_{i_{l2}k_{l2}}})(\sum_{k_{l3}=k_{l4}=1}^{m}{M_{i_{l3}k_{l3}}M_{i_{l4}k_{l4}}})...(\sum_{k_{l(n-1)}=k_{ln}=1}^{m}{M_{i_{l(n-1)}k_{l(n-1)}}M_{i_{ln}k_{ln}}})}=\\
\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}{(\sum_{k=1}^{m}{M_{i_{l1}k}M_{i_{l2}k}})(\sum_{k=1}^{m}{M_{i_{l3}k}M_{i_{l4}k}})(\sum_{k=1}^{m}{M_{i_{l(n-1)}k}M_{i_{ln}k}})}=\\
\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}{P_{i_{l1}i_{l2}}P_{i_{l3}i_{l4}}...P_{i_{l(n-1)}i_{ln}}}$$

对于$n$阶矩来说，求和式$\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}$共有 $(n-1)!!$项。

综上，$$E[y_{i_1}y_{i_2}...y_{i_n}]=\sum_{l1,l2...ln=reRange(1,2...n)}^{All Divide Cases}{P_{i_{l1}i_{l2}}P_{i_{l3}i_{l4}}...P_{i_{l(n-1)}i_{ln}}}$$

这个结论叫做 Isserlis’ theorem [https://en.wikipedia.org/wiki/Isserlis%27_theorem] 

下面以四阶和六阶矩为例来演示结论

$$E[y_iy_jy_ky_l]=P_{ij}P_{kl} + P_{ik}P_{jl} + P_{il}P_{jk}\\
E[y_iy_iy_jy_k] = P_{ii}P_{jk} + P_{ij}P_{ik} + P_{ik}P_{ij}=P_{ii}P_{jk}+2P_{ij}P_{ik}\\
E[y_iy_iy_jy_j] = P_{ii}P_{jj} + P_{ij}P_{ij} + P_{ij}P_{ij}=P_{ii}P_{jj}+2P_{ij}^2\\
E[y_iy_iy_iy_j]=P_{ii}P_{ij}+P_{ii}P_{ij}+P_{ij}P_{ii}=3P_{ii}P_{ij}\\
E[y_iy_iy_iy_i]=P_{ii}P_{ii}+P_{ii}P_{ii}+P_{ii}P_{ii}=3P_{ii}^2\\
E[y_iy_jy_ky_ly_my_n]=P_{ij}P_{kl}P_{mn} + P_{ij}P_{km}P_{ln} + P_{ij}P_{kn}P_{lm} +\\
P_{ik}P_{jl}P_{mn} + P_{ik}P_{jm}P_{ln} + P_{ik}P_{jn}P_{lm} +\\
P_{il}P_{jk}P_{mn} + P_{il}P_{jm}P_{kn} + P_{il}P_{jn}P_{km} +\\
P_{im}P_{jk}P_{ln} + P_{im}P_{jl}P_{kn} + P_{im}P_{jn}P_{lk} +\\
P_{in}P_{jk}P_{lm} + P_{in}P_{jl}P_{km} + P_{in}P_{jm}P_{kl}$$



# 4. 随机变量经非线性变换后的期望与协方差矩阵
设 $\boldsymbol{x}$ 是均值为 $\boldsymbol{\overline{x}}$，协方差矩阵为 $\boldsymbol{P_{xx}}$ 的多维随机变量，映射$\boldsymbol{f}$ 可将$\boldsymbol{x}$映射为$\boldsymbol{y}$，且$\boldsymbol{f}$是解析的，即：
$$\boldsymbol{y}=\boldsymbol{f}(\boldsymbol{x})=\\
\boldsymbol{f}(\boldsymbol{\overline{x}}) + \boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x} + \frac{1}{2!}\boldsymbol{\nabla^2 f({\overline{x}})}*\boldsymbol{\delta x^2} + \frac{1}{3!}\boldsymbol{\nabla^3 f({\overline{x}})}*\boldsymbol{\delta x^3} + \frac{1}{4!}\boldsymbol{\nabla^4 f({\overline{x}})}*\boldsymbol{\delta x^4} + ...\\
in\ which:\boldsymbol{\delta x}=\boldsymbol{x} - \boldsymbol{\overline{x}}\tag{1}$$
若$\boldsymbol{y}$是$m$维变量，$\boldsymbol{x}$是$n$维变量，则$\boldsymbol{\nabla^i f({\overline{x}})}$就是一个$m$行$1$列的张量矩阵，其元素都是$i$阶$n$维张量，而$\boldsymbol{\delta x^i}$也是$i$阶$n$维张量，因此$\boldsymbol{\nabla^i f({\overline{x}})}*\boldsymbol{\delta x^i}$退化为一个$m$行$1$列传统矩阵（矢量）。

$\boldsymbol{y}$的期望$E[\boldsymbol{y}]$为：
$$\boldsymbol{\overline{y}} = E[\boldsymbol{y}] = E[\boldsymbol{f}(\boldsymbol{x})]  = \\
\boldsymbol{f}(\boldsymbol{\overline{x}}) + \frac{1}{2!}\boldsymbol{\nabla^2 f({\overline{x}})}*\boldsymbol{P_{xx}} + \frac{1}{3!}\boldsymbol{\nabla^3 f({\overline{x}})}*E[\boldsymbol{\delta x^3}] + \frac{1}{4!}\boldsymbol{\nabla^4 f({\overline{x}})}*E[\boldsymbol{\delta x^4} ]+ ...$$
上式中因为$E[\boldsymbol{\delta x}]=0$所以略去了$\boldsymbol{\nabla f({\overline{x}})}*E[\boldsymbol{\delta x}]$这一项，并用$\boldsymbol{P_{xx}}$替换了$E[\boldsymbol{\delta x^2}]$。
$\boldsymbol{y}$的偏差$\boldsymbol{\delta y}$为：
$$\boldsymbol{\delta y} = \boldsymbol{y}-\boldsymbol{\overline{y}} = \\
\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x}  + \frac{1}{2!}\boldsymbol{\nabla^2 f({\overline{x}})}*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}}) + \frac{1}{3!}\boldsymbol{\nabla^3 f({\overline{x}})}*(\boldsymbol{\delta x^3}-E[\boldsymbol{\delta x^3}]) + \frac{1}{4!}\boldsymbol{\nabla^4 f({\overline{x}})}*(\boldsymbol{\delta x^4}-E[\boldsymbol{\delta x^4}]) + ...$$
偏差$\boldsymbol{y}$的张量平方$\boldsymbol{\delta y^2}$为：
$$\boldsymbol{\delta y^2} =\boldsymbol{\delta y \delta y^T} = (\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})(\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})^T + \\
\frac{1}{2!}(\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})[\boldsymbol{\nabla^2 f({\overline{x}})}*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})]^T +\\
\frac{1}{2!}[\boldsymbol{\nabla^2 f({\overline{x}})}*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})](\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})^T +\\
\frac{1}{3!}(\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})[\boldsymbol{\nabla^3 f({\overline{x}})}*(\boldsymbol{\delta x^3}-E[\boldsymbol{\delta x^3}])]^T+\\
\frac{1}{3!}[\boldsymbol{\nabla^3 f({\overline{x}})}*(\boldsymbol{\delta x^3}-E[\boldsymbol{\delta x^3}])](\boldsymbol{\nabla f({\overline{x}})}*\boldsymbol{\delta x})^T+\\
\frac{1}{2!2!}[\boldsymbol{\nabla^2 f({\overline{x}})}*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})][\boldsymbol{\nabla^2 f({\overline{x}})}*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})]^T  + ...=\\
(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * (\boldsymbol{\delta x})^2 +\\
\frac{1}{2!}(\boldsymbol{\nabla f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})}) * [(\boldsymbol{\delta x})(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})] + \\
\frac{1}{2!}(\boldsymbol{\nabla^2 f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * [(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})(\boldsymbol{\delta x})] + \\
\frac{1}{3!}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})})*[(\boldsymbol{\delta x})(\boldsymbol{\delta x^3}-E[\boldsymbol{\delta x^3}])]+\\
\frac{1}{3!}(\boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )*[(\boldsymbol{\delta x^3}-E[\boldsymbol{\delta x^3}])(\boldsymbol{\delta x})]+\\
\frac{1}{2!2!}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*(\boldsymbol{\delta x^2}-\boldsymbol{P_{xx}})^2 + ...$$
$\boldsymbol{y}$的协方差$E[\boldsymbol{\delta y^2}]$为：
$$E[\boldsymbol{\delta y^2}] = E[\boldsymbol{\delta y \delta y^T}] = (\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * E[(\boldsymbol{\delta x^2})] +\\
\frac{1}{2!}(\boldsymbol{\nabla f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}-\boldsymbol{\delta x}\boldsymbol{P_{xx}}] + \\
\frac{1}{2!}(\boldsymbol{\nabla^2 f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}-\boldsymbol{P_{xx}}\boldsymbol{\delta x}] + \\
\frac{1}{3!}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})})*E[\boldsymbol{\delta x^4}-\boldsymbol{\delta x}E[\boldsymbol{\delta x^3}]]+\\
\frac{1}{3!}(\boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )* E[\boldsymbol{\delta x^4}-E[\boldsymbol{\delta x^3}]\boldsymbol{\delta x}]+\\
\frac{1}{2!2!}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*E[\boldsymbol{\delta x^4}-\boldsymbol{\delta x^2}\boldsymbol{P_{xx}} - \boldsymbol{P_{xx}}\boldsymbol{\delta x^2} + \boldsymbol{P_{xx}}^2] + ...
$$
考虑到 $E[\boldsymbol{\delta x}]=0,\ E[(\boldsymbol{\delta x^2})]=\boldsymbol{P_{xx}}$，上式可进一步简化：
$$E[\boldsymbol{\delta y^2}] = (\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * \boldsymbol{P_{xx}}+\\
\frac{1}{2!}(\boldsymbol{\nabla f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}] + \\
\frac{1}{2!}(\boldsymbol{\nabla^2 f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}] + \\
\frac{1}{3!}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})})*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{3!}(\boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )* E[\boldsymbol{\delta x^4}]+\\
\frac{1}{2!2!}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*(E[\boldsymbol{\delta x^4}]- E[\boldsymbol{\delta x^2}]\boldsymbol{P_{xx}} - \boldsymbol{P_{xx}}E[\boldsymbol{\delta x^2}] +\boldsymbol{P_{xx}}^2) + ...=\\
(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * \boldsymbol{P_{xx}}+\\
\frac{1}{2}(\boldsymbol{\nabla f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})} + \boldsymbol{\nabla^2 f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}] + \\
\frac{1}{6}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})} + \boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{4}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*(E[\boldsymbol{\delta x^4}]-\boldsymbol{P_{xx}}^2) + ...$$
整理下结果就是：
$$E[\boldsymbol{y}] = \boldsymbol{f}(\boldsymbol{\overline{x}}) + \frac{1}{2!}\boldsymbol{\nabla^2 f({\overline{x}})}*\boldsymbol{P_{xx}} + \\
\frac{1}{3!}\boldsymbol{\nabla^3 f({\overline{x}})}*E[\boldsymbol{\delta x^3}] + \frac{1}{4!}\boldsymbol{\nabla^4 f({\overline{x}})}*E[\boldsymbol{\delta x^4} ]+ ...\tag{2-a}$$
$$E[\boldsymbol{\delta y^2}] = (\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * \boldsymbol{P_{xx}}+\\
\frac{1}{2}(\boldsymbol{\nabla f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})} + \boldsymbol{\nabla^2 f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * E[\boldsymbol{\delta x^3}] + \\
\frac{1}{6}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})} + \boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{4}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*(E[\boldsymbol{\delta x^4}]-\boldsymbol{P_{xx}}^2) + ...\tag{3-a}$$
对应的分量形式：
$$E[y_i] = f_i(\boldsymbol{\overline{x}}) + \frac{1}{2!}\boldsymbol{\nabla^2} f_i(\boldsymbol{\overline{x}})*\boldsymbol{P_{xx}} + \\
\frac{1}{3!}\boldsymbol{\nabla^3} f_i(\boldsymbol{\overline{x}})*E[\boldsymbol{\delta x^3}] + \frac{1}{4!}\boldsymbol{\nabla^4}f_i(\boldsymbol{\overline{x}})*E[\boldsymbol{\delta x^4} ]+ ...\tag{2-b}$$
$$E[y_iy_j] = (\boldsymbol{\nabla} f_i(\boldsymbol{\overline{x}}) \boldsymbol{\nabla} f_j(\boldsymbol{\overline{x}})) * \boldsymbol{P_{xx}}+\\
\frac{1}{2}(\boldsymbol{\nabla} f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla^{2} f_j(\boldsymbol{\overline{x}})} + \boldsymbol{\nabla^2} f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla^{}} f_j(\boldsymbol{\overline{x}})) * E[\boldsymbol{\delta x^3}] + \\
\frac{1}{6}(\boldsymbol{\nabla} f_i(\boldsymbol{\overline{x}}) \boldsymbol{\nabla^{3}} f_j(\boldsymbol{\overline{x}}) + \boldsymbol{\nabla^3}f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla} f_j(\boldsymbol{\overline{x}}) )*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{4}(\boldsymbol{\nabla^2} f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla^{2}} f_j(\boldsymbol{\overline{x}}))*(E[\boldsymbol{\delta x^4}]-\boldsymbol{P_{xx}}^2) + ...\tag{3-b}$$


如果通过先验知识已知随机变量 $\boldsymbol{x}$ 的三阶(中心)矩为0（比如$\boldsymbol{x}$是中心对称分布的所有奇数阶矩都为0），则上式可直接去掉3阶项（甚至后续的所有奇数阶项），进一步简化为：
$$E[\boldsymbol{y}] =\boldsymbol{\overline{y}} = \boldsymbol{f}(\boldsymbol{\overline{x}}) + \frac{1}{2!}\boldsymbol{\nabla^2 f({\overline{x}})}*\boldsymbol{P_{xx}} + \frac{1}{4!}\boldsymbol{\nabla^4 f({\overline{x}})}*E[\boldsymbol{\delta x^4} ]+ ...\tag{4-a}$$
$$E[\boldsymbol{\delta y^2}] = (\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{T} f({\overline{x}})}) * \boldsymbol{P_{xx}} + \\
\frac{1}{6}(\boldsymbol{\nabla f({\overline{x}})} \boldsymbol{\nabla^{3T} f({\overline{x}})} + \boldsymbol{\nabla^3 f({\overline{x}})}\boldsymbol{\nabla^{T} f({\overline{x}})} )*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{4}(\boldsymbol{\nabla^2 f({\overline{x}})}\boldsymbol{\nabla^{2T} f({\overline{x}})})*(E[\boldsymbol{\delta x^4}]-\boldsymbol{P_{xx}}^2) + ...\tag{5-a}$$
对应的分量形式：
$$E[y_i] = f_i(\boldsymbol{\overline{x}}) + \frac{1}{2!}\boldsymbol{\nabla^2} f_i(\boldsymbol{\overline{x}})*\boldsymbol{P_{xx}} + \frac{1}{4!}\boldsymbol{\nabla^4}f_i(\boldsymbol{\overline{x}})*E[\boldsymbol{\delta x^4} ]+ ...\tag{4-b}$$
$$E[y_iy_j] = (\boldsymbol{\nabla} f_i(\boldsymbol{\overline{x}}) \boldsymbol{\nabla} f_j(\boldsymbol{\overline{x}})) * \boldsymbol{P_{xx}}+\\
\frac{1}{6}(\boldsymbol{\nabla} f_i(\boldsymbol{\overline{x}}) \boldsymbol{\nabla^{3}} f_j(\boldsymbol{\overline{x}}) + \boldsymbol{\nabla^3}f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla} f_j(\boldsymbol{\overline{x}}) )*E[\boldsymbol{\delta x^4}]+\\
\frac{1}{4}(\boldsymbol{\nabla^2} f_i(\boldsymbol{\overline{x}})\boldsymbol{\nabla^{2}} f_j(\boldsymbol{\overline{x}}))*(E[\boldsymbol{\delta x^4}]-\boldsymbol{P_{xx}}^2) + ...\tag{5-b}$$
