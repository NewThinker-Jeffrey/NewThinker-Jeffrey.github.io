# Theory of robustified least square method in ceres-solver

A non-linear least square problem is usually modeled in the form: 
$$\min_x \sum_i \frac{1}{2}f^T_i(x)f_i(x)\tag{1}$$
where $f_i(x)$ is a vector valued *cost function* which usually represents the difference between the prediction and the realization of a measurement . 
To solve this non-linear problem, we usually beforehand linearize $f_i$ as $f_i(x_0+\delta x)=f_i(x_0)+J_i(x_0)\delta x$, where $J_i$ is the jacobian of $f_i$. Once $x_0$ is chosen, $J_i(x_0)$ and $f_i(x_0)$ can be regarded as constants, so we denote $f_i(x_0)$ as $f_i$ and $J_i(x_0)$ as $J_i$ in the following equations for representation convenience. Thus $f_i(x_0+\delta x)=f_i+J_i\delta x$, and now the original problem has a simplified approximate form: 
$$\min_{\delta x} \sum_i \frac{1}{2}(f_i+J_i\delta x)^T(f_i+J_i\delta x) =\\
\sum_i \frac{1}{2}(f^T_if_i+2f^T_iJ_i\delta x+\delta x^TJ^T_iJ_i\delta x)\tag{2}$$
By solving $(2)$ we get a step-result for problem $(1)$. If we iteratively do this approximation, we finally get a proper result for $(1)$ .

However, we need a more robustified form to handle outliers. To achive this, we need to slightly change the above form to:
$$\min_{\delta x} \sum_i \frac{1}{2}\rho_i(f^T_if_i+2f^T_iJ_i\delta x+\delta x^TJ^T_iJ_i\delta x)\tag{3}$$
where each $\rho_i(s)$ is a scalar valued *loss function*. Note that $s=(f^T_if_i+2f^T_iJ_i\delta x+\delta x^TJ^T_iJ_i\delta x)$ is also a scalar.
choose $s_0=f^T_if_i$, then 
$$\rho_i(s_0+\delta s)\approx^{2ndOrder} \rho_i(s_0)+\rho_i'\delta s+\frac{1}{2}\rho_i''\cdot (\delta s)^2\tag{4}$$
where:
$\delta s=s-s_0=2f^T_iJ_i\delta x+\delta x^TJ^T_iJ_i\delta x$
$(\delta s)^2=\delta s^T \delta s=^{2ndOrder}4\delta x^T (J_i^T f_if^T_iJ_i) \delta x$
so $(4)$ becomes:
$$\rho_i(s_0+\delta s)\approx^{2ndOrder}\\
\rho_i(s_0)+\rho_i'\cdot (2f^T_iJ_i\delta x+\delta x^TJ^T_iJ_i\delta x) + \frac{1}{2}\rho_i''\cdot (4\delta x^T J_i^T f_if^T_iJ_i \delta x)=\\
\rho_i(s_0)+2\rho_i'f^T_iJ_i\delta x+\delta x^TJ^T_i(\rho_i'I+2\rho_i''f_if^T_i)J_i\delta x\tag{5}$$

and $(2)$ becomes :
$$\min_{\delta x} \sum_i \frac{1}{2}[\rho_i(s_0)+2\rho_i'f^T_iJ_i\delta x+\delta x^TJ^T_i(\rho_i'I+2\rho_i''f_if^T_i)J_i\delta x]\tag{6}$$
notice that $(6)$ makes sense only if $J^T_i(\rho_i'I+2\rho_i''f_if^T_i)J_i$ is positive definite, otherwise there may not exist a minimal. so we'd better guarantee that $(\rho_i'I+2\rho_i''f_if^T_i>0)$ or more strictly $(\rho_i'+2\rho_i''|f_i|^2>0)$. How do we do this elegantly? 

$(6)$ is (roughly) equivalent to the below problem: (this step is named 'rescaling' in ceres solver)
$$\min_{\delta x} \sum_i \frac{1}{2}(\hat{f_i}+\hat{J_i}\delta x)^T(\hat{f_i}+\hat{J_i}\delta x)\tag{7}$$
where $\hat{f_i}=\frac{\sqrt{\rho'}}{1-\alpha}f_i$ and $\hat{J_i}=\sqrt{\rho'}(I-\alpha\frac{f_if_i^T}{|f_i|^2})J_i$, and $\alpha$ is a root of $(\alpha^2-2\alpha-\frac{2\rho''}{\rho'}|f_i|^2=0)$ or $(\alpha^2-2\alpha=\frac{2\rho''}{\rho'}|f_i|^2)$ when $(\rho_i'+2\rho_i''|f_i|^2>0)$, otherwise limit $\alpha$ to $1$ (or $1-\epsilon$ with a small $\epsilon$).
By this rescaling step, we totally avoid the trouble of the potential non-positive definite hession with only slightly distorting the loss function upon some extremely divergent outliers(who make $\rho_i'+2\rho_i''|f_i|^2<0$ ).
> here we can illustrate the equivalence when $(\rho_i'+2\rho_i''|f_i|^2>0)$. For this case, $(\alpha^2-2\alpha=\frac{2\rho''}{\rho'}|f_i|^2)$.
expand the $i$th item of $(7)$:
$$(\hat{f_i}+\hat{J_i}\delta x)^T(\hat{f_i}+\hat{J_i}\delta x)=\\
\hat{f_i}^T\hat{f_i} + 2\hat{f_i}^T\hat{J_i}\delta x + \delta x^T\hat{J_i}^T\hat{J_i}\delta x$$
compare the coefficients of $(6)$ and $(7)$ on the first order:
$$\hat{f_i}^T\hat{J_i}=\frac{\sqrt{\rho'}}{1-\alpha}f_i^T\sqrt{\rho'}(I-\alpha\frac{f_if_i^T}{|f_i|^2})J_i=\\
\rho'\frac{1}{1-\alpha}(f_i^T-\alpha\frac{f_i^Tf_if_i^T}{|f_i|^2})J_i=\\
\rho'\frac{1}{1-\alpha}(f_i^T-\alpha\frac{(f_i^Tf_i)f_i^T}{(f_i^Tf_i)})J_i=\\
\rho'\frac{1}{1-\alpha}(1-\alpha)f_i^TJ_i=\rho_i'f^T_iJ_i
$$
compare the coefficients of $(6)$ and $(7)$ on the second order:
$$
\hat{J_i}^T\hat{J_i}=\rho'J_i^T(I-\alpha\frac{f_if_i^T}{|f_i|^2})(I-\alpha\frac{f_if_i^T}{|f_i|^2})J_i=\\
\rho'J_i^T(I-2\alpha\frac{f_if_i^T}{|f_i|^2}+\alpha^2\frac{f_if_i^Tf_if_i^T}{|f_i|^4})J_i=\\
\rho'J_i^T(I-2\alpha\frac{f_if_i^T}{|f_i|^2}+\alpha^2\frac{f_i(f_i^Tf_i)f_i^T}{|f_i|^2(f_i^Tf_i)})J_i=\\
\rho'J_i^T(I-2\alpha\frac{f_if_i^T}{|f_i|^2}+\alpha^2\frac{f_if_i^T}{|f_i|^2})J_i=\\
\rho'J_i^T[I+(\alpha^2-2\alpha)\frac{f_if_i^T}{|f_i|^2}]J_i=\\
\rho'J_i^T[I+(\frac{2\rho''}{\rho'}|f_i|^2)\frac{f_if_i^T}{|f_i|^2}]J_i=\\
\rho'J_i^T[I+\frac{2\rho''}{\rho'}f_if_i^T]J_i=\\
J^T_i(\rho_i'I+2\rho_i''f_if^T_i)J_i
$$
