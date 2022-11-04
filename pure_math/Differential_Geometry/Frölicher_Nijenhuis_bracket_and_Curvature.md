[TOC]

## A brief introduction to algebra and derivation

### [Algebra](https://en.wikipedia.org/wiki/Algebra_over_a_field#Definition) (over a field/ring)

**algebra over a field**
An algebra over a field $K$ (often simply called an algebra) is a vector space $A$ equipped with a **bilinear product** "$\cdot$", i.e. for $w,x,y,z\in A$, $\alpha,\beta\in K$, we have
- $(w+x)\cdot (y+z)=w\cdot y+w\cdot z + x\cdot y + x\cdot z$
- $(\alpha x)\cdot (\beta y)=\alpha\beta (x\cdot y)$

We might omit the symbol "$\cdot$" if the context is clear.

**generalization: algebra over a ring**
Replacing the field $K$ of scalars by a *commutative ring* $R$ leads to the more general notion of an *algebra over a ring*. Note now the algebra itself is assumed to be an $R$-module (instead of a vector space over $K$).


#### [Associative algebra](https://en.wikipedia.org/wiki/Associative_algebra)

An algebra is an *associative algebra* if in addition:
- $(x\cdot y)\cdot z=x\cdot (y\cdot z)$

> Examples:
> - The tensor algebra
> - The exterior algebra

A *commutative algebra* is an associative algebra that has a *commutative multiplication*, or, equivalently, an associative algebra that is also a *commutative ring*.

#### [Graded algebra](https://en.wikipedia.org/wiki/Graded_ring#Graded_algebra)

[Graded ring](https://en.wikipedia.org/wiki/Graded_ring)

A graded ring $R$ is a ring such that the underlying additive group is a direct sum of abelian groups ${\displaystyle \{R_{i}\}_{i\in I}}$ such that ${\displaystyle R_{i}R_{j}\subseteq R_{i+j}}$.
The index set $I$ is usually the set of nonnegative integers or the set of integers, but can be any monoid (幺半群).

[Graded algebra](https://en.wikipedia.org/wiki/Graded_ring#Graded_algebra)
An algebra $A$ over a ring $R$ is a graded algebra if it is graded as a ring.
In the usual case where the ring $R$ is not graded (**in particular if $R$ is a field**), it is given the trivial grading (every element of $R$ is of degree 0). Thus, ${\displaystyle R\subseteq A_{0}}$ and the graded pieces ${\displaystyle A_{i}}$ are R-modules.

In the case where the ring $R$ is also a graded ring, then one requires that

${\displaystyle R_{i}A_{j}\subseteq A_{i+j}}$
In other words, we require $A$ to be a graded left module over $R$.

#### [Graded commutative algebra](https://en.wikipedia.org/wiki/Graded-commutative_ring)

 [graded commutative ring](https://en.wikipedia.org/wiki/Graded-commutative_ring)
a graded-commutative ring (also called a skew-commutative ring) is a graded ring that is commutative in the graded sense; that is, homogeneous elements x, y satisfy
${\displaystyle xy=(-1)^{|x||y|}yx}$,
where $|x |$ and $|y |$ denote the degrees of $x$ and $y$.

> A commutative (non-graded) ring, with trivial grading, is a basic example. 
> An exterior algebra is an example of a graded-commutative ring that is not commutative in the non-graded sense. 外代数不是commutative的，即一般 $\eta\wedge \psi\ne \psi\wedge\eta$；但作为一个分级代数，它是graded-commutative的。

#### [Commutator](https://en.wikipedia.org/wiki/Commutator)
The commutator gives an indication of *the extent to which a certain binary operation fails to be commutative*.
There are different definitions used in group theory and ring theory.

**Commutator for a groups**
The commutator of two elements, $g$ and $h$, of a group $G$, is the element
$[g, h] = g^{−1}h^{−1}gh$.
But many group theorists might also define the commutator as
$[g, h] = ghg^{−1}h^{−1}$

However, both of the two definition can give an indication of *the extent to which the group operation fails to be commutative*.

**Commutator for rings or associative algebras**
The commutator of two elements $a$ and $b$ of a ring $R$ (including any associative algebra) is defined by
${\displaystyle [a,b]=ab-ba.}$

**Commutator for graded rings and algebras**
When dealing with graded algebras, the commutator is usually replaced by the *graded commutator*, defined in homogeneous components as

${\displaystyle [\omega ,\eta ]_{gr}:=\omega \eta -(-1)^{\deg \omega \deg \eta }\eta \omega .}$

#### [Lie algebra](https://en.wikipedia.org/wiki/Lie_algebra)

In the case of Lie algebra, we usually use the Lie bracket instead of the dot product "$\cdot$". A Lie algebra is usually written as $\mathfrak{g}$.
The additional conditions for an algebra to be a Lie algebra are:
- Anticommutativity: $[x,y]=-[y,x]$
- Alternativity: $[x,x]=0$
> If the field's characteristic is not 2 then anticommutativity implies alternativity;

- The Jacobi identity: $[x,[y,z]]+[y,[z,x]]+[z,[x,y]]=0$
> The Jacobi identity can be also written as $[x,[y,z]]=[[x,y],z]+[y,[x,z]]$. If we define $\text{ad}_x=[x,]$，then Jacobi identity is just saying that $ad_x$ is a *derivation* (which we'll introduce later), i.e. $\text{ad}_x[y,z]=[\text{ad}_xy,z]+[y,\text{ad}_xz]$

Every assoicative algebra is a Lie algebra with the commutator $[x,y]=xy-yx$ as bracket.

#### [graded Lie algebra](https://en.wikipedia.org/wiki/Graded_Lie_algebra)

- **graded Lie algebra**: a graded Lie algebra is a Lie algebra endowed with a gradation which is compatible with the Lie bracket. However, some authors might refer this term to "graded Lie **super**algebra", as defined below.
- **graded Lie superalgebra**: A *graded Lie superalgebra* extends the notion of a *graded Lie algebra* in such a way that the Lie bracket is no longer assumed to be necessarily anticommutative. These arise in the study of *derivations on graded algebras*, and in the theory of Lie derivatives. The Lie bracket for a *graded Lie superalgebra* satisfies: (Let $E=\bigoplus E_i$ be the underlying graded algebra$)
  - $[, ]$ respects the gradation of $E$:
${\displaystyle [E_{i},E_{j}]\subseteq E_{i+j}.}$
  - (Symmetry) For all $x$ in $E_i$ and $y$ in $E_j$:
${\displaystyle [x,y]=-(-1)^{ij}\,[y,x]}$ (graded anticommutative)
  - (Jacobi identity) For all $x$ in $E_i$, $y$ in $E_j$, and $z$ in $E_k$,
${\displaystyle (-1)^{ik}[x,[y,z]]+(-1)^{ij}[y,[z,x]]+(-1)^{jk}[z,[x,y]]=0.}$
> The Jacobi identity can be also written as ${\displaystyle [x,[y,z]]=[[x,y],z]+(-1)^{ij} [y,[x,z]].}$. If we define $\text{ad}_x=[x,]$, then Jacobi identity is just saying that $ad_x$ is *a homogeneous derivation* (which we'll introduce later) of degree $|x|$, i.e. $ad_x\in \text{Der}_kE$.
(If the underlying field $K$ has characteristic 3, then the Jacobi identity must be supplemented with the condition ${\displaystyle [x,[x,x]]=0}$ for all $ x$ in $E_{odd}$.)


Every graded assoicative algebra is a graded Lie superalgebra with the commutator ${\displaystyle [\omega ,\eta ]:=\omega \eta -(-1)^{\deg \omega \deg \eta }\eta \omega .}$.


### [Derivation](https://en.wikipedia.org/wiki/Derivation_(differential_algebra)) on an algebra

#### derivation:  linear + Leibniz's law

Given an algebra $A$ over a ring or a field $K$, a derivation is a *K-linear map* $D : A \rightarrow A$ that satisfies *Leibniz's law*:
- Leibniz's law: ${\displaystyle D(ab)=aD(b)+D(a)b.}$

> 如果把$A$看做流形上$p$点处的光滑函数芽组成的向量空间$C_p^\infty(M)$（两个函数芽之间的加法和乘法定义为在它们定义域交集内的逐点相加和相乘），那$C_p^\infty(M)$上的derivation就是一个切向量。这正是切向量的导数定义。
如果把$A$看做流形上的光滑函数层$C^\infty(M)$（两个函数之间的加法和乘法定义为在它们定义域交集内的逐点相加和相乘），那$C^\infty(M)$上的derivation就是一个切向量场。每个光滑切向量场都是一个derivation，这是容易看到的；但"每个derivation都对应一个光滑切向量场"就不是那么显然了，这需要些证明。

#### [Graded derivations](https://en.wikipedia.org/wiki/Derivation_(differential_algebra)#Graded_derivations)

**graded derivation**, or **homogeneous derivation** of degree $k$.
Given a graded algebra $A=\bigoplus A_i$ and a homogeneous linear map $D$ of grade $|D|=k$ on $A$ (i.e. D[A_i] \subset A_{i+k}). Then $D$ is a *homogeneous derivation* of degree $k$ if
${\displaystyle {D(ab)=D(a)b+\varepsilon ^{|a|k}aD(b)}}$
for every *homogeneous* element $a$ and every element $b$ of $A$ for a *commutator factor* ε = ±1. 
-  If ε = 1 (or ε = -1 but $|D|$ is even), this definition reduces to the usual case (${\displaystyle D(ab)=aD(b)+D(a)b.}$).
- If ε = −1 and $|D|$ is odd, however, then
${\displaystyle {D(ab)=D(a)b+(-1)^{|a|}aD(b)}}$
and $D$ is called an **anti-derivation**. Examples of anti-derivations include the exterior derivative and the interior product acting on differential forms.

> 这里为什么要对*Leibniz's law*做出调整？ 
如果一个algebra，其元素间具有某种交换对称性，此时受限于$D$的 linearity，*Leibniz's law* 可能不得不做出调整。
设$A$是一个 algebra，且其元素间具有某种交换对称性，比如 $ab=s_{ab}ba$，其中$s: A\times A\rightarrow \{-1,1\}$ 是一个符号函数，$s_{ab}=s(a,b)=±1$。注意对不同的$a,b$，$s_{ab}$取值可以是不同的（1或-1）。比如对于前面提到的 graded commutative algebra，我们有$s_{ab}=(−1)^{deg(a)deg(b)}$。
这时*Leibniz's law*给出： 
${\displaystyle D(ab)=aD(b)+D(a)b.}$
${\displaystyle D(s_{ab}ba)=D(ba)=bD(a)+D(b)a=s_{Da,b}D(a)b+s_{a,Db}aD(b).}$
再考虑到$D$的线性性，又必须有 ${\displaystyle D(s_{ab}ab)=s_{ab}aD(b)+s_{ab}D(a)b.}$,它与前一个式子应该相等。但$A$的交换对称性并不一定能使得 $s_{ab}=s_{Da,b}=s_{a,Db}$。所以我们可能需要调整*Leibniz's law*的形式，加上一个ε符号项，以兼容某些algebra（尤其是graded commutative algebra）中的交换对称性。

The space of all homogeneous derivations of degree $k$ is denoted by $\text{Der}_k A$.
Let $\text{Der} A= \bigoplus \text{Der}_k$ be the direct sum of these spaces.
Then $\text{Der} A$ is a graded algebra whose *homogeneous components* consist of all graded derivations of a given degree; And it becomes a graded Lie superalgebra with the graded commutator as bracket: 
${\displaystyle [D_{1},D_{2}]=D_{1}\circ D_{2}-(-1)^{d_{1}d_{2}}D_{2}\circ D_{1}.}$

> 外微分是个一阶导子，且外微分是唯一的。参见陈省身Chapter 3 Theorem 2.1，其中用4条公理唯一刻画了外微分。
对比一阶导子条件，即线性+lebniz，也正是陈省身书中公理1和2。（这两条是很一般的性质，可参考切向量作为导数的定义，代数上的导子)。虽然外微分是唯一的，但一阶导子并不唯一。外微分的唯一性依赖了陈书中的公理3和4。
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/2681d5aad6714ab3d88768e9da8d4a23.png)

## Vector valued differential forms

### [E-valued differential forms](https://en.wikipedia.org/wiki/Vector-valued_differential_form#Definition)
Let M be a smooth manifold and E → M be a smooth vector bundle over M. We denote the space of smooth sections of a bundle E by Γ(E).
An E-valued differential form of degree $p$ is a smooth section of the *tensor product bundle* of E with Λp(T ∗M), the p-th exterior power of the cotangent bundle of M. The space of such forms is denoted by

${\displaystyle \Omega ^{p}(M,E)=\Gamma (E\otimes \Lambda ^{p}T^{*}M).}$
Because Γ is a [strong monoidal functor](https://en.wikipedia.org/wiki/Strong_monoidal_functor), this can also be interpreted as
${\displaystyle \Gamma (E\otimes \Lambda ^{p}T^{*}M)=\Gamma (E)\otimes _{\Omega ^{0}(M)}\Gamma (\Lambda ^{p}T^{*}M)=\Gamma (E)\otimes _{\Omega ^{0}(M)}\Omega ^{p}(M),}$
where the latter two tensor products are the tensor product of modules over the ring Ω0(M) of smooth R-valued functions on M (see the seventh example here). 
Equivalently, an E-valued differential form can be defined as a bundle morphism
${\displaystyle TM\otimes \cdots \otimes TM\to E}$
which is totally skew-symmetric.

#### E-valued 0-form
By convention, an E-valued **0-form is just a section** of the bundle E. That is,
${\displaystyle \Omega ^{0}(M,E)=\Gamma (E).\,}$

### V-valued differential forms
Let V be a fixed vector space. A V-valued differential form of degree p is a differential form of degree p with values in the *trivial bundle M × V*. 
The space of such forms is denoted Ωp(M, V). 

#### Ordinary differential forms can be viewed as $\mathbb{R}$-valued differential forms
When V = R one recovers the definition of an ordinary differential form. If V is finite-dimensional, then one can show that the natural homomorphism
${\displaystyle \Omega ^{p}(M)\otimes _{\mathbb {R} }V\to \Omega ^{p}(M,V),}$
where the first tensor product is of vector spaces over R, is an isomorphism.

### Exterior derivetive for vector valued differential forms


V-valued differential forms naturally inherit the exterior derivetive. (choose a base, and get the ordinary exterior derivetive of the commponents) .
But general E-valued  differential forms not, which need extra structure (a connection).
See wiki.

## [Frölicher–Nijenhuis bracket](https://en.wikipedia.org/wiki/Fr%C3%B6licher%E2%80%93Nijenhuis_bracket#Definition)

### the graded Lie superalgebra $\mathrm {Der} \,\Omega ^{*}(M)$

Let Ω*(M) be the sheaf of exterior algebras of differential forms on a smooth manifold M. This is a graded algebra in which forms are graded by degree:
${\displaystyle \Omega ^{*}(M)=\bigoplus _{k=0}^{\dim{M} }\Omega ^{k}(M)=\bigoplus _{k=-\infty }^{\infty }\Omega ^{k}(M).}$
where for $k<0$ or $k>\dim{M}$, $\Omega ^{k}(M)$ is defined to be $\{0\}$.

A *graded derivation* of degree ℓ is a mapping
${\displaystyle D:\Omega ^{*}(M)\to \Omega ^{*+l}(M)}$
which is linear with respect to constants and satisfies
${\displaystyle D(\alpha \wedge \beta )=D(\alpha )\wedge \beta +(-1)^{\ell \deg(\alpha )}\alpha \wedge D(\beta ).}$
> Thus, in particular, the interior product with a vector defines a graded derivation of degree ℓ = −1, whereas the exterior derivative is a graded derivation of degree ℓ = 1.

The vector space of all derivations of degree ℓ is denoted by DerℓΩ*(M). 
The direct sum of these spaces is a graded vector space whose *homogeneous components* consist of all graded derivations of a given degree; it is denoted
${\displaystyle \mathrm {Der} \,\Omega ^{*}(M)=\bigoplus _{k=-\infty }^{\infty }\mathrm {Der} _{k}\,\Omega ^{*}(M).}$
This forms a *graded Lie superalgebra* under the *anticommutator* (graded commutator) of derivations defined on homogeneous derivations D1 and D2 of degrees d1 and d2, respectively, by
${\displaystyle [D_{1},D_{2}]=D_{1}\circ D_{2}-(-1)^{d_{1}d_{2}}D_{2}\circ D_{1}.}$

### the insertion operator $i_K$ for $K \in \Omega^k(M, TM)$ and algebraic derivation

A homogeneous derivation $D\in \mathrm {Der} _{k-1}$ is *algebraic* if $Df=0$ for all $f\in \Omega^0(M)$.
So if $D$ is algebraic, then 
$D(f\omega) = (Df)\wedge\omega + (-1)^0 fD(\omega)=fD(\omega)$
i.e. $D$ is $C^\infty(M)$-linear and thus of tensorial character:

- the value of $D\omega$ at a point $x\in M$ only depends on the value of $\omega$ at that single point. So for each $x$, $D$ determines a map $D_x: \Omega^* T_x^*M \rightarrow \Omega^{*+(k-1)} T_x^*M$ which is defined as $D_x \omega_x = (D\omega)_x$
> i.e. $(D\omega^1)_x=(D\omega^2)_x$ if $\omega^1_x=\omega^2_x$. Without loss of generality (bacause of the linearity of $D$), let's say $\omega^1 = \alpha du^0$ and $\omega^1 = \beta du^0$, where $(U,u)$ is a chart and $\alpha,\beta$ are smooth factor functions. Then $\alpha_x=\beta_x$ since $\omega^1_x=\omega^2_x$. So $(D\omega^1)_x=(D(\alpha du^0))_x=(\alpha D(du^0))_x=\alpha_x (D(du^0))_x = \beta_x (D(du^0))_x = (D\omega^2)_x$. 
> This property fails if $D$ is not algebraic since the proof above depends on the fact that $D(\alpha du^0)=\alpha D(du^0)$, without which the proof can't proceed.

- $D_x$ is $\mathbb R$-linear since $D$ does.
- $D_x(\alpha_x \wedge \beta_x )=D_x(\alpha_x )\wedge \beta_x +(-1)^{(k-1) \deg(\alpha )}\alpha_x \wedge D_x(\beta_x)$ because $D(\alpha \wedge \beta )=D(\alpha )\wedge \beta +(-1)^{(k-1) \deg(\alpha )}\alpha \wedge D(\beta)$.

So, $D_x$ is a graded derivation on $\Omega^* T_x^*M$ of degree (k-1). (it's $\mathbb R$-linear and satisfies the graded Lebniz rule).
Note $D_x$ is totally determined by its restriction on $\Omega^1 T_x^*M$ because every higher order form is a combination of wedge products of 1-forms. But the restriction $D_x|\Omega^1 T_x^*M:\Omega^1 T_x^*M\rightarrow \Omega^{k} T_x^*M$ acts like a $(1,k)$ tensor which is alternating in the contravariant part. Let's denote this tensor, to be more specific, a $T_xM$-valued k-form, by $K_x$.
There is a $K_x$ for every $x\in M$ so we get a tensor field $K$ of type $(1,k)$ on $M$. To be more specific, $K$ is a $TM$-valued k-form. And as a map from $\Omega^1(M)$ into $\Omega^k(M)$, $K$ is the same with $D|\Omega^1(M)$. $K$ is smooth since $D|\Omega^1(M)$ is $C^\infty(M)$-linear.
That is, *any algebraic derivation $D$ (of degree $(k-1)$) determines a unique $TM$-valued $k$-form $K$*.

Conversely, every $TM$-valued $k$-form $K$ can be regarded as a $C^\infty(M)$-linear map $K: \Omega ^1(M) \rightarrow \Omega ^{k}(M)$ that transforms a 1-form to a $k$-form. So we can extend $K$ linearly to a new map $i_K: \Omega ^*(M) \rightarrow \Omega ^{*+(k-1)}(M)$ according to the graded Lebniz rule. Then the extended map $i_K$ is an algebraic derivation. By writing out the extension, we finally get:

${\displaystyle i_{K}\,\omega (X_{1},\dots ,X_{k+\ell -1})={\frac {1}{k!(\ell -1)!}}\sum _{\sigma \in {S}_{k+\ell -1}}{\textrm {sign}}\,\sigma \cdot \omega (K(X_{\sigma (1)},\dots ,X_{\sigma (k)}),X_{\sigma (k+1)},\dots ,X_{\sigma (k+\ell -1)})}$

> 系数 ${\frac {1}{k!(\ell -1)!}}$ 正好约去重复项;

> Every (differential) k-form is a $C^\infty(M)$-linear combination of forms like $\omega^1 \wedge \omega^2 ... \wedge \omega^k$ for $\omega^i\in \Omega ^1(M)$. 
If $D$ is algebraic, then $D(f\omega^1 \wedge \omega^2 ... \wedge \omega^k)=fD(\omega^1 \wedge \omega^2 ... \wedge \omega^k)$,
but $D(\omega^1 \wedge \omega^2 ... \wedge \omega^k)$ can be determined by every $D(\omega^i)$ hence by every $D(e^i)$.
Thus, $D$ is totally determined by its restriction on $\Omega ^1(M)$, i.e. for algebraic $D_1$ and $D_2$, $D_1=D_2$ if and only if $D_1|\Omega ^1(M)=D_2|\Omega ^1(M)$.


For $k=0$, i.e. when $K$ is a vector field, the insertion operation $K\omega$ is just the *interior product* of $K$ and $\omega$.
Now, let's summarize:
- Every insertion derivation $i_K$ is an algebraic derivation ($i_Kf=0$); 
- Every algebraic derivation $D$ corrensponds to a unique $K$ such that $D=i_K$.
> **$i_K$和$K$是同一个 $(1,k)$型张量的不同作用**
$D=i_K$ 作用于一个1-form $\omega$时，有 $i_K(\omega)=\omega\circ K$。
注意，上式中，$K$代表一个vector valued k-form, 作为一个函数，它的输入参数是$k$个vector field，输出一个vector field；而 $i_K$ 的输入参数是个 1-form，输出是个 $k-form$；它们是同一个 $(1,k)$型张量的不同作用。

For $f\in \Omega^0(M)$, since $i_K$ is algebraic, so $i_Kf=0$.
> 对于 $\ell=0$ 的情况，此时$\omega$是一个0-form，即一个函数$f$。此时$i_K(...)$等式右方求和项为空（没有东西求和），所以$i_K f$等于0.
> 或者理解为，$i_{K}$ 用于把微分形式"降k-1阶"，0-form再降阶变成负阶，但负阶的微分形式只有0.


**Algebraic bracket (Nijenhuis Richardson bracket)**

The algebraic bracket, or known as NR bracket, denoted by $[,]^\wedge$，可以通过 $i$ 来定义：$i([L,K]^\wedge)=[i_L, i_K]=i_L\circ i_K-(-1)^{\ell k}i_K\circ i_L$；
> TM-valued differential form $K$ of degree $k$ 可拆成若干项的和 $K(Y_1, Y_2...Y_k)=\sum (\omega_i(Y_1,Y_2...Y_k)  X_i)$，或者
当把$K$看做一个$(1,k)$ 型张量时可写作 $K=\sum_i K_i=\sum_i (\omega_i \otimes X_i)$。

通过把$i([L,K]^\wedge)$它作用于一个1-form $\psi$，可得到$[,]^\wedge$的具体形式： $[L,K]^\wedge=i_LK-(-1)^{\ell k}i_KL$,
> 其中$i_LK$是把 $i_L$ 的定义扩展了，$i_L$本来只作用于一般的微分形式，但这里我们还把它作用于 TM-valued differential form。
扩展后的定义为  $i_L(\omega\otimes X)=i_L(\omega)\otimes X$， 其中 $i_L(\omega)$ 是个 $(\ell+|\omega|-1)$ 阶的普通微分形式，因此 $i_L(\omega)\otimes X$ 具有$(1,\ell+|\omega|-1)$型张量的性质，从而也是个vector valued  $(\ell+|\omega|-1)$-form。
由于$K$可以拆成$K=\sum_i (\omega_i \otimes X_i)$的形式，因此 $i_LK$本身也代表一个vector valued $(\ell+k-1)$-form。

> 通过作用在一个1-form $\psi$上， 我们可以看到$i_L\circ i_K=i_LK$：
$(i_L\circ i_K)(\psi)=i_L(i_K(\psi)) = i_L(\psi\circ K)  = i_L(\sum_i \psi\circ K_i) = i_L(\sum_i \langle X_i, \psi \rangle \omega_i)  =  \sum_i \langle X_i, \psi \rangle i_L( \omega_i) =\sum_i (i_L( \omega_i)\otimes X_i)(\psi)=(i_LK)(\psi)=\psi\circ i_LK$
同理$i_K\circ i_L=\psi\circ i_KL$;

Note that $[L,K]^\wedge$ is a $TM$ valued $(\ell+k-1)$-form. 
> $i_L\in Der_{\ell-1}$,
$i_K\in Der_{k-1}$,
So $i([L,K]^\wedge)=[i_L, i_K]\in Der_{(\ell-1)+(k-1)}=Der_{\ell+k-2}$,
So $[L,K]^\wedge$ is a ${(\ell+k-2)+1}$-form.

### $\mathcal{L}_K$ Lie derivative along $K \in \Omega^k(M, TM)$

$\mathcal{L}_K$ is defined as $\mathcal{L}_K=[i_K,d]=i_Kd-(-1)^{k-1}di_K$.
> Specially, if $K$ is 1-form, then  $\mathcal{L}_K=[i_K,d]=i_Kd-di_K$


$[\mathcal{L}_K,d]=[[i_K,d],d]=0$ for any $K$ by the Jacobi identity and the fact $[d,d]=0$.
> $[i_K,[d,d]]=[[i_K,d], d] + (-1)^{(k-1)}[d, [i_K,d]]=[[i_K,d], d] + (-1)^{(k-1)}(-(-1)^{k}[[i_K,d],d]=[[i_K,d], d] + [[i_K,d], d] = 0$

For $f\in \Omega^0(M)$, $L_Kf=i_Kdf$ (since $i_Kf=0$).
$\mathcal{L}_K$ is algebraic ($\mathcal{L}_Kf=i_Kdf=df\circ K=0$ for any $f$) if and only if $K=0$.


### Compare $\mathcal{L}_K$ and $i_K$

$i_K$ is algebraic (i.e. $i_Kf=0$ for any $f$) for any $K$, but $\mathcal{L}_K$ is algebraic if and only if $K=0$.
$[\mathcal{L}_K,d]=0$ for any $K$ (as mentioned above), but $[i_K,d]=\mathcal{L}_K=0$ (which implies $\mathcal{L}_K$ is algebraic) if and only if $K=0$;

As graded derivations,  $\mathcal{L}_K$ and $i_K$ are of different degrees: $\mathcal{L}_K\in Der_k$ while $i_K\in Der_{k-1}$

$K\mapsto \mathcal{L}_K$ is injective since $\mathcal{L}$ is linear and its kernel is $K=0$, i.e. $\mathcal{L}_K=0$ if and only if $K=0$. 
$K\mapsto i_K$ is bijective as already mentioned (它们是同一个$(1,k)$型张量的不同作用). 

### $D=\mathcal L_K + i_L$ for $D\in Der_k$ and $\ell=k+1$

Every $D\in Der_k$ can be written as 
$D=\mathcal L_K + i_L$
for $K\in \Omega^k(M,TM)$ and $L\in \Omega^{k+1}(M,TM)$

proof sketch:
Let $X_1,X_2,...X_k$ be vector fields and $f$ a smooth function on $M$. Then $Df$ is a k-form and hence $Df(X_1,X_2,...X_k)$ is a smooth function on $M$. So the vector fields $(X_1,X_2,...X_k)$ determine a map $f\mapsto Df(X_1,...X_k)$, denoted by $K(X_1,X_2,...X_k): C^{\infty} (M)\rightarrow C^{\infty} (M)$. 
It's easy to check that this map is $\mathbb {R}$-linear: $\lambda f+g\mapsto  (\lambda Df+Dg)(X_1,...X_k)$, and satisfies the Lebniz rule: $fg\mapsto (Df\cdot g+fDg)(X_1,...X_k)$.
So $K(X_1,X_2,...X_k)$ is a derivation on $ C^{\infty} (M)$. Such a derivation corresponds to a vector field. That is, $K(X_1,X_2,...X_k)$ is a vector field.
So $D$ determines a map $K$: $(X_1,X_2,...X_k)\mapsto K(X_1,X_2,...X_k)\in \mathfrak X(M)$, i.e. $K$ maps $k$ vector fields to a single vector field (so $K$ is actually a tensor of type $(1,k)$), and it's $C^\infty(M)$-linear in each $X_i$ and alternating, thus it's a $TM$ valued $k$-form.
Since $Df(X_1,...X_k)=K(X_1,X_2,...X_k)f=df(K(X_1,X_2,...X_k))$, so $Df=df\circ K=i_Kdf=\mathcal {L}_Kf$. Then $(D-\mathcal L_K)$  is algebraic hence there is a unique $L\in \Omega^{k+1}(M,TM)$ such that $D-\mathcal L_K=i_L$.

So,
- $[D,d]=0$ if and only if $L=0$, i.e. $D=\mathcal L_K$ for some $K$
- $D$ is algebraic if and only if $K=0$, i.e. $D=i_L$ for some $L$ (which we already know in the previous sections).

### Frölicher–Nijenhuis bracket

Since $[\mathcal L_L, d]=0=[\mathcal L_K, d]$，it's easy to find that $[[\mathcal L_L,\mathcal L_K], d]=0$. But this implies there is a unique $J\in \Omega^{k+\ell}(M,TM)$ such that $[\mathcal L_L,\mathcal L_K]=\mathcal L_J$.
Let's denote this $J$ by $[L,K]$, then we have the Frölicher–Nijenhuis bracket.
With this bracket, the space $\Omega^* (M,TM)$ is a also graded Lie superalgebra.

### Summary of brackets

We've met 3 new brackets and they induce 3 graded Lie superalgebras:
- $[,]: Der_k \times Der_{\ell}\rightarrow Der_{k+\ell}$: the graded commutator on graded derivations, defined by $[D_1, D_2]=D1\circ D2-(-1)^{d_1d_2}D_2\circ D_1$ (for homogeneous $D1,D2$ of degree $d_1,d_2$), which induces a graded Lie superalgebra on the derivation space $Der(\Omega^*(M))$
- $[,]^\wedge: \Omega^k(M,TM)\times \Omega^{\ell}(M,TM)\rightarrow \Omega^{k+\ell-1}(M,TM)$:  Algebraic bracket (Nijenhuis Richardson bracket), defined by $i_{[L,K]^\wedge}=[i_L, i_K]$, which induces a graded Lie superalgebra on $\Omega^{*-1}(M)$.  (The superscript $^{*-1}$ means a $TM$ valued $k$-form $K$ is of degree $(k-1)$ in the graded Lie superalgebra induced by $[,]^\wedge$).
- $[,]: \Omega^k(M,TM)\times \Omega^{\ell}(M,TM)\rightarrow \Omega^{k+\ell}(M,TM)$: Frölicher–Nijenhuis bracket, defined by $\mathcal {L}_{[L,K]}=[\mathcal {L}_L, \mathcal {L}_K]$, which induces a graded Lie superalgebra on $\Omega^*(M)$.

### Projection and Curature


For $K,L\in \Omega^1(M,TM)$，we have the explicit form for for the Frölicher–Nijenhuis bracket:
$[K,L]\in \Omega^2(M,TM)$
$$[K, L](X,Y)=[KX, LY] − [KY, LX]\\
 − L([KX, Y] − [KY, X]) \\
− K([LX, Y] − [LY, X]) \\
+ (LK + KL)[X, Y]$$

Curvature:

If $P\in \Omega^1(M,TM)$ is a projection, i.e. $P^2=P$, then the curvature $R$ of $P$ is defined as: $[P, P] = 2R + 2\bar R$.
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/427fb914c564cc767a7e3472dbf0a1b0.png)

按照上式，令$K=L=P$,

$$[P, P] = ([PX, PY] − [PY, PX]) − \\
(P([PX, Y] − [PY, X]) − P([PX, Y] − [PY, X])) + \\
(P^2 + P^2)  [X, Y] = \\
2[PX, PY] - 2P([PX, Y] − [PY, X]) + 2P[X, Y] $$

$$[P, P]/2= [PX, PY] - P[PX, Y] + P[PY, X] + P[X, Y] \\
= [PX, PY] - (P[PX, PY] - P[PX, PY]) - P[PX, Y] + P[PY, X] + P[X, Y] = \\
\left([PX, PY] - P[PX, PY]\right) + \left(P[PX, PY] - P[PX, Y] + P[PY, X] + P[X, Y]\right) = \\
(Id-P)[PX, PY] +P[(Id-P)X, (Id-P)Y] = \\
\bar R + R $$

> The cocurvature $\bar R$ is usually zero for a connection on a fibre bundle since the verticle bundle is integrable ($[PX, PY]$ is verticle and hence $P[PX, PY]=Id[PX, PY]$).


The Bianchi identity says: (It's just a consequence of the graded Jacobi identity $[P, [P,P]]=0$)
![](https://github.com/NewThinker-Jeffrey/Figures/raw/main/figures/098a2959f4e553ed1e3c9666e0673237.png)


### 一些相关概念

- differential graded algebra
- f-related vector field for a smooth mapping $f:M\rightarrow N$
- f-related vector valued differential form for a smooth mapping $f:M\rightarrow N$

- FN括号相关链接
  -  https://en.wikipedia.org/wiki/Fr%C3%B6licher%E2%80%93Nijenhuis_bracket
  -  https://arxiv.org/pdf/1706.00870.pdf  第4节
  -  [https://encyclopediaofmath.org/wiki/Frölicher-Nijenhuis_bracket](https://encyclopediaofmath.org/wiki/Frölicher-Nijenhuis_bracket)


