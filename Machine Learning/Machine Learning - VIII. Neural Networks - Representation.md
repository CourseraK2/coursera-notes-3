## Non-linear Hypotheses

	Neural Network 也是一种 ML 的 algorithm
	
	多 feature 的问题，用多项式预测是不明智的，以二项式为例，n 个 feature 可以组合出 n^2/2 种二项式（x1^2, x1*x2, ..., x1*xn; x2^2, x2*x3, ..., x2*xn; ...），一可能 overfitting，二来计算起来过于 expensive
	
	
	然后课中提到了一个 "通过图片像素识别汽车" 的例子，这属于 computer version 的范围
	
	一个 50x50 的图，2500 个 pixels，就是 2500 个 feature
	
	
	对于这样 input feature space (i.e. n) is large 的，complex nonlinear hypotheses。用Neural Network 比较合适  
	
-----
	
## Neurons and the Brain
	
	Neural Network 是一个很老的 algorithm，初衷是通过 ML 来研究 brain
	
	这节课讲 background
	
	Neural Networks:
	* Origins: Algorithms that try to mimic the brain.
	* Was very widely used in 80s and early 90s; popularity diminished in late 90s.
	* Recent resurgence: State-of-the-art technique for many applications
		
-----

## Model Representation I

	Dendrite (树突): input wires
	Axon (轴突): output wires
	Cell body + Nucleus (['nju:klɪəs], 细胞核): processor
	
	
	Dendrite              Axon
	x1 ↘    
	x2 ->    Cell-Body -> h(X) (同 logistic regression 的公式)
	x3 ↗    
	
	画 Neural Network 的时候，有时也把 x0 画进来，x0 称为 "bias unit" 或者 "bias neuron"
	
	
	这里 g(z) 又叫做 Sigmoid (logistic) activation function
	θ 又称为 "weights"
	
	
	Neural Network:
	
	Layer1  Layer2    Layer3
	  x1 -> a1^(2) 
	    ↘↗       ↘
	  x2 -> a2^(2) -> Cell-Body -> h(X)
	    ↗↘       ↗
	  x3 -> a3^(3) 
		
	the first Layer: input layer
	the final Layer: output layer
	the middle layer(s): hidden layer(s) (values you do not observe in the training set)
	
	x1, x2, x3 are input units
	a1^(2), a2^(2), a2^(2) are hidden units
	
	
	ai^(j): "activation" of unit i in layer j
	Θ^(j): matrix of weights controlling function mapping from layer j to layer (j+1)
	
	
	a1^(2) = g(Θ10^(1) * x0 + Θ11^(1) * x1 + Θ12^(1) * x2 + Θ13^(1) * x3)
	a2^(2) = g(Θ20^(1) * x0 + Θ21^(1) * x1 + Θ22^(1) * x2 + Θ23^(1) * x3)
	a3^(2) = g(Θ30^(1) * x0 + Θ31^(1) * x1 + Θ32^(1) * x2 + Θ33^(1) * x3)
	
	h(X) = a1^(3) = g(Θ10^(1) * a0^(2) + Θ11^(1) * a1^(2) + Θ12^(1) * a2^(2) + Θ13^(1) * a3^(2))
	
	Θ^(1) = | Θ10^(1) Θ11^(1) Θ12^(1) Θ13^(1) | => 3x4
			| Θ20^(1) Θ21^(1) Θ22^(1) Θ23^(1) |
			| Θ30^(1) Θ31^(1) Θ32^(1) Θ33^(1) |
	
	If network has Sj units in layer j, Sj+1 1units in layer j+1, then Θ^(j) will be of dimension Sj+1 * (Sj + 1), i.e. 后 layer unit# * (前 layer unit# + 1), 注意这种计算方法里的 +1 就是给 x0 准备的，如果 x0 已经画出来了，那这个 +1 就可以免了
	
-----

## Model Representation II
	
	a1^(2) = g(z1^(2))
	a2^(2) = g(z2^(2))
	a3^(2) = g(z3^(2))
	
	X = | x0 |
		| x1 |
		| x2 |
		| x3 |
		
	z^(2) = | z1^(2) |
			| z2^(2) |
			| z3^(2) |
		  = Θ^(1) * X
	a^(2) = g(z^(2))
	
	我们附加定义
	a^(1) = X => 4x1
	a0^(2) = 1 => 这样 a^(2) 也是个 4x1
	
	这么一来：
	z^(2) = Θ^(1) * a^(1)
	z^(3) = Θ^(2) * a^(2)
	h(X) = a1^(3) = g(z^(3))
	
	总结一下，有点乱……
	
	a^(1) = X = | x0 |
				| x1 |
				| x2 |
				| x3 |
	
	a^(2) = | a0^(2) |
			| a1^(2) |
			| a2^(2) |
			| a3^(2) |
	
	
	
	z^(2) = | z1^(2) |
			| z2^(2) |
			| z3^(2) |
		  = Θ^(1) * X
		  => 3x4 * 4x1 = 3x1
		  
	按 a^(2) = g(z^(2)) 计算的话也只能得到 3x1，这里实际是：
	
	g(z^(2)) = | a1^(2) |
			   | a2^(2) |
			   | a3^(2) |
	
	a0^(2) = 1 是我们自己添的
	
	完整的计算链就是：
	1. a^(1) = X  => 4x1
	2. z^(2) = Θ^(1) * X = Θ^(1) * a^(1)  => 3x1
	3. a^(2) = g(z^(2)) 添上 a0^(2) = 1  => 3x1 变 4x1
	4. z^(3) = Θ^(2) * a^(2)  => 1x1
	5. a^(3) = h(X) = g(z^(3))
	
	这个计算链也叫 forward propagation
	
-----
	
## Examples and Intuitions I
	
	Non-linear classification example: XOR/XNOR/AND, where x1, x2 are binary
	
	以 XOR (异或) 为例：
	x1 = 0, x2 = 0 => y = 0
	x1 = 0, x2 = 1 => y = 1
	x1 = 1, x2 = 0 => y = 1
	x1 = 1, x2 = 1 => y = 0
	
	BTW，你可以发散成 4 块区域：
	x1 = 0, x2 = 0 附近的 training set 都是 y = 0
	x1 = 0, x2 = 1 附近的 training set 都是 y = 1
	x1 = 1, x2 = 0 附近的 training set 都是 y = 1
	x1 = 0, x2 = 0 附近的 training set 都是 y = 0
	
	
	
	教授实际计算的例子是 y = x1 AND x2，一个没有 hidden layer 的 architecture
	
	h(X) = g(-30 + 20x1 + 20x2)
	
	查表可知：g(4) = 0.99, g(-4) = 0.01，所以：
	
	x1 = 0, x2 = 0 => h(X) = g(-30) ≈ 0
	x1 = 0, x2 = 1 => h(X) = g(-10) ≈ 0
	x1 = 1, x2 = 0 => h(X) = g(-10) ≈ 0
	x1 = 1, x2 = 1 => h(X) = g(10) ≈ 1
	
	
	类似地，h(X) = g(-10 + 20x1 + 20x2) 可以预测 y = x1 OR x2
	
-----

## Examples and Intuitions II

	h(X) = g(10 - 20x1) 可以预测 y = NOT x1 
	h(X) = g(10 - 20x1 - 20x2) (NOT x1) AND (NOT x2)
	
	然后 put it together:
	
	x1 -> x1 AND x2             ↘
	 ↗↘                         x1 OR x2 => x1 XNOR x2 
	x2 -> (NOT x1) AND (NOT x2) ↗
	
-----

## Multiclass Classification

	one-vs-all
	
	我们把多个 one-vs-all 组合起来，就可以做 Multiclass Classification 了
	
	比如一个 4-class 的问题，我们原来是 y ∈ {1, 2, 3, 4}，现在要改成 y ∈ {y1, y2, y3, y4}，其中：
	
	y1 = | 1 |
		 | 0 |
		 | 0 |
		 | 0 |
		 
	y2 = | 0 |
		 | 1 |
		 | 0 |
		 | 0 |
	
	依此类推。这时 output layer 就要有 4 个 units（相当于 4 个 flag）

	但是，input layer 不一定也是 4 个 units （因为 class 不等同于 feature）
	
	最后得到的可能是这样一个结果:
	a^(n) = | 0.88 |
			| 0.01 |
			| 0.06 |
			| 0.01 |
		 
	（注意这里并不表示是概率，也没有加起来等于 1 这样规律）然后取 max(a^(n))，得到的 index 是 1，于是就归为 class 1  
	也就是说，这里可能需要额外的一些计算（max, 取 index 等）来得出 h(X) = 1，把 a^(n) 计算出来并不意味着就大功告成了
	
-----

## Q5

	If a neural network is overfitting the data, one solution would be to increase the regularization parameter λ. (√)
	
	A larger value of λ will shrink the magnitude of the parameters Θ, thereby reducing the chance of overfitting the data.
	
	A smaller value of λ allows the model to more closely fit the training data, thereby increasing the chances of overfitting.
	
	这里总结一下：
	λ 过小：overfitting
	λ 过大：underfitting
	已经 overfitting 了：加大 λ
	已经 underfitting 了：减小 λ
	
	
	The outputs of a neural network are not probabilities, so their sum need not be 1.
	
-----

## Programming Assignment 4: Part 2 - One-vs-all classifier training

	for c = 1:num_labels
		[theta] = fmincg (@(t)(lrCostFunction(t, X, (y == c), lambda)), initial_theta, options); % size(theta) = 401x1
		all_theta(c, 1:end) = theta'(:,1:end); % size(all_theta) = 10x401
	endfor

	注意这里 y == c 的妙用  

	我们的 class 是 1, 2, ....., 10，y == n 就得到一个 0-1 矩阵，0 表示此处元素 != n，1 表示此处元素 == n，这样一个 for (n in [1:10]) 就相当于是做了 10 次 binary classfication，然后把训练的来的 theta 拼到一起就可以了。Bravo! 
	
	
	
	
	
	
	
	
	
	
	
	