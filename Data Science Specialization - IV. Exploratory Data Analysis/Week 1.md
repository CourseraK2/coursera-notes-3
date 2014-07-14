## Introduction
	
	从 quiz 来看，感觉这一课程的目的是 “全力画图，寻找模型”？

-----
	
## Principles of Analytic Graphics

	a book <<beatiful evidence>>

	Prcp 1. Show comparisons
		 2. Show causality, mechanism, explanation, systematic structure
		 3. Show multivariate data
	
	Simpson's paradox: In probability and statistics, Simpson's paradox, or the Yule–Simpson effect, is a paradox in which a trend that appears in different groups of data disappears when these groups are combined, and the reverse trend appears for the aggregate data. 比如四个季度都是 positively linear，年度的反而是 negatively linear
	
		 4. Integration of evidence
		 5. Describe and document the evidence with appropriate labels, scales, sources, etc.
		 6. Content is king
	
-----

## Exploratory Graphs (part 1)	
	
	One dimension plot:
		* Five-number summary: summary(df$x)
		* Boxplots: boxplot(df$x, col="blue"); abline(h=12) h for horizontal
		* Histograms: hist(df$x, col="blue"); rug(df$x); abline(v=12) v for vertical
		* Density plot
		* Barplot: barplot(table(df$x))
	
## Exploratory Graphs (part 2)	
	
	2 dimension plot:
		* Multiple/overlayed 1-D plots(Lattice/ggplot2)
		* Scatterplots：散点图
		* Smooth Scatterplots
		
		boxplot(a ~ b, data=xxx)
		with(xxx, plot(x, y))
		
		
	>2 dimension plot:
		* Multiple/Overlayed 2-D plots; coplots
		* Use color, size, shape to add dimensions
		* Spinning plots
		* Actual 3-D plots (not that useful)
	
-----
	
## Plotting Systems in R
	
	1. Bsse Plotting System: 原始的点线面，一般是先 plot，然后再改 label 之类的，所以画一个图可能有多条 R 语句
	
	library(datasets)
	data(cars)
	with(cars, plot(speed, dist))
	
	2. Lattice (格栅) System：一行命令搞定，但是相应的细节参数会很多；不能后续再修改 plot，不如上面一种灵活
	
	xyplot, bwplot
	
	Most useful for Co-plots or Conditioning Plots: Looking at how y changes with x across levels of z （偏导数？）
	
	Good for putting many many plots on a screen
	
	library(lattice)
	state <- data.frame(state.x77, region=state.region)
	xyplot(Life.Exp ~ Income | region, data=state, layout=c(4, 1)) ## x轴是 Income，y轴是 Life.Exp 第一象限（数值都是正数所以只有第一象限）按 region 分割成 4x1 的 grids 
	
	3. ggplot2 System：有很多默认参数，plot 之后可以加新命令来修改
	
	library(ggplot2)
	data(mpg)
	gplot(displ, hwy, data=mpg)
	
-----
	
## Base Plotting System (part 1)
	
	Initialize a new plot
	Annotate the plot
	
	Device: 指图像输出的位置，一般我们就是把图像输出到一个新 window，也可以输出到 browser 之类的
	
	par()：can be used to set or query graphical parameters.
	
	library(datasets)
	hist(airquality$Ozone)
	
	with(airquality, plot(Wind, Ozone)) ## 散点图
	
	airquality <- transform(airquality, Month=factor(month))
	boxplot(Ozone ~ Month, airquality, xlab="Month", ylab="Ozone (ppb)")
	
	
	pch: ploting symbol (default is open circle)
	lty: lint type (default is solid line), can be dash, dot etc
	lwd: line width
	col: line color; the colors() function will give you a vector of colors by name, a big help
	xlab:
	ylab:
	
	
	par() 的 parameter：
	
	las: style of axis labels，比如是水平排列还是垂直排列
	bg: background color
	mar: margin size
	oma: outer margin size (default is 0)
	mfrow: 比如 mfrow = c(2,2) 就是2x2共4个格子，4个图按行顺序填完
	mfcol：和 mfrow 一样，不过是按列顺序填完
	
	par("lty")
	par("mar") => x, y, z, w 按顺序分别是 bottom, left, top, right margin（顺时针）
	
## Base Plotting System (part 2)

	plot(): make a scatterplot，我们常见的直线其实也算是散点图
	
	lines(): add lines to a plot
	
	points(): add points to a plot
	
	text(): add text labels for a point (x, y)
	
	title(): add annotations to x, y axis labels, plot title, subtitle, outer margin
	
	mtext: add text to the margins
	
	axis: add axis
	
	library(datasets)
	with(airquality, plot(Wind, Ozone))
	title(main = "Ozone and Wind in New York City")  ## Add a title
	
	
	with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City"))
	with(subset(airquality, Month == 5), points(Wind, Ozone, col = "blue")) ## 额外将 5 月的数据用蓝色的点画出来
	
	
	with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City", type = "n"))
	with(subset(airquality, Month == 5), points(Wind, Ozone, col = "blue")) ## 5 月的用蓝点
	with(subset(airquality, Month != 5), points(Wind, Ozone, col = "red")) ## 非 5 月的用红点
	legend("topright", pch = 1, col = c("blue", "red"), legend = c("May", "Other Months")) ## 右上角画图例
	
	
	Base Plot with Regression Line：
	
	with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City", pch = 20))
	model <- lm(Ozone ~ Wind, airquality)
	abline(model, lwd = 2) ## 添加 regression line
	
	
	
	par(mfrow = c(1, 2))
	with(airquality, {
		plot(Wind, Ozone, main = "Ozone and Wind")
		plot(Solar.R, Ozone, main = "Ozone and Solar Radiation")
	}) ## 一个 window 里画俩图
	
-----
	
## Base Plotting Demonstration

	example(points): 显示 pch 等和点形状相关的用法示例
	
	legend 的四个位置：
		* bottomleft
		* topleft
		* topright
		* bottomright
	
	x <- rnorm(100)
	y <- rnorm(100)
	g <- gl(2, 50, labels=c("foo", "bar"))
	plot(x, y, type="n") # 出图但是不显示点
	points(x[g == "foo"], y[g == "bar"], col="green")
	points(x[g == "bar"], y[g == "foo"], col="blue")
	
-----

## Graphics Devices in R (part 1)

	Windows: Windows
	Mac: Quartz
	Linux: X11

	?Devices
	
## Graphics Devices in R (part 1)	
	
	dev.cur(): show currently active graphics device
	
	dev.copy(): copy to another device
	dev.copy2pdf(): 
	
	dev.copy(png, file="myplot.png")
	dev.off() ## Don't forget this!
	
-----

	
	
	
	
	