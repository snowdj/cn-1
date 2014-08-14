---
layout: post
title: ""
date: 2008-01-01
comments: true
categories: 
- 
tags:
- 
---

    import matplotlib.pyplot as plt
    import networkx as nx
    
    G=nx.DiGraph()
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
    
    
    edges,colors = zip(*nx.get_edge_attributes(G,'weight').items())
    d = G.out_degree(weight = 'weight') #计算节点的中心度
    pos=nx.spring_layout(G) #设置网络的布局
    nx.draw(G, pos, node_color = 'orange', with_labels = True,
            nodelist = d.keys(), node_size = [v*5 for v in d.values()], 
            edgelist = edges, edge_color = colors, width = 5, edge_cmap=plt.cm.Blues)

![](http://chengjun.qiniudn.com/demo.png)


 
