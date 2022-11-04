[TOC]


## 微分形式和外微分的自然定义

### 欧氏空间的微分形式

为什么微分形式要被定义为反对称的多线性变换？而不是其他形式？
这里我们主要讨论最自然的欧氏空间的微分形式。
首先思考一个问题，假如忽略欧氏空间的自然(黎曼)度量、以及由其诱导的"体积"，我们又该如何讨论"体积"的概念？ 体积可以是个任意性很强的概念。

我们先了解下有向 $n$ 维超平行体 (n-parallelotope) ：n-维超平行体可看做由 n 个向量围成的区域。举例：一般地，一维"平行体"是线段，二维是平行四边形，三维是平行六面体。而"有向"，即考虑围成超平行体的n个向量的方向和顺序。
但注意，如果n个向量中有某两个向量是平行的（更一般地说，如果这n个向量是线性相关的），那它们围成的区域就不再是一般的$n$维超平行体了，而是被"拍扁"退化从而损失了维度；
此外还有有向n维体积 (n-volume) 的概念。举例，一维体积是线段长度，二维体积就是面积，三维体积就是常说的体积。"有向"，就是为体积加上正负号。

欧氏空间的一个k-微分形式就是这样一种东西：
- 微观的说，它为空间中各处的（无穷小）k维超平行体定义了（无穷小）有向"k-体积"
- 或者宏观地说，它为空间中的 k-surface k维超曲面定义了有向"k-体积"

> 对于一般的微分形式，有向"k-体积"是个实数，有正负；但还有 vector valued forms, 这时的有向"k-体积"还可以是个vector；甚至还有 vector bundle valued forms，此时有向"k-体积"的概念更为抽象，甚至不再具有指导意义。

从微观到宏观，就是把无数个不重叠（或这有边缘重叠）的无穷小k维超平行体拼接成一个k-surface。k-surface的体积，就是对无数个无穷小k维超平行体的体积求和，或者说"积分"。
再思考一个问题：我们在做积分时，到底在积什么？注意这里我们考虑的是有向积分。
最简单的积分，线积分。每小段片段乘一个线长(一维体积)＂比例＂(函数在该片段附近的值) 再加起来;
多元函数，多维空间中的＂片段＂，就是一个高维的＂平行四边形＂，n个无穷小向量。＂比例＂必须对每个向量线性，因此是n线性的; 任意两个向量同向时，高维的＂平行四边形＂被拍扁，体积为0; 由此公设，可推出多维空间中被积分的"比例"，在每一点附近是个反对称的多线性映射，即现在定义的微分形式;
更详尽的阐述见terence-tao笔记。



### 欧氏空间的外微分

设 $V=\mathbb {R}^n$ 是我们所讨论的欧氏空间，$\omega: V^k\rightarrow \mathbb {R}$ 是个 $k$-form。这里不妨设$\omega$是 $\omega=a(dx^1\wedge dx^2...\wedge dx^k)=adx^I$这样的简单形式。

微分形式再取微分，得到的是个什么东西？
直接按最自然的想法，对系数$a$取全微分（类似于对向量场求微分），得到
$D \omega=Da\otimes (dx^1\wedge dx^2...\wedge dx^k)$，
to be more specific,
$(D \omega)(X_0, X_1, X_2, ... X_k) = \langle Da, X_0 \rangle \cdot dx^I(X_1, ... X_k)$.

可以看到通过这种方式，我们对$\omega$ 这个 k-form（同时也是一个k阶张量）取某种意义上的微分之后，得到的是个 $k+1$ 阶张量。但这个张量不是反对称的，因而它不再是个微分形式；
我们希望构造一种"自然"的微分运算$d$，它作用于一个$k-form$后得到的不仅仅是个$(k+1)$阶张量，而同时是一个$(k+1)$-form.

> 从代数角度，把这个$D \omega$  直接用反对称算子进行反对称化，得到的 $A(D \omega)$ 就是个$k+1$ form了；但我们更希望从几何和分析角度得到这个形式。

从$D \omega$ 的定义可以看出，作为一个$(k+1)$-linear function，它第1个参数$X_0$的作用相当于一个积分方向（对$Da$积分），其他参数则作为 $dx^I()$ 的参数，然后两部分乘在一起。
为什么把第一个而不是其他参数作为积分方向呢？这使得各个参数的作用有些不够对称。
设$D_i$是仿照$D$定义的另一个张量微分，只不过它是把第$i+1$个参数作为积分方向，即 
$(D_i \omega)(X_0, X_1, X_2, ... X_k)=(D \omega)(\underbrace {X_i}_{\text{put }X_i\text{ first}} , \underbrace{X_0, X_1, X_2, ... \hat{X_i} ... X_{k}}_{\text{remove }X_i}) $，
其中 $X_0, X_1, X_2, ... X_k$ 是欧氏空间中的向量（或常向量场）， $\hat{X_i} $代表移除$X_i$。
$D_i$应该与原来的$D=D_0$有某种平等的地位，区别只是它们把代表积分方向的参数在参数列表中的不同位置；

另外要注意到，$(D_i\omega)(X_0, X_1... X_i ... X_k)\Delta t = \langle Da, X_i\Delta t \rangle  \cdot dx^I(X_0, ...\hat{X_i}... X_k)$ 表示的是：$(X_0, X_1 ... X_k)$ 围成的无穷小$k+1$维超平行体沿$X_i$方向相对的两个超平行面 $(X_0, X_1... \hat{X_i} ... X_k)$上，$\omega$的积分值的差值。

对于有向超平行体$(X_0, X_1 ... X_k)$ ，它的"边界（面）"由 $k+1$对 互相正对的超平行面组成，其中互相正对的两个超平行面有相反的朝向。
> 超平行体$(X_0, X_1 ... X_k)$的"边界（面）"是一个单纯复形，构成该超平行体的向量和顺序给了该复形一个orientation。各边界平面的orientation也由此而来。

把所有正对的超平行面的差值（带符号）加起来，得到的 $(\sum_{i=0}^k{(-1)^i D_i}\omega)(X_0, X_1... X_i ... X_k)\Delta t$，其实就是$\omega$沿整个"边界"的积分；
> 符号项$(-1)^i$从哪里来? 参考代数拓扑中 $(k+1)$-simplex 的边界定义：
$\partial (X_0, X_1 ... X_k)=\sum_{i=0}{k}{(-1)^i(X_0, X_1...\hat{X_i} ... X_k)}$

现在，我们定义 $d=\sum_{i=0}^k {(-1)^i D_i}$。可以发现，这样得到的 $d$ 就是反对称的了，$d\omega$是个 $k+1$-form。
而且，$(\sum_{i=0}^k{(-1)^i D_i}\omega)(X_0, X_1... X_i ... X_k)\Delta t = (d\omega)(X_0, X_1... X_i ... X_k)\Delta t)$ 正好也是 $d\omega$ 沿有向$k+1$-超平行体$(X_0, X_1 ... X_k)$的积分；这里我们看到了斯托克斯定理的雏形：$d\omega$沿无限小超平行体的积分，等于$\omega$沿该超平行体的边界的积分。

为了推广到流形上，需要考虑微分形式对（变化的）向量场的"积分"。
设 $X_0, X_1, X_2, ... X_k$ 是欧氏空间中的向量场，则
$(D \omega)(X_0, X_1, X_2, ... X_k) =  (X_0\omega)(X_1, X_2, ... X_k) $，
注意到
$X_0(\omega(X_1, X_2, ... X_k)) = (X_0\omega)(X_1, X_2, ... X_k) + \sum_{i=1}^k \omega(X_1, X_2, ...  X_0(X_i) ...X_k)$
于是
$$(D \omega)(X_0, X_1, X_2, ... X_k) = X_0(\omega(X_1, X_2, ... X_k))  -  \sum_{j=1}^k \omega(X_1, X_2, ...  X_0(X_j) ...X_k)$$
同理
$$(D_i \omega)(X_0, X_1, X_2, ... X_k) = X_i(\omega(X_0, X_1 ... \hat{X_i}... X_k))  -  \sum_{j=1}^k \omega(X_1, X_2, ...  X_i(X_j) ...X_k)$$
代入$d=\sum_{i=0}^k {(-1)^i D_i}$，得
$$(d\omega)(X_0, X_1, X_2, ... X_k) = \sum_{i=0}^k (-1)^iX_i(\omega(X_0, X_1 ... \hat{X_i}... X_k))+ \sum_{i\lt j} (-1)^{i+j}\omega(X_i(X_j)-X_j(X_i),X_0, X_1 ... \hat{X_i} ... \hat{X_j} ... X_k) \tag{欧氏空间外导数定义式A}$$
另外，注意到在欧氏空间下， $X_i(X_j)-X_j(X_i)=X_iX_j-X_jX_i=[X_i, X_j]$，所以最终得到
$$(d\omega)(X_0, X_1, X_2, ... X_k) = \sum_{i=0}^k (-1)^iX_i(\omega(X_0, X_1 ... \hat{X_i}... X_k))+ \sum_{i\lt j} (-1)^{i+j}\omega([X_i,X_j],X_0, X_1 ... \hat{X_i} ... \hat{X_j} ... X_k)\tag{外导数定义式B}$$ 

这个外微分定义可以推广到流形上（参考 KMS 7.8)。注意此公式同样适用于 vector valued forms 的外微分。
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/0cc489a84acab5c141474a8d7137ff1a.png)


当$k=1$，$\psi$ 是 1-form 时，我们有
$d\psi(X_0,X_1)=X_0(\psi(X_1)) - X_1(\psi(X_0)) - \psi ([X_0, X_1]) $
> KMS 书中，9.7. Local description, Lemma 中Maurer-Cartan formula的证明部分的最后会用到此式。

相关内容
- 外微分的＂自然＂(几何) 定义 (KMS 7.8)
- 外微分的公理定义，公理唯一确定外微分  (陈省身Chapter 3 Theorem 2.1，公理4条)
- 一阶导子条件 (陈省身书中公理1和2，线性+lebniz，参考切向量作为导数的定义，代数上的导子) (一阶导子并不唯一，外微分同样作为一阶导子，其唯一性依赖了公理3和4)



## tangent bundle valued forms 的协变外微分

我们先来观察 tangen bundle valued forms 。若底流形为M，即TM-valued forms。
通过选取局部坐标系，tangent bundle valued forms 可以变成在局部坐标系中的 vector valued forms，更具体一点可以说是 $\mathbb R^n$ valued forms。
我们现在尝试定义 tangen valued forms 的外微分。
观察可以发现，上面外微分定义式中第二个求和项中的 $\omega([X_i,X_j],X_0, X_1 ... \hat{X_i} ... \hat{X_j} ... X_k)$ 随局部坐标系的不同选择是协变的。但第一项中的 $X_i(\omega(X_0, X_1 ... \hat{X_i}... X_k))$  不是协变的，所以选取不同的局部坐标系得到的外微分并“不一致”。
> 对于一般的 vector bundle valued forms, 也可以通过局部坐标把他们局部转化为 vector valued forms，从而定义一个依赖于坐标系的外微分；但同样也有不同局部坐标系给出“不一致”的外微分的问题。

但如果TM上有了联络，把 $X_i(\omega(...))$ 改成 $\nabla_{X_i}(\omega(...))$（注意这时$\omega(...)$返回的是个切向量场，而不是一个$\mathbb$值函数），整个式子就是协变的了。把外微分这样修改之后，就是协变外微分。
$$(d_{\nabla}\omega)(X_0, X_1, X_2, ... X_k) = \sum_{i=0}^k (-1)^i\nabla_{X_i}(\omega(X_0, X_1 ... \hat{X_i}... X_k))+ \sum_{i\lt j} (-1)^{i+j}\omega([X_i,X_j],X_0, X_1 ... \hat{X_i} ... \hat{X_j} ... X_k)\tag{vector valued forms的协变外导数定义式C}$$ 
for $X_0, X_1, X_2, ... X_k\in \Gamma (TM)$

参考 KMS  11.13. Covariant exterior derivative. (书中这个定义作用范围更广，不只是针对切丛，而可以是一般向量丛)
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/ba7fc8d903cdb960085aebfcde6c18e4.png)


> 疑问：欧氏空间外导数定义式A中，第二项中最开始包含的是 $X_i(X_j)-X_j(X_i)$，如果这里也替换成$\nabla_{X_i} X_j - \nabla_{X_j}X_i$，它就不再等于 $[X_i-X_j]$，而是还要加上联络的挠率。但实际上我们并不会在第二项中加入挠率。

## 纤维丛上的协变外微分

(纤维丛上的联络，给出了纤维上的点如何平移。我们要进一步想想，纤维上的切向量如何平移？然后可以发现，我们可以定义纤维上的向量场X沿另一个"水平"向量场的平移，求导方向必须水平。这个定义扩展到非水平向量场，就是只取该场的水平部分。)

考虑纤维丛 E 上的 VE-valued forms（在竖直丛上取值的外形式）。
> 最general的看法是，把标准纤维S上的一个向量丛作为新的标准纤维；
但非常一般的S向量丛作为标准纤维时，新丛沿M的水平移动不一定能定义，甚至新丛上的fibre chart都无法从原丛的fibre chart导出。
有两类特殊的，可以在M上定义fibre chart和水平移动：
- 把标准纤维S的切丛TS作为新的标准纤维；
- 或者考虑S上的平凡向量丛$S\times W$作为新的标准纤维；


> 注意，vector bundle valued forms，本质上也是在 vector bundle 的竖直丛上取值，只不过对于vector bundle E，其数值丛VE上 的向量可以表示为丛本身E上的向量。所以vector bundle valued forms可以看做VE-valued forms的特例。

设 $S\hookrightarrow E\xrightarrow{\pi} U$ 为一般纤维丛。U为底空间。
为了讨论方便，我们先假设E是平凡丛，且标准纤维$S\subset  \mathbb {R}^m$ (所以$TS\equiv S\times  \mathbb {R}^m$)。
> 对于一般丛，可先局部平凡化再选定标准纤维上的局部坐标，使得纤维在局部能看做  $\mathbb {R}^m$ 的开子集


同样，外导数定义中，第二项是协变的；
如何适配第一项？注意我们并没有指定VE上的联络，而只是假设给定了E上的一个联络$\Phi$。
为了协变性，要调整第一项：
注意到，$X_i(\omega(...))=[X_i, (\omega(...))]-(\omega(...))X_i$，且等号左边$X_i(\omega(...))$ 是竖直的，我们有：
 $$X_i(\omega(...))=\Phi(X_i(\omega(...)))=\Phi([X_i, (\omega(...))]) - \Phi((\omega(...))X_i)$$
其中，$\Phi([X_i, (\omega(...))])$ 是协变的。
因此，如果选取的局部坐标系能使得所有 $\Phi((\omega(...))X_i)=0$，即，使得 $(\omega(...))X_i$ 是水平的，我们就可以用协变的$\Phi([X_i, (\omega(...))])$来替换第一项中的$X_i(\omega(...))$。
> 如果想让 $(\omega(...))X_i$ 是水平的，需要满足什么条件呢？
注意到$(\omega(...))$是竖直的，这意味着沿着 竖直的 $(\omega(...))$ ， $X_i$的竖直分量不能变化。
如果$X_i$都是水平的，我们需要保证，对于每根纤维，这根纤维上各点处的水平面在局部坐标系中相互平行。
不一定总能找到这样的坐标系？但对于主丛，这是存在的。


我们把所有输入的向量场水平化，再用 $\Phi([X_i, (\omega(...))])$ 替换原来的 $X_i(\omega(...))$，就得到VE-valued forms的协变外导数：
$$(d_{\Phi}\omega)(X_0, X_1, X_2, ... X_k) = \sum_{i=0}^k (-1)^i\Phi([HX_i,\omega(HX_0, HX_1 ... \hat{HX_i}... HX_k)])+ \sum_{i\lt j} (-1)^{i+j}\omega([HX_i,HX_j],HX_0, HX_1 ... \hat{HX_i} ... \hat{HX_j} ... HX_k)\tag{VE valued forms的协变外导数定义式D}$$
for $X_0, X_1, X_2, ... X_k\in \Gamma (TE)$.

> 用 $\Phi([X_i, (\omega(...))])$ 替换原来的 $X_i(\omega(...))$，这个操作也是"自然"的。 $X_i(\omega(...))$ 代表在欧氏空间中， $\omega(...)$  沿场 $X_i$ 的变化速率（只发生在竖直方向）；而 $\Phi([X_i, (\omega(...))])$ 某种意义上在表达同样含义。且欧式空间下选择自然联络和自然的水平定义， $\Phi([X_i, (\omega(...))])$ 就等于  $X_i(\omega(...))$ 。所以把  $X_i(\omega(...))$  推广为  $\Phi([X_i, (\omega(...))])$  是自然的。

> todo. 把 vector bundle valued forms 也转化为此形式?

如果 $\omega$ 是horizontal form (即它的任何一个输入参数水平分量为0时，它的输出就为0)， 正好有：
$$(d_{\Phi}\omega)(X_0, X_1, X_2, ... X_k) = [\Phi, \omega]_{FN}(HX_0, HX_1, HX_2, ... HX_k)$$
或写为 $d_{\Phi}\omega = H[\Phi, \omega]_{FN}$
待确认：上式可能还有加个负号

如果$\omega$ 是 vertical form (即它的任何一个输入是水平向量场时，它的输出就为0)，正好有
$$(d_{\Phi}\omega)(X_0, X_1, X_2, ... X_k) = \frac{1}{2}[\Phi, \omega]_{FN}(HX_0, HX_1, HX_2, ... HX_k)$$
($\omega$ 是1-form时，上式才有价值，如果$\omega$ 是2-form或更高阶的form，上式为0)
待确认：上式可能还有加个负号，也可能不需要。

$d_{\Phi}\Phi = H[\Phi,\Phi]=[\Phi,\Phi]=R$
$d_{\Phi}R = H[\Phi, R] = H[\Phi, [\Phi,\Phi]] = 0$

> 一个自己的不准确的说法：（可能没啥用)
vector valued forms的普通外导数，针对的是值域空间固定，即不随坐标图的选择而变化，的情况；
vector valued forms的协变外导数，针对的是值域空间随坐标图的选择也会发生变换的情况？不同的坐标图选择，会产生实质不同的普通外导数，但协变外导数不变；

### 主丛上的情况

主丛上的主联络，局部（标准）坐标化后，可以满足，同一根纤维上的水平面是平行的，从而$X_i(\omega(...))=\Phi(X_i(\omega(...)))$（见 KMS 11.5 Theorem (10)，提到了这个结论，但并未证明，可能作者觉得太基础了吧）。一般的联络没有这个性质，甚至没有一种"标准"的局部坐标化方法；

主丛局部标准坐标化是指，先局部平凡化：任选$U\subset M$上的一个局部截面，此界面映射到U底平面，然后右乘g，映射到 $U\times G$；
再局部坐标化：把G的一个开子集用对数映射局部坐标化为W（$r^g$ 能否变成W上沿某个方向的平移？），得到$U\times G$的一个局部坐标化 $U\times W$，在这个坐标下，同一根纤维上主联络的水平面互相平行。（李群上的左不变向量场用对数映射推前到李代数上后，是李代数上的平行向量场，或者说常向量场？）。

把平凡主丛的情况想明白即可理解一般主丛。主丛只要有局部截面就可以局部平凡化。主联络只要有局部水平截面，局部的曲率就为0？

一般纤维丛的标准纤维具有连续对称性（是个李群），该丛就是主丛；联络满足对称性，就是主联络；

> 前面提到，对于水平form， $d_{\Phi}\omega = H[\Phi, \omega]_{FN}$；如果在主丛上，$\omega$ 还是个 G-等变的form，那么将直接有 $d_{\Phi}\omega = [\Phi, \omega]_{FN}$？见 KMS 11.5 Theorem (10).

主丛上，可以进一步把 VE valued forms  的协变外导数 转化为教材上常见的 $\mathfrak g$ valued forms 的 协变外导数；


### W-valued forms $\Omega(E, W)$ 上的协变外导数?

主丛上 $\mathfrak g$ valued forms 的 协变外导数可以从形式上直接扩展到W-valued forms上，其中W是任意有限维向量空间。

> 最general的看法是，把标准纤维S上的一个向量丛作为新的标准纤维；
VE valued form，对应把标准纤维S的切丛TS作为新的标准纤维；
W-valued forms，对应把标准纤维S上的平凡向量丛 $S\times W$ 作为新的标准纤维；

但如何更自然地解释这种扩展？就像 $\mathfrak g$ valued forms 的 协变外导数与 VE valued forms  的协变外导数之间有紧密联系一样。
那能否构造一个丛Q使得其与该丛的VQ valued forms上的协变外导数建立起联系？

> 下面是一个不完整的也可能失败的尝试方向:
设 E 的标准纤维为 S，$\Phi$ 是E上的联络；
能否构造一个丛Q，使得$\Omega(E, W)$ 上的协变外导数正好是Q上 满足特定的条件的 VQ valued forms 的协变外微分？
我们可以构造一个新的纤维丛 $Q=E\otimes_M M\times W$，以及其上的联络 $\Psi=(\Phi, Id_W): TE\times TW\rightarrow VE\times TW$；



## 未完事项

待复习问题：

- 普通纤维丛上的 G-equivariant form和主丛上的G-equivariant form定义和联系；

- 协变外导数作用于 微分形式的外积；两次协变外导数；

- 李群的李代数上的李括号按左不变向量场定义还是按右？二者是否相同？主丛上的右基本向量场对应左不变向量场？

- 切丛上的 bianchi 第一和第二恒等式（见Tu 的书中22.2, 22.3, 22.6）； Cartan 第一和第二结构方程（梁灿彬？）； 

-----

待思考或学习的问题：


- 主丛上定义的协变外导数为什么叫"协变"？协变外导数为什么要是 horizontal form？

- 一般纤维丛上可以定义VE valued forms 的协变外导数；但如何理解 VE 协变外导数与丛上协变导数的关系？
> VE 上的协变外导数作用于一个0-form Y时（0-form指VE上的section，即一个竖直向量场），按习惯应该得到协变导数；但这样实际得到的东西 $\Phi[, Y]$ 似乎不太好定义为协变导数? 协变导数的狭义定义应该是，输入一个 E 的section，输出 $\Omega(M, VE)$ 中的一个 1-form；这两者能否自然地联系起来？或者把输入看做成以 $\mathfrak X(S)$ 为纤维、M为底的无限维向量丛上的section？协变外微分在这种意义下如何定义？这时$\Phi[CX, Y]$似乎更合乎直觉一点？然后再将定义域扩充？
> 注意当VE以M为底时，相当于纤维是无限维的；以E为底时，纤维是有限维的；

- 矩阵形式的计算；（选定坐标系后，vector valued k-forms甚至vector bundle valued k-forms都可以写成一个vector，每个entry是个普通的（R值的）k-form；见梁灿彬书纤维丛附录或陈的书；白国正的书中用的分量形式）

- Frame Bundle 上的 canoncial form, torsion forms；
- Frame Bundle 上的 Cartan forms 如何转化为切丛上的 Cartan forms，还有torsion forms。
- 切丛上的曲率和挠率能唯一确定联络？所以，黎曼流形上，曲率能导出度量吗？

-----

一些看过的链接

https://www.researchgate.net/publication/257370330_General_covariant_derivatives_for_general_connections
水平的 VE valued forms 的协变外导数与联络$\Phi$的 FN 括号 $d_{\Phi}\omega := [\Phi, \omega]_{FN}$ 有一定的联系。
https://www.google.com/search?q=Exterior+covariant+derivative+in+general+fibre+bundle&ei=JxnzYuzNE9iXr7wP1oy-kAk&start=10&sa=N&ved=2ahUKEwis_cCUnrv5AhXYy4sBHVaGD5IQ8NMDegQIARBS&biw=1792&bih=834&dpr=2

https://www.google.com/search?q=fr%C3%B6hlicher+nijenhuis+bracket%2C+covariant+exterior+derivative&oq=fr%C3%B6hlicher+nijenhuis+bracket%2C+covariant+exterior+derivative&aqs=chrome..69i57j33i10i160.34520j0j7&sourceid=chrome&ie=UTF-8

https://arxiv.org/abs/1412.2533

https://www.researchgate.net/publication/247386596_Remarks_on_the_Frolicher-Nijenhuis_bracket

https://inspirehep.net/literature/585619

https://en.wikipedia.org/wiki/Maurer%E2%80%93Cartan_form



