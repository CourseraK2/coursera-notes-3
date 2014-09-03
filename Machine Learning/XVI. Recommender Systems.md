## Problem Formulation

	Why talk about RS?
	* industrial application widely used
	* an occasion where features can be choosen by algorithm itself
	
	以用户打分为例子，0-5 stars
	
	n_u = user#
	n_m = movie#
	r(i,j) = 1 if user j has rated movie i
	y^(i,j) = rating given by user j to movie i (given r(i,j)=1)
	
	RS 就是给用户没看过的电影评分
	
-----
	
## Content Based Recommendations

	以 movie 的 genre 作为 feature，比如：
	* x_1 for romance
	* x_2 for action
	
	照例有 x_0 = 1
	
	若 x^(1) 是一部爱情片，可能有：
	* x^(1)_0 = 1
	* x^(1)_1 = 0.9
	* x^(1)_2 = 0
	
	假设有 n features
	
	For each user j, learn a parameter θ^(j)∈R^(n+1). Predict user j as rating movie i with (θ^(j))^T*x^(i) stars.
	
	
	
	Problem Formulation
	
	r(i,j) = 1 if user j has rated movie (0 otherwise) 
    y^(i,j) = rating by user j on movie i (if r(i,j) = 1)  
    θ^(j) = parameter vector for user j 
    x^(i) = feature vector for movie i 
	
	For user j, movie i, predicted rating: (θ^(j))^T*x^(i)
	
    m^(j) = movie# rated by user j

	To learn θ^(j): min J(θ^(j)) = 
	(1/(2*m^(j))) * sum(for any i that r(i,j)=1) { 
		((θ^(j))^T*x^(i) - y^(i,j))^2 
	} + (λ/(2*m^(j))) * sum(k=1:n) { (θ^(j)_k)^2 }
		
	去掉 m^(j) 这个因子也可以
	
	To learn θ^(1), θ^(2), ..., θ^(n_u): min J(θ^(1), θ^(2), ..., θ^(n_u)) = 
	(1/2)*sum(j=1:n_u) {
		sum(for any i that r(i,j)=1) { 
			((θ^(j))^T*x^(i) - y^(i,j))^2 
		}
	} + (λ/2)*sum(j=1:n_u) {
		sum(k=1:n) { (θ^(j)_k)^2 }
	}
	
	Gradient descent update:
	
	for k = 0 => 
	θ^(j)_k := θ^(j)_k - α*sum(for any i that r(i,j)=1) { 
					((θ^(j))^T*x^(i) - y^(i,j))*x^(i)_k
				}
	
	for k ≠ 0 => 
	θ^(j)_k := θ^(j)_k - α*(
					sum(for any i that r(i,j)=1) { 
						((θ^(j))^T*x^(i) - y^(i,j))*x^(i)_k
					} + λ*θ^(j)_k
				)
	
	这其实就是 variation of linear regression
	
	This is called Content Based Recommendations because we assume that we have available for us, features of the different movies that capture the content.
	
	如果没有这些 features 该怎么办？用 Collaborative Filtering
	
-----

## Collaborative Filtering

	It does "feature learning", which means it is an algorithm that can start to learn for itself what features to use.
	
	如果不能得到 x^(i) = feature vector for movie i，但是用户告诉了我们他自己的 θ^(j) = parameter vector for user j，这时就变成了 "已知 θ 和 y，反推 x"
	
	
	
	Given θ^(1), θ^(2), ..., θ^(n_u), to learn x^(i)
	
	min J(x^(i)) = 
		(1/2)* sum(for any i that r(i,j)=1) { 
			((θ^(j))^T*x^(i) - y^(i,j))^2 
		} + (λ/2)*sum(k=1:n) { 
			(x^(i)_k)^2 }
		}
	
	
	To learn x^(1), ..., x^(n_m):
	
	min J(x^(1), ..., x^(n_m)) = 
		(1/2)*sum(j=1:n_m) {
			sum(for any i that r(i,j)=1) { 
				((θ^(j))^T*x^(i) - y^(i,j))^2 
			}
		} + (λ/2)*sum(i=1:n_m) {
			sum(k=1:n) { (x^(i)_k)^2 }
		}
	
	
	Content Based Recommendations:
	* Given x^(1), ..., x^(n_m) (and y), estimate θ^(1), ..., θ^(n_u)
	
	Collaborative Filtering:
	* Given θ^(1), ..., θ^(n_u) (and y), estimate x^(1), ..., x^(n_m)
	
	一种做法是：Guess θ -> Get x -> Get better θ -> Get better x -> ...，最终 converge
	
	
	叫 Collaborative Filtering 是基于 "多个用户对同一部电影 i 打分（意味着多个 θ^(j)）能帮助确定 x^(i)" 这么一种 sense
	
-----

## Collaborative Filtering Algorithm

	除了前面 Guess θ -> Get x -> Get better θ -> Get better x -> ...，最终 converge 的算法外，还有一种 simultaneously minimizing 的算法：
	
	min J(x^(1), ..., x^(n_m), θ^(1), ..., θ^(n_u)) = 
		(1/2)*sum(for any (i,j) that r(i,j)=1) { 
			((θ^(j))^T*x^(i) - y^(i,j))^2 
		} + (λ/2)*sum(i=1:n_m) {
			sum(k=1:n) { (x^(i)_k)^2 }
		} + (λ/2)*sum(j=1:n_u) {
			sum(k=1:n) { (θ^(j)_k)^2 }
		}
	
	这里不需要 x_0 = 1, 所以也没有 θ_0
	
	
	Collaborative Filtering Algorithm:
	1. initialize x^(1), ..., x^(n_m), θ^(1), ..., θ^(n_u) to small random values
		* This serves as symmetry breaking (similar to the random initialization of a neural network’s parameters) and ensures the algorithm learns features x^(1), ..., x^(n_m) that are different from each other.
	2. minimize J(x^(1), ..., x^(n_m), θ^(1), ..., θ^(n_u)) ising gradient descent
	3. For a user with parameters θ and a movie with (learned) features x, predict a star rating of θ^T*x    
	
	x^(i)_k := x^(i)_k - α*(
					sum(for any j that r(i,j)=1) { 
						((θ^(j))^T*x^(i) - y^(i,j))*θ^(j)_k
					} + λ*x^(i)_k
				)
	
	θ^(j)_k := θ^(j)_k - α*(
					sum(for any i that r(i,j)=1) { 
						((θ^(j))^T*x^(i) - y^(i,j))*x^(i)_k
					} + λ*θ^(j)_k
				)
	
-----

## Vectorization: Low Rank Matrix Factorization

	Y = | y(1, 1) ..... y(1, n_u)|
		| y(2, 1) ......y(2, n_u)|
		| .......................|
		| y(n_m, 1)...y(n_m, n_u)|
	
	X = | --  (x^(1))^T  -- |
		| --  (x^(1))^T  -- |
		| --  .........  -- |
		| -- (x^(n_m))^T -- |
	
	Θ = | --  (θ^(1))^T  -- |
		| --  (θ^(1))^T  -- |
		| --  .........  -- |
		| -- (θ^(n_u))^T -- |
	
	X*Θ^T
	
	这个计算就叫 Low Rank Matrix Factorization：
	* Factorization 是因式分解，这里是矩阵分解的意思
	* rank 指矩阵的秩
	
	
	Another question: how to find movies j related to movie i?
	
	movie i's feture vector => x^(i)
	movie j's feture vector => x^(j)
	
	find, say 5, movies j with the smallest ||x^(i) - x^(j)||
	
-----

## Implementational Detail: Mean Normalization

	问题：如何对一个没有做过 rating 的用户（比如新注册的豆瓣用户）来推荐？这个用户的 θ 是全 0，对任意的电影都是 θ^T*x = 0
	
	答：用 Mean Normalization
	
	Y = Y - rowMeans(Y)
	
	算出 θ 和 x 后，用 (θ^(j))^T*x^(i) + rowMeans(Y) 作为预测的 rating
	
	这样新用户的预测 rating 就直接是 rowMeans(Y) 了
	
-----

## Q2

	In which of the following situations will a collaborative filtering system be the most appropriate learning algorithm (compared to linear or logistic regression)?

	You've written a piece of software that has downloaded news articles from many news websites. In your system, you also keep track of which articles you personally like vs. dislike, and the system also stores away features of these articles (e.g., word counts, name of author). Using this information, you want to build a system to try to find additional new articles that you personally will like. (×)
	
	This system uses predetermined features and has only one user, so it is not a good application of collaborative filtering.
	
	