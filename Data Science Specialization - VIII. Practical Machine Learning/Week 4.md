## 1. Regularized regression

	Basic idea：
	* Fit a regression model
	* Penalize (or shrink) large coefficients
	
	Pros:
	* Can help with the bias/variance tradeoff
	* Can help with model selection
	
	Cons:
	* May be computationally demanding on large data sets
	* Does not perform as well as random forests and boosting
	
	
	这一节全是数学
	
-----
	
## 2. Combining predictors

	Key ideas
	* You can combine classifiers by averaging/voting
	* Combining classifiers improves accuracy
	* Combining classifiers reduces interpretability
	* Boosting, bagging, and random forests are variants on this theme
	
	Approaches for combining classifiers
	1. Bagging, boosting, random forests
	* Usually combine similar classifiers
	1. Combining different classifiers
	* Model stacking (discussed in this video)
	* Model ensembling
	
### 2.1 Example with Wage data
	
	library(ISLR); data(Wage); library(ggplot2); library(caret);
	Wage <- subset(Wage,select=-c(logwage))

	# Create a building data set and validation set
	inBuild <- createDataPartition(y=Wage$wage,
								  p=0.7, list=FALSE)
	validation <- Wage[-inBuild,]; buildData <- Wage[inBuild,]

	inTrain <- createDataPartition(y=buildData$wage,
								  p=0.7, list=FALSE)
	training <- buildData[inTrain,]; testing <- buildData[-inTrain,]
	
	mod1 <- train(wage ~.,method="glm",data=training)
	mod2 <- train(wage ~.,method="rf",
				  data=training, 
				  trControl = trainControl(method="cv"),number=3)
	
	pred1 <- predict(mod1,testing)
	pred2 <- predict(mod2,testing)
	qplot(pred1,pred2,colour=wage,data=testing)
	
	## Fit a model that combines predictors
	predDF <- data.frame(pred1,pred2,wage=testing$wage)
	combModFit <- train(wage ~.,method="gam",data=predDF)
	combPred <- predict(combModFit,predDF)
	
	## Testing errors
	sqrt(sum((pred1-testing$wage)^2))
		[1] 827.1
	sqrt(sum((pred2-testing$wage)^2))
		[1] 866.8
	sqrt(sum((combPred-testing$wage)^2))
		[1] 813.9 ## lowest here
	
	## Predict on validation data set
	pred1V <- predict(mod1,validation)
	pred2V <- predict(mod2,validation)
	predVDF <- data.frame(pred1=pred1V,pred2=pred2V)
	combPredV <- predict(combModFit,predVDF)
	
	## Evaluate on validation
	
	sqrt(sum((pred1V-validation$wage)^2))
		[1] 1003
	sqrt(sum((pred2V-validation$wage)^2))
		[1] 1068
	sqrt(sum((combPredV-validation$wage)^2))
		[1] 999.9 ## lowest
	
### 2.2 Notes

	* Even simple blending can be useful
	* Typical model for binary/multiclass data
		* Build an odd number of models
		* Predict with each model
		* Predict the class by majority vote
	* Simple blending in caret: caretEnsemble
	* Wikipedia "ensemlbe learning"
	* Recall: scalability matters
	
-----

## 3. Forecasting

	Q: how to predict Time series data
	
	What is different?
	* Data are dependent over time
	* Specific pattern types
		* Trends - long term increase or decrease
		* Seasonal patterns - patterns related to time of week, month, year, etc.
		* Cycles - patterns that rise and fall periodically
	* Subsampling into training/test is more complicated
	* Similar issues arise in spatial data
		* Dependency between nearby observations
		* Location specific effects
	* Typically goal is to predict one or more observations into the future.
	* All standard predictions can be used (with caution!)
	
	
	Beware spurious correlations! (可能有 confounder)
	Beware extrapolation! In mathematics, extrapolation is the process of estimating, beyond the original observation range, the value of a variable on the basis of its relationship with another variable.
	
### 3.1 Google data

	library(quantmod)
	from.dat <- as.Date("01/01/08", format="%m/%d/%y")
	to.dat <- as.Date("12/31/13", format="%m/%d/%y")
	getSymbols("GOOG", src="google", from = from.dat, to = to.dat)
		[1] "GOOG"
	
	head(GOOG)
				   GOOG.Open GOOG.High GOOG.Low GOOG.Close GOOG.Volume
		2008-01-02     692.9     697.4    677.7      685.2     4306848
		2008-01-03     685.3     686.9    676.5      685.3     3252846
		2008-01-04     679.7     681.0    655.0      657.0     5359834
	
	## Summarize monthly and store as time series
	
	mGoog <- to.monthly(GOOG)
	googOpen <- Op(mGoog)
	ts1 <- ts(googOpen,frequency=12) ## ts for Time Series
	plot(ts1,xlab="Years+1", ylab="GOOG")
	
	
	Time series decomposition
	* Trend - Consistently increasing pattern over time
	* Seasonal - When there is a pattern over a fixed period of time that recurs.
	* Cyclic - When data rises and falls over non fixed periods
	
	## Decompose a time series into parts
	plot(decompose(ts1),xlab="Years+1")
	
	## Training and test sets
	ts1Train <- window(ts1,start=1,end=5)
	ts1Test <- window(ts1,start=5,end=(7-0.01))
	
	## Simple moving average
	plot(ts1Train)
	lines(ma(ts1Train,order=3),col="red")
	
	## Exponential smoothing
	ets1 <- ets(ts1Train,model="MMM")
	fcast <- forecast(ets1)
	plot(fcast); lines(ts1Test,col="red")
	
	## Get the accuracy
	accuracy(fcast,ts1Test)
	
-----

## 4. Unsupervised Prediction

	Key ideas:
	* Sometimes you don't know the labels for prediction
	* To build a predictor
		* Create clusters
		* Name clusters
		* Build predictor for clusters
	* In a new data set
		* Predict clusters
	
### 4.1 Iris example ignoring species labels

	data(iris); library(ggplot2)
	inTrain <- createDataPartition(y=iris$Species,
								  p=0.7, list=FALSE)
	training <- iris[inTrain,]
	testing <- iris[-inTrain,]
		
	## Cluster with k-means
	kMeans1 <- kmeans(subset(training,select=-c(Species)),centers=3)
	training$clusters <- as.factor(kMeans1$cluster)
	qplot(Petal.Width,Petal.Length,colour=clusters,data=training)
	
	## Build predictor
	modFit <- train(clusters ~.,data=subset(training,select=-c(Species)),method="rpart")
	
	## Apply on test
	testClusterPred <- predict(modFit,testing) 
	
### 4.2 Notes
	
	* The cl_predict function in the `clue` package provides similar functionality
	* Beware over-interpretation of clusters!
	
-----

## Q1

	library(ElemStatLearn)
	library(caret)
	data(vowel.train)
	data(vowel.test) 

	vowel.train$y <- as.factor(vowel.train$y)
	vowel.test$y <- as.factor(vowel.test$y)

	set.seed(33833)

	modFit1 <- train(y~.,data=vowel.train,method="rf") 
	modFit2 <- train(y~.,data=vowel.train,method="gbm")

	pred1 <- predict(modFit1,vowel.test)
	pred2 <- predict(modFit2,vowel.test)
	
	confusionMatrix(vowel.test$y,pred1) ## Accuracy : 0.6039 
	confusionMatrix(vowel.test$y,pred2) ## Accuracy : 0.5238
	
	combTest <- data.frame(pred1,pred2,y=vowel.test$y)
	combModFit <- train(y~.,method="rf",data=combTest)
	confusionMatrix(combTest$y,predict(combModFit,combTest)) ## Accuracy : 0.6429  
	
	## combModFit 不要用 video 里的 method="gam"，不然 accuracy 超低
	
## Q2

	library(caret)
	library(gbm)
	
	set.seed(3433)
	
	library(AppliedPredictiveModeling)
	data(AlzheimerDisease)

	adData = data.frame(diagnosis,predictors)
	inTrain = createDataPartition(adData$diagnosis, p = 3/4)[[1]]
	training = adData[ inTrain,]
	testing = adData[-inTrain,]
	
	set.seed(62433)
	
	modFit1 <- train(diagnosis~.,data=training,method="rf") 
	modFit2 <- train(diagnosis~.,data=training,method="gbm")
	modFit3 <- train(diagnosis~.,data=training,method="lda")
	
	pred1 <- predict(modFit1,testing)
	pred2 <- predict(modFit2,testing)
	pred3 <- predict(modFit3,testing)
	
	combTest <- data.frame(pred1,pred2,pred3, diagnosis=testing$diagnosis)
	combModFit <- train(diagnosis~.,data=combTest,method="rf")
	
	combPred <- predict(combModFit,combTest)
	
	confusionMatrix(testing$diagnosis,pred1) ## Accuracy : 0.7683
	confusionMatrix(testing$diagnosis,pred2) ## Accuracy : 0.7927
	confusionMatrix(testing$diagnosis,pred3) ## Accuracy : 0.7683
	confusionMatrix(testing$diagnosis,combPred) ## Accuracy : 0.8049

## Q3

	set.seed(3523)
	library(AppliedPredictiveModeling)
	data(concrete)
	inTrain = createDataPartition(concrete$CompressiveStrength, p = 3/4)[[1]]
	training = concrete[ inTrain,]
	testing = concrete[-inTrain,]
	
	set.seed(233)
	
	modFit <- train(CompressiveStrength~.,data=training,method="lasso") 
	
	plot.enet(modFit$finalModel, xvar=c("penalty"), use.color=TRUE)
	
	## legend('topright', modFit$finalModel$vn, lty=1, col=sample(colors(), 8), bty='n', cex=.75) ## does not work
	
	注意问的是 LAST
	
## Q4

	library(lubridate)  # For year() function below
	dat = read.csv("gaData.csv")
	training = dat[year(dat$date) < 2012,]
	testing = dat[(year(dat$date)) > 2011,]
	tstrain = ts(training$visitsTumblr)
	
	library(forecast)
	modFit <- bats(tstrain)
	length <- dim(testing)[1]
	fcast <- forecast(modFit,h=length,level=c(95))
	
	## str(data.frame(fcast))
	## 'data.frame':   235 obs. of  3 variables:
	##  $ Point.Forecast: num  207 197 236 236 236 ...
	##  $ Lo.95         : num  -300 -333 -296 -297 -298 ...
	##  $ Hi.95         : num  715 728 767 768 769 ...

	pred1 <- data.frame(fcast)[[2]]
	pred2 <- data.frame(fcast)[[3]]
	
	sum((testing$visitsTumblr>pred1)&(testing$visitsTumblr<pred2)) / length
	
	https://class.coursera.org/predmachlearn-005/forum/thread?thread_id=66
	
## Q5

	set.seed(3523)
	library(AppliedPredictiveModeling)
	data(concrete)
	inTrain = createDataPartition(concrete$CompressiveStrength, p = 3/4)[[1]]
	training = concrete[ inTrain,]
	testing = concrete[-inTrain,]
	
	set.seed(325)
	
	library("e1071")
	
	model <- svm(CompressiveStrength ~ ., data = training)
	pred <- predict(model, testing)
	## confusionMatrix(testing$CompressiveStrength,pred) ## won't work 
	
	## root-mean-square deviation (RMSD) == root-mean-square error (RMSE)
	
	error <- pred-testing$CompressiveStrength
	RMSD <- sqrt(sum(error^2)/length(pred))
	
	https://class.coursera.org/predmachlearn-005/forum/thread?thread_id=76
	http://www.jstatsoft.org/v15/i09/paper