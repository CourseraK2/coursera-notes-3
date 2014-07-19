## Reproducible Research: Concepts and Ideas (part 1)
	
	The ultimate standard for strengthening scientific evidence is replication of findings and conducting studies with independent: 
		* Investigators
		* Data 
		* Analytical methods 
		* Laboratories 
		* Instruments
	
	Some studies cannot	be replicated	
		* No time, opportunistic
		* No money
		* Unique
		
	Reproducible Research: 
		to Make analytic data and code available so that others may	reproduce findings
	
	简单说就是要打破 “Replication or nothing” 的情况，做到 Reproducible 的中间状态，具有 Reproducibility
	
	注意 replication 和 Reproducibility 是完全不同的概念，replication 是说用不同的数据和方法（但可以是类似的，不能完全相同），得到相同的结论。Reproducibility 是说可以让人完全按照你的数据和方法得到相同的结论
	
## Reproducible Research: Concepts and Ideas (part 2)

	Research Pipeline 这个很重要，看 slide
	
	omics：（生物或化学的）构成学
	
## Reproducible Research: Concepts and Ideas (part 3)

	感觉这个课题包含了很多待开发的工作，比如：
		1. 可检验性；reproduce 的工作简化工具。我觉得可以参考下 test framework 之类的设计，让 reproduce 这个过程更加简单
		2. 文档在这里同样重要，是否可以参考软件工程的某些做法？
		3. 貌似在线存放大规模数据还不是很方便，存储设计和存储工具开发也应该可以做做文章。and 云存储？
		4. 如何 Intuitively 来做 reproduce？如何设计我们的语言？
		
	当然，感觉这些都是偏实现和工具的问题，不是理论上的问题。
	
-----
	
## Scripting Your Analysis

	
## Structure of a Data Analysis (part 1)

	Steps in a data analysis:
	
	* Define the question: 一定要有 science 做驱动。基本上你随便拿一个 dataset 来做分析，总能发现 something interesting，但是 interesting 并不能解决什么实际问题
	* Define the ideal data set: The data set may depend on your goal
		* Descriptive - a whole population
		* Exploratory - a random sample with many variables measured
		* Inferential - the right population, randomly sampled
		* Predictive - a training and test data set from the same population
		* Causal - data from a randomized study
		* Mechanistic - data about all components of the system
	* Determine what data you can access
		* free data on the web
		* buy data
		* generate yourself
	* Obtain the data
	* Clean the data
	* Exploratory data analysis
	* Statistical prediction/modeling
	* Interpret results
	* Challenge results
	* Synthesize/write up results
	* Create reproducible code	
	
## Structure of a Data Analysis (part 2)	
	
	library(kernlab)
	data(spam) ## 4601 records
	
	# Perform the subsampling
	set.seed(3435)
	trainIndicator = rbinom(4601, size = 1, prob = 0.5)
	table(trainIndicator)
	
	## trainIndicator
	##    0    1 
	## 2314 2287
	
	trainSpam = spam[trainIndicator == 1, ]
	testSpam = spam[trainIndicator == 0, ]
	
	Exploratory data analysis
	* Look at summaries of the data
	* Check for missing data
	* Create exploratory plots
	* Perform exploratory analyses (e.g. clustering)
	
	names(trainSpam)
	head(trainSpam)
	table(trainSpam$type)
	plot(trainSpam$capitalAve ~ trainSpam$type)
	plot(log10(trainSpam$capitalAve + 1) ~ trainSpam$type)
	plot(log10(trainSpam[, 1:4] + 1))
	
	hCluster = hclust(dist(t(trainSpam[, 1:57])))
	plot(hCluster)
	
	hClusterUpdated = hclust(dist(t(log10(trainSpam[, 1:55] + 1))))
	plot(hClusterUpdated)
	
	Statistical prediction/modeling
	* Should be informed by the results of your exploratory analysis
	* Exact methods depend on the question of interest
	* Transformations/processing should be accounted for when necessary
	* Measures of uncertainty should be reported
	
	trainSpam$numType = as.numeric(trainSpam$type) - 1
	costFunction = function(x, y) sum(x != (y > 0.5))
	cvError = rep(NA, 55)
	library(boot)
	for (i in 1:55) {
		lmFormula = reformulate(names(trainSpam)[i], response = "numType")
		glmFit = glm(lmFormula, family = "binomial", data = trainSpam)
		cvError[i] = cv.glm(trainSpam, glmFit, costFunction, 2)$delta[2]
	}

	## Which predictor has minimum cross-validated error?
	names(trainSpam)[which.min(cvError)]
	## [1] "charDollar"
	
	Get a measure of uncertainty
	
	## Use the best model from the group
	predictionModel = glm(numType ~ charDollar, family = "binomial", data = trainSpam)

	## Get predictions on the test set
	predictionTest = predict(predictionModel, testSpam)
	predictedSpam = rep("nonspam", dim(testSpam)[1])

	## Classify as `spam' for those with prob > 0.5
	predictedSpam[predictionModel$fitted > 0.5] = "spam"
	
	## Classification table
	table(predictedSpam, testSpam$type)
	##              
	## predictedSpam nonspam spam
	##       nonspam    1346  458
	##       spam         61  449

	## Error rate
	(61 + 458)/(1346 + 458 + 61 + 449)
	## [1] 0.2243
	
	
	
	Interpret results
	* Use the appropriate language
		* describes
		* correlates with/associated with
		* leads to/causes
		* predicts
	* Give an explanation
	* Interpret coefficients
	* Interpret measures of uncertainty
	
	
	Challenge results
	* Challenge all steps:
		* Question
		* Data source
		* Processing
		* Analysis
		* Conclusions
	* Challenge measures of uncertainty
	* Challenge choices of terms to include in models
	* Think of potential alternative analyses

	
	Synthesize/write-up results
	* Lead with the question
	* Summarize the analyses into the story
	* Don't include every analysis, include it
		* If it is needed for the story
		* If it is needed to address a challenge
	* Order analyses according to the story, rather than * chronologically
	* Include "pretty" figures that contribute to the story
	
-----

## Organizing Your Analysis
	
	Data analysis files

	* Data
		* Raw data
		* Processed data
	* Figures
		* Exploratory figures
		* Final figures
	* R code
		* Raw / unused scripts
		* Final scripts
		* R Markdown files
	* Text
		* README files
		* Text of analysis / report
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	