## 01 Introduction to inference and motivating examples

	Statistical inference is the process of drawing formal conclusions from data.
	
	In our class, we wil define formal statistical inference as settings where one wants to infer facts about a population using noisy statistical data where uncertainty must be accounted for.
	
	Estimator: (mathematics) A function of a random sample of a population used to estimate some parameter of the whole population
	
	It difficult to use data to create general conclusions about a population.

	Paramount (Highest) among our concerns are:
	* Is the sample representative of the population that we'd like to draw inferences about?
	* Are there known and observed, known and unobserved or unknown and unobserved variables that contaminate our conclusions?
	* Is there systematic bias created by missing data or the design or conduct of the study?
	* What randomness exists in the data and how do we use or adjust for it? Here randomness can either be explicit via randomization or random sampling, or implicit as the aggregation of many complex unkown processes.
	* Are we trying to estimate an underlying mechanistic model of phenomena under study?

	Statistical inference requires navigating the set of assumptions and tools and subsequently thinking about how to draw conclusions from data.
	
	Example goals of inference
	1. Estimate and quantify the uncertainty of an estimate of a population quantity (the proportion of people who will vote for a candidate).
	2. Determine whether a population quantity is a benchmark value ("is the treatment effective?").
	3. Infer a mechanistic relationship when quantities are measured with noise ("What is the slope for Hooke's law?")
	4. Determine the impact of a policy? ("If we reduce polution levels, will asthma ([ˈæsmə] 哮喘) rates decline?")
	5. Talk about the probablity that something occurs
	
	
	
	Example tools of the trade
	1. Randomization: concerned with balancing unobserved variables that may confound inferences of interest
	2. Random sampling: concerned with obtaining data that is representative of the population of interest
	3. Sampling models: concerned with creating a model for the sampling process, the most common is so called "iid", independent identically distributed.
	4. Hypothesis testing: concerned with decision making in the presence of uncertainty
	5. Confidence intervals: concerned with quantifying uncertainty in estimation
	6. Probability models: a formal connection between the data and a population of interest. Often probability models are assumed or are approximated.
	7. Study design: the process of designing an experiment to minimize biases and variability.
	8. Nonparametric bootstrapping: the process of using the data to, with minimal probability model assumptions, create inferences.
	9. Permutation, randomization and exchangeability testing: the process of using data permutations to perform inferences
	
	
	Control: A separate group or subject in an experiment against which the results are compared where the primary variable is low or non-existent. 对照组，一般也叫 Control Group. 这样的试验也叫 Control Experiment
	
	
	belabor: 就…作过度的说明; 向…唠叨
	
	
	Different thinking about probability leads to different styles of inference. 
	There are several different styles of inference. Two broad categories that get discussed a lot are:

	1. Frequency probability: is the long run proportion of times an event occurs in independent, identically distributed repetitions. (e.g. flipping a coin)
	2. Frequency inference: uses frequency interpretations of probabilities to control error rates. Answers questions like "What should I decide given my data controlling the long run proportion of mistakes I make at a tolerable level."
	3. Bayesian probability: is the probability calculus of beliefs, given that beliefs follow certain rules. (P(A|B)=P(B|A)*P(A)/P(B))
		* [贝叶斯、概率分布与机器学习](http://www.cnblogs.com/LeftNotEasy/archive/2010/09/27/1837163.html)
	4. Bayesian inference: the use of Bayesian probability representation of beliefs to perform inference. Answers questions like "Given my subjective beliefs and the objective information from the data, what should I believe now?"
	
	
	In this class, we will primarily focus on basic sampling models, basic probability models and frequency style analyses to create standard inferences.
	
## Probability

## Conditional probability

	IID random variables
	
	Random variables that are Independent and Identical Distributed
	* Independent: statistically unrelated from one and another
	* Identically Distributed: all having been drawn from the same population distribution
	
## Expectation

	When the expected value of an estimator is what its trying to estimate, we say that the estimator is **unbiased**
	
-----

## Q1

12% + x -6% = 17% => x = 11%

## Q6

P(A+B) = 0.9
P(A) = 0.8
P(B) = 0.8
P(A*B) = 0.7

P(!A*!B) = 1 - P(A+B) = 0.1 = P(!A) - P(!A*B) = 0.2 - P(!A*B)
=> P(!A*B) = 0.1 

P(B|!A) = P(B*!A)/P(B) = 0.5 
	
	
	
	
	
	
	
	