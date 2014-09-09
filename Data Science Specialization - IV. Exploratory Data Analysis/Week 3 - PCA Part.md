# Part 1

## 原 pattern

set.seed(12345)
par(mar = rep(0.2, 4))
dataMatrix <- matrix(rnorm(400), nrow = 40) ## 40x10
## image(1:10, 1:40, t(dataMatrix)[, nrow(dataMatrix):1])
heatmap(dataMatrix)

## Add a pattern

set.seed(678910)
for (i in 1:40) {
    # flip a coin
    coinFlip <- rbinom(1, size = 1, prob = 0.5)
    # if coin is heads add a common pattern to that row
    if (coinFlip) {
        dataMatrix[i, ] <- dataMatrix[i, ] + rep(c(0, 3), each = 5)
    } ## 50% 的行会有 dataMatrix[i, ] + [0 0 0 0 0 3 3 3 3 3] 的 pattern，heatmap 上左右分明
}

## Patterns now in rows and columns

hh <- hclust(dist(dataMatrix))
dataMatrixOrdered <- dataMatrix[hh$order, ]
par(mfrow = c(1, 3))
image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
plot(rowMeans(dataMatrixOrdered), 40:1, , xlab = "Row Mean", ylab = "Row", pch = 19)
plot(colMeans(dataMatrixOrdered), xlab = "Column", ylab = "Column Mean", pch = 19)

-----

# Part 2

## Components of the SVD - u and v

udv <- svd(scale(dataMatrixOrdered)) ## scale for  feature scaling/mean normalization (centering)
par(mfrow = c(1, 3))
image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
plot(udv$u[, 1], 40:1, , xlab = "Row", ylab = "First left singular vector", 
    pch = 19)
plot(udv$v[, 1], xlab = "Column", ylab = "First right singular vector", pch = 19)

## Components of the SVD - Variance explained

par(mfrow = c(1, 2))
plot(udv$d, xlab = "Column", ylab = "Singular value", pch = 19)
plot(udv$d^2/sum(udv$d^2), xlab = "Column", ylab = "Prop. of variance explained", pch = 19) ## 显示各 variance 占的比例

## Relationship to principal components

udv <- svd(scale(dataMatrixOrdered))
pca1 <- prcomp(dataMatrixOrdered, scale = TRUE)
plot(pca1$rotation[, 1], udv$v[, 1], pch = 19, xlab = "Principal Component 1", ylab = "Right Singular Vector 1")
abline(c(0, 1))

## Components of the SVD - Variance explained 2

constantMatrix <- dataMatrixOrdered*0
for(i in 1:dim(dataMatrixOrdered)[1]){constantMatrix[i,] <- rep(c(0,1),each=5)} ## 新 pattern 新例子
udv <- svd(constantMatrix)
par(mfrow=c(1,3))
image(t(constantMatrix)[,nrow(constantMatrix):1])
plot(udv$d,xlab="Column",ylab="Singular value",pch=19)
plot(udv$d^2/sum(udv$d^2),xlab="Column",ylab="Prop. of variance explained",pch=19)

## What if we add a second pattern?

set.seed(678910)
for (i in 1:40) {
    # flip a coin
    coinFlip1 <- rbinom(1, size = 1, prob = 0.5)
    coinFlip2 <- rbinom(1, size = 1, prob = 0.5)
    # if coin is heads add a common pattern to that row
    if (coinFlip1) {
        dataMatrix[i, ] <- dataMatrix[i, ] + rep(c(0, 5), each = 5)
    } ## shift pattern
    if (coinFlip2) {
        dataMatrix[i, ] <- dataMatrix[i, ] + rep(c(0, 5), 5)
    } ## alternating pattern
}
hh <- hclust(dist(dataMatrix))
dataMatrixOrdered <- dataMatrix[hh$order, ]

## Singular value decomposition - true patterns

svd2 <- svd(scale(dataMatrixOrdered))
par(mfrow = c(1, 3))
image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
plot(rep(c(0, 1), each = 5), pch = 19, xlab = "Column", ylab = "Pattern 1")
plot(rep(c(0, 1), 5), pch = 19, xlab = "Column", ylab = "Pattern 2")

## v and patterns of variance in rows

svd2 <- svd(scale(dataMatrixOrdered))
par(mfrow = c(1, 3))
image(t(dataMatrixOrdered)[, nrow(dataMatrixOrdered):1])
plot(svd2$v[, 1], pch = 19, xlab = "Column", ylab = "First right singular vector")
plot(svd2$v[, 2], pch = 19, xlab = "Column", ylab = "Second right singular vector")

## d and variance explained

udv <- svd(scale(dataMatrixOrdered))
par(mfrow = c(1, 2))
plot(udv$d, xlab = "Column", ylab = "Singular value", pch = 19)
plot(udv$d^2/sum(udv$d^2), xlab = "Column", ylab = "Percent of variance explained", pch = 19)

-----

# Part 3

## Missing values

dataMatrix2 <- dataMatrixOrdered
## Randomly insert some missing data
dataMatrix2[sample(1:100, size = 40, replace = FALSE)] <- NA
udv <- svd(scale(dataMatrix2))  ## Doesn't work! ## Error: infinite or missing values in 'x'

## Imputing {impute}

library(impute)  ## Available from http://bioconductor.org
dataMatrix2 <- dataMatrixOrdered
dataMatrix2[sample(1:100,size=40,replace=FALSE)] <- NA
dataMatrix2 <- impute.knn(dataMatrix2)$data ## k-nearest-neighbors. take the k rows closest to the row with NA, impute the NA with average of the k rows
udv <- svd(scale(dataMatrixOrdered)); svd2 <- svd(scale(dataMatrix2))
par(mfrow=c(1,2)); plot(udv$v[,1],pch=19); plot(svd2$v[,1],pch=19)

## Face example

	load("data/face.rda")
	image(t(faceData)[, nrow(faceData):1])

### Face example - variance explained
	
	udv <- svd(scale(faceData))
	plot(udv$d^2/sum(udv$d^2), pch = 19, xlab = "Singular vector", ylab = "Variance explained")

### Face example - create approximations
	
	udv <- svd(scale(faceData)) ## Note that '%*%' is matrix multiplication
	
	approx1 <- (udv$u[, 1] * udv$d[1]) %*% t(udv$v[, 1]) ## 这里必须加一个括号，不然 'd %*% t(v)' 会先结合 

	# 'diag' is used to make the diagonal matrix out of d
	approx5 <- udv$u[, 1:5] %*% diag(udv$d[1:5]) %*% t(udv$v[, 1:5])
	approx10 <- udv$u[, 1:10] %*% diag(udv$d[1:10]) %*% t(udv$v[, 1:10])

### Face example - plot approximations

	par(mfrow = c(1, 4))
	image(t(approx1)[, nrow(approx1):1], main = "(a)")
	image(t(approx5)[, nrow(approx5):1], main = "(b)")
	image(t(approx10)[, nrow(approx10):1], main = "(c)")
	image(t(faceData)[, nrow(faceData):1], main = "(d)")  ## Original data