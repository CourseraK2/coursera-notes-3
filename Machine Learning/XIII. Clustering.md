## Unsupervised Learning: Introduction

	handling data without label
	
	可以简单理解为没有 class 的一组 points，find some structures among them
	
	最基础的就是 clustering 问题了（可以简单理解为就是归类）
	
-----
	
## K-Means Clustering Algorithm
	
	e.g. a 2-cluster problem
	
	1. ramdomly initialize 2 points called cluster centroids 
	2. cluster assignment: go through each of the examples, depending whether it is closer to a certain centroid, assign the example to this centroid (这里 assign 可以是指 labeling 或者 coloring，和 centroid 标记成一样就可以了)
	3. move centroid: move each centroid to the average position of the points assigned to it
	4. repeat 2 and 3 (util the results do not change anymore)
	
	
	K-Means Clustering Algorithm
	
	Input: 
	* K (number of clusters) (做 clustering 前要预先知道要分多少个 cluster，后续会讲如何确定 K 值)
	* Training set x^(1), ..., x^(m), x^(i)∈R^n (discard the `x_0 = 1` convention) (说没有 lebel，其实就是没有 y，y 就是 lebel)
	
	
	Randomly initialize K cluster centroids μ_1, μ_1, ..., μ_K∈R^n 
	Repeat { 
		for i = 1:m  
			c^(i):= index (from 1 to K) of cluster centroid closest to x^(i) 
		
			i.e. calculate min ||x^(i)-μ_k||^2, assign c^(i) = k which makes the minimum
			
		for k = 1:K  
			μ_k:= average (mean) of points assigned to cluster k 
	} 
	
	
	
	一个问题：如果一开始有一个 centroid 离得超级远该怎么办？不管怎么 repeat 都不会有 example assign 给它……
	
	lecturer 的回答：最常用的是 eliminate it，变成 K-1 clusters……
	
	
	
	K-means 对 non-well-separated clusters 效果也是不错的

-----

## Optimization Objective

	c^(i) = index of cluster centroid (1,2,..K) closest to x^(i); i.e. index of cluster (1,2,..K) to which example x^(i) is currently assigned
	
	μ_k = kth cluster centroid
	
	μ_c^(i) = centroid of the cluster to which example x^(i) is currently assigned，简单说就是 x^(i) 所在的 cluster 的 centroid
	
	
	knowing Optimization Objective of K-means helps:
	1. debug
	2. avoid local optima
	
	
	J(c^(1), ..., c^(m), μ_1, ..., μ_K) = (1/m)*sum(1:m) { ||x^(i)-μ_c^(i)||^2 }
	
	Optimization Objective 就是求 min J
	
	J(c^(1), ..., c^(m), μ_1, ..., μ_K) 又称 distortion function of K-means
	
	
	这么一来，cluster assignment 这一步的数学意义就是：minimize J wrt c^(1), ..., c^(m), holding μ_1, ..., μ_K fixed
	
	move centroid 这一步的数学意义就是：minimize J wrt μ_1, ..., μ_K 
	
-----

## Random Initialization
	
	Random Initialization also helps avoid local optima
	
	Random Initialization 其实有很多中做法，lecturer 说这一种是 recommended: 
	* Should have K < m
	* Randomly pick K training examples.
	* Set μ_1, ..., μ_K equal to these K examples. 
	
	还是那个老问题，可能一开始 centroid 选得就离得远，或者两个 centroid 选到同一个 cluster 里面了，这样对结果就很有影响（i.e. min J 得到 local optima）。进一步说明，不同的 Initialization 会产生不同的 cluster 结果
	
	一般的做法是把 K-means 跑多次，把 min J 最优的一次作为追中结果
	
	K 越大，越有可能 local optima
	
-----

## Choosing the Number of Clusters

	How to choose K?
	
	Elbow method: 简单说就是试 K=2,3,4,...，计算 min J
	
	然后 plot (min J, K)，一般是一个下降的曲线，会有一个类似拐点的点 (elbow)，这个点之前 min J 是极速下降，这个点之后 min J 是缓慢下降，我们就取这个 elbow 对应的 K
	
	但是有时候 elbow 十分不明显（i.e. 曲线很平滑）
	
	所以正确的态度应该是：elbow method 值得一试，但是不是万能的
	
	
	更合适的做法是：结合你应用的实际。比如你把 T-shirt size 粗分，那就是 S/M/L 三类，如果细分，那可能是 XS/S/M/L/XL 五类
	
-----

## Q5

	On every iteration of K-means, the cost function J(c^(1),...,c^(m),μ_1,...,μ_k) (the distortion function) should either stay the same or decrease; in particular, it should not increase.
	
	True. 注意它问的是一次 K-means 计算中，重复两个 steps 时，J 的确是下降的。并不是问跑多次 K-means 时得到的 J
	
	
	
	
	
	