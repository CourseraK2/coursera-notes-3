## Prediction motivation

	最后一页 slide 有个 resource list 可以看看
	
## What is prediction?
	
	Components of a predictor

	raise a question -> collect/obtain input data -> calculate features -> choose an algorithm -> estimate parameters -> make a evaluation
	
## Relative importance of steps
	
	garbage in, garbage out: (computing, information technology) if input data are not complete, accurate and timely, then the resulting output is unreliable and of no useful value.
	
	Properties of good features

	* Lead to data compression
	* Retain relevant information
	* Are created based on expert application knowledge

	Common mistakes on features

	* Trying to automate feature selection
	* Not paying attention to data-specific quirks
	* Throwing away information unnecessarily
	
	Prediction is about accuracy tradeoffs
	
	* Interpretability versus accuracy
	* Speed versus accuracy
	* Simplicity versus accuracy
	* Scalability versus accuracy
	
## In and out of sample errors
	
	In Sample Error: The error rate you get on the same data set you used to build your predictor. Sometimes called resubstitution error.
	Out of Sample Error: The error rate you get on a new data set. Sometimes called generalization error.

	Key ideas: 

	1. Out of sample error is what you care about
	2. In sample error < out of sample error
	3. The reason is overfitting, because you are matching your algorithm to the data you have
	
	So what is overfitting?
	
	Data have two parts
	* Signal
	* Noise

	The goal of a predictor is to find signal
	You can always design a perfect in-sample predictor
	You capture both signal + noise when you overfit
	Predictor won't perform as well on new samples
	
-----
	
## Prediction study design
	
	Prediction study design: 

	1. Define your error rate
	2. Split data into:
		* Training, Testing, Validation (optional)
	3. On the training set pick features
		* Use cross-validation
	4. On the training set pick prediction function
		* Use cross-validation
	5. If no validation dataset
		* Apply the best model 1x (exactly one time) to test set
		* (我的疑问) to avoid overfitting?
	6. If there is validation dataset (and test data set)
		* Apply to test set and refine
		* Apply 1x to validation set
	

#### Know the benchmarks: 看 slide
	
	
#### Rules of thumb for prediction study design 

	* If you have a large sample size
		* 60% training
		* 20% test
		* 20% validation
	* If you have a medium sample size
		* 60% training
		* 40% testing
	* If you have a small sample size
		* Do cross validation
		* Report caveat ['kævɪæt] (警告; 声明) of small sample size
	
	
#### Some principles to remember
	
	* Set the test/validation set aside and don't look at it
	* In general randomly sample training and test
	* Your data sets must reflect structure of the problem
		* If predictions evolve with time split train/test in time chunks (called backtesting in finance)
	* All subsets should reflect as much diversity as possible
		* Random assignment does this
		* You can also try to balance by features - but this is tricky
	
-----

## Types of errors

#### Basic terms

	In general, Positive = identified and negative = rejected. Therefore:

	* True positive = correctly identified
	* False positive = incorrectly identified
	* True negative = correctly rejected
	* False negative = incorrectly rejected

	Medical testing example:

	* True positive = Sick people correctly diagnosed as sick
	* False positive= Healthy people incorrectly identified as sick
	* True negative = Healthy people correctly identified as healthy
	* False negative = Sick people incorrectly identified as healthy.
	
#### Key quantities
	
	* Sensitivity: P(positive test|disease) ## 做出了 positive prediction (you have disease) 然后命中 (你真的有disease)
	* Specificity: P(negative test|no disease) ## 做出了 negative prediction (you are OK) 而且也命中 (你真的没有disease)
	* Positive Predictive Value: P(disease|positive test)
	* Negative Predicitive Value: P(no disease|negative test)
	Accuracy: P(correct outcome)
	

	Key quantities as fractions：看 slide
	
	概率的计算讲得很好，看 slide
	
#### Error Measures
	
	For continuous data:
		* Mean squared error (MSE)
		* Root mean squared error (RMSE)
	
	Common error measures: 
	
	* Mean squared error (or root mean squared error)
		* for Continuous data, sensitive to outliers
	* Median absolute deviation
		* for Continuous data, often more robust
	* Sensitivity (recall)
		* If you want few missed positives
	* Specificity
		* If you want few negatives called positives
	* Accuracy
		* Weights false positives/negatives equally
	* Concordance
		
	outlier: (statistics) a value in a statistical sample which does not fit a pattern that describes most other data points; specifically, a value that lies 1.5 IQR (interquartile range) beyond the upper or lower quartile.
	
-----

## Receiver Operating Characteristic

	ROC curve: to measure the quality of a prediction algorithm
	
	Area under the curve: AUC
	
	AUC = 0.5: random guessing
	AUC = 1: perfect classifer
	In general AUC of above 0.8 considered "good"
	
	图的轴是概率，要结合概率那部分一起看
	
## Cross validation

	Cross validation is a tool for:
	* detecting relevent features
	* building models
	* estimating parameters
	
	记得前面讲得 training set / test set 划分吗？这里说的一个问题是：“But we can't use the test set when building the model or it becomes part of the training set”，所以在 building the model 的时候，我们要把 training set 再继续划分成两部分：sub-training set 和 sub-test set
	
	然后我们在这两个 sub set 上开始尝试。在反复调整模型和参数的同时，我们也可以反复调整这两个 sub set 的划分，所以有的数据这次是用来 training 的，下次可能就是用来 test 的。Cross 说的就是这样一种意思。
	
	调整两个 sub set 的划分，常用的手段用：
	1. Random Sampling，每次都随机选
	2. K-folder，比如 k = 3，那第一次 sub-test set 是 training set 的前 1/3，第二次是中间的 1/3，第三次是后 1/3
	3. Leave one out: 每次只留一个 sample 做 test
	
	
	Considerations:
	
	* For time series data data must be used in "chunks"
	* For k-fold cross validation
		* Larger k = less bias, more variance
		* Smaller k = more bias, less variance
	* Random sampling must be done without replacement (指随机抽取时可以重复抽取到同一个元素)
	* Random sampling with replacement is the bootstrap
		* Underestimates of the error (抽取到重复元素时，要么都对，要么都错)
		* Can be corrected, but it is complicated (0.632 Bootstrap)
	* If you cross-validate to pick predictors estimate you must estimate errors on independent data (指我们一开始预留的那个 test set).
	
-----

## What data should you use?

	简单说就是有鱼吃鱼，有肉吃肉。
	
	有 NBA 球员数据统计，就预测球员场上表现
	有天气数据，就预测天气
	
	反例又是那个很无厘头的预测：X 轴是国家人均巧克力消费量, Y 轴是国家诺贝尔获奖人数
	
-----

## Q5

TP/(TP + FN) = 0.99
TN/(TN + FP) = 0.99

(TP + FN)/(FP + TN) = 0.001

TP = 99
FN = 1
TN = 98901
FP = 999

TP/(TP + FP) = 0.09
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	