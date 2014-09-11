## 05 Variability (The variance)
	
	Densities with a higher variance are more spread out than densities with a lower variance.
	
## 06 Common distributions

	n! 读作 n factorial
	
## 07 Asymptotics [æsɪmp'tɒtɪks] 渐近性（其实就是讲 sample size n->+∞ 时的一些性质）
	
	* The average of a random sample from a population, i.e. \\( bar{X} = \frac{1}{n} \sum\_n {x_i} \\), is itself a random variable
	* \\( E[\bar{X}] = \mu \\)
	* \\( Var(\bar{X}) = \sigma\^2/n \\)
	
	
	
	About what is the probability of getting 45 or fewer heads out 100 flips of a fair coin? (Use the CLT, not the exact binomial calculation).
	
	Bernoulli distribution, \mu = p = 0.5, \sigma\^2 = p*(1-p) = 0.25
	
	bar{X} ~ N(0.5, 0.25/100)
	
	pnorm(0.45, mean=0.5, sd=0.05)
	## [1] 0.1586553

-----

## Q2

	> pnorm(70, mean=80, sd=10)
	[1] 0.1586553

## Q3

	> qnorm(0.95, mean=1100, sd=75)
	[1] 1223.364

## Q4

	> qnorm(0.95, mean=1100, sd=7.5)
	[1] 1112.336

## Q5
	
	> pbinom(3, size=5, prob=0.5, lower.tail = FALSE)
	[1] 0.1875

## Q6

	> pnorm(16, mean=15, sd=1) - pnorm(14, mean=15, sd=1)
	[1] 0.6826895

	实际上就是 mean 周围 1 倍 sd，不用算也知道是 68%
	
## Q7

	E(X) = (1/2)(a+b) = 0.5
	Var = (1/12)(b-a)^2 = 1/12
	
	a + b = 1
	b - a = 1 (b > 1)
	
	a = 0
	b = 1
	
## Q8

	(1/12)/100 开根号
	
## Q9

	> ppois(10, lambda=5*3)
	[1] 0.1184644

## Q10

	sample variance is consistent
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	