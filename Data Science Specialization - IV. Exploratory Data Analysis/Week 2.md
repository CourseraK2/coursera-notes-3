## Lattice Plotting System (part 1)

	适用于 high density plots
	
	library(lattice)
	library(datasets)
	## Simple scatterplot
	xyplot(Ozone ~ Wind, data = airquality)
	
	
	library(datasets)
	library(lattice)
	## Convert 'Month' to a factor variable
	airquality <- transform(airquality, Month = factor(Month))
	xyplot(Ozone ~ Wind | Month, data = airquality, layout = c(5, 1)) ## Ozone ~ Wind by month. 因为正好有 5 个 month，所以 layout 就定为 5x1 的 grid
	
	X ~ Y | f * g: by f and then by g，所以会有 size(f)*size(g) 个 grid
	
	
	The lattice system is ideal for creating conditioning plots where you examine the same kind of plot under many different conditions.
	
	
	Lattice functions behave differently from base graphics functions in one critical way.

	* Base graphics functions plot data directly to the graphics device (screen, PDF file, etc.)
	* Lattice graphics functions return an object of class *trellis* (latticework used to support climbing plants)
	* The `print` methods for lattice functions actually do the work of plotting the data on the graphics device.
	* Lattice functions return "plot objects" that can, in principle, be stored (but it’s usually better to just save the code + data).
	* On the command line, *trellis* objects are auto-printed so that it appears the function is plotting the data
	
	p <- plot(...)
	print(p)
	
## Lattice Plotting System (part 2)

	set.seed(10)
	x <- rnorm(100)
	f <- rep(0:1, each = 50)
	y <- x + f - f * x + rnorm(100, sd = 0.5)
	f <- factor(f, labels = c("Group 1", "Group 2"))
	xyplot(y ~ x | f, layout = c(2, 1))  ## Plot with 2 panels


	Panel functions can be specified/customized to modify what is plotted in each of the plot panels
	
	
	## Custom panel function
	xyplot(y ~ x | f, panel = function(x, y, ...) {
		panel.xyplot(x, y, ...)  ## First call the default panel function for 'xyplot'. 是不是有点像 super() 构造器？
		panel.abline(h = median(y), lty = 2)  ## Add a horizontal line at the median for each grid (panel)
	})
	
	## Custom panel function
	xyplot(y ~ x | f, panel = function(x, y, ...) {
		panel.xyplot(x, y, ...)  ## First call default panel function
		panel.lmline(x, y, col = 2)  ## Overlay a simple linear regression line for each grid (panel)
	})
	
	Any function from the Base Plotting System cannot be used here inside panel function. It is a general rule that not mix functions between different plotting systems.
	
-----

## ggplot2 (part 1)

	理论基础是 _The Grammar of Graphics_

	qplot(): quick plot
	
	Plots are made up of aesthetics [i:s'θetɪks] (e.g. size, shape, color) and geoms (e.g. points, lines)
	
## ggplot2 (part 2)
	
	library(ggplot2)
	str(mpg) # mpg is a data set shipped with ggplot2
	
	qpolt(displ, hwy, data=mpg) ## x=displ, y=hwy
	
	## Modifying aesthetics
	qplot(displ, hwy, data = mpg, color = drv) ## different color for different drv. or we can say color is mapped to drv. (drv is a column)

	## Adding a geom
	qplot(displ, hwy, data = mpg, geom = c("point", "smooth")) ## 有点像 panel 调用 `panel.xyplot(x, y, ...)`，"point" 就是画出散点图，和直接画图没区别。然后附加一个 "smooth" geom, a loess fit line and confidence limits
	
	"smooth" 的定义参 http://www.statmethods.net/advgraphs/ggplot2.html
	
	"loess fit" 的定义参 http://peltiertech.com/WordPress/loess-smoothing-in-excel/
	
	## Histograms
	qplot(hwy, data = mpg, fill = drv) ## fill 的效果也是指定颜色, fill the bar
	
	## Facets
	qplot(displ, hwy, data = mpg, facets = . ~ drv) ## 有点像 xyplot(y ~ x | f * g) 的意味
	qplot(displ, hwy, data = mpg, facets = drv ~ ., binwidth = 2) 
	
	facet formula 右值是 column of the plot grids，左值是 row of the plot grids
	
	可参 http://www.statmethods.net/advgraphs/ggplot2.html
	
	
	
	qplot(log(eno), data = maacs, geom = "density", color = mopos) ## 画 density 曲线

	qplot(log(pm25), log(eno), data = maacs, shape = mopos)
	qplot(log(pm25), log(eno), data = maacs, color = mopos) 

	qplot(log(pm25), log(eno), data = maacs, color = mopos, geom = c("point", "smooth"), method = "lm") ## 这个 method 应该是指画 "smooth" 的 method。这里 smooth 也会按 mopos 区分，所以会有两条
	
	qplot(log(pm25), log(eno), data = maacs, geom = c("point", "smooth"), method = "lm", facets = . ~ mopos) ## 与其在一个图里画两条 smooth，不如直接把图分成两个，每个 grid 里画一条 smooth
	
	
	另外，这里用 log 是个很好的示范：当数据分布很散的时候，用 log 可以整体缩小范围，还能保持大小关系
	
## ggplot2 (part 3)

	Basic Components of a ggplot2 Plot 
	* A data frame 
	* aesthetic mappings: how data are mapped to color, size  
	* geoms: geometric objects like points, lines, shapes.  
	* facets: for conditional plots  
	* stats: statistical transformations like binning, quantiles, smoothing.  
	* scales: what scale an aesthetic map uses (example: male = red, female = blue)
	* coordinate system 
	
	When building plots in ggplot2 (rather than using qplot) the “artist’s palette” model may be the closest analogy 

	Plots are built up in layers 
	* Plot the data 
	* Overlay a summary 
	* Metadata and annotation 
	
	
	这一讲的主要内容就是如何用 ggplot 的底层 API 来画出和 qplot 一样的图
	
	目标：qplot(logpm25, NocturnalSympt, data = maacs, facets = . ~ bmicat, geom = c("point", "smooth"), method = "lm")

	底层重建步骤：
	
	g <- ggplot(maacs, aes(logpm25, NocturnalSympt)) ## this is Aesthetics
	
	## see summary(g) for more
	
	## print(g) => Error: No layers in plot
	
	p <- g + geom_point() ## now print(p) works
	
	最终得到 p <- g + geom_point() + facet_grid(. ~ bmicat) + geom_smooth(method = "lm") ## "smooth" is also a layer; facet also add labels to each grid
	
## ggplot2 (part 4)
	
	Annotation 
	* Labels: xlab(), ylab(), labs(), ggtitle() 
	* Each of the "geom" functions has options to modify  
	* For things that only make sense globally, use theme()  
		* Example: theme(legend.position = "none")  
	* Two standard appearance themes are included 
		* theme_gray(): The default theme (gray background) 
		* theme_bw(): More stark/plain  
	
	g + geom_point(color = "steelblue", size = 4, alpha = 1/2)
	g + geom_point(aes(color = bmicat), size = 4, alpha = 1/2) ## alpha 指透明度

	
	g + geom_point(aes(color = bmicat)) + labs(title = "MAACS Cohort") + labs(x = expression("log " * PM[2.5]), y = "Nocturnal Symptoms") ## Modifying	Labels

	g + geom_point(aes(color = bmicat), size = 2, alpha = 1/2) + geom_smooth(size = 4, linetype = 3, method = "lm", se = FALSE) ## Customizing the Smooth
	## se = FALSE => do not show the Standard Error bars

	
	g + geom_point(aes(color = bmicat)) + theme_bw(base_family = "Times") ## Changing the Theme. "Times" is font name
	
## ggplot2 (part 5)

	Axis Limits

	比如有个别的噪音点 (x_noise, y_noise)（又称 outlier），y_noise 超出其他的 y 很多（比如 y_noise = 50，其他的 y 在 [-3, 3] 之间），画图时我想限定 y 轴的长度：
	
	错误的做法：g + geom_line() + ylim(-3, 3) ## (x_noise, y_noise) 这一点 went missing，曲线会不连贯
	正确的做法：g + geom_line() + coord_cartesian(ylim = c(-3, 3)) ## 曲线连贯，(x_noise, y_noise) 这一点超出图片范围
	
	
	
	## Setup ggplot with data frame!
	g <- ggplot(maacs, aes(logpm25, NocturnalSympt))
	
	## Add layers!
	g + geom_point(alpha = 1/3)
	 + facet_wrap(bmicat ~ no2dec, nrow = 2, ncol = 4) ## make pamels
	 + geom_smooth(method="lm", se=FALSE, col="steelblue") ## add smoother
	 + theme_bw(base_family = "Avenir", base_size = 10) ## change theme
	 + labs(x = expression("log " * PM[2.5])
	 + labs(y = "Nocturnal Symptoms")
	 + labs(title = "MAACS Cohort") ## add labels

	
	
	其实 qplot 也可以 add layer，比如：
	qplot(votes, rating, data = movies) + geom_smooth()