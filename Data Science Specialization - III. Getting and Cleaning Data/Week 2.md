## contents

* Reading from MySQL 
* Reading from HDF5
* Reading from The Web
* Reading From APIs
* Reading From Other Sources

-----

## Reading from MySQL 

	install.packages("RMySQL") 有点麻烦，参考：
		Official instructions - http://biostat.mc.vanderbilt.edu/wiki/Main/RMySQL (may be useful for Mac/UNIX users as well)
		Potentially useful guide - http://www.ahschulz.de/2013/07/23/installing-rmysql-under-windows/

-----
		
## Reading from HDF5

	Hierarchical Data Format
	
	.h5 file extension
	
-----
		
## Reading from The Web
	
	web scraping
	
	way 1：使用 XML package 来分析 html
	way 2：httr package
	
-----

## Reading From APIs

	use httr package
	
	oauth_app
	
	jsonlite::fromJSON(toJSON(content)) ## create a data.frame
	
-----

## Reading From Other Sources

	gzfile
	bzfile
	
	read.arff
	read.dta
	read.mtp
	read.octave
	read.spss
	read.xport
	
	jpeg
	readbitmap
	png
	
	mongodb
	access
	
	tuneR
	seewave
	
	The `sqldf` package allows for execution of SQL commands on R data frames.
	sqldf("select * from df where x < 50")
	
-----

## Quiz 1

	for twitter api: additional httpuv package required
	
	callback url use "http://localhost:1410", do not follow the github help on "https://developer.github.com/guides/basics-of-authentication/", use "http://stackoverflow.com/questions/13169305/httr-github-api-callback-url-issues" instead
	
	library(httr)
	oauth_endpoints("github")
	myapp <- oauth_app("[You App Name]", "[Client ID]", "[Client Secret]")
	github_token <- oauth2.0_token(oauth_endpoints("github"), myapp) ## this will send a request of "http://localhost:1410" (your callback url) to your browser
	gtoken <- config(token = github_token) 
	req <- GET("[Your Dest Url]", gtoken)
	stop_for_status(req)
	response <- content(req) ## require jsonlite
	response[[1]]$created_at
	
	library(jsonlite)
	json <- fromJSON(toJSON(response))
	json[json$name=="datasharing", ]$created_at
	
	Why localhost:1410?
	
	oauth2.0_token 会先跳到这个 url：
	https://github.com/login/oauth/authorize?client%5Fid=[Client ID]&scope=&redirect%5Furi=[Your Callback Url]&response%5Ftype=code&state=sUx4az7S1z
	
	然后 redirect 到 callback url 验证，而 httr creates a web server on port 1410 to catch the redirection request and the token that comes with it，所以必须是 1410。（参考 https://github.com/cscheid/rgithub 的 README.md）
	
## Quiz 4

	con <- url("http://biostat.jhsph.edu/~jleek/contact.html")
	htmlcode <- readLines(con) ## 注意可能会被墙，goagent 要开 IE 代理
	close(con)
	
	nchar(htmlcode[[10]])

## Quiz 5

	data <- read.fwf("getdata-wksst8110.for", c(-1, 9, -5, 4, 4, -5, 4, 4, -5, 4, 4, -5, 4, 4), skip=4)
	sum(data$V4)

	注意事项：
	* 感觉 read.fwf 比 read.fortran 好用，虽然 .for 是 fortran 文件，但是这里是个 table 文件，是 Fixed Width File
	* -n 表示跳过 n 个 character
	* 每行开头有个空格，太阴了，用 -1 skip 掉
	* 我连中间所有的 spaces 都用 -n 处理掉了，缺省的 sep="\t" 根本没起作用，但是你指定 sep="" 又会出问题（空格会被读到），不知道为何，不写就好了
	* 不指定 column name 的话就自动命名成 V1 V2 这样