---
layout: post
title:  "图: DAG中使用拓扑排序来求单源点最短路径"
date:   2014-10-30 16:55:00
categories:   algorithms
---

求单源点最短路径时，使用Bellman-Ford算法的需要对所有边松弛V-1遍。但是如果我们知道一个图是一个有向无环图时，我们先对所有顶点进行一次拓扑排序，然后依次对每个顶点连接的边松弛一遍就可以了。时间复杂度为O(V+E)。

```
	DAG-SHORTEST-PATHS(G, w, s)
		topologically sort the vetices of G
		INITIALIZE-SINGLE-SOURCE(G,s)
		for each vertex u, taken in topolocially sorted order
			do for each vertex v in Adj[u]
				dor Relax(u, v, w)
```
代码算了吧。。。