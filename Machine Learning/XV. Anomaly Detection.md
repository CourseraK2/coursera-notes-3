## Problem Motivation
	
	已知正常数据的 dataset，现在有一条新的 记录，如何判断它是正常的还是 anomaly？
	
	直观来看，如果 plot 出来，新 point 和原来的 dataset 比较接近，感觉就应该是正常。如果是个 outlier，感觉就应该是 anomaly
	
	
	
	Density estimation
	
	Given {x^(1), ..., x^(m)}, is x_test anomalous?
	
	if p(x_test) < ε => flag x_test anomaly
	if p(x_test) >= ε => OK
	
	
	应用有：
	* Fraud detection: 把网站用户的使用习惯（比如 typing speed）作为 features，检测是否被盗号
	* Manufacturing: 检测不合格产品
	* Monitoring computers in a data center
		* x_1 = memeory use
		* x_2 = CPU load
		* etc

-----

## Gaussian (Normal) Distribution		
	
	Say X∈R. If X is a distributed Gaussian with mean μ, variance σ^2 (standard variance is σ) note X~N(μ, σ^2). Tilde means "distributed as"
	
	
	p(x; μ, σ^2) = 1/(sqrt(2π)*σ)*exp(-(x-μ)^2/(2σ^2))
	
	x=μ 是钟形曲线的中轴线
	σ 是钟形曲线的宽
	
	σ 越小，钟形曲线越窄，也越高
	σ 越大，钟形曲线越宽，也越矮（因为面积都是1）
	
	
	假设 X^(i)~N(μ, σ^2)，根据 dataset 来算 (μ, σ^2)：
	
	μ = (1/m)*sum(1:m) {x^(i)}
	σ^2 = (1/m)*sum(1:m) { (x^(i) - μ)^2 }
	
-----

## Algorithm

	Density estimation
	
	Given {x^(1), ..., x^(m)}, x^(i)∈R^n
	
	p(x) = p(x_1; μ_1, σ_1^2)*p(x_2; μ_2, σ_2^2)*...*p(x_n; μ_n, σ_n^2)
		 = product(1:n) { p(x_j; μ_j, σ_j^2) }
	
	μ_j = (1/m)*sum(i=1:m) {x^(i)_j} % j-th column 整列的 mean
	σ_j^2 = (1/m)*sum(i=1:m) { (x^(i)_j - μ_j)^2 }
	
-----

## Developing and Evaluating an Anomaly Detection System

	Assume we have some labeled data, of anomalous and non-anomalous examples. (y = 0 if normal, y = 1 if anomalous). 

	Training set (unlabeled): x^(1), x^(2), ..., x^(m)
	CV set (labeled): (x_cv^(1), y_cv^(1)), ..., (x_cv^(m_cv), y_cv^(m_cv))
	Test set (labeled): (x_test^(1), y_test^(1)), ..., (x_test^(m_test), y_test^(m_test))
	
	
	
	Classification accuracy is not a good way to measure the algorithm's performance because of skewed classes (so always predicting y = 0 will also have high accuracy)
	
	Possible evaluation metrics: 
	* True positive, false positive, false negative, true negative 
	* Precision/Recall 
	* F1-score 
	
	
	
	Can also use cross validation set to choose parameter ε
	
-----

## Anomaly Detection vs. Supervised Learning

	AD:
	* few positive examples
	* large number of negative examples
	* Many different "types" of anomalies. Hard for any algorithm to learn from positive examples what the anomalies look like; future anomalies may look nothing like any of the anomalous examples we’ve seen so far.
	
	SL:
	* large number of both positive and negative examples
	* Enough positive examples for algorithm to get a sense of what positive examples are like; future positive examples likely to be similar to ones in training set.
	
-----

## Choosing What Features to Use

	hist every feature:
	* 基本符合高斯分布的就用
	* 如果不符合：
		* 试试 log(x_j)
		* 试试 sqrt(x_j)
		* 试试 (x_j)^t, t=1/3, ..., 0.1, 0.05 etc.
		
	也可以先从少量的 feature 做起，看没有检出的 anomaly 有啥特点，能否 inspire 出新的 feature
	
	还可以拿现有的 feature 组合，比如正常情况下，server 的 CPU load 和 network traffic 正相关，那我就可以用 (CPU load)/(network traffic) 或者 (CPU load)^2/(network traffic) 来表示 "非 network traffic 引起的 CPU load 过高"
	
----

## Multivariate Gaussian Distribution (Optional)

	比如 x_1 和 x_2 都符合 Gaussian Distribution，它们两个做一个三维 plot 的话，概率在同一切面圆弧上是相同的，但可能实际情况是正常的 example 只在半边圆弧或者某条弦上分布，另外半边上是 anomaly 但是 p(x) 相同
	
	此时就要把 x_1 和 x_2 联合起来求 GD
	
	given x∈R^n（把 x 当做一个 example，有 n 个 feature，值分别是 x_1, x_2, ..., x_n），之前我们是求 p(x) = p(x_1)*...*p(x_n)，现在则是：
	
	μ∈R^n, Σ∈R^(n*n) (covariance matrix)
	
	p(x; μ, Σ) = 1/((2π)^(n/2)*|Σ|^(1/2))*exp(-(1/2)*(x-μ)^T*Σ^(-1)*(x-μ))
	
	|Σ| = determinant of Σ (矩阵的行列式，det(sigma))
	
	Σ 除了可以控制山峰高度和斜度外，还可以控制切面的形状（由圆形变椭圆），还可以控制切面中轴线的方向
	
-----

## Anomaly Detection using the Multivariate Gaussian Distribution (Optional)

	Given {x^(1), ..., x^(m)}, x^(i)∈R^n
	
	μ = (1/m)*sum(1:m) {x^(i)}
	Σ = (1/m)*sum(1:m) { (x^(i) - μ)*(x^(i) - μ)^T }
	
	原来的 p(x) = p(x_1; μ_1, σ_1^2)*p(x_2; μ_2, σ_2^2)*...*p(x_n; μ_n, σ_n^2) 其实可以看做一类特殊的 Multivariate Gaussian Distribution：它的 contour 一定是 axis-aligned (原模型的切面也可以是椭圆)
	
	对应的有：
	
	Σ = | σ_1^2                 |
		|       σ_2^2           |
		|             ...       |
		|                 σ_n^2 |
	
	
	原有模型
	* 需要你自己来组合 feature（比如前面那个 CPU load 的例子）
	* computationally cheaper
	* OK even if small m
	
	MGD 模型
	* automatically captures correlations between features
	* computationally expensive
	* must have m>n or Σ is non-invertible
		* lecturer 的 rule of thumb 是 m >= 10n，这样既保证效果，也可以控制 n 不要过大，否则 nxn 的矩阵运算会十分昂贵
	
	
	Σ is non-invertible in two cases:
	* m<=n as just mentioned
	* redundant features as:
		* duplicated x_i
		* some x_i == x_j by chance
		* x_i == x_j + x_k (这种情况下应该干掉 x_i)
	
-----

## Q1

	Given an image of a face, determine whether or not it is the face of a particular famous individual.(×)
	
	This problem is more suited to traditional supervised learning, as you want both famous and non-famous images in the training set.
	这个题目的关键是：样本的情况。如果是专门给这个 celebrity 的图片库，用 Anomaly Detection 也是可以的。
	
	
	
	
	