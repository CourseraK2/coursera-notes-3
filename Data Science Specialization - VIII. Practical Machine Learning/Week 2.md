## 1. Caret package

### 1.1 SPAM Example: Data splitting

	library(caret); library(kernlab); data(spam)
	inTrain <- createDataPartition(y=spam$type, p=0.75, list=FALSE) ## 75% training, 25% testing
	training <- spam[inTrain,]
	testing <- spam[-inTrain,]
	dim(training)
	
		[1] 3451   58
	
### 1.2 SPAM Example: Fit a model

	set.seed(32343)
	modelFit <- train(type ~.,data=training, method="glm")
	modelFit
	
		Generalized Linear Model 

		3451 samples
		  57 predictors
		   2 classes: 'nonspam', 'spam' 

		No pre-processing
		Resampling: Bootstrapped (25 reps) 

		Summary of sample sizes: 3451, 3451, 3451, 3451, 3451, 3451, ... 

		Resampling results
		
			Accuracy  Kappa  Accuracy SD  Kappa SD
			0.9       0.8    0.02         0.04  
	
### 1.3 SPAM Example: Final model

	modelFit <- train(type ~.,data=training, method="glm")
	modelFit$finalModel
	
		Call:  NULL

		Coefficients:
			  (Intercept)               make            address                all              num3d  
				-1.78e+00          -7.76e-01          -1.39e-01           3.68e-02           1.94e+00 
			  ……
			  ……
	
### 1.4 SPAM Example: Prediction	
	
	predictions <- predict(modelFit,newdata=testing)
	predictions
	
		 [1] spam    spam    spam    nonspam nonspam   
		 [6] spam    spam    spam    spam    spam   
		 ……
		 ……
	
### 1.5 SPAM Example: Confusion Matrix

	confusionMatrix(predictions,testing$type)
	
		Confusion Matrix and Statistics

				  Reference
		Prediction nonspam spam
		   nonspam     665   54
		   spam         32  399

					  Accuracy : 0.925        
						95% CI : (0.908, 0.94) ## CI for Accuracy
		   No Information Rate : 0.606        
		   P-Value [Acc > NIR] : <2e-16       

						 Kappa : 0.842        
		Mcnemar's Test P-Value : 0.0235       

				   Sensitivity : 0.954          
				   Specificity : 0.881
				Pos Pred Value : 0.925
	
-----

## 2. Data slicing

### 2.1 SPAM Example: Data splitting

	library(caret); library(kernlab); data(spam)
	inTrain <- createDataPartition(y=spam$type, p=0.75, list=FALSE) ## 75% training, 25% testing
	training <- spam[inTrain,]
	testing <- spam[-inTrain,]
	dim(training)
	
		[1] 3451   58
	
### 2.2 SPAM Example: K-fold

	set.seed(32323)
	folds <- createFolds(y=spam$type,k=10,list=TRUE,returnTrain=TRUE)
	sapply(folds,length)
	
		Fold01 Fold02 Fold03 Fold04 Fold05 Fold06 Fold07 Fold08 Fold09 Fold10 
		  4141   4140   4141   4142   4140   4142   4141   4141   4140   4141 

	
	folds[[1]][1:10]
 
		[1]  1  2  3  4  5  6  7  8  9 10
	
### 2.3 SPAM Example: Return test

	set.seed(32323)
	folds <- createFolds(y=spam$type,k=10,list=TRUE,returnTrain=FALSE)

	sapply(folds,length)
	
		Fold01 Fold02 Fold03 Fold04 Fold05 Fold06 Fold07 Fold08 Fold09 Fold10 
		   460    461    460    459    461    459    460    460    461    460 

	folds[[1]][1:10]
 
		[1] 24 27 32 40 41 43 55 58 63 68
	
### 2.4 SPAM Example: Resampling

	set.seed(32323)
	folds <- createResample(y=spam$type,times=10,list=TRUE)

	sapply(folds,length)
	
		Resample01 Resample02 Resample03 Resample04 Resample05 Resample06 Resample07 Resample08 Resample09 
			  4601       4601       4601       4601       4601       4601       4601       4601       4601 
		Resample10 
			  4601 
		
	folds[[1]][1:10]
		
		[1]  1  2  3  3  3  5  5  7  8 12
	
### 2.5 SPAM Example: Time Slices

	set.seed(32323)
	tme <- 1:1000
	folds <- createTimeSlices(y=tme,initialWindow=20,horizon=10)
	
	names(folds)
	
		[1] "train" "test" 
	
	folds$train[[1]]
	
		[1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
	
	folds$test[[1]]
		
		……
	
-----

## 3. Training options

	args(train.default)
	
		function (x, y, method = "rf", preProcess = NULL, ..., weights = NULL, 
			metric = ifelse(is.factor(y), "Accuracy", "RMSE"), 
			maximize = ifelse(metric == "RMSE", FALSE, TRUE), 
			trControl = trainControl(), tuneGrid = NULL, tuneLength = 3) 
		NULL
		
	args(trainControl)
	
		function (method = "boot", number = ifelse(method %in% c("cv", "repeatedcv"), 10, 25), 
			repeats = ifelse(method %in% c("cv", "repeatedcv"), 1, number), p = 0.75, 
			initialWindow = NULL, horizon = 1, fixedWindow = TRUE, verboseIter = FALSE, 
			returnData = TRUE, returnResamp = "final", savePredictions = FALSE, 
			classProbs = FALSE, summaryFunction = defaultSummary, selectionFunction = "best", 
			custom = NULL, preProcOptions = list(thresh = 0.95, ICAcomp = 3, k = 5), 
			index = NULL, indexOut = NULL, timingSamps = 0, 
			predictionBounds = rep(FALSE, 2), seeds = NA, allowParallel = TRUE) 
		NULL
	
### 3.1 Metric options

	Continous outcomes:
	* RMSE = Root mean squared error
	* RSquared = R2 from regression models
	
	Categorical outcomes:
	* Accuracy = Fraction correct
	* Kappa = A measure of concordance
	
### 3.2 trainControl resampling

	method
	* boot = bootstrapping
	* boot632 = bootstrapping with adjustment
	* cv = cross validation
	* repeatedcv = repeated cross validation
	* LOOCV = leave one out cross validation
	
	number
	* For boot/cross validation
	* Number of subsamples to take
	
	repeats
	* Number of times to repeate subsampling
	* If big this can slow things down
	
### 3.3 Setting the seed

	* It is often useful to set an overall seed
	* You can also set a seed for each resample
	* Seeding each resample is useful for parallel fits
	
-----

## 4. Plotting predictors

	caret 的 feature plot 其实是个对称的，比如 y~(x_1, x_2, x_3)，那么这个图就是个 4x4 的 grid，分别是：
	
	y~x_1    y~x_2   y~x_3   y~y
	x_3~x_1  ……              x_3~y
	x_2~x_1  ……              x_2~y
	x_1~x_1  ……              x_1~y
	
	
	
	cutWage <- cut2(training$wage,g=3)
	
	p1 <- qplot(cutWage,age, data=training,fill=cutWage, geom=c("boxplot")) ## Stacked boxplot
	
	p2 <- qplot(cutWage,age, data=training,fill=cutWage, geom=c("boxplot","jitter")) ## Stacked boxplot with points overlayed

	grid.arrange(p1,p2,ncol=2) ## 把 p1 p2 按 1x2 排列
	
	
	
	t1 <- table(cutWage,training$jobclass)
	t1
	
		cutWage         1. Industrial 2. Information
		  [ 20.1, 91.7)           437            267
		  [ 91.7,118.9)           365            360
		  [118.9,318.3]           263            410
	
	prop.table(t1,1) ## 1 表示计算 row-wise proportion
	
		cutWage         1. Industrial 2. Information
		  [ 20.1, 91.7)        0.6207         0.3793
		  [ 91.7,118.9)        0.5034         0.4966
		  [118.9,318.3]        0.3908         0.6092
	
	
	
	qplot(wage,colour=education,data=training,geom="density") ## Density plots
	
	
	
	Notes: 
	
	* Make your plots only in the training set
		** Don't use the test set for exploration!
	* Things you should be looking for
		* Imbalance in outcomes/predictors
		* Outliers
		* Groups of points not explained by a predictor
		* Skewed variables
	
	
	这一篇都是 EDA 的内容，看下 slide 里的效果就好了~
	
----

## 5. Basic preprocessing

	mean(training$capitalAve)
		[1] 4.709
	sd(training$capitalAve)
		[1] 25.48
	
	可见这个 skew 得非常厉害
	
	trainCapAve <- training$capitalAve
	trainCapAveS <- (trainCapAve  - mean(trainCapAve))/sd(trainCapAve) ## Standardizing
	
	mean(trainCapAveS)
		[1] 5.862e-18
	sd(trainCapAveS)
		[1] 1
	
	
	或者用 preProcess 来做 Standardizing
	
	preObj <- preProcess(training[,-58],method=c("center","scale")) ## 58th column 是 y，忽略掉
	trainCapAveS <- predict(preObj,training[,-58])$capitalAve
	
	注意这里的逻辑，preObj 更像是一个 function reference，然后 predict 也不是 make a prediction，而是 apply the preObj to training[,-58]
	
	或者用 preProcess 参数：
	
	modelFit <- train(type ~.,data=training,preProcess=c("center","scale"),method="glm")
	
	
	# Impute and standardize
	preObj <- preProcess(training[,-58],method="knnImpute")
	
	
	Notes:
	* Training and test must be processed in the same way
	* Test transformations will likely be imperfect
		* Especially if the test/training sets collected at different times
	* Careful when transforming factor variables!
	
-----

## 6. Covariate creation

	Covariate == Predictors == Features
	
	Covariate creation:
	* level 1: raw data -> covariate
	* level 2: tranforming tidy covariates
	
### 6.1 Level 1, Raw data -> covariates

	* Depends heavily on application
	* The balancing act is summarization vs. information loss
	* Examples:
		* Text files: frequency of words, frequency of phrases (Google ngrams), frequency of capital letters.
		* Images: Edges, corners, blobs, ridges (computer vision feature detection)
		* Webpages: Number and type of images, position of elements, colors, videos (A/B Testing)
		* People: Height, weight, hair color, sex, country of origin.
	* The more knowledge of the system you have the better the job you will do.
	* When in doubt, err on the side of more features
	* Can be automated, but use caution!
	
### 6.2 Level 2, Tidy covariates -> new covariates

	* More necessary for some methods (regression, svms) than for others (classification trees).
	* Should be done only on the training set
	* The best approach is through exploratory analysis (plotting/tables)
	* New covariates should be added to data frames
	
### 6.3 Common covariates to add, dummy variables

	Basic idea - convert factor variables to indicator variables

	table(training$jobclass)

		 1. Industrial 2. Information 
				  1090           1012 
	
	dummies <- dummyVars(wage ~ jobclass,data=training)
	head(predict(dummies,newdata=training))
	
			   jobclass.1. Industrial jobclass.2. Information
		231655                      1                       0
		86582                       0                       1
		11141                       0                       1
	
### 6.4 Removing zero covariates
	
	nsv <- nearZeroVar(training,saveMetrics=TRUE)
	nsv
	
				   freqRatio percentUnique zeroVar   nzv
		year           1.029       0.33302   FALSE FALSE
		age            1.122       2.80685   FALSE FALSE
		sex            0.000       0.04757    TRUE  TRUE
		maritl         3.159       0.23787   FALSE FALSE
		race           8.529       0.19029   FALSE FALSE
		education      1.492       0.23787   FALSE FALSE
		region         0.000       0.04757    TRUE  TRUE
	
### 6.5 Spline basis

	library(splines)
	bsBasis <- bs(training$age,df=3) 
	bsBasis
	
					  1         2         3
		   [1,] 0.00000 0.0000000 0.000e+00
		   [2,] 0.23685 0.0253768 9.063e-04
		   [3,] 0.44309 0.2436978 4.468e-02
		   [4,] 0.43081 0.2910904 6.556e-02
		   [5,] 0.42617 0.1482327 1.719e-02

	用来生成 polynomial
	
### 6.6 Notes

	* Level 1 feature creation (raw data to covariates)
		* Science is key. Google "feature extraction for [data type]"
		* Err on overcreation of features
		* In some applications (images, voices) automated feature creation is possible/necessary
		* http://www.cs.nyu.edu/~yann/talks/lecun-ranzato-icml2013.pdf
	* Level 2 feature creation (covariates to new covariates)
		* The function preProcess in caret will handle some preprocessing.
		* Create new covariates if you think they will improve fit
		* Use exploratory analysis on the training set for creating them
		* Be careful about overfitting!
	* If you want to fit spline models, use the gam method in the caret package which allows smoothing of multiple variables.
	
-----

## 7. Preprocessing with principal components analysis

### 7.1 Correlated predictors

	library(caret); library(kernlab); data(spam)
	inTrain <- createDataPartition(y=spam$type, p=0.75, list=FALSE)
	training <- spam[inTrain,]
	testing <- spam[-inTrain,]

	M <- abs(cor(training[,-58])) ## 57x57 的 matrix
	diag(M) <- 0 ## Cor(X,X) 赋成 0，方便过滤
	which(M > 0.8,arr.ind=T) ## array indices be returned
	
			   row col
		num415  34  32
		direct  40  32
		num857  32  34
		direct  40  34
		num857  32  40
		num415  34  40

	找到 3 对 Cor > 0.8 的，分别是 (num415, num857) (number857, direct) (number415, direct)，所以这 3 features 可以 reduce to 1 feature
	
### 7.2 Basic PCA idea

	* We might not need every predictor
	* A weighted combination of predictors might be better
	* We should pick this combination to capture the "most information" possible
	* Benefits
		* Reduced number of predictors
		* Reduced noise (due to averaging)
	
### 7.3 We could rotate the plot

	X <- 0.71*training$num415 + 0.71*training$num857
	Y <- 0.71*training$num415 - 0.71*training$num857
	plot(X,Y) ## 发现基本是 X 轴在变，Y 轴基本恒定，所以我们选 (num415 + num857) 作为新 feature
	
### 7.4 Principal components in R - prcomp

	smallSpam <- spam[,c(34,32)]
	prComp <- prcomp(smallSpam)
	plot(prComp$x[,1],prComp$x[,2]) ## 得到的图和 7.3 的图基本一致
	
	prComp$rotation
	
		          PC1     PC2
		num415 0.7081  0.7061
		num857 0.7061 -0.7081
	
	竖着看，表示 PC1，i.e. prComp$x[,1] 是 0.7081*num415 + 0.7061*num857，PC2 同理
	
### 7.5 PCA with caret

	typeColor <- ((spam$type=="spam")*1 + 1)
	preProc <- preProcess(log10(spam[,-58]+1),method="pca",pcaComp=2) ## 分解成两个 Components
	spamPC <- predict(preProc,log10(spam[,-58]+1))
	plot(spamPC[,1],spamPC[,2],col=typeColor)
	
	log10 是对 Y 的处理
	
	还有一些代码见 slide
	
### 7.6 Final thoughts on PCs

	* Most useful for linear-type models
		* Can make it harder to interpret predictors
	* Watch out for outliers!
		* Transform first (with logs/Box Cox)
		* Plot predictors to identify problems (EDA)
	
-----

## 8. Predicting with Regression

	Key ideas:
	* Fit a simple regression model
	* Plug in new covariates and multiply by the coefficients
	* Useful when the linear model is (nearly) correct
	
	Pros:
	* Easy to implement
	* Easy to interpret
	
	Cons:
	* Often poor performance in nonlinear settings
	
	
	Old Faithful Geyser Data: 
	Waiting time between eruptions and the duration of the eruption for the Old Faithful geyser in Yellowstone National Park, Wyoming, USA.
	
	geyser [ˈgaɪzər] 间歇泉，是间断喷发的温泉，多发生于火山运动活跃的区域; Old Faithful 是 geyser 名
	
	
	
	lm1 <- lm(eruptions ~ waiting,data=trainFaith)
	summary(lm1)
		
		……
		
	plot(trainFaith$waiting,trainFaith$eruptions,pch=19,col="blue",xlab="Waiting",ylab="Duration")
	lines(trainFaith$waiting,lm1$fitted,lwd=3) ## 画线
	
	
	
	如果来了个新输入 waitingTime = 80，那么输出 duration 就等于 coef(lm1)[1] + coef(lm1)[2]*80，这里 coef 函数获取系数。或者用下面的方法来计算：
	
	newdata <- data.frame(waiting=80)
	predict(lm1,newdata)
	
	
	
	Get training set/test set errors
	
	# Calculate RMSE on training
	sqrt(sum((lm1$fitted-trainFaith$eruptions)^2))
		[1] 5.752
	# Calculate RMSE on test
	sqrt(sum((predict(lm1,newdata=testFaith)-testFaith$eruptions)^2))
		[1] 5.839
	
	
	
	Prediction intervals
	pred1 <- predict(lm1,newdata=testFaith,interval="prediction")
	ord <- order(testFaith$waiting)
	plot(testFaith$waiting,testFaith$eruptions,pch=19,col="blue")
	matlines(testFaith$waiting[ord],pred1[ord,],type="l",,col=c(1,2,2),lty = c(1,1,1), lwd=3) ## 画出 3 条线，自身一条，interval 两个边界各一条
	
	
	
	Same process with caret
	
	modFit <- train(eruptions ~ waiting,data=trainFaith,method="lm")
	summary(modFit$finalModel)
	
-----

## 9. Predicting with Regression Multiple Covariates
	
	modFit<- train(wage ~ age + jobclass + education, method = "lm",data=training)
	
	注意 factor 会变成 indicator variable 来处理
	
	看 slide 就好了
	
-----

## Q2

	library(AppliedPredictiveModeling)
	data(concrete)
	library(caret)
	set.seed(975)
	inTrain = createDataPartition(mixtures$CompressiveStrength, p = 3/4)[[1]]
	training = mixtures[ inTrain,]
	testing = mixtures[-inTrain,]


	
	qplot(seq_along(CompressiveStrength), CompressiveStrength, data=training, color=FlyAsh)
	
## Q3

	A monotone transformation is a transformation that preserves inequalities of its arguments. That is, if T is a monotone transformation, then: iff x>y, then Tx>Ty, and iff x<y, then Tx<Ty.

	
	
	library(AppliedPredictiveModeling)
	data(concrete)
	library(caret)
	set.seed(975)
	inTrain = createDataPartition(mixtures$CompressiveStrength, p = 3/4)[[1]]
	training = mixtures[ inTrain,]
	testing = mixtures[-inTrain,]
		
		
		
	par(mfrow=c(1,2))
	hist(training$Superplasticizer)
	hist(log10(training$Superplasticizer + 1))

	> range(training$Superplasticizer)
	[1] 0.0000000 0.0131493

	有大量的 0 存在，所以不能直接用 log10() 处理，因为 log10(0) 得到的是 -Inf。注意 log10(x) plot 出来的确是有点像 normal distribution，不要被迷惑了。
	
	用 log10(x+1) 可以发现形状没有明显的变化
	
## Q4

	library(caret)
	library(AppliedPredictiveModeling)
	set.seed(3433)
	data(AlzheimerDisease)
	adData = data.frame(diagnosis,predictors)
	inTrain = createDataPartition(adData$diagnosis, p = 3/4)[[1]]
	training = adData[ inTrain,]
	testing = adData[-inTrain,]

	

	t2 <- training[,grep("^IL", colnames(training))]

	udv <- svd(scale(t2))
	sum(cumsum(udv$d^2/sum(udv$d^2)) < 0.90)
	# 8
	# 累积到第 8 个还是小于 90%，所以需要 9 个 PC
	
## Q5
	
	library(caret)
	library(AppliedPredictiveModeling)
	set.seed(3433)
	data(AlzheimerDisease)
	adData = data.frame(diagnosis,predictors)
	inTrain = createDataPartition(adData$diagnosis, p = 3/4)[[1]]
	training = adData[ inTrain,]
	testing = adData[-inTrain,]
	
	
	
	tr <- training[,c(1, grep("^IL", colnames(training)))] ## 第一行是 diagnosis，是 outcome
	
	modelFit <- train(diagnosis ~ .,method="glm",data=tr)
	confusionMatrix(testing$diagnosis,predict(modelFit,testing)) ##  Accuracy : 0.6463

	## 注意题目问的是 accuracy in the TEST set
	
	preProc <- preProcess(tr[,-1],method="pca",thresh=0.8)
	trPC <- predict(preProc, tr[,-1])
	modelFit2 <- train(tr$diagnosis ~ .,method="glm",data=trPC) ## trPC 没有包含 diagnosis

	te <- testing[,c(1, grep("^IL", colnames(testing)))]
	
	## preProc <- preProcess(te[,-1],method="pca",thresh=0.8) ## 不需要这一句，直接按 tr 的 preProc 来做 te 的 PCA，否则分得的 PC 不同，也不合逻辑（因为 modelFit2 是按 tr 的 PC 来算的）
	tePC <- predict(preProc, te[,-1])
	confusionMatrix(te$diagnosis,predict(modelFit2,tePC)) ## Accuracy : 0.7195

	
	
	
	