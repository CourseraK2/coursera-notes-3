## Classification

	指的是这样一类预测：y ∈ { 0, 1 }
	* 0 代表 negative class
	* 1 代表 positive class
	
	也有 multi-class classification, 比如 y ∈ { 0, 1, 2, 3 }
	
	一般 y ∈ { 0, 1 } 的称为 binary classification
	
	
	如果我们对 classification 的 dataset 做 linear regression，就需要定一个 threshold，比如 0.5
	* if h(x) >= 0.5, predict y=1
	* else predict y=0
	但这样的预测是十分不准确的。linear regression is not a great idea for classification questions. 而且还可能出现 h(x)>1 或者 h(x)<0 的情况
	
	
	对付 classification problem 我们的利器是 Logistic Regression，它可以满足 0<=h(x)<=1
	
-----
	
## Hypothesis Representation

	h(x) 也叫 classifier
	
	对于 linear regression，我们是 h(x) = θ^T*x
	logistic regression 是 h(x) = g(θ^T*x), where g(z) = 1 / (1 + e^(-z))
	
	这个 g(z) 称为 Sigmoid function 或 Logistic function (sigmoid 指 curved like C or S, C形或S形)
	
	h(x) = estimated probability that y=1 on input x 
		 = P(y=1|x,θ), probability that y=1, given x, parameterized by θ
	
	
	P(y=0|x,θ) + P(y=1|x,θ) == 1
	
-----

## Decision Boundary

	g(z) >= 0.5 when z>=0 
			=> θ^T*x >= 0
	g(z) < 0.5 when z<0 
			=> θ^T*x < 0
	
	
	θ^T*x = 0 所表示的直线、曲线或平面称为 Decision Boundary
	
	比如 h(x) = g(-1 + x1^2 + x2^2) 的 Decision Boundary 就是 x1^2 + x2^2 = 1，是一个圆，这就是 Non-linear Decision Boundary 了
	
	
	教授强调了两次 Decision Boundary is not a property of dataset, but a property of paramter θ。dataset 决定 θ，θ 决定 Decision Boundary。
	
-----

## Cost Function

	本节内容：how to choose θ (automatically)
	
	convex 凸的，凸面的
	
	这是一个 supervised learning problem
	
	
	Training Set: {(X1, y1), (X2, y2), ..., (Xm, ym)} => m examples, n features
	
	Xi = | x0 |, x0=1, yi ∈ { 0, 1 }
		 | x1 |
		 | .. |
		 | xn |
	
	
	假如我们定义 cost(h(Xi), yi) = (h(Xi) - yi)^2 / 2，因为 h(Xi) 是有 g(z) 的，J(θ) 曲线是 non-convex 的（有点像 "弧线上有很多锯齿" 的形状），会有很多 local-optiumn。此时按 GD 算法来计算 min(J(θ)) 就不太合适
	
	于是我们把 cost function 换成一个 convex 曲线的：
	
	cost(h(Xi), yi) = -log(h(Xi)) if yi=1
				or  = -log(1-h(Xi)) if yi=0	
	
	if y=1, h(X)=1, then cost=0
	if y=1, h(X)→0, then cost→+∞, 这个非常科学，y=1 的时候我们预测 h(X)→0，cost 肯定是巨大的
	
	
	if y=0, h(X)=0, then cost=0
	if y=0, h(X)→1, then cost→+∞,
	
-----

## Simplified Cost Function and Gradient Descent

	我们把 cost function 的两种情况合并
	
	cost(h(X), y) = -y*log(h(X)) - (1-y)*log(1-h(X))
	
	J(θ) = (1/m) * sum(1:m) { cost(h(Xi), yi) }
		 = -(1/m) * sum(1:m) { yi*log(h(Xi)) + (1-yi)*log(1-h(Xi))}
	
	BTW, this J(θ) can be derived from statistics using the principle of maximum likelihood estimatation, which is an idea from statistics for how to efficiently find parameters data for different models. (not going to be covered in this class)
	
	
	
	我们还是用 GD 来算 min(J(θ))，和 linear regression 的算法完全一致
	
	feature scaling 这里也同样适用

-----
	
## Advanced Optimization
	
	Given θ, J(θ), partial(J(θ)), we can use these optimization algorithms:
	* Gradient Descent
	* Conjugate Gradient
	* BFGS
	* L-BFGS
	
	后三个是 adavanced numerical computing 的内容，这里不 cover。简单说下这三个算法的特点：
	* 优点：
		* No need to manually pick alpha (learning rate)(换言之算法会自己平衡 alpha 的值)
		* Often faster than gradient descent
	* 缺点：
		* More complex
		
	教授的观点是，如果你不是 expert in adavanced numerical computing，就不要自己去实现这三个算法，use a softeware library 就可以了，但是要注意比较不同 library 的 performance
	
	
	
	假设 J(θ) = (θ1 - 5)^2 + (θ2 - 5)^2
	
	function [jVal, gradient] = costFunction(theta) 
	
	jVal = (theta(1)-5)^2 + ... + (theta(2)-5)^2; 
	gradient = zeros(2,1); 
	gradient(1) = 2*(theta(1)-5); 
	gradient(2) = 2*(theta(2)-5);
	
	
	options = optimset('GradObj', 'on', 'MaxIter', '100'); 
	initialTheta = zeros(2,1); 
	[optTheta, functionVal, exitFlag] = fminunc(@costFunction, initialTheta, options);
	
	fminunc: function minimization unconstrained in Octave
	
	'GradObj', 'on': 表示由我自己来提供 gradient 参数
	'MaxIter', '100': 表示 iterate 100 次
	
	help fminunc
	
	
	
	function [jVal, gradient] = costFunction(theta) 
	jVal = [J(θ)]; 
	gradient(1) = [partial(J(θ), θ0)]; 
	gradient(2) = [partial(J(θ), θ1)]; 
	gradient(n+1) = [partial(J(θ), θn)];
	
-----

## Multiclass Classification: One-vs-all
	
	one-ve-all, a.k.a. one-vs-rest
	
	比如一个 3-class 的问题，我们分成 3 个 binary classification 的问题，得到 3 个 Decision Boundary，3 个 h(x)。然后我们用 max(h(x)) 来做预测
	
	有 k 个 class，就可以分成 k 个 binary classification
	
-----

## Q3
	
	Adding new features can only improve the fit on the training set: 
	since setting θ3=θ4=θ5=0 makes the hypothesis the same as the original one, gradient descent will use those features (by making the corresponding θj non-zero) only if doing so improves the training set fit.
	
	
	Adding polynomial features would decrease J(θ): 
	The summation in J(θ) is over examples, not features. Furthermore, the hypothesis will now be more accurate (or at least just as accurate) with new features, so the cost function will decrease.
	
	
	
	