[TOC]

## Ceres 中一些重要的tips

### Line Search 方法的使用

与 Trust Region 方法的一个重要不同，是 Line Search 方法 不需要在每个step中求解线性方程组。
 Line Search 法支持的下降方向：

#### 下降方向选择

- STEEPEST_DESCENT
- NONLINEAR_CONJUGATE_GRADIENT： 非线性共轭梯度法。支持三种类型 nonlinear_conjugate_gradient_type（差异在如何选择共轭方向上）：
    - FLETCHER_REEVES: 理论上可以证明，使用满足(strong) Wolfe condition  的soft line search ，也可以保证收敛性；
    - POLAK_RIBIERE：对于二次优化问题与FLETCHER_REEVES等价，但对于非二次问题二者并不等价；此方法用在实际问题中时通常也会收敛，且表现出的性能普遍会优于FLETCHER_REEVES；然而，此方法在实际中获得的成功从理论上并不好解释，因为理论上可以构造出一些特殊问题，在这些问题中此方法甚至无法收敛（即便用 exact line search）。
    - HESTENES_STIEFEL
- BFGS： a full, **dense** approximation to the inverse Hessian is maintained and used to compute a quasi-Newton step. BFGS is currently the best known general quasi-Newton algorithm.
- LBFGS： A limited memory approximation to the full BFGS method in which the last M iterations are used to approximate the inverse Hessian used to compute a quasi-Newton step

#### soft line search

- Ceres::ARMIJO:  backtracking and interpolation based
- Ceres::WOLFE:  a sectioning / zoom interpolation (strong) Wolfe condition

Currently Ceres Solver supports both a backtracking and interpolation based Armijo line search algorithm, and a sectioning / zoom interpolation (strong) Wolfe condition line search algorithm. 
**However, note that in order for the assumptions underlying the BFGS and LBFGS methods to be guaranteed to be satisfied the Wolfe line search algorithm should be used.**



### Trust Region 方法的使用

支持 LM 和 DogLeg 两种 Trust Region 方法。两种方法中，最耗时的部分是求解线性方程组。

#### 线性求解器

#####  通用线性求解器：

- DENSE_QR:  (exact step)
对Jacobian做QR分解，$J=QR$, 然后解方程 $\Delta x^*= -R^{-1}Q^Tf$；

- DENSE_NORMAL_CHOLESKY & SPARSE_NORMAL_CHOLESKY:  (exact step)
对伪Hessian阵做 Cholesky分解， $H=R^TR$,  然后解方程 $\Delta x^*= R^{-1}R^{-T}g$;
稀疏求解时，优先使用 SuiteSparse 的CHOLMOD，
    - SuiteSparse
    - Apple’s Accelerate framework.
    - Eigen’s sparse linear solvers.

- CGNR:  (inexact step)  Conjugate Gradients solver on the **normal equations** $H\Delta x=g$
共轭梯度法迭代求近似解。一般配合 Preconditioner  使用，改善条件数，加快迭代求解的收敛速度；
Preconditioner只能选择 JACOBI (block diagonal)。
For general sparse problems, if the problem is too large for CHOLMOD or a sparse linear algebra library is not linked into Ceres, another option is the CGNR solver.（如果问题的维数非常高，即便稀疏性较好，CHOLMOD可能也无法再适用）



##### 专门为 BA 问题适配的求解器（舒尔补）

BA 问题的特殊结构： landmark 相互之间无约束；landmark数量通常远远大于 camera pose数量。

使用舒尔补先把 landmark 消元，得到 reduced camera matrix，并求解这个reduced camera matrix；再代回求解landmark。
如何求解 reduced camera matrix ，有以下几种选择：

- DENSE_SCHUR & SPARSE_SCHUR:  (exact step)
通过稠密或稀疏的Cholesky factorization of reduced camera matrix 来求解camera pose；

- ITERATIVE_SCHUR: (inexact step)
通过共轭梯度法近似求解camera pose；
通过 ```Solver::Options::use_explicit_schur_complement```  来控制是否显式计算舒尔补S（因为可以做到只在需要时计算S与某个向量x的乘积），默认是隐式的；
可选的Preconditioner  有：
     - SCHUR_JACOBI ： 一般的 JACOBI  preconditioner (block diagonal).
     - CLUSTER_JACOBI  & CLUSTER_TRIDIAGONAL:  根据Camera之间的共视关系把各Camera聚类重排，生成更紧凑的 block diagonal 或 block tridiagonal, 作为preconditioner；这两种 preconditioner 都需要对 camera 做聚类，所以还要选择聚类方法。可选的有 CANONICAL_VIEWS（慢，cluster效果好） 和 SINGLE_LINKAGE（快，但cluster效果可能不如前者）。

##### 消元顺序  Ordering

http://ceres-solver.org/nnls_solving.html#ordering

ceres 支持灵活的消元顺序配置：可以完全由 ceres 自己决定消元顺序，也可完全由用户指定消元顺序，也可由用户给出变量分组（每组有个组号，组号越小，越先被消元；组号为 0 的最先被消元）。
变量的分组信息主要用于 CAMD （constrained Approximate Minimum Degree)  排序算法，CAMD 保证组号越小的变量组越先被消元；每组组内各变量间的消元顺序则由 CAMD 算法自己选择（Within each group, CAMD is free to order the parameter blocks as it chooses）。

但注意仅当 ```sparse_linear_algebra_library_type = SUITE_SPARSE``` 且  ```linear_solver_ordering_type = AMD``` 时，CAMD 算法才被启用；
其他情况下，比如 ```linear_solver_ordering_type = NESDIS``` (Nested Dissection algorithm) ，或者 ```sparse_linear_algebra_library_type``` 不是 ``` SUITE_SPARSE ```时，用户指定的 Ordering 信息 (the value of ```linear_solver_ordering```) 会被忽略，而是由 AMD（而不是CAMD）或 NESDIS 算法计算一个 fill reducing ordering，具体用哪个算法取决于  ```linear_solver_ordering_type ``` 的值。

```ParameterBlockOrdering``` 类用于指定各变量的消元顺序（组号）。为某个变量设置组号的函数为：
```
bool ParameterBlockOrdering::AddElementToGroup(const double *element, const int group)
```

在 ```Solver::Options``` 中设置 ParameterBlockOrdering：
```
shared_ptr<ParameterBlockOrdering> Solver::Options::linear_solver_ordering
```

> 关于 BA 问题中变量的排序
http://ceres-solver.org/nnls_solving.html#bundle-adjustment
用户可以自己把 landmark 对应的变量放在第 0 分组，或者由ceres自己判断哪些变量在第 0 分组：
If the user leaves the choice to Ceres, then the solver uses an **approximate maximum independent set** algorithm to identify the first elimination group [LiSaad].


#####  exact step 和 inexact step 总结

注意，LM 法支持 exact step  和 inexact step 两种求解方式，但 **DogLeg 只支持 exact step** !

exact step 是基于矩阵分解的方法，精确求解线性方程组；
inexact step 是通过迭代的方法（共轭梯度法），近似求解 线性方程组；

> When the user chooses ```CGNR``` as the linear solver, Ceres automatically switches from the exact step algorithm to an inexact step algorithm.
> When the user chooses ```ITERATIVE_SCHUR``` as the linear solver, Ceres automatically switches from the exact step algorithm to an inexact step algorithm.

#### Trust Region 方法 中Robust kernel 的适配

ceres中，Robust kernel 的作用，在每一步的线性方程组的求解中，会被转化为对 Jacobian 和对0点处（线性化点处）的 residual 的修正；
> gtsam中，也会做类似的适配，但是对Robust kernel的近似阶数与ceres有差异：
ceres尽量保持Robust kernel 的二阶近似，gtsam中只保持其一阶近似。



