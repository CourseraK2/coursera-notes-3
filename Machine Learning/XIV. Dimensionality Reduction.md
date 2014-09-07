## Motivation I: Data Compression

## Motivation II: Visualization (because we can only plot 2D or 3D)

-----

## Principal Component Analysis: Problem Formulation
	
	Principal Component Analysis (PCA) problem formulation
	
	* Reduce from 2-dimension to 1-dimension: 	
		* Find a direc1on (a vector u^(1)∈R^n) onto which to project the data so as to minimize the projection error (projection error 就是点到直线的距离). 
	* Reduce from n-dimension to k-dimension: 
		* Find k vectors u^(1), u^(2), ..., u^(k) onto which to project the data so as to minimize the projection error.

-----
	
## Principal Component Analysis: Algorithm
	
	Data preprocessing:
	
	Training set: x^(1), ..., x^(m)
	
	Preprocessing (feature scaling/mean normaliza1on): 
	μ_j = (1/m)*sum(i=1:m) { x_j^(i) }
	Replace each `x_j^(i)` with `x_j^(i) - μ_j`. 
	If different features on different scales (e.g. x_1 = size of house, x_2 = number of bedrooms), scale features to have comparable range of values. 
		
	
	
	PCA algorithm
	
	suppose we are reducing data from n-dimensions to k-dimensions
	
	Compute **convariance matrix** (协方差矩阵):
	
	Σ = (1/m)*sum(1:n) { x^(i)*(x^(i))^T }
	
	vecterized Σ = (1/m)*X^T*X
	
	Compute **eigenvectors** (['aɪ(d)gənvektə], 特征向量) of matrix Σ：
	`[U, S, V] = svd(Σ)`, svd for Singular Value Decomposition (奇异值分解). `eig(Σ)` also works but less stable
	
	convariance matrix always sstisfies a property called "symmetric positive semidefinite" (对称半正定矩阵), so `svd` == `eig`
	
	x^(i): nx1
	x^(i)^T: 1xn
	X: mxn
	Σ: nxn
	vecterized Σ: nxn
	U: nxn
	
	U = [u_1 u_2 ... u_n], u_i is nx1
	  = |  /   /       /  |
	  = |  /   /       /  |
	  = | u_1 u_2 ... u_n |
	  = |  /   /       /  |
	  = |  /   /       /  |
	
	we want to reduce to k-dimensions, so pick up the first k columns of U, i.e. u_1, u_2, ..., u_k
	
	U_reduce = [u_1 u_2 ... u_k] % nxk
			 = U(:, 1:k)
	
	z^(i) = (U_reduce)^T*x^(i) % kxn * nx1 = kx1
	
	vecterized Z = X*U_reduce % mxn * nxk = mxk
	
	X = | -- x^(1)T -- |
		| -- x^(2)T -- |
		|      ...     |
		| -- x^(m)T -- |
	
	Z = | -- z^(1)T -- |
		| -- z^(2)T -- |
		|      ...     |
		| -- z^(m)T -- |
	
-----

## Choosing the Number of Principal Components
	
	How to choose k
	
	
	Average squared projection error: 
	(1/m)*sum(1:m) { ||x^(i) - x^(i)_approx||^2 }
	
	Total variation in the data:
	(1/m)*sum(1:m) { ||x^(i)||^2 }
	
	Typically, choose k to be smallest value so that:
	
	Average squared projection error / Total variation in the data <= 0.01, which means "99% of variance is retained"
	
	这只是提出了一个选 k 的标准，至于具体怎么选……还是只有是 k = 1,2,... 一个个的试
	
	不过此时我们利用 `[U, S, V] = svd(Σ)` 的 S 来方便我们的计算，S 是一个 nxn 的 diagonal
	
	S = | s_11               |
		|      s_22          |
		|           ...      |
		|               s_nn |
	
	for a given k, Average squared projection error / Total variation in the data = 1 - (sum(1:k) {s_ii} / sum(1:n) {s_ii})
	
	这样我们只用计算一次 `[U, S, V] = svd(Σ)`，然后在 sum(1:k) {s_ii} / sum(1:n) {s_ii} 这个式子上来试 k = 1,2,...，而不是多次取 U_reduce 再来计算

-----

## Reconstruction from Compressed Representation

	z^(i) = (U_reduce)^T*x^(i)
	
	x^(i)_approx = U_reduce * z^(i) % nxk * kx1 = nx1
	
	而 x^(i)_approx ≈ x^(i)，这就相当于 Reconstruct x^(i) from z^(i)
	
	X_apporx = Z*U_reduce';
	
-----

## Advice for Applying PCA

	PCA can be used to speedup learning algorithm
	
	Most common is the supervised learning speedup by PCA
	
	suppose we have (x^(1), y^(1)), ..., (x^(m), y^(m)) and n=10000 (feature#)
	
	Extract inputs to make a unlabeled dataset x^(1), ..., x^(m)∈R^10000
	
	Apply PCA. say we reduce to 1000 features. so we have z^(1), ..., z^(m)∈R^1000
	
	Then we have a new training set (z^(1), y^(1)), ..., (z^(m), y^(m))
	
	Feed the new training set to a learning algorithm
	
	
	Note: The mapping x^(i) -> z^(i) should be **defined** by running PCA **only** on the training set. But this mapping can be **applied** as well to the examples x_cv^(i) and x_test^(i) in the cross valida1on and test sets
	
	Application of PCA 
	* Compression
		* Reduce memory/disk needed to store data 
		* Speed up learning algorithm 
			=> choose k by xx% of variance retaining
	* Visualiza1on
		=> choose k=2 or k=3
		
	
	Bad use of PCA: To prevent overfitting
	
	Use PCA to reduce the number of features. Thus, fewer features, less likely to overfit. => This is bad use
	
	PCA isn’t a good way to address overfilng. Use regularization instead. 
	
	
	Note: Before implemen1ng PCA, first try running whatever you want to do with the original/raw data x^(i). Only if that doesn’t do what you want, then implement PCA and consider using z^(i). 