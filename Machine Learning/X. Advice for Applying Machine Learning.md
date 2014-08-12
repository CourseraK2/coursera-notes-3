## Deciding What to Try Next

	Suppose you have implemented regularized linear regression to predict housing prices.
	
	However, when you test your hypothesis on a new set of houses, you ﬁnd that  it makes unacceptably large errors in its predictions. What should you try next?  

	* Get more training examples (并不是越多越好，后面后 cover 到)
	* Try smaller sets of features (需要 carefully selecting)
	* Try getting additional features 
	* Try adding polynomial features (x_1^2, x_2^2, x_1x_2, etc)
	* Try decreasing lambda
	* Try increasing lambda
	
	但是我们要如何判断：到底要采取以上哪些措施呢？这任意一个措施，肯能都会带来昂贵的计算或者数据收集工作。此时我们需要：
	
	Machine learning diagnostic: A test that you can run to gain insight what is/isn’t working with a learning algorithm, and gain guidance as to how best to improve its performance. 

	Diagnostics can take time to implement, but doing so can be a very good use of your time.
	
-----
	
## Evaluating a Hypothesis
	
	min training error may mean overfitting
	
	如何判断是 overfitting？当 feature > 3 时是很难用 plot 来判断的
	
	此时可以用类似 cross validation 的处理方式：
	
	Training/testing procedure for linear regression: 
	* Learn parameter θ from training data (minimizing training error J(θ)) 
	* Compute test set error 

	Training/testing procedure for logistic regression: 
	* Learn parameter θ from training data (minimizing training error J(θ)) 
	* Compute test set error 
	* Or use the Misclassification Error metric
	
	err(h(x), y) = {
		1, if h(x) >= 0.5, y = 0
			or h(x) < 0.5, y = 1
		0, otherwise
	}
	
	TestError = (1/m_test) * sum(1:m_test) {
		error(h(x_i), y_i)
	}
	
-----

## Model Selection and Train/Validation/Test Sets

	1. h(X) = θ_0 + θ_1x ## linear function; d=1
	2. h(X) = θ_0 + θ_1x + θ_2x^2 ## quadratic function; d=2
	3.  h(X) = θ_0 + θ_1x + ... + θ_3x^3 ## cubic function; d=3
	...
	10. h(X) = θ_0 + θ_1x + ... + θ_10x^10 ## 10th-order polynomial; d=10
	
	d = degree of polynomial
	
	Training error: J_train(θ)
	Cross Validation error: J_cv(θ)
	Test error: J_test(θ)
	
	对上述 10 个 model，我们计算 J_cv(θ)，挑选 min J_cv(θ) 的 model
	
	然后对这个选中的 model 计算 J_test(θ) 来 estimate generalization error
	
-----

## Diagnosing Bias vs. Variance

	如果我们以 d 为 x-axis，error 值为 y-axis，那么 J_train(θ) 是逐渐下降的，J_cv(θ) 和 J_test(θ) 是先下降再上升（最低点的 d 最 fit，underfitting 和 overfitting 的 error 都很高）
	
	Suppose your learning algorithm is performing less well than you were hoping (i.e. J_cv(θ) or J_test(θ) is high). Is it a bias problem or a variance problem?
	
	* bias problem means underfitting, d too small
	* variance problem means overfitting, d too large
	
	* if J_train is high and J_cv is close to J_train (or both high), it is bias problem
	* if J_train is low but J_cv is high (J_cv >> J_train), it is variance problem
	
-----

## Regularization and Bias/Variance

	* large lambda (e.g. 10000), θ_1 ≈ θ_2 ... ≈ 0, h(x) ≈ θ_0 => high bias (underfitting)
	* small lambda (e.g. → 0) => high variance (overfitting)
	
	how can we automatically choose lambda?
	
	1. try lambda = 0
	2. try lambda = 0.01
	3. try lambda = 0.02
	4. try lambda = 0.04
	...
	12. try lambda = 10.24
	
	和前面一样，选择 J_cv 最小的 lambda，然后计算 J_test 来 estimate generalization error。
	
	以 lambda 为 x-axis，error 为 y-axis：
	* 随着 lambda ↑，J_train ↑ （从 overfitting 到 underfitting）
	* 随着 lambda ↑，J_cv 先下降再上升
	
-----
	
## Learning Curves

	对一个已经选定的 model，以 m (training set size) 为 x-axis，error 为 y-axis 
	
	当 m 很小时，J_train 也很小（比如只有1个 example 时，可以完美 fitting）
	
	随着 m 增加，J_train 也增加
	
	当 m 很小时，J_cv 会很大（因为 underfitting）
	
	随着 m 增加，J_cv 减小（越来越 fitting）
	
	
	如果一个 model has high bias (underfitting)，随着 m 增加，J_cv 减小并趋平，J_train 增大也趋平，并且迫近 J_cv。所以 bias problem 的特点是 J_cv and J_train are both high
	
	If a learning algorithm is suffering from high bias, getting more training data  will not (by itself) help much
	
	
	如果一个 model has high variance (overfitting)，随着 m 增加，J_cv slightly or smoothly decrease，J_train slightly or smoothly increase。there is a large gap between J_cv and J_train，所以 bias problem 的特点是 J_cv is high while J_train is low
	
	If a learning algorithm is suffering from high variance, getting more training data is likely to help
	
-----

## Deciding What to Do Next - Revisited
	
	* Get more training examples => fix high variance
	* Try smaller sets of features (需要 carefully selecting) => fix high variance
	* Try getting additional features => fix high bias
	* Try adding polynomial features (x_1^2, x_2^2, x_1x_2, etc) => fix high bias
	* Try decreasing lambda => fix high bias
	* Try increasing lambda => fix high variance
	
	when it comes to neural networks:
	
	"Small" neural network:
	* fewer parameters
	* more prone to underfitting
	* Computationally cheaper
	
	"Large" neural network:  
	* more parameters
	* more prone to overfitting 
	* Computationally more expensive
	* Use regularization (lambda) to address overfitting.
	
	可以以 l (layer#) 为 x-axis，error 为 y-axis 来研究究竟多少 layer 比较合适
	
	
	
	
	
	
	
	
	
	