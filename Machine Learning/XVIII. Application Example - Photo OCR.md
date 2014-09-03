## Problem Description and Pipeline

	OCR: Optical Character Recognition
	
	Photo OCR pipeline:
	
	1. Text detection (确定图片上有文字并定位)
	2. Character segmentation (将单词划分为字母)
	3. Character classification (识别字母)
	4. Character correction (比如根据 context 将误识别的数字 1 改成字母 l; not discussed in this lecture)
	
	
	How to design a pipeline 也是门学问。
	
-----

## Sliding Window Classifier

	这个用 Pedestrian Detection 做例子更合适。
	
	人站姿在图片里的长宽比例大致是一致的。根据这个比例做一个 window，制定横向和纵向的 step-size，然后去用这个 window 去扫描图片（window 前后两个位置可能有重叠，比如横向的 step 是 1/2 window 宽度）。
	
	又由于远近的关系，人在图片里有大有小，所以一遍扫描后要等比扩大 window 的长宽，再次扫描。
	
	扫描得到的图像都统一缩放成固定尺寸作为输入交给 ML 算法预测。这样几轮扫描下来，就可以确定图片里的行人。
	
	
	Expansion Operator：如果两个区域用被判定为字母，而且离得很近（比如 5 pixels 以内），就把这两个区域连接起来。这个操作的目的是确定连续的字母区域。
	
	
	Character segmentation 其实也是用 Sliding Window 来做：扫描字母区域，满足 "中间有狭长空白区域" 这个 pattern 的是 y = 1，不满足的就是 y = 0，得到一个 ML 算法后再拿来识别其他的输入。
	
	
	Character classification 应该是一个 multi-class classification
	
-----

## Getting Lots of Data and Artificial Data Synthesis

	比如 OCR 领域

	Create from scratch：
	* 比如自己找字体然后自己贴图来创造 data set
	create by introducing distortion:
	* 比如用 "把原图片 rotate 一定角度"、"放大"、"扭曲" 这些手段来创造新的 data set
	
	
	比如 Speech Recognition 领域：
	
	create by introducing distortion:
	* 添加 background
	* 添加 noise
	
	
	
	Discussion on getting more data
	
	1. Make sure you have a low bias classifier before expending the effort. (Do plot learning curves first). E.g. keep increasing the number of features/number of hidden units in neural network until you have a low bias classifier. 
	2. "How much work would it be to get 10x as much data as we currently have?" 
		* Artificial data synthesis 
		* Collect/label it yourself (开始人工 label 之前要估算一下工作量)
		* "Crowd source" (E.g. Amazon Mechanical Turk) 
	
-----

## Ceiling Analysis: What Part of the Pipeline to Work on Next

	道理其实很简单，就是逐个假设某项输出是 100% accurate，看最后整个 system 的 accuracy (或者其他指标) 能提升多少。
	
	比如你人为把 process A 的输出改成 100% accurate，整个 system 由 80% 提升到 90%；而把 process B 的输出改成 100% accurate，整个 system 是由 90% 提升到 91% (process B 在 process A 之后，整个 system 必然是 90% 起步)。这么一对比，必然是优先提升 process A 更有效。
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	