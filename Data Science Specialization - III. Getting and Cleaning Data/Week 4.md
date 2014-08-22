## Editing Text Variables

	这应该就是讲 String 操作了
	
	tolower(c("A", "B", "C"))
	
	一上来就 tolower(names(df)) 是个不错的主意
	
	
	
	> splitName <- strsplit(c("A", "B", "Location.1"), "\\.")
	[[1]]
	[1] "A"

	[[2]]
	[1] "B"

	[[3]]
	[1] "Location" "1" 
	
	> sapply(splitNames, function(x) { x[1] })
	[1] "A"        "B"        "Location"

	这样就把 "Location.1" 变成了 "Location"
	
	
	
	> sub("_", "", c("A", "B", "C_1")) ## sub for substitute
	[1] "A"  "B"  "C1"

	> sub("_", "", c("A", "B", "C_1_1")) ## 但是 sub 只能替换第一个遇到的 _
	[1] "A"    "B"    "C1_1"

	> gsub("_", "", c("A", "B", "C_1_1")) ## 使用 gsub 替换全部 _
	[1] "A"   "B"   "C11"

	
	
	> names <- c("x", "y", "foo", "bar")
	> grep("foo", names) ## 返回元素 element
	[1] 3
	> grepl("foo", names)
	[1] FALSE FALSE  TRUE FALSE
	> table(grepl("foo", names))

	FALSE  TRUE 
		3     1 

	names[!grepl("foo", names), ]
	
	
	> names <- c("x", "y", "foo12", "bar34")
	> grep("foo", names, value=TRUE) ## 匹配并显示匹配的值
	[1] "foo12"

	
	> grep("z", names)
	integer(0)
	> grep("z", names, value=TRUE)
	character(0)
	
	length(grep) == 0 可以断定没有匹配 
	
	
	
	
	nchar("foo") ## 3 characters
	substr("foobar", 2, 5) ## "ooba", [2, 5]
	paste("foo", "bar") ## "foo" + "bar" == "foo bar"，主意中间会自带一个空格
	paste0("foo", "bar") ## "foobar" 不带空格
	
	library(stringr) 
	str_trim("foo   ") ## String.trim()
	
	
#### important points about text in data sets

	* Names of variables should be
		* All lower case when possible
		* Descriptive (Diagnosis versus Dx)
		* Not duplicated
		* Not have underscores or dots or white spaces
	* Variables with character values
		* Should usually be made into factor variables (depends on application)
		* Should be descriptive (use TRUE/FALSE instead of 0/1 and Male/Female versus 0/1 or M/F)
		
-----

## Regular Expressions

	常用于 grep, grepl, sub, gsub

	Regular Expression has 2 components:
	* literals: keywords，一个词，比如 "foo"
	* metacharacters
		* whitespace word boundaries
		* sets of literals
		* the beginning and end of a line
		* alternatives ("foo" or "bar")
	
	
	Simplest pattern consists only of literals; a match occurs if the sequence of literals occurs anywhere in the text being tested
	
	
	^i think: match lines starting with "i think"
	
	morning$: match lines ending with "morning" or we can say $ represents the end of a line
	
	[Ff][Oo][Oo]: 匹配 FOO, FOo, FoO, Foo, fOO, fOo, foO, foo；主意这种形式是可以匹配 xxFOOzz 这样字符串的，因为我们并没有指定 [Ff][Oo][Oo] 是匹配一个完整的词
	
	
	^[Ii] am
	
	^[0-9][a-zA-Z]: 匹配两位，比如 7th 里的 7t
	
	[^?.]$: anything other than ? or . at the end of a line；注意这个语法，[?.] 表示 ? or .，前面加个 ^ 就是 not ? or .
	
## Regular Expressions II

	"." is used to refer to any single character
	
	比如 3.21 可以匹配 3-21, 3/21, 203.219, 03:21, 3321 等
	
	
	flood|fire: flood or fire, 比如 firewire, floods
	
	
	^[Gg]ood|[Bb]ad: 注意 ^ 只作用于 [Gg]ood
	^([Gg]ood|[Bb]ad): 这样 ^ 就同时起作用了
	
	
	[Gg]eorge( [Ww]\.)? [Bb]ush: ? indicates that the indicated expression is optional，可以匹配 George W. Bush, George Bush 等。\. 是转义
	
	
	(.*): 匹配括号内的任意多个字符
	
	
	[0-9]+: 任意一个或多个数字
	
	
	[Bb]ush( +[^ ]+ +){1,5} debate: 首先看中间那个括号，+个空格，然后+个非空格，再+个空格。然后这整个括号的结构可以重复 1-5 次。简单说就是 bush 和 debate 之间可以有 1-5 个单词，这 1-5 个单词之间可以有+个空格
	
	{5}: 表示 exactly 重复 5 次
	{1,}: 表示重复 at least 1 次

	 +([a-zA-Z]+) +\1+: +个空格，接着+个字母，再+个空格，然后 \1 表示与前面括号内的匹配内容一样（这种用法仅限于引用括号的匹配内容），也是+个。简单说就这就用来匹配两个相同的单词的，比如 " foo foo"
	
	
	
	The * is “greedy” so it always matches the longest possible string that satisfies the regular expression. 

	比如 ^s(.*)s 会匹配整个 "sitting at starbucks"，而不是 "sitting at s"
	
	The greediness of * can be turned off with the ?, as in ^s(.*?)s$
	
------

## Working with Dates

	class(date()) 其实是 "character"
	class(Sys.Date()) 才是 "Date"
	
	d1 = date()
	d1
	[1] "Sun Jan 12 17:48:33 2014"
	
	d2 = Sys.Date()
	d2
	[1] "2014-01-12"
	
	
	%d = day as number (0-31) 
	%a = abbreviated weekday 
	%A = unabbreviated weekday 
	%m = month (00-12) 
	%b = abbreviated month 
	%B = unabbrevidated month 
	%y = 2 digit year, 
	%Y = four digit year

	format(d2,"%a %b %d")
	[1] "Sun Jan 12"

	
	x = c("1jan1960", "2jan1960", "31mar1960", "30jul1960")
	z = as.Date(x, "%d%b%Y")
	z
	[1] "1960-01-01" "1960-01-02" "1960-03-31" "1960-07-30"
	
	z[1] - z[2]
	Time difference of -1 days
	
	as.numeric(z[1]-z[2])
	[1] -1
	
	
	weekdays(d2)
	[1] "Sunday"
	months(d2)
	[1] "January"
	
	julian(d2) ## days since 1970-01-01
	[1] 16082
	attr(julian(d2),"origin")
	[1] "1970-01-01"
	
	
	library(lubridate); 
	ymd("20140108")
	[1] "2014-01-08 UTC"
	mdy("08/04/2013")
	[1] "2013-08-04 UTC"
	dmy("03-04-2013")
	[1] "2013-04-03 UTC"
	
	
	ymd_hms("2011-08-03 10:15:03")
	[1] "2011-08-03 10:15:03 UTC"
	ymd_hms("2011-08-03 10:15:03",tz="Pacific/Auckland")
	[1] "2011-08-03 10:15:03 NZST"

	
	?Sys.timezone
	
	
	x = dmy(c("1jan2013", "2jan2013", "31mar2013", "30jul2013"))
	wday(x[1])
	[1] 3
	wday(x[1],label=TRUE)
	[1] Tues
	Levels: Sun < Mon < Tues < Wed < Thurs < Fri < Sat
	
-----

## Data Resources

	介绍了一些可以找到 Data 的站点
	
-----

## Q1

	csv <- read.csv("getdata-data-ss06hid.csv")
	splitNames <- strsplit(names(csv), "wgtp")
	
## Q2
	
	## 同 week 3 的 Q2
	## 加了一句 stringsAsFactors=FALSE
	## 因为 MillionsUSD 有逗号和空格，直接 as.numeric 不好转，直接当做 String 来操作，免得 factor 来生事
	gdp <- read.csv("getdata-data-GDP.csv", header=FALSE, skip=5, nrows=190, stringsAsFactors=FALSE)
	gdp <- data.frame(gdp$V1, gdp$V2, gdp$V4, gdp$V5, stringsAsFactors=FALSE)
	colnames(gdp) <- c("Country", "Ranking", "Economy", "MillionsUSD")
	
	gdp$MillionsUSD <- gsub(" |,", "", gdp$MillionsUSD)
	gdp$MillionsUSD <- as.numeric(gdp$MillionsUSD)
	mean(gdp$MillionsUSD)

## Q3

	grep("^United", gdp$Economy)
	
## Q4

	ed <- read.csv("getdata-data-EDSTATS_Country.csv", header=TRUE)
	
	merged <- merge(ed, gdp, by.x="CountryCode", by.y="Country")
	
	fye <- grep("Fiscal year end", merged$Special.Notes, value=TRUE)
	length(grep("June", fye))

## Q5 

	library(quantmod)
	amzn = getSymbols("AMZN",auto.assign=FALSE)
	sampleTimes = index(amzn) 

	length(sampleTimes[format(sampleTimes, "%Y")=="2012"])

	length(sampleTimes[format(sampleTimes, "%Y %A")=="2012 星期一"])

	
	
	