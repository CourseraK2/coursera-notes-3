## Prioritizing What to Work On: Spam classiﬁcafion example

	watch the video，轻松愉快
	
-----

## Error Analysis
	
	Recommended approach: 
	- Start with a simple algorithm that you can implement quickly. Implement it and test it on your crossPvalida3on data. 
	- Plot learning curves to decide if more data, more features, etc. are likely to help. 
	- Error analysis: Manually examine the examples (in cross/validation set) that your algorithm made errors on. See if you spot any systematic trend in what type of examples it is making errors on.
	
	stemming software (e.g. Porter Stemmer) is used to treat discount/discounts/discounted/discounting as the same word. Error analysis may not be helpful for deciding if this is likely to improve performance. The only solution is to try stemming software and see if it works. 
	
	watch the video，轻松愉快

-----

## Error Metrics for Skewed Classes

	假定一个 cancer prediction 是 99% accurate，看上去还不错是吗？但是实际情况是只有 0.5% 的人有 cancer，所以我们直接预测 h(x) = 0 都有 99.5% 的 accuracy
	
	对于这种某个 class 的概率远大于另一个 class 的情况，我们称为 Skewed Classes
	
	此时 accuracy 不见得是一个很好的判断 algorithm 好坏的标准。此时我们可以用 Precision/Recall 来判断
	
	y = 1 in presence of rare class that we want to detect
	
	True Positive: h(x) = 1 and y = 1
	True Negative: h(x) = 0 and y = 0
	False Positive: h(x) = 1 and y = 0
	False Negative: h(x) = 0 and y = 1
	
	Precision: Of all patients where we predicted y=1, what fraction actually has cancer?
	
	Precision = TP#/(TP# + FP#)
	
	Recall: Of all patients that actually have cancer, what fraction did we correctly detect as having cancer? 
	
	Recall = TP#/(TP# + FN#)
	
	对于 h(x) = 0，TP# = 0，所以 Precision = Recall = 0
	
	对于 h(x) = 1，Recall = 1, precision is low
	
-----

## Trading Off Precision and Recall

	Predict 1 if h(x) >= 0.5
	Predict 0 if h(x) < 0.5
	
	0.5 is threshold
	
	Suppose we want to predict y=1 (cancer) only if very confident.
	
	=> Increase threshold => Higher precision, lower recall
	
	Suppose we want to avoid missing too many cases of cancer (avoid false negatives). 

	=> Decrease threshold => Higher recall, lower precision
	
	So is there a way to choose threshold automatically? Or more generally, how to compare <precision, recall> pairs? The answer is F_1 Score
	
	F_1 = 2*P*R/(P+R)
	
-----

## Data For Machine Learning

	How many data to train on?
	Under cetain conditions, having more data gets better performance.
	
	
	Large data rationale:
	
	* Assume features X has sufficient information to predict y accurately.
		* Counterexample: Predict housing price from only size feet2) and no other features. 
		* Useful test: Given the input X, can a human expert confidently predict y? If not, X does not have enough information 
	* Use a learning algorithm with many parameters (e.g. logistic regression/linear regression with many features; neural network with many hidden units).
		* low-bias algorithm
		* large training set makes it unlikely to overfit
	
	
	
	
	
	
	