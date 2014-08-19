## Optimization Objective
	
	这一章都是讲 SVM: support vector machine 算法
	
	上来的切入点是 logistic regression
	
	if y = 1, we want h(X) ≈ 1, θ^T*X >> 0
	if y = 0, we want h(X) ≈ 0, θ^T*X << 0
	
	
	当 y = 1 时，cost = -log(1/(1+e^-z)), z = θ^T*X
	
	以 z 为 x-axis 画图，得到一条下降的曲线。这曲线可以用两条直线来模拟：从 z>=1 开始，cost = 0; 0<z<1 的部分用一条直线贴合
	
	这个新的 cost 函数称为 cost_1(z)
	
	
	这个新的近似的 cost 可以方便 SVM 的计算，后续的优化也会更容易（supervised 算法里也就这货比较不稳定需要优化？）
	
	当 y = 0 时类似，得到 cost_0(z)
	
	## 看 slide 补充
	
-----

## Large Margin Intuition

	SVM: large margin classifier
	
	if y = 1, we want h(X) ≈ 1, θ^T*X >= 1
	if y = 0, we want h(X) ≈ 0, θ^T*X <= -1
	
	this builds an extra safety margin into SVM
	
	## 看 video 解释 margin
	## C 的大小会影响对 outlier 的敏感度
	
-----

## Mathematics Behind Large Margin Classification

-----

## Kernels I
	
	SVM 适用于 non-linear classification?
	
## Kernels II
	
	How to choose landmarks? Directly use training examples as landmarks
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	