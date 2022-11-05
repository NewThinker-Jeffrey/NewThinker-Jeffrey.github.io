[TOC]

##  因子图读书零碎笔记

### LM vs Powell's Dog Leg

LM 方法中存在拒绝更新的情况，这时需要调整 $\lambda$，然后重新解方程（矩阵分解）计算新的步长；而解方程（矩阵分解）是计算中最耗时的部分；
Powell's Dog Leg 先单独计算 高斯牛顿和梯度下降对应的更新方向，然后直接组合二者得到当前step的更新方向；避免出现拒绝更新（意味着一部分计算被浪费了）；

Powell's Dog Leg 要求Hession矩阵可逆，且数值运算稳定；LM 方法中因为H矩阵上还要再加一个对角阵，竖直计算更稳定；

### 因子图消元算法

因子图消元，是指把因子图变回一个贝叶斯网络，只不过这个新的贝叶斯网络中的每个node都对应一个variable；（SLAM建模生成的初始贝叶斯网络中，node分两类，variable和观测）；
书中提到，这样消元后，更便于做MAP估计;
Algorithm 3.1 和 Algorithm 3.2 用伪代码介绍了一般因子图的消元过程；
但要注意到一点，Algorithm 3.2 中的第 5 步，我们需要 $\psi(x_j,S_j)$  能够被分解成 $p(x_j|S_j)\tau(S_j)$ 这样的形式，消元才能继续下去。在线性高斯假设下，这是可以实现的，且等价于jacobian矩阵A的QR分解，或$A^TA$的Cholesky分解 （LM算法中，$A^TA$会再加上一个对角线矩阵，它的Cholesky分解不再等价于原jacobian的QR分解；但$A^TA$上加一个对角线矩阵，其实等价于在原因子图中为各变量又加入了一些先验的 factor，而新因子图对应的jacobian的QR分解等价于LM算法中需要的Cholesky分解）
> In the case of linear measurement functions and additive normally distributed noise, the elimination algorithm is equivalent to sparse matrix factorization. Both sparse Cholesky and QR factorization are a special case of the general algorithm.

每个factor 对应 jacobian 的一行，每个variable对应jacobian的一列；
消元第1个variable时（jacobian的第一列），去掉与该variable关联的所有factor (即去掉所有第一列非0的行)，这些factor的乘积可分解成$\psi(x_j,S_j)=p(x_j|S_j)\tau(S_j)$ （其中新factor $\tau(S_j)$对应jacobian的一个新行，见Figure 3.6右侧矩阵的红色行；$p(x_j|S_j)$则对应蓝色行；准确的说，Figure 3.6中的每一行可能是一个"行块"，而不一定是单纯的一行 ）；
完成所有变量的消元后，正好就完成了jacobian矩阵的QR分解，剩余的就是R矩阵；
jacobian矩阵A的QR分解，与信息矩阵$A^TA$的Cholesky分解，是等价的；

因子图消元变成新的贝叶斯网络，等价于，jacobian矩阵做QR分解；
上三角方阵R，对应消元后的贝叶斯网络；

> 因子图对应一般的$m\times n$的Jacobian矩阵；
马尔科夫随机场 MRF 对应 $n\times n$的 hession 阵；
贝叶斯网络对应上三角方阵；
因子图与其对应的MRF是否有相同的边结构？

### QR 分解， partial QR， multifrontal QR

QR 分解: （Wiki解释得很好）
- Hermite? 两个列向量接近正交时存在数值稳定性问题；
- Householder reflection matrix： 这种方法与书中用到的 partial QR息息相关，partial QR中的Q也是正交阵，但可以只消去头k列而不是做完整QR；

设$x,y$是Jacobian的两个列向量，
$u=x-\alpha e_1$, where $|x|=|\alpha|$ and $\alpha$ should get the opposite sign as the k-th coordinate $x^k$ of $x$;
$u^Tu=u\cdot u=|u|^2=|x|^2+\alpha^2-2x^1$， 其中$x^1=x\cdot e_1$;
$uu^T=xx^T+\alpha e_1e_1^T - \alpha xe_1^T - \alpha e_1 x^T$
$uu^Ty=x(x^Ty)+\alpha e_1y^1 - \alpha x y^1 - \alpha e_1 (x^Ty)$
> 利用此式可解释 Figure 3.6 矩阵中那些不发生改变的entry：
如果 $x^Ty=x\cdot y=0$ 且 $e_1^Ty=y^1=0$（比如，因子图消元时，$e_1$的非零元所在行是与待消元变量x相连的factor所对应的某一行，且变量y与x在因子图中不相连，即jacobian中它们所对应的列无公共非0行时），上式变为 $uu^Ty=0$。此时y在Q的作用下保持不变，即$Qy=y$，Q的定义见下面。


$v=\frac{u}{|u|}$
$Q=I-2vv^T=I-\frac{2uu^T}{|u|^2}$
> 从几何意义上（见wiki，$\alpha e_1$ 与 $x$构成等腰三角形，$u$是底边），可以看出，$2v(v^Tx)=u$，因此，$Qx=x-u=\alpha e_1$；从而实现第1列的消元。

$Q=Q^T$  对称
$QQ^T=QQ=I+4(vv^T)(vv^T)-4(vv^T)=I+4v(v^Tv)v^T-4(vv^T)=I+4vv^T-4(vv^T)=I$  正交

> 稠密QR分解复杂度：数量级上也是(mn^2+n^3)，具体的带系数的版本见书中的式4.3；
式4.2给出更精细的适用于稀疏QR分解的复杂度；

partial QR （只消去部分列，比如前k列）可以用Householder reflection matrix方法实现；
 multifrontal QR ?

### Fill-in 与 ordering

Fill-in 的定义：
- 消元后得到的上三角R阵比 Hessian 阵（对角线以上的区域）多出的非0元素；或
- 消元后得到的上三角R阵对应的贝叶斯网络，比 Hessian 阵对应的 MRF 无向图中多出的边

降低fill-in的两类方法 （**Minimum Degree Orderings** 最小度顺序法，和**Nested Dissection Orderings** 嵌套分割顺序法）

重排不影响Hessian阵(对应MRF无向图)的稀疏性，但影响矩阵分解后R的稀疏性;


**Minimum Degree Orderings** 最小度顺序法: 
> 问题：参考的图是因子图(对应 jacobian A)，还是MRF无向图（对应$A^TA$）,还是二者等价？

- MMD: 
    - eliminate all variables of minimal degree in one call of the elimination function, known as multiple elimination or minimum degree MMD;
    - In addition *indistinguishable* nodes (如何定义indistinguishable？) are eliminated, whose elimination will not introduce additional dependencies as they are subsumed by another elimination performed in the same step;
- AMD (Approximate Minimum Degree):  ```gtsam/inference/Ordering.cpp: Ordering::Colamd()```
    - avoids computing the exact vertex degrees when eliminating one or more variables, by collecting nodes into cliques, and only the degrees for the cliques are calculated; 
    - an approximate bound on the degree of the remaining vertices can be kept up to date relatively cheaply.

[COLAMD](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.124.1109&rep=rep1&type=pdf): 列近似最小度排序。附件中也保存了此paper；
> Sparse Gaussian elimination with partial pivoting computes the factorization PAQ = LU of a sparse matrix A, where the row ordering P is selected during factorization using standard partial pivoting with row interchanges(行也有重排？). The goal is to select a column preordering, Q, based solely on the nonzero pattern of A, that limits the worst-case number of nonzeros in the factorization. The fill-in also depends on P, but Q is selected to reduce an upper bound on the fill-in for any subsequent choice of P. The choice of Q can have a dramatic impact on the number of nonzeros in L and U.

**Nested Dissection Orderings** 嵌套分割顺序法？(divideand-conquer 分而治之)  ```gtsam/inference/Ordering.cpp: Ordering::Metis()```
分而治之的可行性在于，消去一个node时，只会在与之直连的node之间引入新的约束factor；
recursively partitioning the graph, and returning a post-fix notation of the partitioning tree as the ordering
理论依据：对于某些特殊类别的图（比如chain-like graphs, planar graph 等）， 满足 f(n)-Separator Theorem 原理；
SLAM种可能比较实用的是 [planar graph](https://en.wikipedia.org/wiki/Planar_graph)；
大致做法 https://en.wikipedia.org/wiki/Nested_dissection
1. 对graph做AB+C分割，分割后，得到A、B两个不相连的子图，以及Separators组成的C；
    - 把graph中的所有node分成了三份。我们借助二叉树想象一下这个分割过程：刚开始所有节点在一起（一个大圆），然后分裂成了ABC三份（三个小圆），变成了一个以C为根节点，AB为左右子节点的二叉树；
2. A和B再递归地做上述分割（A、B各自再分裂成深层的二叉树），直到满足终止条件：|A| , |B| ≤ epsilon， or |A| , |B| = 1；
3. 完成上述递归后，得到一个较深的二叉树；按照后序 post-order 遍历二叉树的所有节点（每个树节点代表目标graph的若干节点），作为消元顺序。
> each of the two subgraphs formed by removing the separator is eliminated first, and then the separator vertices are eliminated.

### Ordering Heuristics in Robotics

“natural” orderings 自然顺序（fill-in严重）
- 先消landmark，再消keyframe:
消掉一个landmark（对应jacobian中的一个列块）时，直接观测到这个 landmark 的所有 keyframe 会一起被一个新的 factor连接起来；消完所有的landmark后，keyframe之间的因子比原来复杂了很多；
The so-called “Schur complement trick” eliminates all landmarks first, but this leads to a very connected graph on the robot poses. 
- 先消keyframe，在消landmark:
Doing the reverse (poses first) would similarly lead to a fully connected clique of landmarks. 最后变全连接？为什么会变全连接？
消掉第一个keyframe K0后，该 keyframe 的相邻keyframe K1、以及该keyframe观测到的所有landmarks（我们可以先考虑其中一个landmark，记为L0），一起被一个新的 factor连接；注意此时剩下的因子图中L0与K1已经有了连接；
随着继续消掉下一帧 keyframe K1：因为K2、L1和L0在消掉A1前都与K1相连，因此消去K1后，L1、L0、K2将相互连接；
以此来推，消掉所有keyframe后，所有landmark将互相连接；


COLAMD
In contrast, an approximate minimum degree ordering (using COLAMD) leads to much less fill-in and considerably faster linear solve steps.

### iSAM

#### Updating a Matrix Factorization

设 t 时刻的 Jacobian 矩阵为A， t+1时刻，新增若干新的variable (新增列) 和若干新的 观测 （即factor，新增行）；
由于 t 时刻之前的 factor都不依赖 新增的 variable ，所以新增列的前m行为0，因此 t+1时刻的 jacobian 为：
$\left[\begin{matrix} A & 0 \\ a_o & a_n \end{matrix}\right]$
其中$a = \left[\begin{matrix} a_o & a_n \end{matrix}\right]$ 代表新增的factor，$na_o$ 代表新增的factor与t时刻已经存在的老variable的作用，$a_n$代表新factor与新增的varibale的作用；
设$QR=A$ 是t时刻的QR分解，增量更新R时，只需考虑
$$ R_a = \left[\begin{matrix} R & 0 \\ a_o & a_n \end{matrix}\right] =   \left[\begin{matrix} Q^T & 0 \\ 0 & I \end{matrix}\right] \left[\begin{matrix} A & 0 \\ a_o & a_n \end{matrix}\right]$$
(参考书中式5.4。这里写出的公式相比书中的5.4，考虑了新增的列。a可以是多行)

然后，利用 Givens Rotation 方法，可以逐元素、逐行消去 $R_a$ 中对角线以下的元素，从而得到更新后的$R'$，参考书中Figure 5.2 和式 5.6；

#### Marginalization

Marginalization 时，如果已经有了R阵，且变量排列顺序上要保留的变量都在R的后几行，则只需保留R阵的后几行即可实现边缘化（式5.13。hint: 上/下三角矩阵的逆还是上/下三角）；
但注意，这个操作非常依赖从Jacobian分解得到R的过程中变量的消元顺序。
Note that simply dropping the earliest pose in the **factor graph** (instead of the **Bayes net** resulting from elimination) is not equivalent to marginalization, but results in a loss of information. （见71页第2段说明）

如果消元时总是先消时间上更早的variable，且边缘化时也是移除较早的variable，这个方法就可以使用；但这与得到更好的稀疏性而采取的消元顺序通常是不一致的；好在，fixed-lag smoother 过程中，我们每时刻保留的variable数量是有界的，稀疏性可能能做一些妥协。

In summary, whether we can **easily** marginalize or not depends:
• Covariance form: always easy, select rows and columns from Σ.
• Information form: always hard, involves Schur complement on Λ.
• Square-root information form: depends on the variable ordering.

从Graph的角度看，R阵（上三角矩阵）对应一个有向无环图（贝叶斯网络）。
Any variable that does not point to other variables （对于第 i 个variable 来说，就是指R阵上的第 i 列除对角线外全是0） can always be marginalized out without any computation, 一个变量不指向其他变量，在贝叶斯网络中代表的含义是，其他变量的概率分布不依赖这个变量；所以直接去掉不影响其他变量的概率分布；and the equivalent also holds for groups of variables；（对于第 i 个到第 j 个variable 组成的group来说，就是指R阵上的这几列在第 i 行以上、第 j 行以下全是0；此时，信息矩阵 $R^TR$中，第 i 到 j 行/列，除了中间的 i*j block外都是0；这一block可以分离出来，通过重排可以把这一block放在右下角，然后再取逆就是协方差；取逆过程中，这一block不会影响到其他变量组成的block）；

补充，对于第 i 个variable：
- R阵中第 i 行的非0元素所在列对应的variable，在图中会指向 第 i 个variable ；
- R阵中第 i 列的非0元素所在行对应的variable，在图中会被第 i 个variable 指向；

#### Fixed-lag smooth

主要过程如书中Figure 5.4所示：
- 拿到上一时刻的贝叶斯网络（对应R阵）
- 添加新变量 $x_5$，局部恢复因子图 (Figure 5.4b)：把原根节点$x_4$的density $p(x_4)$ 转化为一个一元factor $f(x_4)$，再加上 $x_5$相关的factor ; 
- 边缘化，去掉变量 $x_2$;
- 局部因子图消元得到新的贝叶斯网络（对应R阵）

这里补充说明，Figure 5.4b 中，除了添加新变量 $x_5$ 所对应的 factor 外，还需要把 $x_4$对应的 measurement factor 还原回来，然后再重新消掉 $x_4$；
这是因为，上一时刻消掉 $x_4$ 时，因子图中还没有 $x_5$，现在有了 $x_5$就需要重新消 $x_4$; 更早的变量，比如 $x_3$，则不受加入新变量 $x_5$ 的影响，因为因子图上它们并未直接相互作用；
 另外，再次强调，边缘化过程中，直接drop 因子图中的变量，和 drop消元后得到的贝叶斯网络R中的变量，是不等价的；前者会带来信息丢失；

Fixed-lag smooth过程中出现的图操作，所对应的矩阵操作如下：
Note that the graphical operations of elimination and marginalization above subsume matrix computations in this linear inference case:
- eliminating a graph corresponds to matrix factorization, 
- and marginalization corresponds to dropping rows and columns, 
- as we are adding one measurement at a time, the factorization can be done incrementally using Givens rotations

SRIF and SRIS:
When doing the computations in square-root information form, these versions of the linear fixed-lag filter and smoother are also called a square-root-information filter or SRIF
[15], or SRIS [110] in the smoother case.

#### Nonlinear Filtering and Smoothing

##### Bayes tree 

因子图消元得到的贝叶斯网络都满足一个特殊的性质：chordal（这需要证明，但书中并未给出）。chordal 是指，消元得到的贝叶斯网络的任意cycle，如果长度大于3，则必然包含一条弦（连接cycle中不相邻的两个点）；In AI and machine learning a chordal graph is more commonly said to be *triangulated*（任意cycle都由可由三角形拼接而成？）

满足此特性的贝叶斯网络，网络中的 clique 可以组成贝叶斯树；(类似于无向图的 clique tree，但又有所不同); Bayes tree 保留了源Bayes  Net 的箭头方向（clique之间）；
Bayes tree 的每个节点代表一个clique（一组互联的variables）；不同的clique可能存在一些共有的variable；
一个clique $C_k$ 又可以划分成两部分： Separator $S_k$ 和 frontal variables  $F_k$，写作 $C_k=F_k:S_k$，其中$S_k = C_k \cap \Pi_k$ 是 $C_k$ 与其在Bayes tree 中的父节点 $\Pi_k$ 的交集 (这里并未要求 $\Pi_k$ 必须是父节点的 frontal variables)；
对于每个variable，只在唯一的一个clique中充当  frontal variable，在其他clique中只充当Separator；（这一行内容待确认？大概率正确）
对于Bayes tree 的根节点，$S_k=\emptyset$ and $C_k=F_k$；
借助Bayes tree ，所有变量X的联合分布可写为： $p(X)=\prod_k p(F_k|S_k)$

##### Bayes tree 的更新

算法伪代码见 p76. Algorithm 5.1，图示见 p75. Figure 5.6；

以新增一个二元 factor为例；当一个新的二元 factor $f(x_j, x_{j'})$ 加入到因子图中时，只有 $x_j$ 和 $x_{j'}$ 所在的 clique （一个variable可能存在于多个clique中，但只有一个clique以该variable为frontal variable。所以，准确的说，应该是，以$x_j$ 或 $x_{j'}$ 为frontal variable 的clique） 各自到 Bayes tree 根节点之间的路径上的clique会受到影响；这些clique 的子树，和其他不含$x_j$和$x_{j'}$的子树一样，不会受到影响；
> Why? 先从贝叶斯网络的层次考虑。
新增涉及到 $x_j$ 和 $x_{j'}$ 的factor时，原 Bayes Net （消元所得，对应R阵）中，哪些 variable 会受影响需要重新消元？
首先 ，$x_j$ 和 $x_{j'}$ 上新增了factor，所以必然受影响，需要重新消元；
按照原先的消元顺序，必须等 $x_j$ 和 $x_{j'}$消元后才能消元的 variable 都是受影响的variable; 
注意，可能存在一些variable，他们原先的消元顺序在 $x_j$ 和 $x_{j'}$之后，但它们的消元并不依赖$x_j$ 和 $x_{j'}$，这部分variable是不受影响的；
具体来说，$x_j$ 和 $x_{j'}$ 在重新消元过程中产生的 factor $\tau(S_j),\tau(S_{j'})$ 跟原来不一样了，因此原 Bayes Net 中$x_j$ 和 $x_{j'}$ 的父 variable （在原 Bayes Net 中，指向$x_j$ 或 $x_{j'}$的variable），都将受到影响；依次类推，原 Bayes Net 中$x_j$ 和 $x_{j'}$ 的祖父节点直至根节点都将受到影响；其他节点不受影响；
因此，在 Bayes tree 中，只有 $x_j$和$x_{j'}$ 所在的 clique 各自到 Bayes tree 根节点之间的路径上的clique会受到影响；


把Bayes tree 中受影响的部分（前述路径上的这些clique）转回因子图，并把新的因子加入其中 (新的factor可能还涉及到新增的variable)；
> 每个clique，对应一个条件分布 $p(F_k|S_k)$，我们可以把这个分布转化为一个factor，这个factor连接了clique中的所有变量($F_k+S_k$)；受影响的所有clique，转换成多个factor，且相互之间存在公共的变量，所以最终链接成一个因子图；
这里恢复出来的因子图，只要能够用来等价表达Bayes tree 的clique $p(F_k|S_k)$即可，不需要跟原始的完整因子图的局部保持结构一致；
参考Figure 5.6右侧，可见恢复出的因子图与原来的因子图的局部结构并不一致；

对这个临时因子图再重新消元（消元顺序按需选择），生成一个新的Bayes tree；
> 由于不是做全局优化，增量式的更新对消元顺序带来的稀疏性要求并不苛刻？更重要的是保证每次增量更新时只影响尽可能少的变量？

最后，再把原Bayes tree 中未受影响的部分（Algorithm 5.1 第4步中的"孤儿"子树 orphaned sub-trees Torph）添加到新的Bayes tree上，就完成了Bayes tree的更新；
> 为了使得 "孤儿"子树 们在上一步生成的新Bayes tree上能找到爸爸， "孤儿"子树根节点的 Separator 必须完整包含在新Bayes tree上某个clique节点$C_k$中；这一点是如何被保证的？上一步中的消元顺序随意选择，都可以保证这一点吗？是的，因为：
1. 因子图消元一个变量后，与之有factor连接的其他variable（separator）必然都会产生一个指向被消元variable的有向箭头；而separator中的variable之间也会被一个因子链接，它们之中的任何一个再被消元时，separator中其他variable也会生成一个指向它的箭头；所以，这样消元结束后，初始时有因子相连的variable都会形成一个clique；
2. 而上面在把Bayes tree 中受影响的clique节点转化为因子图时，是每个clique变成一个 factor（连接该clique中所有variable）；所以，重新消元后，原来的clique必然包含于消元后新生成的某个clique中；


#####  Incremental Smoothing and Mapping

- 加入新的factor时，Bayes tree 中受影响的部分转化得到的临时因子图重新消元时，消元顺序的选择：
使用CCOLAMD (Constrained COLAMD)算法：把最近更新的 clique 放在根节点（最后消元）以便未来的更新中只牵涉尽可能少的clique，并在此基础上尽可能减少fill-in；在SLAM问题中，除非有大的loop closure，否则这种消元顺序效率是比较高的；
可以看到，**isam 问题与全局优化问题，对ordering的需求有很大不同**: 对于全局优化问题，ordering的目的是尽可能提高稀疏性；而isam问题中，ordering更多是为了保证未来的更新中只牵涉尽可能少的clique；


- 更新完Bayes tree 后，我们再更新各变量的值(update the solution)
从Bayes tree 的根节点开始往下更新(back-substitution)；考虑到Bayes tree 的局部变动不会影响远处的 variable，所以一般不必更新所有节点。只需在更新过程中，评估每个clique节点的变化量对它下方子节点的影响，当这个变化量小于特定阈值时，就不再更新它下面的子节点，以减少不必要的计算；

- 重新线性化
完成上一步“变量值更新”后，如果某些变量偏离线性化点较多，就需要对该变量涉及的factor重新线性化。
而且要在原始因子图中做这一步：We also have to go back to the original factors, instead of directly turning the cliques into a factor graph. And that requires caching certain quantities during elimination. 
而且，不同于添加新 factor 时所导致树结构变动（只会影响以factor涉及到的变量为frontal variable的clique节点，及其到根节点的路径上的节点），变量重新线性化时，所有包含该变量的clique节点（无论该变量在该clique中是frontal variable，还是separator）及其到根节点的路径上的节点都会被影响。
重新线性化的影响范围较大，可能带来较多计算量；

##### 实验对比

在 isam2 的论文后半部分，作者通过分析运行时间、矩阵的 nonzeros 数量，来讨论各算法的性能；
但做各算法的 timing 对比时，好像 isam2 在速度上还不如 isam1 快？
作者提到不能光看时间，因为代码和可用库本身的 implementation 问题……
作者提到的因素可能确实影响理论上对算法效率的评判。但实际运用中，算法时间还是更有说服力；


