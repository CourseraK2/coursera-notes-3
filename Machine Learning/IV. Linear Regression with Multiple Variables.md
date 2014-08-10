Multiple Features

	n = number of features (input types)
	x^(i) = input (features) of the ith training example
	x^(i)j = value of feature j of the ith training example
	
	你把整个 training set 想象成一个 data.frame，每一个 column 是一个 feature
	n = ncol(df)
	x^(i) = ith row, df[i,]
	x^(i)j = df[i, j]
	
	h(X) = θ0 + θ1x1 + θ2x2 + ... + θnxn
	为处理起来方便，我们引入一个 x0 = 1
	h(X) = θ0x0 + θ1x1 + θ2x2 + ... + θnxn
	这时：
	X = | x0 |
		| x1 |
		| .. |
		| xn |
		
	Θ = | θ0 |
		| θ1 |
		| .. |
		| θn |
	
	h(X) = Θ^T * X

-----
	
Gradient Descent for Multiple Variables	

	J(Θ)

	公式和单变量时很相似，稍微有点变化，看 slide 吧，太难写了

-----

Gradient Descent in Practice I (practical trick I) - Feature Scaling

	features on a similar scale (数值量级相近) makes gradient descents converge more quickly

	数值量级差很多的情况下，得到的 J 等高线图不是太瘦就是太肥：
	* 太瘦的话，从边际到达中心的路线可能要来回波动多次
	* 太肥的话，从边际到中心要走很多步

	Scaling 的方法是标准化，直观的做法就是除以 feature 的范围值，比如 x1 表示 size，范围是 (0, 2000]
	那我们取 x1 = size /2000，这样 x 范围就是 (0, 1] 了
	
	一般情况下，接近 [-1, 1] 范围的都可以，比如 [-2, 0.5] 这样也是可以接受的

	教授自己用的 rule of thumb 是 [-3, 3] 以内
	
	x0 固定是 1，所以 scale 靠近 1 也是正常的
	
	常见的变换手法是：
	
	x => (x - mean(x)) / (max - min) 
	
-----

Gradient Descent in Practice II (practical trick II) - Learning Rate

	Q1: how to make sure GD is working correctly? (like debugging)
	Q2: how to choose learning rate α

	Q1：
	以 J 为 y 轴，Iteration# 为 x 轴 plot，基本是个下降的弧线才对

	way 2: automatic converence test:
	Decalre convergence if J decreases by less than 10^-3 in one iteration

	Q2:
	if J increases or 先降后升，说明 α 大了
	to choose α，try ..., 0.001, 0.001, 0.1, 1...
	教授的 rule of thumb 是 ...0.001, 0.003, 0.01, 0.03, 0.1, 0.3, 1...
	或者用夹击法：比如 0.01 太小，0.3 太大，那很可能是 0.1

-----

Features and Polynomial [ˌpɒlɪ'nəʊmɪəl] (多项式的) Regression

	Q1: choice of features you have
	Q2: how you can get different learning algorithm by choosing appropriate features
	
	create a new feature by yourself, 比如房屋的两个 feature：长度和宽度，你可以合并成面积这一个 feature

	Polynomial Regression
	针对一个 feature
	比如二次的：h(x) = θ0 + θ1x + θ2x^2
	三次的：h(x) = θ0 + θ1x + θ2x^2 + θ3x^3
	
	我们可以设置 x1 = foo, x2 = foo^2, x3 = foo^3，然后把 univariate Polynomial Regression 转变成 multivariate linear Regression
	但是注意要用 Feature Scaling 处理下
	
	还有一种常见的 model 是 h(x) = θ0 + θ1x + θ2 * sqrt(x)
	
	领悟：你要对各种函数曲线的形状有所了解，根据 sample 的 plot 直观感觉哪种函数可能跟贴合 sample
	
	后续我们会有 algorithm 来 automatically choose features，so don't worry，这一节讲的是一种感官上大致的处理原则
	
-----

Normal Equation

	法(线)方程||normal equation; 在最小二乘法平差中，为求解条件方程组和误差方程组所组成的对称正定的方程组。

	也有翻译成 正规方程

	与 GD 一样，是另一种 linear reg 的算法，但是不像 GD 是 iterative 的，NE 是 solve in one step
	
	Intuition:
	对于二次曲线 J(θ) = aθ^2 + bθ + c
	J' = 2aθ + b，在 x = -b/2a 时取最小值（我们假设 a > 0）

	Normal Equation 的想法就是使所有的 θj 的偏微分都等于 0，然后求解所有的 θj

	矩阵 θ = (X^T * X)^-1 * X^T * Y

	design matrix
	
	Octave: pinv(X'*X)*X'*Y

	NE 不需要做 Feature Scaling

-----

Normal Equation and Noninvertibility

	Q: what if X'*X is non-invertible?
	
	two functions actually
	pinv: pseudo-inverse
	inv: inverse
	
	pinv 可以解决不可逆的问题

	不可逆的原因一般有两种：
	
	1. redundant features (linearly dependent)
		解决方法：合并 feature
	2. too many features (e.g. m <= n)
	m = training example#
	n = feature#
		解决方法：1. delete feature
				  2. use Regularization





	
	
	
	
