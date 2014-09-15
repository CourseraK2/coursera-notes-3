## 1. Predicting with trees
	
	Key ideas:
	* Iteratively split variables into groups
	* Evaluate "homogeneity" within each group
	* Split again if necessary
	
	Pros:
	* Easy to interpret
	* Better performance in nonlinear settings
	
	Cons:
	* Without pruning/cross-validation can lead to overfitting
	* Harder to estimate uncertainty
	* Results may be variable
	
	
	
	Basic algorithm
	1. Start with all variables in one group
	1. Find the variable/split that best separates the outcomes
	1. Divide the data into two groups ("leaves") on that split ("node")
	1. Within each split, find the best variable/split that separates the outcomes
	1. Continue until the groups are too small or sufficiently "pure"
	
	
	
	Measures of impurity 看 slide
	
	
	
### 1.1 Example: Iris Data
	
	data(iris); library(ggplot2)
	table(iris$Species)
	
		setosa versicolor  virginica 
        50         50         50 
	
### 1.2 Create training and test sets
	
	inTrain <- createDataPartition(y=iris$Species,p=0.7, list=FALSE)
	training <- iris[inTrain,]
	testing <- iris[-inTrain,]

### 1.3 Iris petal widths/sepal width

	qplot(Petal.Width,Sepal.Width,colour=Species,data=training)
	
	library(caret)
	modFit <- train(Species ~ .,method="rpart",data=training)
	print(modFit$finalModel)
	
		n= 105 

		node), split, n, loss, yval, (yprob)
			  * denotes terminal node

		1) root 105 70 setosa (0.3333 0.3333 0.3333)  
		  2) Petal.Length< 2.45 35  0 setosa (1.0000 0.0000 0.0000) *
		  3) Petal.Length>=2.45 70 35 versicolor (0.0000 0.5000 0.5000)  
			6) Petal.Length< 4.75 31  0 versicolor (0.0000 1.0000 0.0000) *
			7) Petal.Length>=4.75 39  4 virginica (0.0000 0.1026 0.8974) *
	
### 1.4 Plot tree

	plot(modFit$finalModel, uniform=TRUE, 
      main="Classification Tree")
	text(modFit$finalModel, use.n=TRUE, all=TRUE, cex=.8)
	
### 1.5 Prettier plots

	library(rattle)	
	fancyRpartPlot(modFit$finalModel)
	
### 1.6 Predicting new values

	predict(modFit,newdata=testing)
	
### 1.7 Notes

	* Classification trees are non-linear models
		* They use interactions between variables
		* Data transformations may be less important (monotone transformations)
		* Trees can also be used for regression problems (continuous outcome)
	* Note that there are multiple tree building options in R both in the caret package - `method="party"`, `method="rpart"` and out of the caret package - `tree`
	
-----

## 2. Bagging

	Bagging = bootstrap aggregating
	
	Basic idea:
	* Resample cases and recalculate predictions
	* Average or majority vote
	
	Notes:
	* Similar bias
	* Reduced variance
	* More useful for non-linear functions
	
### 2.1 Example: Bagged loess
	
	library(ElemStatLearn); data(ozone,package="ElemStatLearn")
	ozone <- ozone[order(ozone$ozone),]
	
	ll <- matrix(NA,nrow=10,ncol=155)
	for(i in 1:10) {
		ss <- sample(1:dim(ozone)[1],replace=T)
		ozone0 <- ozone[ss,]; ozone0 <- ozone0[order(ozone0$ozone),]
		loess0 <- loess(temperature ~ ozone,data=ozone0,span=0.2)
		ll[i,] <- predict(loess0,newdata=data.frame(ozone=1:155))
	}
	
	plot(ozone$ozone,ozone$temperature,pch=19,cex=0.5)
	for(i in 1:10) {
		lines(1:155,ll[i,],col="grey",lwd=2)
	}
	lines(1:155,apply(ll,2,mean),col="red",lwd=2)
	
### 2.2 Bagging in caret

	* Some models perform bagging for you, in `train` function consider `method` options
		* bagEarth
		* treebag
		* bagFDA
	* Alternatively you can bag any model you choose using the `bag` function
	
	
	
	predictors = data.frame(ozone=ozone$ozone)
	temperature = ozone$temperature ## outcome
	treebag <- bag(predictors, temperature, B = 10, ## replication with 10 sub samples
                bagControl = bagControl(fit = ctreeBag$fit,
                                        predict = ctreeBag$pred,
                                        aggregate = ctreeBag$aggregate))
	
	see [bag.default {caret}](http://www.inside-r.org/packages/cran/caret/docs/nbBag)
	
	
	plot(ozone$ozone,temperature,col='lightgrey',pch=19) 
	points(ozone$ozone,predict(treebag$fits[[1]]$fit,predictors),pch=19,col="red") ## 只 plot 第一个 sample 的结果
	points(ozone$ozone,predict(treebag,predictors),pch=19,col="blue") ## 显示 aggregated 的结果
	
	
	ctreeBag$fit
	ctreeBag$pred
	ctreeBag$aggregate
	三个函数
	
### 2.3 Notes
	
	* Bagging is most useful for nonlinear models
	* Often used with trees - an extension is random forests
	* Several models use bagging in caret's train function
	
-----

## 3. Random Forests
	
	Basic idea:
	1. Bootstrap samples
	1. At each split, bootstrap variables
	1. Grow multiple trees and vote
	
	Pros:
	* Accuracy
	
	Cons:
	* Speed
	* Interpretability
	* Overfitting
		
### 3.1 Example: Iris

	data(iris); library(ggplot2)
	inTrain <- createDataPartition(y=iris$Species,
								  p=0.7, list=FALSE)
	training <- iris[inTrain,]
	testing <- iris[-inTrain,]
	
	library(caret)
	modFit <- train(Species~ .,data=training,method="rf",prox=TRUE) ## "rf" for Random Forests
	modFit
	
	getTree(modFit$finalModel,k=2) ## Getting a single tree
	
	irisP <- classCenter(training[,c(3,4)], training$Species, modFit$finalModel$prox)
	irisP <- as.data.frame(irisP); irisP$Species <- rownames(irisP)
	p <- qplot(Petal.Width, Petal.Length, col=Species,data=training) ## 圆点是原数据
	p + geom_point(aes(x=Petal.Width,y=Petal.Length,col=Species),size=5,shape=4,data=irisP) ## × plot 出来有点 cluster center 的意味
	
	
	
	pred <- predict(modFit,testing); ## Predicting new values
	
	
	testing$predRight <- pred==testing$Species
	table(pred,testing$Species)
	
			pred         setosa versicolor virginica
		      setosa         15          0         0
		      versicolor      0         14         1
		      virginica       0          1        14
			  
	可见有两个 mis-classified
	
	## 画出 mis-classified 的两个点
	qplot(Petal.Width,Petal.Length,colour=predRight,data=testing,main="newdata Predictions")
	
### 3.2 Note

	* Random forests are usually one of the two top performing algorithms along with boosting in prediction contests.
	* Random forests are difficult to interpret but often very accurate.
	* Care should be taken to avoid overfitting (see rfcv funtion)

-----

## 4. Boosting

	Basic idea:
	* Take lots of (possibly) weak predictors
	* Weight them and add them up
	* Get a stronger predictor
	
	most common: Adaboost
	
	看 slide
	
-----

## 5. Model Based Prediction
	
	Basic idea:
	1. Assume the data follow a probabilistic model
	1. Use Bayes' theorem to identify optimal classifiers (就是 P(B|A) 那个公式)
	
	Pros:
	* Can take advantage of structure of the data
	* May be computationally convenient
	* Are reasonably accurate on real problems
	
	Cons:
	* Make additional assumptions about the data
	* When the model is incorrect you may get reduced accuracy
	
### 5.1 Model based approach
	
	看 slide，数学内容
	
### 5.2 Classifying using the model

	A range of models use this approach

	* Linear discriminant analysis assumes fk(x) is multivariate Gaussian with same covariances
	* Quadratic discrimant analysis assumes fk(x) is multivariate Gaussian with different covariances
	* Model based prediction assumes more complicated versions for the covariance matrix
	* Naive Bayes assumes independence between features for model building
	
### 5.3 Why linear discriminant analysis?

	还是数学内容
	
### 5.4 Discriminant function

	还是数学
	
### 5.5 Naive Bayes

	ditto
	
### 5.6 Example: Iris data

	data(iris); library(ggplot2)
	
	inTrain <- createDataPartition(y=iris$Species,
                              p=0.7, list=FALSE)
	training <- iris[inTrain,]
	testing <- iris[-inTrain,]
	
	modlda = train(Species ~ .,data=training,method="lda") ## linear discriminant analysis
	modnb = train(Species ~ ., data=training,method="nb") ## Naive Bayes
	plda = predict(modlda,testing); pnb = predict(modnb,testing)
	table(plda,pnb)
	
	equalPredictions = (plda==pnb)
	qplot(Petal.Width,Sepal.Width,colour=equalPredictions,data=testing)
	
-----

## Q1

	library(AppliedPredictiveModeling)
	data(segmentationOriginal) ## 2019x119
	library(caret)

	tr <- segmentationOriginal[segmentationOriginal$Case=="Train",] ## 1009x119
	te <- segmentationOriginal[segmentationOriginal$Case=="Test",] ## 1010x119

	set.seed(125)
	
	## CART for Classification And Regression Trees
	
	modFit <- train(Class ~ .,method="rpart",data=tr)
	
	## 注意 TotalIntench2 是 modFit 自己的新 feature，原 dataset 没有，所以无法构造数据来预测
	## ste <- te[1:4,]
	## ste[1,]$TotalIntench2 = 23000 
	## ste[1,]$FiberWidthCh1 = 10
	## ste[1,]$PerimStatusCh1 = 2 
	## ste[2,]$TotalIntench2 = 50000 
	## ste[2,]$FiberWidthCh1 = 10
	## ste[2,]$VarIntenCh4 = 100
	## ste[3,]$TotalIntench2 = 57000
	## ste[3,]$FiberWidthCh1 = 8
	## ste[3,]$VarIntenCh4 = 100
	## ste[4,]$FiberWidthCh1 = 8
	## ste[4,]$VarIntenCh4 = 100
	## ste[4,]$PerimStatusCh1 = 2 
	
	## 老实点目测好了……
	
	library(rattle)	
	fancyRpartPlot(modFit$finalModel)
	
## Q2

	In statistics, the bias (or bias function) of an estimator is the difference between this estimator's expected value and the true value of the parameter being estimated.
	
	其实就是 ML 的 bias/variance 的概念，翻下 Ng 的笔记看看？
	
	K↓ size(Test)↑ 
	
	If K in a K-fold cross validation is small:
	* the bias in the estimate of out-of-sample (test set) accuracy is larger
	* the variance in the estimate of out-of-sample (test set) accuracy is smaller
	
## Q3

	library(pgmm)
	data(olive)
	olive = olive[,-1]
	
	library(caret)
	modFit <- train(Area ~ .,method="rpart",data=olive)
	
	predict(modFit,newdata=as.data.frame(t(colMeans(olive))))

## Q4

	library(ElemStatLearn)
	data(SAheart)
	set.seed(8484)
	train = sample(1:dim(SAheart)[1],size=dim(SAheart)[1]/2,replace=F)
	trainSA = SAheart[train,]
	testSA = SAheart[-train,]
	
	set.seed(13234)
	
	modFit <- train(chd ~ age+alcohol+obesity+tobacco+typea+ldl,method="glm",family="binomial",data=trainSA)
	
	missClass = function(values,prediction){sum(((prediction > 0.5)*1) != values)/length(values)}
	
	missClass(trainSA$chd, predict(modFit,newdata=trainSA))
	##	[1] 0.2727273
	missClass(testSA$chd, predict(modFit,newdata=testSA))
	##	[1] 0.3116883

## Q5

	library(ElemStatLearn)
	data(vowel.train)
	data(vowel.test) 
	
	vowel.train$y <- as.factor(vowel.train$y)
	vowel.test$y <- as.factor(vowel.test$y)
	
	## set.seed(33833)
	set.seed(125) ## forum 说应该用 125，用 33833 的话结果与4个选项都不符
	
	modFit <- train(y~ .,data=vowel.train,method="rf",prox=TRUE) ## "rf" for Random Forests
	
	varImp(modFit)

	
	
	
	