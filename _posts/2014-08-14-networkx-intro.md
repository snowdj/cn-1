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


    '''
    # get flow distance
    '''
    def toSink(G, i):
            try:
                di = G[i]['sink'].values()[0]
            except:
                di = 0 
            return di
            
    def flowDistanceDissipationToSink(G): #input a balanced nx graph
        R = G.reverse()
        mapping = {'source':'sink','sink':'source'} 
        H = nx.relabel_nodes(R,mapping)
        #---------initialize flow distance dict------
        L = dict((i,1) for i in G.nodes())  #FlowDistance
        #---------prepare weighted out-degree dict------
        D = {i: toSink(G, i) for i in G.nodes()} #Di
        T = G.out_degree(weight='weight')        #Ti
        #---------iterate until converge------------
        ls = np.array(L.values())
        delta = len(L)*0.01 + 1
        while delta > len(L)*0.01:
            for i in L:
                l=1
                for m,n in H.edges(i):
                    l+=L[n]*H[m][n].values()[0]/float(T[m])
                L[i]=l
            delta = sum(np.abs(np.array(L.values()) - ls))
            ls = np.array(L.values())
        #---------clean the result-------
        del L['sink']
        for i in L:
            L[i]-=1
        L['sink'] = L.pop('source')
        T['sink'] = T.pop('source')
        D['sink'] = D.pop('source')
        return L.values(), D.values(), T.values()



 
    def sizeAdjustedFit(G):
        # G = h
        fd, di, ti = flowDistanceDissipationToSink(G)
        # sort data.frame
        mt = np.array([fd, di, ti]).T
        mts = sorted(mt.tolist(), key = lambda x: x[0])
        fd, di, ti = np.array(mts).T
        # get sum values
        Drmax = sum(di)
        Dmax = sum(di)
        Tmax = sum(ti)
        di_cum = [ sum(di[:i+1]) for i in range(len(fd))]
        # replace zero with a small number
        for k, i in enumerate(di_cum):
            if i==0:
                di_cum[k] = 0.000001
            else:
                pass
        # regression
        ti_cum = [ sum(ti[:i+1]) for i in range(len(fd))]
        di_cum_log = log(di_cum)
        ti_cum_log = log(ti_cum)
        Drmax_log = [log(Drmax) for i in range(len(fd))]
        import statsmodels.api as sm
        x = sm.add_constant(di_cum_log)
        X = np.column_stack((x, Drmax_log))
        model = sm.OLS(ti_cum_log, X)
        res = model.fit()
        b, a = res.params[1:]
        return [a, b, Dmax, Tmax]
    
    sizeAdjustedFit(G)
