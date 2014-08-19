## Hierarchical Clustering (part 1)

	Clustering organizes things that are close into groups
	
	Q:
	* How do we define close?
	* How do we group things?
	* How do we visualize the grouping?
	* How do we interpret the grouping?
	
	Hierarchical clustering: 
	* It is an agglomerative (clustered together but not coherent) approach
		* Find closest two things
		* Put them together
		* Find next closest
	* It requires
		A defined distance
		A merging approach
	* It produces
		* A tree (a.k.a. dendrogram) showing how close things are to each other
	
	How do we define close?
	* Most important step
		* Garbage in -> garbage out
	* Distance or similarity
		* Continuous - euclidean distance (就是几何学的两点间的距离，扩展到 n 维)
		* Continuous - correlation similarity
		* Binary - manhattan distance => abs(x_1 - x_2) + abs(y_1 - y_2) + abs(z_1 - z_2) + ...
	* Pick a distance/similarity that makes sense for your problem
	
## Hierarchical Clustering (part 2)

	set.seed(1234)
	par(mar = c(0, 0, 0, 0))
	x <- rnorm(12, mean = rep(1:3, each = 4), sd = 0.2)
	y <- rnorm(12, mean = rep(c(1, 2, 1), each = 4), sd = 0.2)
	plot(x, y, col = "blue", pch = 19, cex = 2)
	text(x + 0.05, y + 0.05, labels = as.character(1:12)) ## 在点的右上方加 text
	
	
	dataFrame <- data.frame(x = x, y = y)
	dist(dataFrame) ## calculate distance. euclidean by default
	
	
	dataFrame <- data.frame(x = x, y = y)
	distxy <- dist(dataFrame) ## 得到 distance table
	hClustering <- hclust(distxy)
	plot(hClustering) ## 得到 cluster dendrogram
	
	
	如果我们把 within distance of 2 视为 close，可能得到 n 个 group（直观的判断方法就是在 cluster dendrogram 里以 y = 2 水平切割）。
	如果我们把 within distance of 1 视为 close，可能得到 m 个 group。所以这个标准需要你根据实际情况制定 
	
## Hierarchical Clustering (part 3)	
	
	Prettier dendrograms:
	
	myplclust <- function(hclust, lab = hclust$labels, lab.col = rep(1, length(hclust$labels)), hang = 0.1, ...) {
		## modifiction of plclust for plotting hclust objects *in colour*!  Copyright
		## Eva KF Chan 2009 Arguments: hclust: hclust object lab: a character vector
		## of labels of the leaves of the tree lab.col: colour for the labels;
		## NA=default device foreground colour hang: as in hclust & plclust Side
		## effect: A display of hierarchical cluster with coloured leaf labels.
		y <- rep(hclust$height, 2)
		x <- as.numeric(hclust$merge)
		y <- y[which(x < 0)]
		x <- x[which(x < 0)]
		x <- abs(x)
		y <- y[order(x)]
		x <- x[order(x)]
		plot(hclust, labels = FALSE, hang = hang, ...)
		text(x = x, y = y[hclust$order] - (max(hclust$height) * hang), labels = lab[hclust$order], 
			col = lab.col[hclust$order], srt = 90, adj = c(1, 0.5), xpd = NA, ...)
	}
	
	dataFrame <- data.frame(x = x, y = y)
	distxy <- dist(dataFrame)
	hClustering <- hclust(distxy)
	myplclust(hClustering, lab = rep(1:3, each = 4), lab.col = rep(1:3, each = 4))
	
	
	更酷炫的效果见 http://gallery.r-enthusiasts.com/RGraphGallery.php?graph=79
	
	
	Q: when you merge a point together, what represents its new location?
	
	A:
	* Average linkage: x_cluster = sum(1:n) { x_i }/n, y_cluster = sum(1:n) { y_i }/n
	* Complete linkage: if you want to measure the distance between to clusters, take the farthest distance of points from the clusters 
	
	
	Heatmap:
	
	dataFrame <- data.frame(x = x, y = y)
	set.seed(143)
	dataMatrix <- as.matrix(dataFrame)[sample(1:12), ]
	heatmap(dataMatrix)
	
	更多 heatmap 内容请参 http://flowingdata.com/2010/01/21/how-to-make-a-heatmap-a-quick-and-easy-solution/
	
-----
	
## K-Means Clustering (part 1)
	
	K-means clustering
	* It is a partioning approach
		* You need to fix a number of clusters beforehand
		* Get centroids (质心, center of gravity) of each cluster
		* Assign things to closest centroid
		* Reclaculate centroids
	* It requires
		* A defined distance metric
		* A number of clusters
		* An initial guess as to cluster centroids
	* It produces
		* Final estimate of cluster centroids
		* An assignment of each point to clusters
	
	我们还是用原来那个数据集：
	
	set.seed(1234)
	par(mar = c(0, 0, 0, 0))
	x <- rnorm(12, mean = rep(1:3, each = 4), sd = 0.2)
	y <- rnorm(12, mean = rep(c(1, 2, 1), each = 4), sd = 0.2)
	plot(x, y, col = "blue", pch = 19, cex = 2)
	text(x + 0.05, y + 0.05, labels = as.character(1:12)) ## 在点的右上方加 text
	
	
	具体算法参 http://www.csdn.net/article/2012-07-03/2807073-k-means
	
## K-Means Clustering (part 2)

	dataFrame <- data.frame(x, y)
	kmeansObj <- kmeans(dataFrame, centers = 3)
	names(kmeansObj)
	## [1] "cluster"      "centers"      "totss"        "withinss"    
	## [5] "tot.withinss" "betweenss"    "size"         "iter"        
	## [9] "ifault"
	
	kmeansObj$cluster
	##  [1] 3 3 3 3 1 1 1 1 2 2 2 2
	
	
	
	par(mar = rep(0.2, 4))
	plot(x, y, col = kmeansObj$cluster, pch = 19, cex = 2)
	points(kmeansObj$centers, col = 1:3, pch = 3, cex = 3, lwd = 3)
	
	
	set.seed(1234)
	dataMatrix <- as.matrix(dataFrame)[sample(1:12), ]
	kmeansObj2 <- kmeans(dataMatrix, centers = 3)
	par(mfrow = c(1, 2), mar = c(2, 4, 0.1, 0.1))
	image(t(dataMatrix)[, nrow(dataMatrix):1], yaxt = "n")
	image(t(dataMatrix)[, order(kmeansObj$cluster)], yaxt = "n")
	
	
	K-means is not deterministic
	* Different # of clusters causes different # of iterations
	
	Hierarchical Clustering is deterministic
	
-----

## Dimension Reduction (part 1)

	slide 的标题是 Principal Components Analysis and Singular Value Decomposition
	
	
	这里上来是自己构造了一个例子：一个看不出有什么 pattern 的数据集，我们人为加了一列，随机赋成 0-1，结果这 0-1 pattern 成了最明显的 pattern。反过来说，这一列 0-1 最好地描述了整个数据集，其余的列对 pattern 可以说是毫无贡献。由此引出我们的问题：
	
	
	You have multivariate variables X1,...,Xn，其中 X1=(X11,…,X1m)...
	
	Q1: Find a new set of X1,...,Xn that are uncorrelated and explain as much variance as possible.
	Q2: If you put all the variables together in one matrix, find the best matrix created with fewer variables (lower rank) that explains the original data.
	
	The first goal is *statistical* and the second goal is *data compression*.
	
	PCA solves Q1  
	SVD solves Q2
	
	
	SVD:

	If X is a matrix with each variable in a column and each observation in a row then the SVD is a "matrix decomposition" that decomposes the original matrix into 3 separate matrices as

	X=UDV^T
	
	where the columns of U (left singular vectors) are orthogonal , the columns of V (right singular vectors)  are orthogonal and D (singular values) is a diagonal matrix .

	PCA:

	The principal components are equal to the right singular values V if you first scale (subtract the mean, divide by the standard deviation) the variables.
		
## Dimension Reduction (part 2)

	Components of the SVD - u and v

	svd1 <- svd(scale(dataMatrixOrdered))
	par(mfrow = c(1, 3))
	image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
	plot(svd1$u[, 1], 40:1, , xlab = "Row", ylab = "First left singular vector", pch = 19)
	plot(svd1$v[, 1], xlab = "Column", ylab = "First right singular vector", pch = 19)
	
	
	
	Components of the SVD - Variance explained
	
	par(mfrow = c(1, 2))
	plot(svd1$d, xlab = "Column", ylab = "Singular value", pch = 19)
	plot(svd1$d^2/sum(svd1$d^2), xlab = "Column", ylab = "Prop. of variance explained", pch = 19)
	
	
	
	Relationship to principal components
	
	svd1 <- svd(scale(dataMatrixOrdered))
	pca1 <- prcomp(dataMatrixOrdered, scale = TRUE)
	plot(pca1$rotation[, 1], svd1$v[, 1], pch = 19, xlab = "Principal Component 1", ylab = "Right Singular Vector 1")
	abline(c(0, 1))
	
	
	Components of the SVD - variance explained
	
	constantMatrix <- dataMatrixOrdered*0
	for(i in 1:dim(dataMatrixOrdered)[1]) {constantMatrix[i,] <- rep(c(0,1),each=5)}
	svd1 <- svd(constantMatrix)
	par(mfrow=c(1,3))
	image(t(constantMatrix)[,nrow(constantMatrix):1])
	plot(svd1$d,xlab="Column",ylab="Singular value",pch=19)
	plot(svd1$d^2/sum(svd1$d^2),xlab="Column",ylab="Prop. of variance explained",pch=19)
	
## Dimension Reduction (part 3)
	
	handle Missing values
	
	这三篇处理的问题我们清楚了，但是 slide 里数学和 code 的细节太多了，建议先搞清楚数学模型，再参照 slide 里的 code 来试验。这里就不记录 code 了。
	
	最后的那个 face example 很值得看下，了解下具体应用是怎样的。
	
	Alternatives: 
	* Factor analysis
	* Independent components analysis
	* Latent semantic analysis
	
-----

## Working with Color in R Plots (part 1)
	
## Working with Color in R Plots (part 2)
	
	The grDevices package has two functions
	* colorRamp
	* colorRampPalette
	* These functions take palettes of colors and help to interpolate between the colors

	The function colors() lists the names of colors you can use in any plotting function
	
## Working with Color in R Plots (part 3)	
	
	这一节的 video 比 slide 有用。轻松愉快
	
	x <- rnorm(10000)
	y <- rnorm(10000)
	smoothScatter(x, y) ## 密集的地方不会是密密麻麻的重叠的点，而是像菌落样的一团
	
## Working with Color in R Plots (part 4)
	
	直接看最后的 summary 写总结好了~
	
	
	
	
	
	
	
	
	
	
	
	
	