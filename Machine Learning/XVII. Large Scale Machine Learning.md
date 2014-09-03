## Learning With Large Datasets

	首先会遇到的就是计算量过大的问题
	
	假设我们有 100,000,000 数据，我们可不可以只用 1000 条来 learn？
	
	要考虑 learning curve，过小的 m 会导致 high variance
	
	所以更有效的办法是在计算方法上提高。Scale up the algorithms to large datasets.
	
-----

## Stochastic ([stə'kæstɪk], 随机的) Gradient Descent
	
	以 linear regression 为例。
	
	原有的 gradient desent 又称为 batch gradient desent，batch 指一次性处理 all the training examples at a time
	
	Stochastic Gradient Descent does not process all the training examples in every single iteration, but only one.
	
	我们把原有的 GD 改写一下：
	
	cost(θ, (x^(i), y^(i))) = (1/2) * (h(x^(i)) - y^(i))^2
	
	J_train(θ) = (1/m) * sum(i=1:m) {
		cost(θ, (x^(i), y^(i)))
	}
	
	Stochastic Gradient Descent:
	
	1. randomly shuffle dataset
	2. repeat (1 to 10 times to get the best) {
		for i=1:m {
			θ_j := θ_j - α*(h(x^(i)) - y^(i))*x^(i)_j
		}
	   }
	
	SGD 的 iteration 可能会要多一些，而且不一定能 converge 而是在 global minimum 周围绕圈，不过近似值一般也足够好了
	
-----

## Mini-Batch Gradient Descent
	
	Batch gradient descent: Use all m examples in each iteration 
	Stochastic gradient descent: Use 1 example in each iteration 
	Mini-batch gradient descent: Use b examples in each iteration 
	
	b = mini-batch size, typiclly 2-100, usually == 10
	
	
	
	Say b=10, m=1000:
	
	repeat {
		for i=1,11,21,31,...,991 {
			θ_j := θ_j - α*(1/10)* sum(k=i:(i+9)) {
				(h(x^(k)) - y^(k))*x^(k)_j
			}
		}
	}
	
-----

## Stochastic Gradient Descent Convergence

	Q1: 如何确定 SGD has converged?
	Q2: how to tune the learning rate α?
	
	详见 slide
	
-----

## Online Learning

	Online learning setting allows us to model problems where we have continuous stream of data coming in.
	
	repeat(forever) {
		Get (x,y) from a user
		update θ using (x,y):
			θ_j := θ_j - α*(h(x) - y)*x_j
	}
	
	(x,y) 这个数据不用保存，用完就扔
	
	
	Application: Product search
	
	Scene:
	* User searches for e.g. "Android phone 1080p camera"
	* Have 100 phones in store. Will return 10 results. 
	
	Algorithm:
	* x = features of phone; how many words in user query match name of phone; how many words in query match description of phone, etc. 
	* y = 1, if user clicks on link.
	* learn p(y=1|x;θ) (a.k.a. CTR, click through rate)	
		
	Result：	
	* Use to show user the 10 phones they're most likely to click on. 
		
		
	Other examples: Choosing special offers to show user; customized selection of news articles; product recommendation etc.
	
-----

## Map Reduce and Data Parallelism

	看 slide，基础知识而已
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	