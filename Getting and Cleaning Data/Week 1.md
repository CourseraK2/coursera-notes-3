## contents

* Obtaining Data Motivation
* Raw and Processed Data
* Components of Tidy Data
* Downloading Files
* Reading Local Files
* Reading Excel Files
* Reading XML
* Reading JSON
* The data.table Package

-----

## Obtaining Data Motivation

	Json 发音 J-son
	
	Raw Data -> Processing script -> tidy data -> data anlysis -> data commmunication
	
	本课程 cover 前三个 phrases
	
-----
	
## Raw and Processed Data
	
	Data：
		* Qualitative: country of origin, sex, treatment
		* Quantitative: Height, weight, bllod pressure
	
	ALL process step should be recorded
	
-----

## Components of Tidy Data

-----

## Downloading Files

	dir.create("foo")

	fileUrl <- "http://foo/bar"
	download.file(fileUrl, destfile="xxx.csv", method="curl")
	
-----

## Reading Excel Files
	
	library(xlsx)
	read.xlsx("foo.xlsx", sheetIndex=1, header=TRUE)
	
	read.xlsx("foo.xlsx", sheetIndex=1, colIndex=1:3, rowIndex=2:4)
	
	write.xlsx
	
	read.xlsx2(): faster than read.xlsx, but for reading subsets of rows may be slightly unstable
	
	XLConnect package
	XLConnect vignette
	
## Reading XML	
	
	library(XML)
	doc <- xmlTreeParse("filepath", useInternalNode=TRUE)
	rootNode <- xmlRoot(doc)
	
	xmlName(rootNode)
	names(rootnode)
	
	rootNode[[1]]
	rootNode[[1]][[1]]
	
	xmlSApply(rootNode, func)
	
	node[@attr-name] ## xpath
	
	xpathSApply(rootNode, '//price', xmlValue)
	
	htmlTreeParse
	
	对于结构完整的 html，也可以用 xpath 来处理
	
-----

## Reading JSON
	
	library(jsonlite)
	jsonData <- fromJSON("http://foo/bar") ## get a data.frame
	
	names(jsonData)
	names(jsonData$owner)
	
	myjson <- toJSON(iris, pretty=TRUE) ## iris is a data.frame; convert df to json
	cat(myjson)
	
	iris2 <- fromJSON(myjson) ## convert json to df
	
-----

## The data.table Package

	Inherets from data.frame
	All functions that accept data.frame work on data.table
	Much faster at subsetting, grouping and updating
	
	library(data.table)
	
	tables(): see all the tables in memory
	
	DT[DT$y='a',] 
	
	DT[2] 或者 DT[2,] ## 抽取第二行
	DT[c(2,3)] 或者 DT[c(2,3),] ## 抽取 2、3 行
	DT 的第二维是没有用来取元素的，DT[,y] 取不到 column，DT[x, y] 也取不到具体的元素
	 
	但是 DT 的第二维可以直接用来构造，比如
	DT[,mean(DT$x)]，此时第二维仍然和 column 的 index 是无关的
	
	DT[,w:=z^2] 添加一个新 column w，值是 DT$z 的平方；
		* 注意这个操作可能会导致内存问题，因为 R 是把原 table copy 一份再添加新 column；
		* 这个操作直接影响 DT，不用重新赋值 DT <- DT[,w:=z^2]
		* 实际上这个操作是有返回值的，而且返回的是新 DT，如果 DT2 <- DT[,w:=z^2]，那么 DT == DT2， 都是加了 w 行的 table
	
	DT[,m:={temp <- x+z; log2(temp+5)}] 更复杂的添加 column 运算；{} 和 function 一样，也是返回最后一个表达式的值
	
	DT[, b:=mean(x+w), by=a] by 就是 group by，a 相同的 rows 算一组，在组内的 rows 上单独算 b := mean(x+w)
	
	.N is an integer, length 1, containing the number of rows in the group. It is renamed to N (no dot) in the result (otherwise a column called ".N" could conﬂict with the .N variable
	
	setkey(DT, x) ## set DT$x as the key
	然后 DT['a'] ## 等价于 DT[DT$x='a',]
	
	setKey(DT1, x); setKey(DT2, x);
	merge(DT1, DT2); ## key 相同的 merge 到一起
	
	fread: fast read

-----

Q1: val = na.omit(csv$VAL)
	length(val[val>=24])
Q3: dat = read.xlsx("getdata-data-DATA.gov_NGAP.xlsx", sheetIndex=1, header=TRUE, colIndex=7:15, rowIndex=18:23)
Q4: xml <- xmlTreeParse("getdata-data-restaurants.xml", useInternalNode=TRUE)
	rootNode <- xmlRoot(xml)
	zipcodes <- xpathSApply(rootNode, '//zipcode', xmlValue)
	length(zipcodes[zipcodes == "21231"])

	
	
	
	
	
	