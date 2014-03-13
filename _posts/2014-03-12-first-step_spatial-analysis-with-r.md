---
layout: post
title: "空间分析初步：读入数据和简单绘图"
date: 2014-03-12 19:44
comments: true
categories: 
- 空间分析
tags:
- R
---

### 引言
空间分析（spatial analysis）对于扩散研究非常重要，它揭示了传播在空间维度上的分布。令人略感惊奇的是空间分析的研究者越来越多地使用R软件。其中一个原因是R包罗万象，而空间分析仍在发展且神情未定。在这个时候难以判定哪种方法最优。此时，策略当然是博观约取。R因其囊括众多统计方法而成为连接不同分析套路的首选；另外在R当中使用者可以继续开发新的数据分析包。可谓一举两得。　

首先，读入点的时空分布数据。　 

	# read data
	library(maptools)
	library(sp)
	library(rgdal)

    setwd("D:/chengjun/Milan")
	dat = read.csv("./Social pulse/Milano_sample.csv", header = FALSE, stringsAsFactors = FALSE, sep = ";", quote = "")
	names(dat) = c("lan", "time", "geo", "timestamp", "mname", "mid", "entities", "user")

进行简单的清洗：
	
	# clean data
	dat = subset(dat, dat$time >= as.POSIXlt("2013-12-01 00:00:00")); dim(dat)
	dat$time = do.call(rbind, strsplit(dat$time, split = "\\."))[,1]
	dat$time = gsub("T", " ", dat$time)
	dat$time = as.POSIXlt(dat$time)
	dat$geo = gsub("[", "", dat$geo, fixed = T)
	dat$geo = gsub("]", "", dat$geo, fixed = T)	

从openstreetmap下载米兰城的交通地理信息。使用rgdal这个R包读入数据：

	# download shp data from 
	# http://metro.teczno.com/#milan
	ost=readOGR("./Milano Grid/milano-grid/milan.imposm-shapefiles/milan.osm-mainroads.shp", layer = "milan.osm-mainroads") #will load the shapefile 

要把地理信息转为经纬度的数据表示形式：

	spl = spTransform(ost, CRS("+proj=longlat")) # convert to longitude and latitude

如果要画出spl的话，速度有点慢。因为绘制的点比较多。这一步

用了其中一个小数据(涵盖一天中的几个小时)，为了展现了每个小时的[动态变化](http://chengjun.github.io/big_data_challenge/visualization.html)，使用CartoDB网站来制作了一个简单的可视化。顺便找了一遍各种javascript的库和其它包（googleVis, Echarts等），发现都不实用，所以还是用R吧。


设置绘图的函数，来看一下数据的形式：
	
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

读者也可以直接使用animation这个R包来绘图。我在实验室的机器上安装ImageMagick有点问题，干脆存为图片了，再转为gif了。

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

这个图还是有点意思的：街道是城市人流的管道（Tube,伦敦好像把地铁直接称为tube）,人的移动等行为（包括社会媒体使用行为）则是穿行其间的流。推特聚集的地方与街道的轮廓高度契合。城市的中心推特的密度大（因为人流的密度大？）。所以可以检验下点的分布是否是随机的。


### 空间点类型分析
这里涉及到空间点类型分析（spatial point pattern analysis）。检验下点的分布是否是随机的最简单的方法是进行完全空间随机（complete spatial randomness， CSR）分析。

**G方程方法：到最近邻居的距离**

这里说的最近邻居，英文当中却成为nearest event。把一个点的存在称之为事件也挺好玩。











