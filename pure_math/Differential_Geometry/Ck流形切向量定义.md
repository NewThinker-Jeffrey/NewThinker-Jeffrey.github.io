
# $C^k$流形切向量定义

[TOC]

## 分析方面的一些小知识点

### $C^r,C^s$ 函数间的乘积

一个美好的猜想是 $C^r$函数与$C^s$函数之积是$C^{r+s}$的，但通常这并不成立。

#### $C^0\cdot C^0=C^0$ 

连续函数之积是连续的，这没什么可说的

#### $C^0\cdot C^s=C^?$

我们假设$s\ge 1$。
$C^r$函数与$C^s$函数之积只能保证是$C^0$的。
比如，定义$f(x)$如下，
$$
f(x)=|x|=\left\{
\begin{aligned}
x,  && x\ge 0 \\
-x,  && x\lt 0 \\
\end{aligned}
\right.
$$
$f$是连续的($C^0$的)，但在$x=0$处不可导，所以$f$是$C^0$的；定义$g(x)=x+1$，$g$是$C^\infty$的。但$fg$依然是$C^0$的，因为$fg$在$x=0$处不可导。
> $x\ge 0$时，$(fg)(x)=x(x+1)$，$(fg)'(x)=2x+1$，$(fg)'(0^+)=1$
> $x\lt 0$时，$(fg)(x)=-x(x+1)$，$(fg)'(x)=-2x-1$，$(fg)'(0^-)=-1$

但在特殊情况下，$(fg)$可在某点处可导：
**若函数$g$满足$g(x_0)=0$，且$g\in C^1$（事实上可以放宽条件，只要求$g$在$x_0$处可导），$f\in C^0$，则$fg$在 $x_0=0$处可导。**
$\frac{\Delta {(fg)}}{\Delta x}=\frac{(f+\Delta f)(g+\Delta g)-fg}{\Delta x}=\frac{(\Delta f)g+\Delta f\Delta g+f\Delta g}{\Delta x}$
当$\Delta x\rightarrow 0$时，
$\frac{f\Delta g}{\Delta x} \rightarrow fg'$
$\frac{\Delta f\Delta g}{\Delta x} \rightarrow (\Delta f)g'$，而$\Delta f\rightarrow 0$，因此$\frac{\Delta f\Delta g}{\Delta x} \rightarrow 0$
由于$f$只是$C^0$的，所以 $\frac{(\Delta f)g}{\Delta x}$ 通常没有极限。但如果假设$g(x_0)=0$，那么在$x_0$附近我们有 $\frac{(\Delta f)g}{\Delta x}=\frac{\Delta f\Delta g}{\Delta x}\rightarrow 0$。但在其他点这个极限一般不成立。
这样，在$x_0$处，我们有$$\lim_{\Delta_x\rightarrow 0}\frac{\Delta {(fg)}}{\Delta x}=fg'+0+0=fg‘$$

推论：
$g(x_0)=0$，$g\in C^{s\ge 2}$（可以放宽条件，只要求$g$在$x_0$处存在$s$阶导数），且$g$的前$s-1$阶导数在$x_0$处都为0，$f\in C^0$。则$fg$在 $x_0=0$处 $n$ 次可导。
前面已证，$(fg}'(x_0)=f(x_0)g'(x_0)$，$fg$ 在 $x_0=0$处一次可导；
依假设，$g'(x_0)=0$，$g'\in C^{n-1\ge 1}$，因此 $fg'$ 在$x_0$也存在导数$fg''$，即$fg$ 在 $x_0=0$处二次可导；
以此类推，$fg$ 在 $x_0=0$处是 $s$次可导的。
事实上对于$k\lt s$，由于已假设 $g^{(k)}(x_0)=0$，所以$(fg)$在$x_0$处的$k$阶导数也等于0。

> 函数在某点处可导，是否一定在该点的某邻域内可导？No.
consider $f(x) = x^2$ if $x$ is rational and $x^3$ if $x$ is irrational. This is differentiable at 0 but not differentiable anywhere else.
> 若函数在某个邻域内可导，则导数在该邻域内必连续，进而函数在该邻域上是$C^1$的？No
https://en.wikipedia.org/wiki/Differentiable_function#Differentiability_classes

#### $C^r\cdot C^s=C^?$

先说结论：不妨假设$r\le s$。那么，$C^r$函数与 $C^s$函数之积，只能保证是 $C^r$的。
一般，如果$f,g$的可导次数够高，我们可以按照下式一直写下去
$$
(fg)'=f'g+fg'=(f''g+f'g')+(f'g'+f''g')=...
$$
当求$r$次导数后，和式中将出现 $C^0\cdot C^{s-r}$的分量。而按上节所述，这样的分量只能保证是 $C^0$的。
所以$C^r$函数与 $C^s$函数之积最多只能保证是 $C^r$的。注意我们假设了$r\le s$。
我们还需要证明，$C^r$函数与 $C^s$函数之积的可微性必须能达到$C^r$。事实上只需证明 $C^r\cdot C^r=C^r$，因为 $C^s\subset C^r$。
首先，$C^0\cdot C^r=C^0$。
如果$C^k\cdot C^k=C^k$成立，可证 $C^{k+1}\cdot C^{k+1}=C^{k+1}$也成立，因为若$f,g\in C^{k+1}$，则$f',g'\in C^k$，那么$[(fg)'=f'g+fg']\in C^k$，所以$(fg)\in C^{k+1}$。

#### 多维情形的推广，向量函数: $\mathbb R^n\rightarrow \mathbb R$

上述结论可推广至多维情形（向量函数）：
- $C^0(\mathbb R^n)$ 函数 与 $C^s(\mathbb R^n)$ 函数 之积（**逐点相乘**得到的新函数）只能保证是 $C^0(\mathbb R^n)$ 的；（把一维函数延拓为多维，再考虑该一维方向的方向导数，就可以利用一维情形的反例了）
- 如果$x_0\in \mathbb R^n$，函数$g(x_0)=0$，$g\in C^1(\mathbb R^n)$ (事实上只要求$g'(x_0)$存在)，$f\in C^0(\mathbb R^n)$，那么$(fg)'(x_0)$存在且 等于 $g’(x_0)f(x_0)$。注意$g’(x_0)$ 是个矩阵(方阵)，$f(x_0)$ 是向量(列矩阵)，二者可以矩阵相乘。
> 用不太规范、但更直观的写法推导一下。"不规范"是因为，对于$\frac{\Delta {(fg)}}{\Delta x}$这样的表达式，其分母和分子都是向量，这种"分数"只是代表某个矩阵$M$(方阵)，满足$M\Delta x=\Delta {(fg)}$。
$\frac{\Delta {(fg)}}{\Delta x}=\frac{(f+\Delta f)(g+\Delta g)-fg}{\Delta x}=\frac{(\Delta f)g+\Delta f\Delta g+f\Delta g}{\Delta x}$
当$\Delta x\rightarrow 0$时，
$\frac{f\Delta g}{\Delta x} \rightarrow fg'$
$\frac{\Delta f\Delta g}{\Delta x} \rightarrow (\Delta f)g'$，而$\Delta f\rightarrow 0$，因此$\frac{\Delta f\Delta g}{\Delta x} \rightarrow 0$
由于$f$只是$C^0$的，所以 $\frac{(\Delta f)g}{\Delta x}$ 通常没有极限。但如果假设$g(x_0)=0$，那么在$x_0$附近我们有 $\frac{(\Delta f)g}{\Delta x}=\frac{\Delta f\Delta g}{\Delta x}\rightarrow 0$。但在其他点这个极限一般不成立。
这样，在$x_0$处，我们有$$\lim_{\Delta_x\rightarrow 0}\frac{\Delta {(fg)}}{\Delta x}=fg'+0+0=fg‘$$


todo

### $C^r\circ C^r=C^?$

下面考虑$r\ge 1$的情况，设$f,g\in C^r$。
$(f\circ g)(x)=f(g(x))$
$(f\circ g)'(x)=f'(g(x))g'(x)=(f'\circ g)(x)\cdot g'(x)$
$(f\circ g)'=(f'\circ g)\cdot g'$

我们证明$C^r\circ C^r=C^r$。
首先，考虑$r=1$的情况，此时，$f',g'$都是$C^0$的，$g$是$C^1$的自然也是$C^0$的。
由于$C^0\circ C^0=C^0$（连续函数的级联还是连续的），$f'\circ g$是$C^0$的；
由于$C^0\cdot C^0=C^0$， 因此 $(f'\circ g)\cdot g$是$C^0$的，即$(f\circ g)'$是$C^0$的，进而$f\circ g$是$C^1$的。

若已知 $C^k\circ C^k=C^k$，设$f\in C^{k+1},g\in C^{k+1}$。
依定义，$f\in C^{k+1}$，故$f'\in C^k$，而$g\in C^{k+1} \subset C^k$，因此，依照归纳假设$C^k\circ C^k=C^k$，$f'\circ g$ 是$C^k$的；同时， $g'$ 也是$C^k$；$C^k$乘以$C^k$还是$C^k$。因此$(f\circ g)'$是$C^k$，进而$f\circ g$是 $C^{k+1}$.

### $C^\infty \cdot C^\infty$, $ C^\infty\circ C^\infty$

有了前面对有限阶连续可导情况的铺垫，显然：
$C^\infty \cdot C^\infty=C^\infty$
$C^\infty \circ C^\infty=C^\infty$


### $C^\omega \cdot C^\omega$, $ C^\omega \circ C^\omega$

对于解析$C^\omega$的情况，不能只考虑是否无穷连续可导，还要考虑taylor级数是否有非0的收敛半径

####  $C^\omega \cdot C^\omega=C^\omega$

若$f,g\in C^\omega$，且在点$x$处它们的收敛半径半径分别为$r_f,r_g$，那么$min(r_f,r_g)$ 是 $fg$ 的收敛半径；

####  $C^\omega \circ C^\omega=C^\omega$

若 $f$ 在$x$ 处的收敛半径为 $r_f$， $g$在$f(x)$处的收敛半径为 $r_g$，以$f(x)$为中心的半径为$r_g$的开球在映射$g$下的原像 $g^{-1}[B(f(x), r_g)]$ 
$g(f)$ 是包含点$x$的开集，选一个包含于此开集中的、以$x$为球心且半径小于$r_f$ 的开球（设此开球半径为 $r_{gf}$）,则 $r_{gf}$是$g\circ f$在$x$处的一个收敛半径

> 解析函数的加减乘除（要求除数非0）都是解析的；常见的初等函数是解析的；解析函数的复合是解析的；
所以能写成初等函数的复合的函数（在各成分函数都有定义且解析的地方）都是解析的；我们初高中或大学时期对"解析解"的理解一般也局限在该解能否写成初等函数的复合。


### 泰勒定理

todo


## 流形$M$上一点$p$处"切/余切向量"和"切/余切空间"的定义

### 余切空间

所有在$p$的某个邻域内有定义的$C^1$函数组成的集合记为 $C^1_p$。
> $C^1_p$是一个$\mathbb R$线性空间。注意函数$f,g$间的加法定义为逐点相加，且定义域取二者定义域的交集：
$(f+g): Dom(f)\cap Dom(g) \rightarrow \mathbb R$
$(f+g): p \mapsto f(p)+g(p)$

在$C^1_p$上定义等价关系如下：
对于$f,g\in C^1_p$，定义 $f\sim_p g$，如果对任意以 $p$ 为中心的$C^r$坐标图 $(U, \phi)$, $p\in U, \phi(p)=0$，有：
$\nabla(f\circ\phi^{-1})|_0=\nabla (g\circ\phi^{-1})|_0$
注意 $g\circ\phi^{-1}$ 和 $f\circ\phi^{-1}$ 是定义在 $R^m$ 的开集 $\phi(U)$ 上的函数，且至少是$C^1$ 的。上式是说它们在0点处的欧氏梯度相等。
实际上，若 $f,g$ 能对任何一个$C^r$坐标图有以上关系，它们就将对所有$C^r$坐标图都有以上关系 （因为坐标图之间的$C^k$兼容性）。
我们记函数$f$在此等价关系下的等价类记为$df|_p$，所有这样的等价类组成的集合记为 $T^*_p(M)$。在函数等价类集合$T^*_p(M)$ 上可以定义加法和数乘两个运算如下：
$df+dg=df+dg$
$\lambda df=d\lambda f$
由欧式梯度的线性性质，易证上述两个运算是良定义的。因此$T^*_p(M)$ 构成一个线性空间，称为$p$点处的余切空间 Cotangent space。而每个$df$与坐标图$(U, \phi)$中原点处的"梯度向量"一一对应，容易验证且这个对应关系实际上是一个线性同构，而所有"梯度向量"组成的“梯度空间”是$m$维的，因此易知 $T^*_p(M)$ 的维数为$m$。

> 也可以用不依赖坐标图的方式来定义上述等价关系，而是依赖过$p$点的$C^1$曲线族: 函数$f,g$等价，如果对任意$C^1$曲线$c: (-\epsilon, \epsilon)\rightarrow M, c(0)=p$, 总有$\frac{d}{dt}(f\circ c)|_0=\frac{d}{dt}(g\circ c)|_0$。参考陈的微分几何讲义。
只不过，在讲义中，陈定义了两层等价关系：先定义了函数芽 germ (两个函数"芽等价"当且仅当它们在$p$的某个邻域内相等)，又在函数芽上定义了等价类。我这里直接用第二层等价关系了，最终得到的等价类是相同的。可将函数 $f$ 的germ等价类记作 $[f]_p$，最终等价类记作 $df|_p$。
函数空间$C^1_p$中，由所有在$p$的某个邻域内恒为0的函数全体组成的子空间记作$C^1_p[0]$，$C^1_p$商掉$C^1_p[0]$即得到芽函数空间 $Germ_p=C^1_p/(C^1_p[0])$
芽空间$Germ_p$中，由所有在$p$点处沿任意$C^1$曲线的导数都为0的函数(芽)全体组成的子空间记为$\nabla_p[0]$，$Germ_p$商掉$\nabla_p[0]$ 即得到余切空间 $T^*_p(M)=Germ_p/\nabla_p[0]=\{C^1_p/(C^1_p[0])\}/\nabla_p[0]$。
陈的讲义中也用更正式的方法证明了 $T^*_p(M)$ 是一个$m$维线性空间。

### 切空间

然后，可用线性空间$T^*_p(M)$ 上的对偶向量(线性泛函) 来定义切向量：（也可按照曲线等价类来定义切向量，曲线等价类与函数等价类有对偶关系）

若 $X_p: T^*_p(M) \rightarrow \mathbb R$是$ T^*_p(M)$上的线性泛函，即：$X_p(\lambda df|_p+dg|_p)=\lambda X_p(df|_p) + X_p(dg|_p)$，则称$X_p$为$p$点处的一个切向量。
$X_p$也可看做是 $C^1_p$ 上的泛函，它作用于函数$f$的值定义为它作用于等价类$df|_p$时的值： $X_p(f)=X_p(df|_p)$。

容易证明，定义域扩展为$C^1_p$后的 $X_p$  满足：
- 线性泛函条件（依定义显然）：$X_p(\lambda f+g)=\lambda X_p(f) + X_p(g)$ 
- 莱布尼兹条件：$X_p(fg)=f_pX_p(g) + g_pX_p(f)$  
> 莱布尼兹条件证明：选定坐标图 $(U, \phi),  \phi(p)=0$。 依定义，$X_p(fg) = X_p(d(fg)|_p) $，而$d(fg)|_p$ 代表的是所有在原点处梯度等于 $\nabla((fg)\circ\phi^{-1})|_0$的函数组成的等价类。
记 $F=f\circ\phi^{-1},G=g\circ\phi^{-1}$, 我们有
$\nabla((fg)\circ\phi^{-1})=\nabla((f\circ\phi^{-1})(g\circ\phi^{-1}))=\nabla(FG)=F\nabla G+G\nabla F$
$\nabla((fg)\circ\phi^{-1})|_0=F(0)\nabla G(0)+G(0)\nabla F(0) = f_p\nabla(g\circ\phi^{-1})|_0+g_p\nabla(f\circ\phi^{-1})|_0$
由之前提到的“梯度空间”与$T^*_p(M)$ 间的线性同构：
$\nabla((fg)\circ\phi^{-1})|_0 \mapsto d(fg)|_p, \nabla(g\circ\phi^{-1})|_0\mapsto dg|_p,  \nabla(f\circ\phi^{-1})|_0\mapsto df|_p$
马上就有 $d(fg)|_p=f_pdg|_p+g_pdf|_p$。而由$X_p$的线性泛函性质，可得$X_p(d(fg)|_p)=f_pX_p(dg|_p) + g_pX_p(df|_p)$ ，进而 $X_p(fg)=f_pX_p(g) + g_pX_p(f)$  

反过来，满足上面两个条件的泛函也一定是一个切向量（需要斟酌对$C^1$空间和函数是否成立？【todo】）。所以上面两个条件也可以直接作为切向量的定义。

所有切向量组成的集合记为$T_p(M)$，易证这是个线性空间，称为$p$点处的切空间。

### 思考：怎么理解 $df$ 这个符号

设$f:M\rightarrow R$，那该怎么理解$df$ ?
有几种理解它的方式：
- $df$ 是函数 $f$ 所述的等价类（等价关系的定义见上面）；
- $df$ 是一个 1-form，或者说是一个 对偶向量场，（或者说是切空间的泛函场？为每点赋值一个该点处切空间上的泛函）。
- 将$R$本身也看做一维流形，$f$是流形间的映射，$df$就是这个映射的微分，或者叫切映射，即$df=f_*$。$df_p=f_{*,p}$ 把$M$上一点$p$处的切向量映射为$R$上 $f(p)$处的一个切向量 。注意 $f(p)$ 处的切空间是一维的，所以$f(p)$处的切向量可用一个标量表示（即该切向量在标准基底 $\frac{d}{dt}$ 下的分量），所以可以认为$df_p=f_{*,p}$  把$M$上一点$p$处的切向量映射为一个数。

> 而在传统微积分中，$df$ 通常"不严格地"被理解为函数$f$的一个无穷小增量；每个 1-form $\omega$ 在传统微积分中都理解作某个函数$g$的一个无穷小增量$dg=\omega$。这种传统的理解在数学上并不严格，但可以辅助思考和计算。

