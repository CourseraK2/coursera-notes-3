## Optimization Objective
	
	SVM: support vector machine 算法其实是 logistic regression 的一种演化
	
	对 logistic regression 而言
	
	if y = 1, we want h(X) ≈ 1, θ^T*X >> 0
	if y = 0, we want h(X) ≈ 0, θ^T*X << 0
	
	
	当 y = 1 时，cost = -log(1/(1+e^-z)), z = θ^T*X
	
	以 z 为 x-axis 画图，得到一条下降的曲线。这曲线可以用两条直线来模拟：从 z>=1 开始，cost = 0; 0<z<1 的部分用一条直线贴合
	
	这个新的 cost 函数称为 cost_1(z)
	
	当 y = 0 时类似，得到 cost_0(z)
	
	我们把 logistic regression 的 J(θ) 乘以 m/λ，得到 SVM 的 J(θ)（这个变换对求 min J(θ) 并没有影响）
	
	J(θ) = C*sum(1:m) { 
		y^(i)*cost_1(θ^T*X^(i)) + 
		(1-y^(i))*cost_0(θ^T*X^(i))
	} + (1/2)* sum(1:n) {θ_j^2}
	
	其中 C = 1/λ
	
	
	
	对于 logistic regression 的 h(x) = g(θ^T*x)，我们之前已经计算出：
	
	g(z) >= 0.5 when z>=0 
			=> θ^T*x >= 0
	g(z) < 0.5 when z<0 
			=> θ^T*x < 0
			
	SVM 和 logistic regression 一样，但是这里我们是直接简化成

	h(x) = 1, if θ^T*x >= 0
	h(x) = 0, if θ^T*x < 0
	
-----

## Large Margin Intuition

	SVM 又称 large margin classifier，我们来看下为什么
	
	
	
	当 C 很大时：
	
	if y = 1, we want h(X) ≈ 1, 可能需要直接要求 θ^T*X >= 1，比原来的 θ^T*x >= 0 更严格
	if y = 0, we want h(X) ≈ 0, 可能需要直接要求 θ^T*X <= -1，比原来的 θ^T*x < 0 更严格
	
	this builds an extra safety margin into SVM
	
	注意这只是在求 min J(θ) 时实际会发生的情况，我们 h(x) 的定义并不发生变化
	
	
	在图像上表现出来的就是：decision boundary 距离 example 的距离会很大。考虑一个 binary class 的情况，decision boundary 是一条直线，这条直线距离两边的阵营都有一定的距离，不会出现贴着某一方分割的情况。这个距离称作 margin
	
	
	这里当 C 很大时，对应的 λ 就很小，参考 logistic regression with regularization 的情况，λ 很小时是 overfitting，这也就说明这个 decision boundary 对 training set examples 的区分做得还是很到位的
	

	
	SVM 对 outlier 是比较敏感的，这也是受 C 控制的：
	
	当 C 很大时，SVM 对 decision boundary 要求偏严格，可能出现 "为了迁就 outlier 而放弃 large margin" 的情况（也算是一种 overfitting）
	如果 C 不是那么大，可能出现 "放弃 outlier 保留 large margin" 的情况（就没有那么 overfitting 了）
	
-----

## Mathematics Behind Large Margin Classification

-----

## Kernels I

	Kernel 是和 SVM 组合使用的一个技术
	
	考虑一个 polynomial 同时也是 non-linear classification 的 h(x)=1 的条件
	
	h(x) = 1 if θ_0 + θ_1*x_1 + θ_2*x_2 + θ_3*x_1*x_2 + θ_4*x_1^2 + θ_5*x_2^2 >= 0
	
	为了简单一点，我们定义:
	f_1 = x_1
	f_2 = x_2
	f_3 = x_1*x_2
	f_4 = x_1^2
	f_5 = x_2^2
	
	所以就变成 θ_0 + θ_1*f_1 + θ_2*f_2 + θ_3*f_3 + θ_4*f_4 + θ_5*f_5 >= 0
	
	按这个套路，一般的 h(x) = 1 的条件就可以写成 θ_0 + θ_1*f_1 + ... + θ_n*f_n >= 0
	
	kernel 的定义如下：
	
	我们选定 l^(1)...l^(n) 一共 n 个点，称为 landmark，对于一个具体的点 x(x_1, x_2)
	
	f_1 = similarity(x, l^(1))
		= exp(-||x-l^(1)||^2/2σ^2)
		
	f_n = similarity(x, l^(n))
		= exp(-||x-l^(n)||^2/2σ^2)
		
	这里 ||u|| 表示 length of vector u
	
	||u|| = sqrt(u_1^2 + u_2^2)
	
	所以 ||x-l^(n)|| 也就是点 x(x_1, x_2) 到 l^(n) 距离的平方。这个 kernel 也因此成为 Gaussian kernel。
	
	当 x ≈ l^(n) 时（换言之，两个点离得比较近），f_n ≈ exp(0) ≈ 1
	
	当 x is far from l^(n) 时，f_n ≈ exp(- large number) ≈ 0
	
	
	
	
	对单个的 f_i 而言，l^(i) 恒定，f_i 可以看做是 (x_1, x_2) 的函数，plot 出来的话是一个山峰形。此时 σ 对山峰形状有影响：
	* 若 σ 偏大，山峰平缓
	* 若 σ 偏小，山峰陡峭
	
	
	总结一下：
	C = 1/λ:    
	* Large C: Lower bias, high variance. 
    * Small C: Higher bias, low variance.
	
	σ^2:
	* Large σ^2: 
		* Feature f_i vary smoothly. 
		* Higher bias, lower variance.
	* Small σ^2: 
		* Feature f_i vary dramatically. 
		* Lower bias, higher variance.
	
## Kernels II
	
	How to choose landmarks? Directly use training examples as landmarks
	
	
	假定有 m 个 example，(x^(1), y^(1)), ..., (x^(m), y^(m))，我们直接取 l^(1) = x^(1), ..., l^(m) = x^(m) 作为 landmark
	
	对任意一个 cross validation 或者 test set 的输入 x，计算：
	f_1 = similarity(x, l^(1))
	...
	f_m = similarity(x, l^(m))
	
	然后得到 f = |  1  |
				 | f_1 |
				 | ... |
				 | f_m |
				 
	h(x) = 1 if θ^T*f >= 0
	
-----

## Using an SVM	
	
	In practice, use a SVM software package (e.g. liblinear, libsvm etc.) to solve for θ parameters.
	
	Things need to specify:
	* Choice of parameter C.
	* Choice of kernel 
		* No kernel (a.k.a. linear kernel. i.e. simply a SVM) 
		* Gaussian kernel:
			* similarity func2on
			* σ^2
		* Polynomial kernel:
			* 比如 f_i = (x^T*l^(i))^2 这样的
		* More esoteric ([ˌesəˈterɪk], 深奥的): String kernel (用于处理 x 是字符串的情况), chi-square kernel, histogram intersection kernel. seldom used
	
	Note: **Do** perform feature scaling before using the Gaussian kernel.
	Note: Not all similarity functions `similarity(x, l)` make valid kernels. (Need to satisfy technical condition called Mercer's Theorem to make sure SVM packages' optimizations run correctly, and do not diverge). 

	
	
	Multi-class classification and SVM

	对于一个 K-class 的 classification (i.e. y ∈ {1,2,...,K})，也可以用 SVM
	
	* Many SVM packages already have builtIin multiIclass classification functionality. 
	* Otherwise, use one-vs-all method (Train K SVMs, one to distinguish y = i from the rest, for i = 1,2,...,K), get θ^(1), θ^(2), ..., θ^(K). Pick class i with largest (θ^(i))^T*x
	
	
	
	logistic regression vs. SVM
	
	假设：
	* n = number of features , x ∈ R^(n+1)
	* m = number of training examples 

	一般有：
	* If n is large (relative to m): Use logistic regression, or SVM without a kernel ("linear kernel") 
	* If n is small, m is intermediate: Use SVM with Gaussian kernel 
	* If n is small, m is large: Create/add more features, then use logistic regression or SVM without a kernel 
	* Neural network likely to work well for most of these secngs, but may be slower to train.
	
	
	The optimization problem that the SVM has is a convex one and so the good SVM optimization software packages will always find the global minimum or something close to it.
	And so for the SVM you don't need to worry about local optima.
	
-----

## Q1

	The figure shows a decision boundary that is underfit to the training set, so we'd like to lower the bias / increase the variance of the SVM. We can do so by either increasing the parameter C or decreasing σ^2.

## Q2

	This figure shows a "narrower" Gaussian kernel centered at the same location which is the effect of decreasing σ^2.
	
## Q4

	Q: Suppose you have a dataset with n = 10 features and m = 5000 examples. After training your logistic regression classifier with gradient descent, you find that it has underfit the training set and does not achieve the desired performance on the training or cross validation sets. Which of the following might be promising steps to take? Check all that apply.


	A neural network with many hidden units is a more complex (higher variance) model than logistic regression, so it is less likely to underfit the data.
	
	You are already underfitting the data, and an SVM with only the linear kernel is comparable to logistic regression, so it will likely underfit the data as well.
	
	By using a Gaussian kernel, your model will have greater complexity and can avoid underfitting the data.
	
## Q5

	T/F: If the data are linearly separable, an SVM using a linear kernel will return the same parameters θ regardless of the chosen value of C (i.e., the resulting value of θ does not depend on C).
	
	False
	
	A linearly separable dataset can usually be separated by many different lines. Varying the parameter C will cause the SVM's decision boundary to vary among these possibilities. For example, for a very large value of C, it might learn larger values of θ in order to increase the margin on certain examples.