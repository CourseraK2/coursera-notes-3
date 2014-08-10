Basic Operations

	% comment goes here
	
	1 == 2  equals
	1 ~= 2  not equals
	
	xor(1, 0)
	0 代表 false
	1 代表 true
	
	PS1('>> ') 修改 prompt 的命令前缀
	
	octave-3.2.4.exe:1> PS1('>> ')
	>> 1 + 2
	ans =  3
	>>
	
	a = 3 赋值
	a = 3; 还是赋值，但是 prompt 不会 print 赋值结果
	
	pi = 3.1416
	
	disp(xxx) 只显示 xxx 的值，而不是显示 ans = xxx
	
	>> sprintf('2 decimals: %0.2f', pi)
	ans = 2 decimals: 3.14
	>> disp(sprintf('2 decimals: %0.2f', pi))
	2 decimals: 3.14
	
	format long 切换到 long 输出模式
	format short 切换到 short 输出模式
	这个会影响到你直接 pi 回车后看到的 pi 的长度
	
	>> A = [1 2; 3 4; 5 6]
	A =

		1   2
		3   4
		5   6
	
	也可以在 ; 后回车然后接着键入，看着要舒服一点
	
	V = [1; 2; 3]  % vector
	
	>> v = 1:0.2:2
	v =

		1.0000    1.2000    1.4000    1.6000    1.8000    2.0000
	
	>> v = 1:5
	v =

		1   2   3   4   5
	
	>> ones(2, 3) % 2x3 matrix with elements all ones
	ans =

		1   1   1
		1   1   1
	
	>> C = 2 * ones(2, 3)
	C =

		2   2   2
		2   2   2
	
	同理有 zeros(2, 3) % 全 0 的 2x3 matrix
	
	rand(2,3) % (0, 1) 区间随机值的 2x3 matrix
	
	randn(2, 3) % 符合正则分布的随机数 2x3 matrix
	
	hist(w): histogram 是这样的，x 轴是矩阵 w 元素的值（分区间，比如 0~5， 5~10），y 轴是该区间内元素的个数，比如 0~5 区间内的元素最多，那么柱子（因为是柱状图嘛）就最高
	
	如果 w = randn，柱状图会是一个钟形曲线形状
	
	hist(w, 50) % plot with 50 buckets or 50 bins, 意思就是分 50 个区间，画 50 根柱子
	
	
	>> eye(4) % eye 应该是 identity 的 pun
	ans =

	Diagonal Matrix

	   1   0   0   0
	   0   1   0   0
	   0   0   1   0
	   0   0   0   1
	
	help eye 显示 help of eye
	
-----

Moving Data Around

	load data, save result etc
	
	size(A) 返回 matrix A 的 m × n
	size(A, 1) 返回 nrow
	size(A, 2) 返回 ncol
	
	length(matrix) 返回 max(nrow, ncol)
	length(vector) 是一般用法
	
	>> pwd
	ans = E:\Octave\3.2.4_gcc-4.4.0\bin
	cd、ls 这些都可以用
	
	load xxx.dat 或者 load('xxx.dat')
	
	who: show variables in Octave workspace
	
	load xxx.dat 之后就能看到 xxx 这个变量（注意：如果 dat 文件里没有存变量名的话，load 进来的数据就以文件名为变量名）；然后 disp(xxx) size(xxx) 这些就可以用了
	
	whos 是 who 的 detailed view
	
	clear xxx 将 xxx 这个变量 free 掉
	clear 是将所有的 variables 都 free 掉
	
	如果 V 是个 vector，那么 v(1:10) 表示 v 的前 10 个元素
	
	save hello.mat xxx 将 xxx 的值存到文件 hello.mat 中
	
	save hello.mat xxx -ascii 指定编码存储
	
	假设 A 是个 3x2
	
	A(3,2) 取 row3 col2 的元素
	A(3,:) 取 row3 的所有元素(以2x1矩阵形式返回)
	A(:,2) 取 col2 的元素(以1x3 vector 形式返回)
	A([1 3],:) 取 row1 和 row3
	
	A(:,2) = xxx 这种形式可以直接赋值，如果原来有 col2 就是覆盖，没有 col2 就是加一个 column 变成 col2
	A = [A [100; 101; 102]] append a column as [100; 101; 102]
	
	一般的原则是这样的：
		A = [A B] 或 [A, B] % append a column (or matrix) B to the right
		A = [B A] 或 [B, A] % append a column (or matrix) B to the left
		A = [A; B] % append a row (or matrix) B to the buttom
		A = [B; A] % append a row (or matrix) B to the top
		
		; 意味着 go to the next line
	
	A(:) 取 A 所有元素，以 num×1 的 vector 形式返回
	
-----

Computing on Data

	假设 A 和 B 都是 m×n
	
	A.*B 将 A B 对应的元素分别相乘，仍然得到一个 m×n
	
	A =

	   1   2
	   3   4
	   5   6

	B =

	   11   12
	   13   14
	   15   16

	>> A.*B
	ans =

	   11   24
	   39   56
	   75   96
	
	A.^2 将 A 的每个元素分别平方，得到一个新的 m×n 
	
	1./A 用 1 除与 A 的每个元素，得到一个新的 m×n
	
	log(X): Compute the natural logarithm, `ln (X)`, for each element of X.
	exp(X): Compute `e^x` for each element of X
	abs(X): 取绝对值
	-X == -1*X
	
	v = [1; 2; 3]
	v + ones(length(v), 1) == [2; 3; 4] == v + 1 
	
	>> a = [5 6 7 8]
	a =

	   5   6   7   8

	>> max(a)
	ans =  8
	>> [x, y] = max(a)
	x =  8
	y =  4 % 表示 max 8 在 index4 上
	
	
	>> a = [5 6; 7 8]
	a =

	   5   6
	   7   8

	>> max(a)
	ans =

	   7   8
	
	>> a = [1 2 3 4]
	>> a < 3
		[1 1 0 0]
	>> find(a<3)
		[1 2] % index1 and index2
	
	magic(3) 返回 3 阶幻方
	
	>> a = magic(3)
	a =

	   8   1   6
	   3   5   7
	   4   9   2

	>> [row, col] = find(a >= 7)
	row =

	   1
	   3
	   2

	col =

	   1
	   2
	   3
	
	sum(a)
		sum(a, 1): column-wise sum
		sum(a, 2): row-wise sum
		sum(sum(magic(3).*eye(3))): 获取对角线上的 sum
	prod(a): 返回各元素乘积
	floor(a): 将各元素向下取整
	ceil(a): 向上取整
	
	flipud(A): Flip A Upside Down
	
	>> A = rand(2)
	A =

	   0.72350   0.70303
	   0.94012   0.16181

	>> B = rand(2)
	B =

	   0.27253   0.62743
	   0.68920   0.73614

	>> max(A, B)
	ans =

	   0.72350   0.70303
	   0.94012   0.73614
	
	max(A, [], 1): column-wise max
	max(A, [], 2): row-wise max
	联系 apply: Apply Functions Over Array Margins，这里 1 表示 by column，2 表示 by row，和 apply 是 反的
	
	max(max(A)) == max(A(:)) 取 matrix A 的最大元素
	
-----

Plotting Data

	plot(x, y) 但是要赋值 y = f(x)
	
	sin(x) cos(x)
	
	plot(x, sin(x))
	hold on; % 保持原曲线
	plot(x, cos(x)) % 在 sin 曲线上再画一个 cos 曲线
	plot(x, cos(x), 'r') % 使用 red 线条来画图（默认是蓝线）
	
	xlabel("foo") % 把 x 轴标记为 foo
	ylabel("bar") % 把 y 轴标记为 bar
	
	legend('sin', 'cos') % 将蓝线图例标记为 sin，将红线图例标记为 cos
	
	title('my plot')
	
	print -dpng 'myplot.png' % save the plot
	
	close % close current plot window
	
	figure(1); plot(x, y) % 在 figure 1 window 里画图
	figure(2); plot(x, y) % 在 figure 2 window 里画图
	
	subplot(1, 2, 1) % 把 plot window split 成 1x2 两个格子，然后使用第一个
	plot(x, y) % 画在第一个格子里
	subplot(1, 2, 2);
	plot(x, y2) % 画在第二个格子里
	
	axis([0.5 1 -1 1]) % 把x轴设置成 [0.5, 1] 区间，y轴设置成 [-1, 1] 区间
	
	clf; clear plot window，但是不关闭 plot window
	
	To change the markers to red \x", we used the option `rx` together with the plot command, i.e., plot(..,[your options here],.., `rx`);
	
	A = magic(15);
	imagesc(A)
	imagesc(A), colorbar, colormap gray;
	
-----

Control Statements: for, while, if statements

	for i = 1:10,
		v(i) = 2^i;
	end;
	
	i = 1;
	while i<=5,
		v(i) = 100;
		i = i+1;
	end;
	
	i = 1;
	while true, 
		v(i) = 999;
		if i == 6, 
			break;
		end;
	end;
	
	if xxx,
		...;
	elseif zzz,
		...;
	else
		...;
	end;
	
	quit / exit: quit Octave
	
	Octave 函数文件用 .m 后缀
	
	function y = my_square(x)
	y = x^2;
	
	Search Path 下的 .m 文件自动加载，不需要 load
	
	function [y1, y2] = my_square_and_cube(x)
	y1 = x^2;
	y2 = x^3;
	
	costFunctionJ 的例子不错
	
	X = [1 x1; 1 x2; 1 x3] % 第一列 x0 全是 1
	y = [y1; y2; y3]
	theta = [theta0; theta1]
	
	function J = costFunctionJ(X, y, theta)
	m = size(X, 1); % nrow(X)
	h = X*theta; % hypothesis
	sqrErrors = (h - y).^2;
	
	J = 1/(2*m) * sum(sqrErrors)
	
-----

Vectorization	
	
	使用 for loop 来计算 sum(thetaj*xj) vs thetaMatrix'*X
	
	Vectorization 简单说就是多使用矩阵运算来简化代码并提高计算效率，要有矩阵化的思想
	
-----

Working on and Submitting Programming Exercises	
	
	column vector 
	row vector
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	