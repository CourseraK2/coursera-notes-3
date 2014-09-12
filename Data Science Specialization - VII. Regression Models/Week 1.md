## 01_01_b Basic least squares

	predict children's height using parents' height

	Correction for gender:
	female_parent_height <- male_parent_height * 1.08
	
	n = 928
	mu: some middle value of Y
	yi: height of child i
	
	求 min：sum(1:n){(yi-mu)^2} 
	
	这里还没有开始预测，只是利用 least squares 来求 mu
	
## 01_01_c Least squares continued

	library(manipulate)
	myHist <- function(mu){
		hist(galton$child,col="blue",breaks=100)
		lines(c(mu, mu), c(0, 150),col="red",lwd=5)
		mse <- mean((galton$child - mu)^2)
		text(63, 150, paste("mu = ", mu))
		text(63, 140, paste("MSE = ", round(mse, 2)))
	}
	manipulate(myHist(mu), mu = slider(62, 74, step = 0.5))
	
	## 会出一个调节 mu 的 bar，可以在图上看到不同的 mu 值对应的 mse 值
	
	
	
	The least squares estimate is the empirical mean
	
	ym <- mean(Y)
	sum(1:n){(yi-mu)^2} = 
		sum(1:n){(yi-ym)^2} + sum(1:n){(ym-mu)^2} 
	
	yi-mu = (yi-ym) + (ym-mu)
	两边分别平方，右边的 2ab 项最终为 0
	
	所以 sum(1:n){(yi-mu)^2} >= sum(1:n){(yi-ym)^2}
	
	mu = ym 使其最小
	
## 01_01_d Regression through the origin

	origin: (mathematics) The point at which the axes of a coordinate system intersect, 原点
	
	这里说的是 through-origin linear regression，所以就是简单的 y = beta*x
	
	求 beta 使得 min sum(i=1:n) {Y_i - X_i*beta}
	
	orthogonal：[ɔ:'θɒgənl] 垂直的，正交的
	
	
	
	myPlot <-function(beta){
		y<-galton$child -mean(galton$child)
		x <- galton$parent - mean(galton$parent)
		freqData <- as.data.frame(table(x, y))
		names(freqData) <- c("child", "parent", "freq")
		plot(
			as.numeric(as.vector(freqData$parent)), 
			as.numeric(as.vector(freqData$child)),
			pch = 21, col = "black", bg = "lightblue",
			cex = .15 * freqData$freq, 
			xlab = "parent", 
			ylab = "child"
		)
		abline(0, beta, lwd = 3)
		points(0, 0, cex = 2, pch = 19)
		mse <- mean( (y - beta * x)^2 )
		title(paste("beta = ", beta, "mse = ", round(mse, 3)))
	}
	manipulate(myPlot(beta), beta = slider(0.6, 1.2, step = 0.02))

	
	
	library(UsingR);data(galton)
	lm(I(child - mean(child))~ I(parent - mean(parent)) - 1, data = galton) ## 简单形式就是 lm(y ~ x - 1)
	
	## I() is used to inhibit the interpretation of operators such as "+", "-", "*" and "^" as formula operators, so they are used as arithmetical operators

	output:
	##	Call:
	##		lm(formula = I(child - mean(child)) ~ I(parent - mean(parent)) -  1, data = galton)
	##	Coefficients:
	##		I(parent - mean(parent)) 
	##						   0.646 

	
	Coefficient of xx 一般翻译为 "xx系数" 或者 "xx率"，其实就是 aX 中的 a

	
-----

## 01_02_a Basic Notation and Background

	empirical mean：sum(1:n) {Xi} / n
	
	tilde ~Xi = Xi - mean(X) ## this process is called 'centering' the random variable 
	
## 01_02_b Normalization and Correlation
	
	emprical [ɪmˈpɪrɪkl] variance: 
	S^2 = sum(1:n) {(Xi - bar(X))^2} / (n-1)
		= (sum(1:n) {Xi^2} - n*bar(X)^2) / (n-1)
		
	emprical stardard deviation: S
	The empirical standard deviation is a measure of spread.
	Sometimes people divide by n  rather than n-1 (the latter produces an unbiased estimate.)
	
	
	Xi/S have empirical standard deviation 1. This is called "scaling" the data.
	
	Zi = (Xi - bar(X))/S have empirical mean zero and empirical standard deviation 1.
	The process of centering then scaling the data is called "normalizing" the data.
	Normalized data are centered at 0 and have units equal to standard deviations of the original data.
	Example, a value of 2 form normalized data means that data point was two standard deviations larger than the mean.
	
	
	covariance: 协方差
	
	empirical covariance:
	
	Cov(X, Y) = sum(1:n){(Xi - bar(X))*(Yi - bar(Y))} / (n-1)
			  = (sum(1:n){Xi*Yi} - n*bar(X)*bar(Y)) / (n-1)
	
	Sometimes people divide by n  rather than n-1 (the latter produces an unbiased estimate.)
	
	The correlation is defined as:
	
	Cor(X, Y) = Cov(X, Y) / (Sx*Sy), S is emprical stardard deviation
	
	
	Some facts about correlation:
	* Cor(X, Y) = Cor(Y, X)
	* −1 ≤ Cor(X, Y) ≤ 1
	* Cor(X, Y) = 1 and Cor(X, Y) = −1 only when the X or Y observations fall perfectly on a positive or negative sloped line, respectively
	* Cor(X, Y) measures the strength of the linear relationship between the X and Y data, with stronger relationships as  Cor(X, Y) heads towards ­1 or 1
	* Cor(X, Y) = 0 implies no linear relationship.

-----

## 01_03_a Linear Least Squares

	predict Y = beta0 + beta1*Xi

	least squares 
		= sum(1:n) { (actual - predicted)^2 }
		= sum(1:n) { (Yi - (beta0 + beta1*Xi))^2 }
	
## 01_03_b Linear Least Squares Special Cases

	看 slide
	
## 01_03_c Linear Least Squares Solved

	前面推导部分看 slide
	
	

	Y = beta0 - beta1*X
	
	beta1 = Cor(Y, X)*Sy/Sx
	beta0 = bar(Y) - beta1*bar(X)
	
	The line passes through point (mean(X), mean(Y))
	
	## If you centered the data as: 
	* Xi - bar(X)
	* Yi - bar(Y)
	with R：
	* yc <- y - mean(y)
	* xc <- x - mean(x)
	## then the regression line pass through the origin
	
	注意此时不能叫 regression throught the origin。对于 centered data，我们做 lm(yc~xc) 是 regression through the means of the data，只是恰好过原点（beta0 = 0 是一个恰好的结果）。regression throught the origin 是要求一定要过原点，回归模型一开始就定为了 y = beta1*x （beta0 = 0 是要求）
	
	BTW，此时有一个很巧合的结果，sum(xc*yc)/sum(xc^2) = coef(lm(y ~ x))[2]，注意我们要是在 (xc, yc) 上做原点回归，而 (xc, yc) 它恰好是必过原点的，所以这个 slope 就不谋而合
	
	## If you normalized the data as:
	* (Xi - bar(X)) / Sx
	* (Yi - bar(Y)) / Sy
	with R：
	yn <- (y - mean(y))/sd(y)
	xn <- (x - mean(x))/sd(x)
	## then the slope is Cor(Y, X)
	
	
	
	y <- galton$child
	x <- galton$parent
	beta1 <- cor(y, x) * sd(y) / sd(x)
	beta0 <- mean(y) - beta1 * mean(x)
	rbind(c(beta0, beta1), coef(lm(y ~ x))) ## 与 lm 的结果做比较
		
	原来这三小节就是讲了 lm 的具体算法……

-----

## 01_04_a Regression to the Mean

	指的是这样的一个现象：高个儿的父母，孩子一般也个儿高，但不如父母高；矮个儿的父母，孩子一般也个儿矮，但是又要比父母高一点。有一种 toward to the mean 的趋势
	
	P(Y < X | X = x) get bigger as x heads into the very large values
	P(Y > X | X = x) get bigger as x heads into the very small values
	
## 01_04_b Regression to the Mean Example

	If the correlation is 1 there is no regression to the mean (if father's height perfectly determine's child's height and vice versa)

-----

Q1: sum(x*w)/sum(w). 自己推导下好了，计算关于 mu 的方程的导数，使其为 0 
Q2: lm(y ~ x-1)
Q3: data(mtcars); lm(mtcars$mpg ~ mtcars$wt)
Q4: `The standard deviation of the predictor is one half that of the outcome` 这句的意思是 Sx = 0.5*Sy。然后 beta1 = Cor(Y, X)*Sy/Sx = 2*Cor(Y, X) = 1
Q5: 因为是 normalized，所以 slope = Cor(X, Y) 而且过原点，y = 0.4*x
Q6: xn <- (x - mean(x))/sd(x)
Q7: lm(y ~ m)
Q8: 这是 centered data，过原点
Q9: 其实就是 mean(x)
Q10: (Cor(Y, X)*Sy/Sx) ÷ (Cor(X, Y)*Sx/Sy) = Sy^2 / Sx^2 = Variance-Y / Variance-X
	
	
	
	
	
	