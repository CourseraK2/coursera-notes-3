## Cost Function

	m = example# or observation#
	L = layer# in network
	s_l = unit# (bias unit not included) in layer l
	s_L = unit# of the output layer
	K = class#
	
	if K == 2, it is binary classification. y = 0 or 1 (y ∈ R, R for real number), s_L = 1
	
	其实你也可以看成 y ∈ R^2，一个是 [0; 1]，一个是 [1; 0]
	
	if K >= 3, it is multi-class classification. Use one-vs-all, y ∈ R^K (K-dimensional vector), s_L = K
	
	
	h(X) ∈ R^K, (h(X))_i = ith output
	
	J(Θ) = (-1/m) * sum(i = 1:m) {
				sum(k = 1:K) {
					y_k^(i) * log((h(Xi))_k + 
					(1 - y_k^(i)) * log(1 - (h(Xi))_k
				}
			} + (λ/2m) * sum(l = 1:L-1) {
				sum(i = 1:s_l) {
					sum(j = 1:s_l + 1) {
						(Θ_ji^(l))^2
					}
				}
			}
	
-----

## Backpropagation Algorithm

	forward propagation 就是前面那个计算链
	
	1. a^(1) = X 
	2. z^(2) = Θ^(1) * X = Θ^(1) * a^(1)
	3. a^(2) = g(z^(2)) 添上 a0^(2) = 1  
	4. z^(3) = Θ^(2) * a^(2)
	5. a^(3) = g(z^(3)) ....
	6. ....
	7. a^(L) = h(X)
	
	Backpropagation 是：
	
	假设 δ_j^(l) = error of node j in layer l
	
	在本节这个 L = 4 的例子中：
	
	δ_j^(4) = a_j^(4) - y_j
			= (h(X))_j - y_j
	
	推到 matrix 的情况就是：
	
	δ^(4) = a^(4) - y
		  = h(X) - y
	
	δ^(3) = (Θ^(3))^T * δ^(4) .* g'(z^(3)), 这里 g' 是 g 的导数，g'(z^(3)) = a^(3) .* (1 - a^(3))
	δ^(2) = (Θ^(2))^T * δ^(3) .* g'(z^(2))
	
	layer 1 不存在 error，所以没有 δ^(1)
	
	
	partial of J(Θ) on Θ_ij^(l) = a_j^(l) * δ_i^(l+1)
	
	有了 partial 就可以算 function [J, grad] 的 grad 了（上一课 assignment 里的 lrCostFunction 实际是 logistic regression 的 [J, grad]），参 https://d396qusza40orc.cloudfront.net/ml/docs/slides/Lecture6.pdf
	
	algorithm 太长，看 slides
	
-----

## Backpropagation Intuition

	反向计算时不需要考虑 +1 项
	
	请总结 slides
	
-----

## Implementation Detail: Unrolling Parameters	
	
	我们的 theta 是 matrix，因为每一层都做了 n 次 one-vs-all
	
	所以需要把 matrix unroll 成 vector 来适应 fminunc 的参数要求
	
	
	假设我们有一个 3-layer 的 network，s1 = s2 = 10, s3 = 1
	
	第一层 Θ^(1) = Theta1 ∈ R^10x11
	第二层 Θ^(2) = Theta2 ∈ R^10x11
	第三层 Θ^(3) = Theta3 ∈ R^1x11
	
	thetaVec = [Theta1(:); Theta2(:); Theta3(:)]; % Theta1(:) 得到一个 110x1，所以这三项竖排得到一个 231x1
	
	从 thetaVec 还原：
	
	Theta1 = reshape(thetaVec(1:110),10,11); 
	Theta2 = reshape(thetaVec(111:220),10,11); 
	Theta3 = reshape(thetaVec(221:231),1,11);
	
	
	所以算法就进一步明晰成：
	
	1. Have initial parameters Θ^(1), Θ^(2), Θ^(3)
	2. Unroll to get `initialTheta` to pass to `fminunc(@costFunction, initialTheta, options)`
	
	function [J, gradientVec] = costFunction(thetaVec)
		1. from thetaVec, get Θ^(1), Θ^(2), Θ^(3)
		2. use FP/BP to computer D^(1), D^(2), D^(3) and J(Θ)
		3. Unroll D^(1), D^(2), D^(3) to get gradientVec
	
-----

## Gradient Checking
	
	是一种检测 FP/BP 实现是否正确的手段（对其他的算法也适用）
	
	
	J(Θ) 曲线上，(θ, J(θ)) 点切线的斜率（也就是导数），假定我们取 θ-ε、θ+ε 两点，那这个斜率就近似:
	(J(θ+ε) - J(θ-ε))/(θ+ε - (θ-ε)) = 
	(J(θ+ε) - J(θ-ε))/2ε
	
	常用 ε = 10^-4，可以得到一个约等于导数的值
	
	也有用 (J(θ+ε) - J(θ))/ε 来算的，称为 one-side difference；我们用的是 two-side difference
	
	
	In Octave:
	
	gradApprox = (J(theta + EPSILON) – J(theta – EPSILON))/(2*EPSILON) 
	
	假设 θ is unrolled version of Θ^(1), Θ^(2), Θ^(3)
	
	partial of J(θ) on θ1 ≈ (J(θ1+ε, θ2, ..., θn) - J(θ1-ε, θ2, ..., θn))/2ε
	partial of J(θ) on θ2 ≈ (J(θ1, θ2+ε, ..., θn) - J(θ1, θ2-ε, ..., θn))/2ε
	......
	partial of J(θ) on θn ≈ (J(θ1, θ2, ..., θn+ε) - J(θ1, θ2, ..., θn-ε))/2ε
	
	
	In Octave:
	
	for i = 1:n, 
		thetaPlus = theta; 
		thetaPlus(i) = thetaPlus(i) + EPSILON; 
		thetaMinus = theta; 
		thetaMinus(i) = thetaMinus(i) – EPSILON; 
		gradApprox(i) = (J(thetaPlus) – J(thetaMinus))/(2*EPSILON); 
	end; 

	Check that gradApprox≈DVec 
	
	
	
	Implementation Note: 
	- Implement backprop to compute DVec (unrolled D^(1), D^(2), D^(3)). 
	- Implement numerical gradient check to compute `gradApprox`. 
	- Make sure they give similar values. 
	- Turn off gradient checking. Using backprop code for learning. 

	Important: 
	- Be sure to disable your gradient checking code before training  your classiﬁer. If you run numerical gradient computation on every iteration of gradient descent (or in the inner loop of costFunction(…)), your code will be very slow.
	
-----

## Random Initialization

	Set initialTheta = zero(n, 1)? May be good for logistic regression but not neural network
	
	设置成全 0 也不好，会导致 next layer 值的同化
	
	
	Random initialization: Symmetry (对称) breaking 
	
	Initialize each θ_ij^(l) to a random value in [-ε, ε], i.e. -ε <= θ_ij^(l) <= ε
 
	In Octave:
	
	Theta1 = rand(10,11)*(2*INIT_EPSILON) - INIT_EPSILON; % rand(10, 11) 产生一个随机的 10x11, 值在 [0, 1] 内
 
	Theta2 = rand(1,11)*(2*INIT_EPSILON) - INIT_EPSILON;
	
	这里的 ε 和 Gradient Checking 的 ε 没有关系
	 
-----

## Putting It Together
	
	First of all:
	
	Pick a network architecture (connectivity pattern between neurons)
	
	input unit#: Dimension of features x^(i)
	output unit#: Number of classes 
	Reasonable default: 
	* 1 hidden layer
	* or if >1 hidden layer, use same hidden unit# in every layer (usually the more the better) 
	
	(more content in later classes)
	
	
	
	Steps of training
	
	1. Randomly initialize weights θ
	2. Implement forward propaga5on to get h(x^(i)) for any x^(i)  
	3. Implement code to compute cost function J(Θ)
	4. Implement backprop to compute partial derivatives of J(Θ) on Θ_jk^(l)

	
	for i = 1:m 
		Perform forward propagation and backpropagation using example (x^(i), y(i)) 
		Get activations a^(l) and delta terms δ^(l) for l = 2,...,L 
		Δ^(l) := Δ^(l) + δ^(l+1)*(a^(l))'
	end
	
	compute partial derivatives of J(Θ) on Θ_jk^(l)
	
	5. Use gradient checking to comparepartial derivatives of J(Θ) on Θ_jk^(l) computed using backpropagation vs. using numerical estimate of gradient of J(Θ). Then disable gradient checking code. 
	6. Use gradient descent or advanced optimization method with backpropagation to try to minimize J(Θ) as a function of parameters Θ
	
	
	
	The cost function for a neural network is non-convex, so it may have multiple minima. Which minimum you find with gradient descent depends on the initialization.
	
-----

## Autonomous Driving

	[ɔ:ˈtɒnəməs] 自治的； 有自主权的
	
	无人驾驶科普时间
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	