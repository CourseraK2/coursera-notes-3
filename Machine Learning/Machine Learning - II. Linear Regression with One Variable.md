Model Representation

	Supervised: 
	Regression Problem: To predict real-valued output
	
	Training Set
	
	m => number of training examples
	x => input variable / features
	y => output variable / target variable
	(x, y) => a single training example
	(x^(i), y^(i)) => ith training example
	
	input(x) | output(y)
	2104	 | 460
	1416	 | 232
	1534	 | 315
	
	h => hypothesis
	
	hθ(x) = θ0 + θ1x: linear regression with one variable / univariate linear regression
	
-----
	
Cost Function
	
	Idea: Choose θ0 and θ1 so that h(x) is close to y for training examples (x, y)
	
	Cost Function: J(θ0, θ1) = (2m)^-1 * Sum(m; i=1) (h(xi)-yi)^2
	
	求 (θ0, θ1) 使 J(θ0, θ1) 最小
	
-----

Cost Function - Intuition I

	
		
Cost Function - Intuition II		
		
	contour 轮廓；等高线	
	contour plot 等高线图 	
		
-----

Gradient Descent 梯度下降		
	
	gradient: 
		A slope or incline.
		A rate of inclination or declination of a slope.
		(calculus) Of a function y = f(x) or the graph of such a function, the rate of change of y with respect to x, that is, the amount by which y changes for a certain (often unit) change in x.
		
	Gradient Descent 是用来计算 min(J) 的：
		Start with (θ0, θ1)
		Keep changing (θ0, θ1) to reduce J(θ0, θ1) util we hopefully end up at minimum
		
	local optimum
		
	Is it a search algorithm?
	
	a:=b 赋值
	a=b equals
	
	\theta_{j} := \theta_{j} - \alpha\frac{\partial}{\partial \theta_{j}}J(\theta_{0}, \theta_{1})
	
	![](http://www.sciweavers.org/tex2img.php?eq=%5Ctheta_%7Bj%7D%20%3A%3D%20%5Ctheta_%7Bj%7D%20-%20%5Calpha%5Cfrac%7B%5Cpartial%7D%7B%5Cpartial%20%5Ctheta_%7Bj%7D%7DJ%28%5Ctheta_%7B0%7D%2C%20%5Ctheta_%7B1%7D%29&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)
		
	repeat this assignment until convergence (聚集, 收敛)
	
	divergence 发散
		
	\alpha	learning rate; how big you step is

	Simultaneous [ˌsɪml'teɪniəs] (同步的) update
	
		temp0 := \theta_{0} - \alpha\frac{\partial}{\partial \theta_{0}}J(\theta_{0}, \theta_{1})
		temp1 := \theta_{1} - \alpha\frac{\partial}{\partial \theta_{1}}J(\theta_{0}, \theta_{1})	
			
		θ0 := temp0
		θ1 := temp1
		
		注意步骤，不能把 θ0 更新了再代入公式去更新 θ1
		
	Partial derivative [diˈrivətiv] 偏导数
	
	比如 f(x, y)=x^2 + xy + y^2，我们只关心 x 的单独变化所引起的 f 变化率，所以 ∂f/∂x = 2x + y，我们把 y 当做一个常数；同理有 ∂f/∂y = 2y + x
	
-----

Gradient Descent Intuition	
	
	R real number 的集合
	
-----

Gradient Descent For Linear Regression
	
	convex 中凸的；凸圆的；凸面的；凸状；凸面体；凸透镜
	
	convex function
	
	Bach Gradient Descent: each step of descent uses all the training examples
	
	J(θ0, θ1) = (2m)^-1 * Sum(m; i=1) (h(xi)-yi)^2
	
	这里我们的 sum 是 1~m，就是用了全部的 training set
	
-----

	The cost function J(θ0,θ1) for linear regression has no local optima (other than the global minimum), so gradient descent will not get stuck at a bad local minimum.
	
	因为二维的话是一个 convex，必定有最低点
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	






