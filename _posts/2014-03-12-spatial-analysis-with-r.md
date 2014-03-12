---
layout: post
title: "使用R进行空间分析"
date: 2014-03-12 19:44
comments: true
categories: 
- 空间分析
tags:
- R
---

## 引言
空间分析（spatial analysis）对于扩散研究非常重要，它揭示了传播在空间维度上的分布。令人略感惊奇的是空间分析的研究者越来越多地使用R软件。其中一个原因是R包罗万象，而空间分析仍在发展且神情未定。在这个时候难以判定哪种方法最优。此时，策略当然是博观约取。R因其囊括众多统计方法而成为连接不同分析套路的首选；另外在R当中使用者可以继续开发新的数据分析包。可谓一举两得。　
　 
	# read data
	library(maptools)
	library(sp)
	library(rgdal)

    setwd("D:/chengjun/Milan")
	dat = read.csv("./Social pulse/Milano_sample.csv", header = FALSE, stringsAsFactors = FALSE, sep = ";", quote = "")
	names(dat) = c("lan", "time", "geo", "timestamp", "mname", "mid", "entities", "user")
	
	# clean data
	dat = subset(dat, dat$time >= as.POSIXlt("2013-12-01 00:00:00")); dim(dat)
	dat$time = do.call(rbind, strsplit(dat$time, split = "\\."))[,1]
	dat$time = gsub("T", " ", dat$time)
	dat$time = as.POSIXlt(dat$time)
	dat$geo = gsub("[", "", dat$geo, fixed = T)
	dat$geo = gsub("]", "", dat$geo, fixed = T)	

	# download shp data from 
	# http://metro.teczno.com/#milan
	ost=readOGR("./Milano Grid/milano-grid/milan.imposm-shapefiles/milan.osm-mainroads.shp", layer = "milan.osm-mainroads") #will load the shapefile 
	spl = spTransform(ost, CRS("+proj=longlat")) # convert to longitude and latitude
	
	# plot function
	make_plot = function(){
	  tz = as.POSIXlt("2013-12-01 00:00:00")
	  end_time = as.POSIXlt("2013-12-02 00:00:00")
	  while(tz <= end_time){
	    print(tz)
	    datd = subset(dat, dat$time > tz& dat$time <= tz + 3600)
	    plot(spl, col = "pink")
	    title(tz)
	    p = do.call(rbind, strsplit(datd$geo, split=','))
	    p1 = as.numeric(p[,1])
	    p2 = as.numeric(p[,2])
	    points(p2~p1, pch = 1, col = "red", cex = 0.1)
	    tz = tz + 3600
	  }
	}
	
	# save figures
	png(file = "./linear%2d.png", 
	    width=5, height=5, 
	    units="in", res=700)
	make_plot()
	dev.off()

![米兰城12月1日每个小时发推特的时空分布](http://farm3.staticflickr.com/2610/13103072964_078e5abcc6_o.gif)

**图1** 米兰城一天当中的发推特的时空分布

把12月31天的数据累积起来，我们可以得到米兰城2013年12月当中的发推特的空间分布。

	# the overall geographical distribution
	png("./milano_social_pulse_December.png", 
	    width=10, height=10, 
	    units="in", res=700)
	plot(spl, col = "purple")
	p = do.call(rbind, strsplit(dat$geo, split=','))
	p1 = as.numeric(p[,1])
	p2 = as.numeric(p[,2])
	points(p2~p1, pch = 1, col = "red", cex = 0.01)
	dev.off()

![](http://farm8.staticflickr.com/7349/13103304784_11e17eae38.jpg)

**图2** 米兰城2013年12月当中的发推特的空间分布


用了其中一个小数据，为了展现了每个小时的[动态变化](http://chengjun.github.io/big_data_challenge/visualization.html)，使用CartoDB网站来制作了一个简单的可视化。


