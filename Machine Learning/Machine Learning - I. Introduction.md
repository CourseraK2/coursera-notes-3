ML applicaion:

	Database mining: Web click data
	applications that cannot program by hand
		Autonomous Vehicle
		handwriting recognition
		NLP: natrual lang processing
		Computer vision
	Self-customizing programs
		recommendation system
		
-----

what is ML?

	A computer program is said to _learn_ from _experience E_ with respect to some _task T_ and some _performance measure P_, if its performance on _T_, as measured by _P_, imporoves with _experience E_
	
Suppose your email program watches which emails you do or do not mark as spam, and based on that learns how to better filter spam. What is the task T in this setting?

	√ Classifying emails as spam or not spam. ==> T
	× Watching you label emails as spam or not spam. ==> E
	× The number (or fraction) of emails correctly classified as spam/not spam.  ==> P
	
-----

ML algorithms

	Supervised learning
		teach the computers how to do sth
	Unsupervised learning
		let computers do sth themselves
	Reinforcement learning
	Recommender system
	
Supervised learning

	quadratic 二次的；平方的；二次方程式
	malignant [məˈlɪgnənt] 恶性的，致命的；
	benign [bɪˈnaɪn] 温和的;良性的;
	
	some "right answers" already given; to predict more answers
	Regression: Predict continuous valued output (连续)
	Classification：predict discrete valued output (离散)
	
	N-feature （你可以理解为 N-variable）的问题，有可能需要 Support Vector Machine

Unsupervised learning

	Here is the data set, data point (what's the point) not given, can you find some structure in the data?
	
	Cluster: 感觉就是分组，group the data
	Cocktail party problem：实质是 Source separation 问题，比如从音频中提取人声和背景音乐
	
	
	
	