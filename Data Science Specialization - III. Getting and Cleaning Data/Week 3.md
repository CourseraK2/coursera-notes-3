## Subsetting and Sorting

#### subsetting

	df[, 1] ## extract column 1
	df[, "A"] ## 提取 column df$A
	df[1:2, "A"] ## 提取 df$A 的前两 row
	
	df[(df$A <= 3 & df$B > 11), ]
	
	df[which(df$A > 8), ] ## df$A 中的 NA 会被忽略掉，i.e. which() 会将 (NA > 8) 判定为 false
	
	举个最简单的例子：
	
	> x <- c(NA, 5, 6)
	> x > 1
	[1]   NA TRUE TRUE
	> which(x > 1)
	[1] 2 3 ## 指 index
	> x[x > 1]
	[1] NA  5  6
	> x[which(x > 1)]
	[1] 5 6

#### sorting
	
	sort(df$A)
	sort(df$A, decreasing=TRUE)
	sort(df$A, na.last=TRUE) ## put NA values at the end of the sort
	
#### ordering

	df[order(df$A),] ## order 是返回 sort 之后的 index 的顺序，所以要将整个 df 按某一列 sort 的话需要这么写
	
	df[order(df$A, df$B),] ## 先按 df$A 排列，df$A 相同的再按 df$B 排列
	
#### ordering with plyr

	library(plyr)
	arrange(df, A) ## 等价于 df[order(df$A),]
	
	arrange(df, desc(A) ## 按 df$A 降序排列
	
#### adding rows and columns

	df$D <- rnorm(5) ## 直接加一个 D column
	
	df <- cbind(df, rnorm(5)) ## 加一个 column，但是 column name 会直接变成 "rnorm(5)"，所以最好用一个变量
	
	df <- rbind(df, rnorm(4)) ## add a row
	
	
	[Data Summarization andManipulation](http://www.biostat.jhsph.edu/~ajaffe/lec_winterR/Lecture%202.pdf)
	
-----

## Summarizing Data

	if(!file.exists("./data")) {
		dir.create("./data")
	}
	fileUrl <- "https://data.baltimorecity.gov/api/views/k5ry-ef3g/rows.csv?accessType=DOWNLOAD"
	download.file(fileUrl,destfile="./data/restaurants.csv",method="curl")
	restData <- read.csv("./data/restaurants.csv")
	
	
	
	head(table, n=3) ## by default n = 6
	tail(table, n=3)
	
	summray(table)
	
	str(table)
	
	quantile(table$A, na.rm=TRUE)
	quantile(table$A, probs=c(0.5, 0.75, 0.9))
	
	
	table(df$A, useNA="ifany") ## 显示每个 A 值有多少个。useNA="ifany" 表示如果有 NA，也会计算 NA 的个数；不指定的话，NA 值是不会被统计的
	
	foo  bar
	123  456
	
	比如上面就表示 df$A=="foo" 的有 123 个，df$A=="bar" 的有 456 个
	
	
	table(df$A, df$B) ## 2-dimensional table
	
	    b1  b2  b3
	a1  1   2   3
	a2  4   5   6
	
	表示 df$A=="a1" & df$B=="b1" 的有一个，依此类推
	
#### checking for missing values

	sum(is.na(df$A)) ## 统计 NA 的数量
	
	any(is.na(df$A)) ## 检查 is.na(df$A) 是否有为 TRUE 的，如果有，返回 TRUE；如果是全 FALSE，返回 FALSE
	
	all(df$A > 0) ## 返回 TRUE / FALSE
	
	
	colSums(is.na(df)) ## is.na(df) 返回一个 TRUE / FALSE 的 table，而且 column 名字还没变，返回的结果大约是这样的：
	
	A  B  C  D
	0  0  2  3
	
	表示 df$A 和 df$B 没有 NA，df$C 有 2 个 NA，df$D 有 4 个
	
	注意 colSums 是带 column name 的，但是 rowSums 不带，只返回一个 1xnrow(df) 的 vector
	
	all(colSums(is.na(df)) == 0) ## 当 column 太多时，colSums(is.na(df) 看起来也不方便，这时用 all 就好了
	
	
#### Values with specific characteristics
	
	table(df$zipCode %in% c("21212"))

	FALSE  TRUE 
	 1299    28 
	
	
	table(df$zipCode %in% c("21212","21213"))

	FALSE  TRUE 
	 1268    59 
	
	df$A %in% c("foo") 返回一个 TRUE / FALSE vector，然后 table 统计一下 TRUE / FALSE 的个数
	
	df[df$A %in% c("foo"), ] ## 选择符合条件的 row
	
#### Cross tabs

	data(UCBAdmissions)
	DF = as.data.frame(UCBAdmissions)
	summary(DF)
		  Admit       Gender   Dept       Freq    
	 Admitted:12   Male  :12   A:4   Min.   :  8  
	 Rejected:12   Female:12   B:4   1st Qu.: 80  
							   C:4   Median :170  
							   D:4   Mean   :189  
							   E:4   3rd Qu.:302  
							   F:4   Max.   :512  
	
	xtabs(Freq ~ Gender + Admit,data=DF)
	
	Admit
	Gender   Admitted Rejected
	Male       1198     1493
	Female      557     1278
	
	这个表的意思是：当 Gender==Male & Admit==Admitted 时，Freq==1198，依次类推
	
	
	xtabs(A ~ .,data=df) ## . 表示 all the other variables
	
#### Flat tables
	
	> df
	  A B C  D
	1 1 4 7 10
	2 2 5 8 11
	3 3 6 9 12
	
	xt <- xtabs(D ~ A+B+C, data=df)
	
	> ftable(xt)
		C  7  8  9
	A B           
	1 4   10  0  0
	  5    0  0  0
	  6    0  0  0
	2 4    0  0  0
	  5    0 11  0
	  6    0  0  0
	3 4    0  0  0
	  5    0  0  0
	  6    0  0 12

	D ~ A+B+C 这样多维的情况下，xtabs 的结果就会很长很散，ftable 可以解决这一问题
	
	但是要注意一定要 ftable(xtable) 套用才行，直接 ftable(D ~ A+B+C, data=df) 的输出也是很长很散的
	
#### Size of a dataset

	> object.size(df)
	1112 bytes

	> print(object.size(df), units="Kb")
	1.1 Kb

	units 还有 "Mb" 等
	
-----	

## Creating New Variables (i.e. New Columns)

#### Creating sequences

	Sometimes you need an index for your data set
	
	s1 <- seq(1,10,by=2); s1
	[1] 1 3 5 7 9
	
	s2 <- seq(1,10,length=3); s2
	[1]  1.0  5.5 10.0

	x <- c(1,3,8,25,100); seq(along = x)
	[1] 1 2 3 4 5
	
#### Subsetting variables
	
	restData$nearMe <- restData$neighborhood %in% c("Roland Park", "Homeland")
	
	table(restData$nearMe)

	FALSE  TRUE 
	 1314    13 
	
	
	restData$zipWrong <- ifelse(restData$zipCode < 0, TRUE, FALSE)

	table(restData$zipWrong,restData$zipCode < 0)

		  FALSE TRUE
	FALSE  1326    0
	TRUE      0    1
	
#### Creating categorical variables

	restData$zipGroups = cut(restData$zipCode,breaks=quantile(restData$zipCode))
	table(restData$zipGroups)

	(-2.123e+04,2.12e+04]  (2.12e+04,2.122e+04] (2.122e+04,2.123e+04] (2.123e+04,2.129e+04] 
					  337                   375                   282                   332 
	
	
	
	table(restData$zipGroups,restData$zipCode)

						  -21226 21201 21202 21205 21206 21207 21208 21209 21210 21211 21212 21213
	(-2.123e+04,2.12e+04]      0   136   201     0     0     0     0     0     0     0     0     0
	(2.12e+04,2.122e+04]       0     0     0    27    30     4     1     8    23    41    28    31
	(2.122e+04,2.123e+04]      0     0     0     0     0     0     0     0     0     0     0     0
	(2.123e+04,2.129e+04]      0     0     0     0     0     0     0     0     0     0     0     0
	
	
	cut 简单说就是按 breaks 的区间来分组：
	
	* 如果 breaks = n，那就是分 n 个组
	* 如果 breaks = c(x, y, z)，那就是分 (x, y], (y, z] 这么两个组，依次类推
	
#### Easier cutting

	library(Hmisc)
	restData$zipGroups <- cut2(restData$zipCode,g=4) ## g=4 表示分 4 个 group
	table(restData$zipGroups)

	[-21226,21205) [ 21205,21220) [ 21220,21227) [ 21227,21287] 
			   338            375            300            314 
	
	注意 cut2 返回的结果类型其实是 factor
	
#### Creating factor variables

	factor(restData$zipCode)
	
	yesno <- sample(c("yes","no"),size=10,replace=TRUE)
	yesnofac <- factor(yesno,levels=c("yes","no")) ## 默认情况下，levels 按字母顺序排列，所以 no 是 level#1，yes 是 level#2。这里指定 levels=c("yes", "no") 的话，那 yes 就是 level#1，no 是 level#2
	relevel(yesnofac,ref="yes") ## not necessary here. relevel 的作用是把 ref 提到 level#1，ref 之前的 levels 顺序往后挪一位。factor 本身的元素顺序并不受影响
	 [1] yes yes yes yes no  yes yes yes no  no 
	Levels: yes no
	
	as.numeric(yesnofac) ## yes 是 level#1，所以转成了 1；no 是 level#2，所以转成了 2
	 [1] 1 1 1 1 2 1 1 1 2 2
	
#### Using the mutate function

	library(Hmisc); 
	library(plyr);
	restData2 <- mutate(restData,zipGroups=cut2(zipCode,g=4)) ## 作用是添加了一个名为 zipCode 的 column，值为 zipCode 所属的分组区间 
	
	table(restData2$zipGroups)

	[-21226,21205) [ 21205,21220) [ 21220,21227) [ 21227,21287] 
			   338            375            300            314 
	
#### Common transforms

	abs(x): absolute value
	sqrt(x): square root
	ceiling(x): ceiling(3.475) is 4
	floor(x): floor(3.475) is 3
	round(x,digits=n): roun(3.475,digits=2) is 3.48
	signif(x,digits=n): signif(3.475,digits=2) is 3.5
	cos(x), sin(x) etc.
	log(x): natural logarithm
	log2(x), log10(x): other common logs
	exp(x): exponentiating x
	
----

## Reshaping Data

	The goal is tidy data:

	* Each variable forms a column
	* Each observation forms a row
	* Each table/file stores data about one kind of observation (e.g. people/hospitals).
	
	
	rownames(df) == dimnames(df)[[1]]
	colnames(df) == dimnames(df)[[2]]
	
	
	
#### Melting data frames


	mtcars$carname <- rownames(mtcars) ## 首先我们给 mtcars 加一个 column，后续用它来演示
	
	library(reshape2)
	
	[Reshaping data with the reshape package](http://had.co.nz/reshape/introduction.pdf)
	
	首先要澄清一点，cbind 多个 vector 产生的是 matrix 而不是 data frame，之所以强调这一点是因为 melt 是一个 generic function，它会根据实际的参数类型来确定具体调用哪个 melt 方法：
	
    `melt.data.frame` for data.frames
    `melt.array` for arrays, matrices and tables
    `melt.list` for lists

	根据 [Creating a data frame from two vectors using cbind](http://stackoverflow.com/questions/12787551/creating-a-data-frame-from-two-vectors-using-cbind)：
	* cbind(A=1:3, B=4:6) 是 matrix
	* data.frame(A=1:3, B=4:6) 是 df，
	* cbind.data.frame(A=1:3, B=4:6) 是 df
	* cbind(df, C=7:9) 是 df
	
	我们先看 melt(df)
	
	> df <- data.frame(A=1:3, B=4:6, C=7:9, D=10:12, E=13:15)
	> df
	  A B C  D  E
	1 1 4 7 10 13
	2 2 5 8 11 14
	3 3 6 9 12 15
	> melt(df, id=c("A"), measure.vars=c("B", "C"))
	  A variable value
	1 1        B     4
	2 2        B     5
	3 3        B     6
	4 1        C     7
	5 2        C     8
	6 3        C     9


	可见 id 和 measure.vars 构成了 molten data frame 的 column。然后 id 那一列是直接照搬的，measure.vars 的 (B, C) 变成了 (variable, value), variable 的取值是 c("B", "C")，value 是对应的元素值，比如 B = 4 就拆成了 (variable="B", value=4)。不管 measure.vars 有多少个，都是按照这个路数来变
	
	现在看下 matrix 的情况：
	
	> df <- cbind(A=1:3, B=4:6, C=7:9, D=10:12, E=13:15)
	> df
		 A B C  D  E
	[1,] 1 4 7 10 13
	[2,] 2 5 8 11 14
	[3,] 3 6 9 12 15
	> melt(df)
	   Var1 Var2 value
	1     1    A     1
	2     2    A     2
	3     3    A     3
	4     1    B     4
	5     2    B     5
	6     3    B     6
	7     1    C     7
	8     2    C     8
	9     3    C     9
	10    1    D    10
	11    2    D    11
	12    3    D    12
	13    1    E    13
	14    2    E    14
	15    3    E    15

	不管你是否指定 id、measure.vars，都是这个结果。Var1 是 rowname，如果没有 rowname 就是 row#；Var2 是 colname；value 就是具体的元素值
	
#### Casting data frames

	cast 就是把 melt 的结果重新拼回来，分为 acast 和 dcast 两种，功能和用法是一样的，只是返回不同
	* dcast 返回 data frame
	* acast 返回 array (matrix)
	
	先看 df 的情况：
	> molten <- melt(df, id=c("A"), measure.vars=c("B", "C"))
	> molten
	  A variable value
	1 1        B     4
	2 2        B     5
	3 3        B     6
	4 1        C     7
	5 2        C     8
	6 3        C     9
	
	> dcast(molten, A ~ variable)
	  A B C
	1 1 4 7
	2 2 5 8
	3 3 6 9

	matrix 的 formula 我还不会写，有时间可以研究下
	
	这个 X ~ Y 可以形象地理解一下：还是以这个为例：
	
	> molten
	  A variable value
	1 1        B     4
	2 2        B     5
	3 3        B     6
	4 1        C     7
	5 2        C     8
	6 3        C     9
	
	我们要 A ~ variable，于是 A 的值保持纵向排列，合并相同值（变成 1, 2, 3），varibale 的值改成横向排列，同样合并相同值（变成 B, C），然后把 value 填到这个新表里
	
	如果是 A ~ variable + value，那横向排列的就是 varible_value

	> dcast(molten, A ~ variable + value, mean)
	  A B_4 B_5 B_6 C_7 C_8 C_9
	1 1   4 NaN NaN   7 NaN NaN
	2 2 NaN   5 NaN NaN   8 NaN
	3 3 NaN NaN   6 NaN NaN   9

#### Averaging values

	head(InsectSprays)
	  count spray
	1    10     A
	2     7     A
	3    20     A
	4    14     A
	5    14     A
	6    12     A
	tapply(InsectSprays$count,InsectSprays$spray,sum) ## sum(InsectSprays$count) for every InsectSprays$spray category；有点先 aggregate 再 sum 的意味
	  A   B   C   D   E   F 
	174 184  25  59  42 200 
	
	
	Another way - split
	
	s <- split(InsectSprays$count,InsectSprays$spray)
	
	$A
	 [1] 10  7 20 14 14 12 10 23 17 20 14 13

	$B
	 [1] 11 17 21 11 16 14 17 17 19 21  7 13

	$C
	 [1] 0 1 7 2 3 1 2 1 3 0 1 4

	$D
	 [1]  3  5 12  6  4  3  5  5  5  5  2  4

	$E
	 [1] 3 5 3 5 3 6 1 1 3 2 6 4
	
	
	Another way - apply
	
	lapply(s, sum)
	
	$A
	[1] 174

	$B
	[1] 184

	$C
	[1] 25

	$D
	[1] 59

	$E
	[1] 42
	
	sapply(s,sum)
	  A   B   C   D   E   F 
	174 184  25  59  42 200 
	
	
	Another way - combine
	
	unlist(lapply(s, sum)) ## to convert a list to vector (with column name)
	  A   B   C   D   E   F 
	174 184  25  59  42 200 
	
	
	
	library(plyr)
	ddply(InsectSprays,.(spray),summarize,sum=sum(count)) ## summarize 的作用类似 mutate；.(spray) 表示 summarize on  InsectSprays$spray，然后加一个 column sum
	
	  spray sum
	1     A 174
	2     B 184
	3     C  25
	4     D  59
	5     E  42
	6     F 200
	
#### Creating a new variable

	spraySums <- ddply(InsectSprays,.(spray),summarize,sum=ave(count,FUN=sum)) ## ave 的作用是在指定的列（或者列的 subset）上执行 FUN（默认是 mean），然后将计算结果附到该列的每个值的后面；也是类似先 aggregate 再 FUN 的作用，不过附带了 “将计算结果附到该列的每个值的后面” 这个效果
	
	head(spraySums)
	  spray sum
	1     A 174
	2     A 174
	3     A 174
	4     A 174
	5     A 174
	6     A 174
	
-----

## Merging Data

	mergedData = merge(reviews, solutions, by.x="solution_id", by.y="id", all=TRUE)
	
	这个学过数据库的应该很了解了
	
	all=TRUE 表示 all.x=TRUE & all.y=TRUE
	
	all.x=TRUE 表示 x 中的所有 row （包括不匹配的）都会进入 merge 后的表，对应的 y 的 column 值被填成 NA。其实就是 Lefe Out Join
	all.y=TRUE 就是 Right Out Join
	all=TRUE 就是 Full Outer Join
	
	参 [join](http://erikyao.github.io/sql/2010/04/03/sql-join/)
	
	不指定 by.x 或者 by.y 的话，merge all common column names
	
#### Using join in the plyr package

	Faster, but less full featured - defaults to left join, see help file for more

	df1 = data.frame(id=sample(1:10),x=rnorm(10))
	df2 = data.frame(id=sample(1:10),y=rnorm(10))
	arrange(join(df1,df2),id)
	   id       x       y
	1   1  0.2514  0.2286
	2   2  0.1048  0.8395
	3   3 -0.1230 -1.1165
	4   4  1.5057 -0.1121
	5   5 -0.2505  1.2124
	6   6  0.4699 -1.6038
	7   7  0.4627 -0.8060
	8   8 -1.2629 -1.2848
	9   9 -0.9258 -0.8276
	10 10  2.8065  0.5794
	
#### If you have multiple data frames

	df1 = data.frame(id=sample(1:10),x=rnorm(10))
	df2 = data.frame(id=sample(1:10),y=rnorm(10))
	df3 = data.frame(id=sample(1:10),z=rnorm(10))
	dfList = list(df1,df2,df3)
	join_all(dfList)
	   id        x        y        z
	1   6  0.39093 -0.16670  0.56523
	2   1 -1.90467  0.43811 -0.37449
	3   7 -1.48798 -0.85497 -0.69209
	4  10 -2.59440  0.39591 -0.36134
	5   3 -0.08539  0.08053  1.01247
	6   4 -1.63165 -0.13158  0.21927
	7   5 -0.50594  0.24256 -0.44003
	8   9 -0.85062 -2.08066 -0.96950
	9   2 -0.63767 -0.10069  0.09002
	10  8  1.20439  1.29138 -0.88586
	
-----

## Q1

	csv <- read.csv("getdata-data-ss06hid.csv", header=TRUE)
	
	agriculture <- (csv$ACR==3) & (csv$AGS==6)
	
	> which(agriculture)[1:3]
	[1] 125 238 262
	> csv[c(125, 238, 262), c("ACR", "AGS")]
		ACR AGS
	125   3   6
	238   3   6
	262   3   6

## Q2

	jpg <- readJPEG("getdata-jeff.jpg", native=TRUE)
	
	> quantile(jpg, probs=c(0.3, 0.8))
		  30%       80% 
	-15259150 -10575416
	
## Q3

	ed <- read.csv("getdata-data-EDSTATS_Country.csv", header=TRUE)

	## 这个 csv 后面有很多 empty column (连续多个逗号在行尾)，所以与其指定 colClasses=c(..., "NULL", ...) 去 skip column，不如直接全部读进来在 extract column 来得方便
	## 然后 read.csv 默认 header=TRUE 的
	## 195 行之后的数据应该用不上，内容也与前面行不一致
	gdp <- read.csv("getdata-data-GDP.csv", header=FALSE, skip=5, nrows=190)
	gdp <- data.frame(gdp$V1, gdp$V2, gdp$V4, gdp$V5)
	colnames(gdp) <- c("Country", "Ranking", "Economy", "MillionsUSD")
	
	merged <- merge(ed, gdp, by.x="CountryCode", by.y="Country")
	ordered <- merged[order(merged$Ranking, decreasing=TRUE), ]
	ordered[, c("Long.Name", "Ranking")][13,]

## Q4

	ig <- ordered[, c("Long.Name", "Ranking", "Income.Group")]
	igSplit <- split(ig$Ranking, ig$Income.Group)
	sapply(igSplit,mean)
	
### Q5 

	rankingGroup <- cut(ordered$Ranking, breaks=quantile(ordered$Ranking, seq(0, 1, 0.2)))
	table(ordered$Income.Group, rankingGroup)


	
	