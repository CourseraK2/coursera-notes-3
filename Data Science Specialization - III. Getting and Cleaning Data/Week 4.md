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
	
	
#### mportant points about text in data sets

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

	Regular Expression has 2 components:
	* literals: keywords，一个词，比如 "foo"
	* metacharacters
		* whitespace word boundaries
		* sets of literals
		* the beginning and end of a line
		* alternatives ("foo" or "bar")
	
	
	Simplest pattern consists only of literals; a match occurs if the sequence of literals occurs anywhere in the text being tested
	
	
	^i thing: match lines starting with "i think"
	
	morning$: match lines ending with "morning" or we can say $ represents the end of a line
	
	[Ff][Oo][Oo]: 匹配 FOO, FOo, FoO, Foo, fOO, fOo, foO, foo；主意这种形式是可以匹配 xxFOOzz 这样字符串的，因为我们并没有指定 [Ff][Oo][Oo] 是匹配一个完整的词
	
	
	^[Ii] am
	
	^[0-9][a-zA-Z]: 匹配两位，比如 7th 里的 7t
	
	[^?.]$: anything other than ? or . at the end of a line；注意这个语法，[?.] 表示 ? or .，前面加个 ^ 就是 not ? or .
	
## Regular Expressions II

	"." is used to refer to any single character
	
	比如 3.21 可以匹配 3-21, 3/21, 203.219, 03:21, 3321 等
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	