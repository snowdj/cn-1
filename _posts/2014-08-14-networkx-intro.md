---
layout: post
title: "NetworkX初步：创建网络、提取属性和绘图"
date: 2014-08-14
comments: true
categories: 
- network
tags:
- networkx
- python
---

NetworkX是使用python分析网络数据的重要武器。它的使用非常简单。

首先，创建网络对象：

    import matplotlib.pyplot as plt
    import networkx as nx
    
    G=nx.DiGraph()
    
然后，添加链接：

    G.add_edge('source',1,weight=80)
    G.add_edge(1,2,weight=50)
    G.add_edge(1,3,weight=30)
    G.add_edge(3,2,weight=10)
    G.add_edge(2,4,weight=20)
    G.add_edge(2,5,weight=30)
    G.add_edge(4,5,weight=10)
    G.add_edge(5,3,weight=5)
    G.add_edge(2,'sink',weight=10)
    G.add_edge(4,'sink',weight=10)
    G.add_edge(3,'sink',weight=25)
    G.add_edge(5,'sink',weight=35)
    
可以很容易提取边的权重: 

    edges,colors = zip(*nx.get_edge_attributes(G,'weight').items())
    
计算加权过的出度：

    d = G.out_degree(weight = 'weight') #计算节点的中心度
    
选择一个常用的可视化方法：

    pos=nx.spring_layout(G) #设置网络的布局
    
绘制网络:

    nx.draw(G, pos, node_color = 'orange', with_labels = True,
            nodelist = d.keys(), node_size = [v*5 for v in d.values()], 
            edgelist = edges, edge_color = colors, width = 5, edge_cmap=plt.cm.Blues)

![](http://chengjun.qiniudn.com/demo.png)


 
