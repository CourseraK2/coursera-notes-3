## The Problem of Overfitting

	underfitting => algorithm has "high bias" or "strong preconception (成见)"
	overfitting => algorithm has "high variance"
	
	
	
	Overfitting: If we have too many features, the learned hypothesis may fit the training set very well (J(θ) ≈ 0), but fail to generalize to new examples (predict prices on new examples).
	
	Here "generalize" means how well a hypothesis applies to new examples 
	
	
	
	Addressing (address 有 to deal with 的意思) overfitting:
	
	1. Reduce number of features.
	* Manually select which features to keep.
	* Model selection algorithm (later in course).
	2. Regularization.
	* Keep all the features, but reduce magnitude/values
	 of parameters.
	* Works well when we have a lot of features, each of 
	which contributes a bit to predicting .
	
-----

## Cost Function

	Small values for parameters θ0, θ1, ..., θn makes:
	* "Simpler" hypothesis
	* Less prone to overfitting
	
	J(θ) = (1/2m) * (
				sum(1:m) { (h(Xi) - yi)^2 } + 
				λsum(1:n) { θj^2 }
			)
	
	注意后面一项 θj^2 的 sum 是从 θ1 到 θn，不包括 θ0
	
	λ 称为 regularization paramater，作用是：control the trade-off between 2 different goals
	* fit the training set well
	* keep parameters θ0, θ1, ..., θn small。因为我们要使 J(θ) 最小，这样的一个 J(θ) 定义不然不允许 θ1, ..., θn 很大
	
	
	if λ is too large, say λ=10^10, algorithm results in underfitting (falis to fit even the training set), 因为这种情况下，θ1, ..., θn 趋近于 0，h(X) 趋近于 θ0
	
-----

## Regularized Linear Regression

#### Gradient Descent

	θ0 := θ0 - α*(1/m) * sum(1:m) { (h(Xi) - yi)*x0i } 
	θj := θj - α*(
			(1/m) * sum(1:m) { (h(Xi) - yi)*xji } + 
			(λ/m) * θj
		)
		= θj*(1-αλ/m) - α*(1/m) * sum(1:m) { (h(Xi) - yi)*xji }
	
	因为 1-αλ/m < 1，所以现在这个 GD 算法是在 shrinking θj (j=1,2,...,n)
	
#### Normal Equation

	原来是 θ = (X^T * X)^-1 * X^T * Y
	Regularize 之后是：
	
	θ = (X^T * X + λ * I)^-1 * X^T * Y
	I = | 0 0 0 0 0 | => (n+1)x(n+1)
		| 0 1 0 0 0 |
		| 0 0 1 0 0 |
		| 0 0 0 1 0 |
		| 0 0 0 0 1 |
	
	X^T * X + λ * I 一定是 invertable 的
	
-----

## Regularized Logistic Regression
	
	原来是 J(θ) = -(1/m) * sum(1:m) { yi*log(h(Xi)) + (1-yi)*log(1-h(Xi))}
	现在是 J(θ) = -(1/m) * sum(1:m) { yi*log(h(Xi)) + (1-yi)*log(1-h(Xi))} + (λ/2m)*sum(1:n) { θj^2 } （注意不包括 θ0）
	
-----

## Q3

	Introducing regularization to the model always results in equal or better performance on examples not in the training set. (×)
	
	A: If we introduce too much regularization, we can underfit the training set and this can lead to worse performance even for examples not in the training set.
	
	
	
	Adding more features might result in a model that overfits the training set, and thus can lead to worse performs for examples which are not in the training set.
	
	Adding many new features gives us more expressive models which are able to better fit our training set. If too many new features are added, this can lead to overfitting of the training set.
	
	
	
	
	