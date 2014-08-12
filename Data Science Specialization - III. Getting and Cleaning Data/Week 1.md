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

	The four things you should have
	* The raw data.
	* A tidy data set
	* A code book describing each variable and its values in the tidy data set.
	* An explicit and exact recipe you used to go from 1 -> 2,3.

	The tidy data
	* Each variable you measure should be in one column
	* Each different observation of that variable should be in a different row
	* There should be one table for each "kind" of variable
	* If you have multiple tables, they should include a column in the table that allows them to be linked

	Some other important tips
	* Include a row at the top of each file with variable names.
	* Make variable names human readable AgeAtDiagnosis instead of AgeDx
	* In general data should be saved in one file per table.
	
	The instruction list
	* Ideally a computer script (in R :-), but I suppose Python is ok too...)
	* The input for the script is the raw data
	* The output is the processed, tidy data
	* There are no parameters to the script

	In some cases it will not be possible to script every step. In that case you should provide instructions like:
	* Step 1 - take the raw file, run version 3.1.2 of summarize software with parameters a=1, b=2, c=3
	* Step 2 - run the software separately for each sample
	* Step 3 - take column three of outputfile.out for each sample and that is the corresponding row in the output data set
	
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

## Q1

csv <- read.csv("getdata-data-ss06hid.csv")
val = na.omit(csv$VAL)  
length(val[val>=24])  

A: 53

## Q2

A: "Tidy data has one variable per column"

## Q3

library(xlsx)
dat = read.xlsx("getdata-data-DATA.gov_NGAP.xlsx", sheetIndex=1, header=TRUE, colIndex=7:15, rowIndex=18:23)
sum(dat$Zip*dat$Ext,na.rm=T) 

A: 36534720

## Q4

library(XML)
xml <- xmlTreeParse("getdata-data-restaurants.xml", useInternalNode=TRUE)
rootNode <- xmlRoot(xml)
zipcodes <- xpathSApply(rootNode, '//zipcode', xmlValue)
length(zipcodes[zipcodes == "21231"])

A: 127

## Q5

A: DT[,mean(pwgtp15),by=SEX]
	
-----

部分整理，未完成：

## <a name="ch2"></a>2. Reading Data

### Downloading Files

<pre class="prettyprint linenums">
if (!file.exists("data")) { ## check to see if the directory exists
    dir.create("data") ## create a directory if it doesn't exist
}
</pre>

<pre class="prettyprint linenums">
fileUrl <- "https://data.baltimorecity.gov/api/views/dz54-2aru/rows.csv?accessType=DOWNLOAD"
download.file(fileUrl, destfile = "./data/cameras.csv", method = "curl")
list.files("./data")
dateDownloaded <- date() ## Be sure to record when you downloaded.
</pre>

### Reading Excel Files

<pre class="prettyprint linenums">
if(!file.exists("data")){dir.create("data")}
fileUrl <- "https://data.baltimorecity.gov/api/views/dz54-2aru/rows.xlsx?accessType=DOWNLOAD"
download.file(fileUrl,destfile="./data/cameras.xlsx",method="curl")
dateDownloaded <- date()

library(xlsx)
cameraData <- read.xlsx("./data/cameras.xlsx",sheetIndex=1,header=TRUE)

colIndex <- 2:3
rowIndex <- 1:4
cameraDataSubset <- read.xlsx("./data/cameras.xlsx",sheetIndex=1, colIndex=colIndex,rowIndex=rowIndex)
</pre>

* The `write.xlsx` function will write out an Excel file with similar arguments.
* `read.xlsx2` is much faster than read.xlsx but for reading subsets of rows may be slightly unstable.
<!-- -->
* The `XLConnect` [package](http://cran.r-project.org/web/packages/XLConnect/index.html) has more options for writing and manipulating Excel files
* The `XLConnect` [vignette](http://cran.r-project.org/web/packages/XLConnect/vignettes/XLConnect.pdf) is a good place to start for that package

### Reading XML

<pre class="prettyprint linenums">
library(XML)
fileUrl <- "http://www.w3schools.com/xml/simple.xml"
doc <- xmlTreeParse(fileUrl,useInternal=TRUE)
rootNode <- xmlRoot(doc)

## 操作举例，这里就不显示结果了，自己去试吧
xmlName(rootNode)
names(rootNode)
rootNode[[1]]
rootNode[[1]][[1]]
xmlSApply(rootNode,xmlValue)
</pre>

还可以使用 xpath：

* `/node` 匹配 Top level node
* `//node` 匹配 Node at any level
* `node[@attr-name]` 匹配 Node with an attribute name
* `node[@attr-name='bob']` 匹配 Node with attribute name attr-name='bob'

<pre class="prettyprint linenums">
fileUrl <- "http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens"
doc <- htmlTreeParse(fileUrl,useInternal=TRUE)
scores <- xpathSApply(doc,"//li[@class='score']",xmlValue)
teams <- xpathSApply(doc,"//li[@class='team-name']",xmlValue)
</pre>

更多内容请参照：

* [Extracting data from XML](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)
* [An Introduction to the XML package for R](http://www.omegahat.org/RSXML/Tour.pdf)
* [A Short Introduction to the XML package for R](http://www.omegahat.org/RSXML/shortIntro.pdf)

### Reading JSON
### The `data.table` Package
### Reading from MySQL 
### Reading from HDF5
### Reading from The Web
### Reading From APIs
### Reading From Other Sources
	
	
	
	