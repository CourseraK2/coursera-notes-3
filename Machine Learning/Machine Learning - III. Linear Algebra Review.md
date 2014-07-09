Dimemsion of matrix: nrow × ncol； R^2×3 表示所有 2×3 矩阵的集合

Aij = "(i,j) entry" in the ith row, jth column

Vector: An n×1 matrix; also called an n-dimensional vector. R^4 表示所有 4-dimensional vector 的集合

Ai = ith element; 1-indexed (常用) vs 0-indexed

-----

Matrix addtion

	两个相同 dimension 的 matrix 相加，同位置的元素相加即可；
	不同 dimension 的 matrix 不能相加

Multiply a matrix by a number == Scalar Multiplication

	一个 matrix 和一个数相乘，把所有元素分别和这个数相乘即可
	
-----

Matrix-Vector and Matrix-Matrix Multiplication

	A(m×n)*B(n*t) -> C(m×t)
	Cpq = Ap1*B1q + Ap2*B2q + ... + Apn*Bnq
	
	假定 X = [X1, X2, X3, X4] 有 4 个值，函数 h(x) = ax + b，我们可以构造一个矩阵乘法，计算起来更加高效：
	
	|X1  1|
	|X2  1| × |a|
	|X3  1|   |b|
	|X4  1|

	若假设我们有三个函数 h1(x) = a1x + b3 ... h3(x) = a3x + b3，则构造成：

	|X1  1|
	|X2  1| × |a1  a2  a3|
	|X3  1|   |b1  b2  b3|
	|X4  1|

	可以立马得到 12 个函数值

-----

Matrix Multiplication Properties

	Property 这里指 "律"，比如 Commutative Property 就是 "交换律"
	
	矩阵 A×B ≠ B×A，交换律不成立
	
	Associative Property 结合律
	
	矩阵 (A×B)×C ≠ A×(B×C) 结合律成立
	
Identity Matrix

	记作 I 或者 In×n，对任意 Am×n，Am×n × In×n = Am×n，或者对任意 An×m，In×n × An×m = An×m
	
	In×n 左上到右下的对角线全是 1，其余全 0
	
-----

Inverse

	就是倒数，比如 3 和 1/3 (3^-1) 互为倒数；

	if A is an m×m (aka square matrix, 只有 square matrix 才可能有 inverse) and if A has an inverse (比如全 0 的矩阵就没有 inverse), A×A^-1 = A^-1×A = I

	octave-3.2.4.exe:1> A = [3 4; 2 16]
	A =

		3    4
		2   16

	octave-3.2.4.exe:2> pinv(A)
	ans =

		 0.400000  -0.100000
		-0.050000   0.075000
	
	matrices that do not have an inverse are "singular" or "degenerate"

-----
	
Transpose

	转置，比如：
	
	A = |1  2  0|  
		|3  5  9| 

	则 A^T = |1  3|
	         |2  5|
             |0  9|
			 
	Aij = A^Tji 
			 
	