---
layout: post
title:  "图:无向图(undirected graph)"
date:   2014-08-12 09:20:20
categories:   algorithms
---

###1.无向图的数据结构
我们一般有两种方式来表示图：<br>

- 邻接矩阵：我们用一个V*V的布尔矩阵来表示一个无向图。当顶点v和顶点w之间有相连接的边时，定义v行w列的元素值为true，否则为false。这种表示方法最简单，但当图的顶点太多，且为一个稀疏矩阵时，空间就有点浪费了。
- 邻接表数组：我们可以使用一个以顶点为索引的列表数组，其中每个元素都是和该顶点相邻接的顶点列表。如图一。一般我们会使用这种表示方法。

如下是java实现（Bag为一种链表实现的数据结构，类似Stack和Queue,但是对数据的先后顺序不关心，见后面的基础数据结构的博客）：
``` java
public class Graph {
	private final int V;		//number of vertices
	private int E;				//number of edges
	private Bag<Integer>[] adj;	//adjacency list

	public Graph(int V) {
		this.V = V;
		this.E = 0;
		adj = (Bag<Integer>[]) new Bag[V];
		for (int v = 0; v < V; v++) 
			adj[v] = new Bag<Integer>();
	}

	public int V() {
		return V;
	}

	public int E() {
		return E;
	}

	public void addEdge(int v, int w) {
		adj[v].add(w);
		adj[w].add(v);
		E++;
	}

	public Iterable<Integer> adj(int v) {
		return ajd[v];
	}
}
```

###2.深度优先搜索
深度优先搜索总是对最近发现的结点v的出发边进行探索，知道该结点的所有出发边都被发现为止。一旦结点v的所有出发边都被发现，搜索则回溯到v的前驱结点，来搜索该前驱结点的出发边。思路其实就是使用栈的先入后出的思想，但是没有显示的使用栈，而是利用了递归。
``` java
public class DepthFirstSearch {
	private boolean[] marked;
	private int count;

	public DepthFirstSearch(Graph G, int s) {
		marked = new boolean[G.V()];
		dfs(G, s);
	}

	private void dfs(Graph G, int v) {
		marked[v] = true;
		count++;
		for (int w : G.ajd[v]) {
			if (!marked[w]) {
				dfs(G, w);
			}
		} 
	}

	public boolean marked(int w) {
		return marked[w];
	}

	public int count() {
		return count;
	}
}
```
###3.广度优先搜索
广度优先搜索时将已经发现节点和未发现节点之间的边界，沿其广度方向向外扩展。也就是说，算法需要在发现所有距离源结点s为k的所有结点之后，才会发现距离源结点s为k+1的其他结点。<br>
广度优先搜索算法使用一个队列来保存所有已经被标记过的但其邻接表还没被检查过的顶点。先将起点加入队列，然后重复以下步骤知道队列为空：

- 取队列中的下一个顶点v并标记它；
- 将与v相邻的所有未被标记过的顶点加入队列。

与深度优先搜索不同的是，广度优先搜索显示使用了一个队列来记录被访问过的结点。java实现如下（Queue为队列，与Bag一样，见Algorithms 4th edition）：<br>
``` java
public BreadFirstSearch {
	private boolean[] marked;
	private int count;
	public BreadFirstSearch(Graph G, int s) {
		this.marked = new boolean[G.V()];
		bfs(G, s);
	}

	private void bfs(Graph G, int s) {
		Queue<Integer> queue = new Queue<Integer>();
		marked[s] = true;
		count++;
		queue.enqueue(s);
		while (!q.isEmpty()) {
			int v = queue.dequeue();
			for (int w : G.ajd[v]) {
				if (!marked[w]) {
					count++;
	 				marked[w] = true;
					queue.enque(w);				
				}
			}
		}
	}

	public boolean marked(int w) {
		return marked[w];
	}

	public int count() {
		return count;
	}
}
```

###4.广度优先搜索与迷宫问题
与图的搜索相关的另一个问题是路径问题。其中在无权图中最典型的应该是迷宫问题，就是找出走出迷宫的最短路径。其实这就是一个简单的单源点最短路径问题，在无权图中，我们可以使用广度优先搜索来实现，只要加一点代码就可以实现。

``` java
public BreadFirstPaths {
	private boolean[] marked;
	private int[] edgeTo;
	private final s;
	public BreadFirstSearch(Graph G, int s) {
		this.marked = new boolean[G.V()];
		this.edgeTo = new int[G.V()];
		this.s = s;
		bfs(G, s);
	}

	private void bfs(Graph G, int s) {
		Queue<Integer> queue = new Queue<Integer>();
		marked[s] = true;
		queue.enqueue(s);
		while (!q.isEmpty()) {
			int v = queue.dequeue();
			for (int w : G.ajd[v]) {
				if (!marked[w]) {
					edgeTo[w] = v;
	 				marked[w] = true;
					queue.enque(w);				
				}
			}
		}
	}

	pulic boolean hasPathTo(int v) {
		return marked[v];
	}

	public Iterable<Integer> pathTo(int v) {
		if(!hasPathTo(v)) return null;

		Stack<Integer> path = new Stack<Integer>();
		for (int x = v; x != s; x = edgeTo[x])
			path.push(x);
		path.push(this.s);
		return path;
	}
}
```