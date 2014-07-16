## Shiny 1-Introduction to Shiny
	
	A shiny project is a directory containing at least two parts: 
	
	* One named ui.R (for user interface) controls how it looks. (generate HTML)
		* or a `www` folder with an `index.html` inside
	* One named server.R that controls what it does. (render the output)

## Shiny 2-basic html and getting input
	
## Shiny 3-Creating a very basic prediction function
	
## Shiny 4-Working with images
	
## Shiny 5-Discussion
	
## More advanced shiny discussion, reactivity 

	介绍了下代码运行机制，自己试一下就知道了，讲的有点啰嗦
	
## More advanced shiny, the reactive function

	x <- reactive({## some code})
	
	reactive 其实是返回一个函数，这个函数的实现写在 {} 里了，后续调用 x() 就相当于调用 {} 中的那一段代码
	
	可以用在需要反复执行的代码上
	
## More advanced shiny, conditional execution of reactive statements
	
	isolate() 让控件不要立刻计算，等待用户提交后再计算
	
	if (input$goButton == 0) 
		"You have not pressed the button"
	else if (input$goButton == 1) 
		"you pressed it once"
	
## More advanced shiny, odds and ends

	fluidPage
	fluidRow
	
	runApp(displayMode='showcase')
	
-----	

## Manipulate
	
	library(manipulate) 
	manipulate(plot(1:x), x = slider(1, 100))
	
	manipulate(myPlot(x), x = slider(min, max, step=1))
	
-----

## Intro to rCharts and GoogleVis

## rCharts introduction

## rCharts more examples

	n1 < rPlot()
	n1$html(): print out the html code
	n1$save(): bring the code back into Slidify document
	
	n1$publish()

## rCharts mapping and discussion	
	
	Leaflet: 用于生成地图
	
	xchart, highchart, rickshaw
	
## GoogleVis
	
	(wrong video; waiting)
	
## shinyApps.io

	可以把你的 app 部署上去，类似 GAE 的感觉
	
## plotly
	
	可以增强 plot 效果，并自动上传 plot 至 plotly 的 repo
	在 repo 里还可以编辑 plot，比如 change color theme, change chart parameter
	还可以 share to twitter or facebook
	
	
	
	
	
	
	
	
	