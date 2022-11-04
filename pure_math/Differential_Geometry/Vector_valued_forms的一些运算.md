[TOC]


## vector valued forms 的外积定义

设 $\psi\otimes v \in \Omega(M, V)$ 是个可分解的V-valued form， $\eta \otimes w \in \Omega(M, W)$ 是个可分解的 W-valued form，则它们的外积定义为： 
$$(\psi\otimes v) \wedge (\eta \otimes w) = (\psi\wedge \eta)\otimes(v\otimes w)$$
此外积定义可线性扩展为一般的V-valued form和 W-valued form之间的外积。

如果$V=W$ 且它是个代数，也可以定义另一种外积：把上面的 $(v\otimes w)$ 改为它们的代数积$vw$，即
$$(\psi\otimes v) \wedge (\eta \otimes w) = (\psi\wedge \eta)\otimes(vw)$$
比如，当$V$是$n\times n$矩阵组成的空间时。
此时对于一般的$V$ valued form $\Phi, \Psi$ (这样的 forms 不一定能写成一个 $\eta \otimes w$ 这样的可分解形式，但可以写成若干个可分解形式之和)，都可以看做一个矩阵，只不过这个矩阵的每个entry不再是一个数而是一个$\mathbb R$ valued forms；且这样的两个$V$ valued forms之间的外积$\Phi \wedge \Psi$，就是两个矩阵相乘 ，只不过元素间的乘法是$\mathbb R$ valued forms的外积；
有一点要注意，对于普通的R-valued forms, $\phi\wedge \psi$与$\psi\wedge \phi$最多只相差一个正负号，且有$\omega\wedge \omega=0$；但这个漂亮的性质一般不能扩展到两个 Matrix-valued forms 的外积之间，这是因为，Matrix-valued forms 之间的外积是一种矩阵乘法，而矩阵乘法不具备交换性，所以对Matrix-valued forms 的外积，$\Phi \wedge \Psi$ 与 $\Psi \wedge \Phi$ 之间一般也没什么简洁的等式，甚至一个Matrix-valued form跟他自己的外积 $\Phi \wedge \Phi$也不是0。


## Lie algebra valued forms 之间的分级李括号

$[\psi\otimes v, \eta \otimes w]=\psi\wedge \eta\otimes [v,w]$
如果所谈论的李代数是矩阵李代数 $\mathfrak {gl}(n)$，那么上式可写为
$[\psi\otimes v, \eta \otimes w]=\psi\wedge \eta\otimes (vw-wv) = (\psi\otimes v) \wedge (\eta \otimes w) - (-1)^{ij}(\eta \otimes w) \wedge (\psi\otimes v)$
其中$i,j$分别是$\psi,\eta$的degree.
对于一般 $\mathfrak {gl}(n)$ valued forms $\Phi, \Psi$， 由于线性性，我们同样有：
$[\Phi, \Psi]=\Phi \wedge \Psi - (-1)^{ij}\Psi \wedge \Phi$
特别的，如果$\Phi$是个1-form，那么
$[\Phi, \Phi]=2\Phi \wedge \Phi$ (注意，前面提到过，一个 Matrix-valued form 与它自己的外积通常不是0)



## Matrix valued forms 之间的矩阵乘积

设Matrix valued form $\omega = (\omega^i_j) = \left[\begin{matrix} \omega^{1}_{1} & ... & \omega^{1}_{k} \\ ... & ... & ... \\ \omega^{k}_{1} & ... & \omega^{k}_{k}\end{matrix}\right]$，其中每个$\omega^i_j$ 都是一个 $\mathbb R$ valued form;

两个 Matrix valued forms $\omega,\varphi$（不一定都要是方阵）， 如果它们的维度符合矩阵乘法的要求，就可以定义它们的矩阵外积
$$\omega\wedge \varphi = ((\omega\wedge \varphi)^i_h) = (\omega^i_j \wedge  \varphi^j_h)$$
 （注意这里使用了爱因斯坦求和约定，需要对指标$j$求和）
即把普通矩阵乘法中元素之间的乘法换成外积运算$\wedge$;
在一点$p\in M$处，若 $v_1,v_2...v_{k+l}\in T_pM$，则
$$  (\omega^i_j \wedge  \varphi^j_h)_p(v_1,v_2...v_{k+l}) = \frac{1}{k!l!} \sum_{\sigma\in S_{k+l}} sgn_{\sigma} \omega^i_j (v_{\sigma(1)}...v_{\sigma(k)}) \varphi^j_h(v_{\sigma(k+1)}...v_{\sigma(k+l)}) $$
$$  (\omega\wedge \varphi)_p(v_1,v_2...v_{k+l}) = \frac{1}{k!l!} \sum_{\sigma\in S_{k+l}} sgn_{\sigma} \omega(v_{\sigma(1)}...v_{\sigma(k)}) \varphi(v_{\sigma(k+1)}...v_{\sigma(k+l)}) $$
注意，上式中，$\omega(v_{\sigma(1)}...v_{\sigma(k)})$ 与 $\varphi(v_{\sigma(k+1)}...v_{\sigma(k+l)})$ 都已是普通矩阵，$\omega(...)\varphi(...)$ 代表它们的普通矩阵乘积。

> Vector valued forms 可看做单列的 Matrix valued forms，并且与Matrix valued forms 相乘。

### $\mathfrak{gl}(V)$ valued form 与  $V$ valued form 之积

假如 $V$ 是一个 $n$ 维线性空间，那李代数 $\mathfrak{gl}(V)$ 就是 $n\times n$ 矩阵组成的空间；如果 $\omega,\varphi$ 分别是 $\mathfrak{gl}(V)$ valued form 和  $V$ valued form，则它们之间就可以运用刚定义的矩阵乘积，且 $\omega\wedge \varphi$ 依然得到一个 $V$ valued form；


## $\mathfrak{g}$ valued form 与表示空间 $V$ valued form 的乘积

参考
Loring W. Tu, Differential Geometry, Connections, Curvature, and Characteristic Classes
31.5 节中，
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/f79f965fa940c037bb044a959aadda64.png)

各字母参考上图定义。
$\tau$ 是 $\mathfrak g$ valued form，$\rho_*$是从$\mathfrak g$ 到 $\mathfrak {gl}(V)$ 的表示， 因此$\tau_*=\rho_*\circ \tau$ 就是一个 $\mathfrak {gl}(V)$ valued form;

而利用前面定义的 matrix valued form 之间的乘积，可以定义 $\mathfrak{gl}(V)$ valued form 和  $V$ valued form 之积。不难发现，**上图中定义的 $\tau\cdot \varphi$，实际上就是 $(\rho_*\circ \tau) \wedge \varphi=\tau_* \wedge \varphi$**，即先把$\tau$变成一个$\mathfrak {gl}(V)$ valued form $\tau_*$，再与$\varphi$ 做matrix valued form间的矩阵乘积；
$$
(\tau\cdot \varphi)_p(...) = ((\rho_*\circ \tau) \wedge \varphi)_p(...) = (\tau_* \wedge \varphi)_p(...) = \frac{1}{k!l!} \sum_{\sigma\in S_{k+l}} sgn_{\sigma} \tau_*(...) \varphi(...)
$$

### $\mathfrak{g}$ valued form 间的 Lie bracket

依然参考上图，看其中的 Example 31.18. 
要注意到，当 $\rho_*$ 是 $\mathfrak g$  伴随表示时，
$$\tau_*(...) \varphi(...) = ad_{\tau(...)}(\varphi(...)) =  [\tau(...) , \varphi(...)]$$
所以才有了 Example 31.18 中的等式。

> 也见 Tu 书中 21.5节中的 式(21.9):
> ![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/c1e0f7f3c13ec7ab07a20ce9a7ca5bf7.png)



需要注意的是，如果 $V=\mathfrak g$  本身已经是个 matrix space 时, 比如 $\mathfrak g=\mathfrak {gl}(n)$，此时 $\mathfrak g$ 中的元素既可看做 $n^2$ 维的向量，又可看做$n\times n$的矩阵；且 $\mathfrak {gl}(V)=\mathfrak {gl}(\mathfrak {gl}(n))$ 就是 $n^2\times n^2$ 的矩阵空间；
又由于 $\mathfrak g$ 是矩阵李代数，且$\tau(...) , \varphi(...)\in \mathfrak g$ 都是普通矩阵，故
$ [\tau(...) , \varphi(...)] = \tau(...)\varphi(...) - \varphi(...) \tau(...)$
带入书中 Example 31.18 中的等式，最终得到
$$[\tau, \varphi] = \tau \wedge \varphi - (-1) ^{kl} \varphi\wedge \tau$$

> 参考Tu 书中 21.5节
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/87929a3b2441b665ba4aefff9a78221c.png)

### 材料对比

1. Tu 书中 31.5 节开头定义的form间的点乘 (在EXample 31.18情况下写成方括号)，
2. 与梁灿彬书中 附录I.8 开头的括号，以及KMS书中11.2中定义的括号，以及Tu书中21.5节中的括号定义
前者(1)范围更广，后者(2)是特例，等价于前者的EXample 31.18。
梁灿彬书中 定理 I-8-13上下，同Tu书中21.5节，也讨论了 Matrix Lie algebra valued form之间的外积操作，以及外积与form 上的李括号的关系。
