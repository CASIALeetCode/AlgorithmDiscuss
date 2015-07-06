# 图相关知识点


## 1.拓扑排序 
将有向图中的顶点以线性方式进行排序。即对于任何连接自顶点u到顶点v的有向边uv，在最后的排序结果中，顶点u总是在顶点v的前面。

两种方法：Kahn算法和DFS方法

###Kahn算法

	L← Empty list that will contain the sorted elements
	S ← Set of all nodes with no incoming edges
	while S is non-empty do
	    remove a node n from S
	    insert n into L
	    foreach node m with an edge e from n to m do
	        remove edge e from the graph
	        if m has no other incoming edges then
	            insert m into S
	if graph has edges then
	    return error (graph has at least onecycle)
	else 
	    return L (a topologically sortedorder)

###DFS方法
	L ← Empty list that will contain the sorted elements
	S ← Set of all nodes with no incoming edges
	while S is non-empty do
	    remove a node n from S
	    add n to tail of L
	    for each node m with an edge e from n to m do
	        remove edge e from the graph
	        if m has no other incoming edges then
	            insert m into S
	if graph has edges then
	    return error (graph has at least one cycle)
	else 
	    return L (a topologically sorted order)
## EXAMPLE
###Problem1  [course-schedule-ii](https://leetcode.com/problems/course-schedule-ii/)

**code 1 (Kahn算法):**

	class Solution {
	public:
		vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
			vector<int> ret;
			if(prerequisites.size() <= 0){
				for(int i=0 ; i<numCourses ; i++){
					ret.push_back(i);
				}
				return ret;
			}
			queue<int> qu;
			vector<int> degree(numCourses,0);
			vector<vector<int> > nums(numCourses);
		
			for(vector<pair<int,int>>::iterator it=prerequisites.begin() ; it!=prerequisites.end() ; it++){
				nums[it->second].push_back(it->first);
				degree[it->first]++;
			}
			for(int i=0 ; i<degree.size() ; i++){
				if(degree[i] == 0){
					qu.push(i);
				}
			}
			int finishnum = 0;
			while(qu.empty() == false){
				int p = qu.front();
				qu.pop();
				ret.push_back(p);
				for(int i=0 ; i<nums[p].size() ; i++){
					degree[nums[p][i]]--;
					if(degree[nums[p][i]] == 0){
						qu.push(nums[p][i]);
					}
				}
			}
			if(ret.size() == numCourses){
				return ret;
			}
			return vector<int>();
		}
	};
**code 2 (DFS方法):**

	class Solution {
	public:
	    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
	        vector<int> ret ;
	        if(prerequisites.size() <= 0){
	            for(int i=0 ; i<numCourses ; i++){
	                ret.push_back(i);
	            }
	            return ret;
	        }
	        
	        vector<vector<int> > nums(numCourses);
	        vector<int> degree(numCourses,0);
	        for(vector<pair<int, int>>::iterator it=prerequisites.begin() ; it!=prerequisites.end() ; it++){
	            nums[it->second].push_back(it->first);
	            degree[it->first]++;
	        }
	        vector<int> nodes;
	        for(int i=0 ; i<degree.size() ; i++){
	            if(degree[i] == 0){
	                nodes.push_back(i);
	            }
	        }
	        
	        vector<int> visit(numCourses,0);
	        for(int i=0 ; i<nodes.size() ; i++){
	            if(DFS(nodes[i],nums,ret,visit) == false){
	                return vector<int>();
	            }
	        }
	        if(ret.size()  != numCourses){
	            return vector<int>();
	        }
	        reverse(ret.begin(),ret.end());
	        return ret;
	    }
	    
	    bool DFS(int noden,vector<vector<int> > &nums,vector<int> &finishvec,vector<int> &visit){
	        visit[noden] = -1;
	        for(int i=0 ; i<nums[noden].size() ; i++){
	            if(visit[nums[noden][i]] == -1){
	                return false;
	            }
	            else if(visit[nums[noden][i]]==0 &&  DFS(nums[noden][i],nums,finishvec,visit) == false){
	                return false;
	            }
	        }
	        visit[noden] = 1;
	        finishvec.push_back(noden);
	        return true;
	    }
	    
	};

###Problem2 [判断是否有环](http://www.geeksforgeeks.org/detect-cycle-in-a-graph/)
可以使用上述拓扑排序的方法求解，不能生成拓扑排序则说明存在环

code:（answer in geeksforgeeks）

	// A C++ Program to detect cycle in a graph
	#include<iostream>
	#include <list>
	#include <limits.h>
	 
	using namespace std;
	 
	class Graph
	{
	    int V;    // No. of vertices
	    list<int> *adj;    // Pointer to an array containing adjacency lists
	    bool isCyclicUtil(int v, bool visited[], bool *rs);  // used by isCyclic()
	public:
	    Graph(int V);   // Constructor
	    void addEdge(int v, int w);   // to add an edge to graph
	    bool isCyclic();    // returns true if there is a cycle in this graph
	};
	 
	Graph::Graph(int V)
	{
	    this->V = V;
	    adj = new list<int>[V];
	}
	 
	void Graph::addEdge(int v, int w)
	{
	    adj[v].push_back(w); // Add w to v’s list.
	}
	 
	// This function is a variation of DFSUytil() in http://www.geeksforgeeks.org/archives/18212
	bool Graph::isCyclicUtil(int v, bool visited[], bool *recStack)
	{
	    if(visited[v] == false)
	    {
	        // Mark the current node as visited and part of recursion stack
	        visited[v] = true;
	        recStack[v] = true;
	 
	        // Recur for all the vertices adjacent to this vertex
	        list<int>::iterator i;
	        for(i = adj[v].begin(); i != adj[v].end(); ++i)
	        {
	            if ( !visited[*i] && isCyclicUtil(*i, visited, recStack) )
	                return true;
	            else if (recStack[*i])
	                return true;
	        }
	 
	    }
	    recStack[v] = false;  // remove the vertex from recursion stack
	    return false;
	}
	 
	// Returns true if the graph contains a cycle, else false.
	// This function is a variation of DFS() in http://www.geeksforgeeks.org/archives/18212
	bool Graph::isCyclic()
	{
	    // Mark all the vertices as not visited and not part of recursion
	    // stack
	    bool *visited = new bool[V];
	    bool *recStack = new bool[V];
	    for(int i = 0; i < V; i++)
	    {
	        visited[i] = false;
	        recStack[i] = false;
	    }
	 
	    // Call the recursive helper function to detect cycle in different
	    // DFS trees
	    for(int i = 0; i < V; i++)
	        if (isCyclicUtil(i, visited, recStack))
	            return true;
	 
	    return false;
	}
	 
	int main()
	{
	    // Create a graph given in the above diagram
	    Graph g(4);
	    g.addEdge(0, 1);
	    g.addEdge(0, 2);
	    g.addEdge(1, 2);
	    g.addEdge(2, 0);
	    g.addEdge(2, 3);
	    g.addEdge(3, 3);
	 
	    if(g.isCyclic())
	        cout << "Graph contains cycle";
	    else
	        cout << "Graph doesn't contain cycle";
	    return 0;
	}

## 2.搜索DFS,BFS
###Problem 3 [克隆图](https://leetcode.com/problems/clone-graph/)
**BFS：queue**

	/**
	 * Definition for undirected graph.
	 * struct UndirectedGraphNode {
	 *     int label;
	 *     vector<UndirectedGraphNode *> neighbors;
	 *     UndirectedGraphNode(int x) : label(x) {};
	 * };
	 */
	class Solution {
	public:
	    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
	        if(node == NULL){
	            return NULL;
	        }
	        queue<UndirectedGraphNode* > qu;
	        map<UndirectedGraphNode *,int> visitFlag; 
	        UndirectedGraphNode * p;
	        unordered_map<UndirectedGraphNode *,UndirectedGraphNode *> mymap;
	        
	        qu.push(node);
	        while(qu.empty() == false){
	            p = qu.front();
	            qu.pop();
	            mymap[p] =  new UndirectedGraphNode(p->label);
	            visitFlag[p] = 0;
	            for(int i=0 ; i<p->neighbors.size() ; i++){
	                if(visitFlag.count(p->neighbors[i]) == 0){
	                    visitFlag[p->neighbors[i]] = 0;
	                    qu.push(p->neighbors[i]);
	                }
	            }
	        }
	        
	        for(unordered_map<UndirectedGraphNode *,UndirectedGraphNode *>::iterator it=mymap.begin() ; it!=mymap.end() ; it++){
	            for(int i=0 ; i<it->first->neighbors.size() ; i++){
	                it->second->neighbors.push_back(mymap[it->first->neighbors[i]]);
	            }
	        }
	        return mymap[node];
	        
	    }
	};
**DFS：**

	/**
	 * Definition for undirected graph.
	 * struct UndirectedGraphNode {
	 *     int label;
	 *     vector<UndirectedGraphNode *> neighbors;
	 *     UndirectedGraphNode(int x) : label(x) {};
	 * };
	 */
	class Solution {
	public:
	    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
	        if(node == NULL){
	            return NULL;
	        }
	        unordered_map<UndirectedGraphNode*,UndirectedGraphNode*> mymap;
	        
	        DFS(mymap,node);
	        for(unordered_map<UndirectedGraphNode*,UndirectedGraphNode*>::iterator it=mymap.begin() ; it!=mymap.end() ; it++){
	            for(int i=0 ; i<it->first->neighbors.size() ; i++){
	                mymap[it->first]->neighbors.push_back(mymap[it->first->neighbors[i]]);
	            }
	        }
	        return mymap[node];
	    }
	    void DFS(unordered_map<UndirectedGraphNode*,UndirectedGraphNode*> &mymap, UndirectedGraphNode *node){
	        mymap[node] = new UndirectedGraphNode(node->label);
	        for(int i=0 ; i<node->neighbors.size() ; i++){
	            if(mymap.count(node->neighbors[i]) == 0){
	                DFS(mymap,node->neighbors[i]);
	            }
	        }
	    }
	};
##3.	欧拉回路
若图G中存在这样一条路径，使得它恰通过G中每条边一次,则称该路径为**欧拉路径**。若该路径是一个圈，则称为**欧拉(Euler)回路**。
- 无向图存在欧拉回路的充要条件
- 
一个无向图存在欧拉回路，当且仅当该图所有顶点度数都为偶数，且该图是连通图。

- 有向图存在欧拉回路的充要条件
- 
一个有向图存在欧拉回路，所有顶点的入度等于出度且该图是连通图。


##4.	[最短路径](http://blog.csdn.net/v_JULY_v/article/details/6182419 )

两个函数

	INITIALIZE-SINGLE-SOURCE(G, s)  
	•   for each vertex v ∈ V[G]  
	•    do d[v] ← ∞  
	•     π[v] ← NIL      //O（V）  

	RELAX(u, v, w)  
	•  if d[v] > d[u] + w(u, v)  
	•   then d[v] ← d[u] + w(u, v)  
	•    π[v] ← u        //O（E）图。


### Dijkstra ###
	DIJKSTRA(G, w, s)
	1  INITIALIZE-SINGLE-SOURCE(G, s)
	2  S ← Ø
	3  Q ← V[G]                                 //V*O（1）
	4  while Q ≠ Ø
	5      do u ← EXTRACT-MIN(Q)     //EXTRACT-MIN，V*O（V），V*O（lgV）
	6         S ← S ∪{u}
	7         for each vertex v ∈ Adj[u]
	8             do RELAX(u, v, w)       //松弛技术，E*O（1），E*O（lgV）。

### BELLMAN-FORD ###
	BELLMAN-FORD(G, w, s)
	1  INITIALIZE-SINGLE-SOURCE(G, s)   //对每个顶点初始化 ，O(V) 
	2  for i ← 1 to |V[G]| - 1
	3       do for each edge (u, v) ∈ E[G]
	4              do RELAX(u, v, w)    //针对每个顶点(V-1个)，都运用松弛技术O(E)，计为O（(v-1)*E)）
	5  for each edge (u, v) ∈ E[G]
	6       do if d[v] > d[u] + w(u, v)
	7             then return FALSE     //检测图中每条边，判断是否包含负权回路，
	                                    //若d[v]>d[u]+w(u，v)，则表示包含，返回FALSE，
	8  return TRUE                      //不包含负权回路，返回TRUE
Bellman-Ford 算法的时间复杂度，由上可得为O（V*E）。

### SPFA ###
Bellman-Ford的队列优化，时效性相对好，时间复杂度O（kE）。（k<<V）。

设立一个先进先出的队列用来保存待优化的结点，优化时每次取出队首结点u，并且用u点当前的最短路径估计值对离开u点所指向的结点v进行松弛操作，如果v点的最短路径估计值有所调整，且 v点不在当前的队列中，就将v点放入队尾。这样不断从队列中取出结点来进行松弛操作，直至队列空为止。

### Floyd ###
求多源、无负权边的最短路。用矩阵记录图。时效性较差，时间复杂度O(V^3)。

Floyd-Warshall算法（Floyd-Warshall algorithm）是解决任意两点间的最短路径的一种算法，可以正确处理有向图或负权的最短路径问题。

**d[k][i][j]定义成：“只能使用第1号到第k号点作为中间媒介时，点i到点j之间的最短路径长度。”**

对于d[k][i][j]（即使用1号到k号点中的所有点作为中间媒介时，i和j之间的最短路径），可以分为两种情况：（1）i到j的最短路不经过k；（2）i到j的最短路经过了k。不经过点k的最短路情况下，d[k][i][j]=d[k-1][i][j]。经过点k的最短路情况下，d[k][i][j]=d[k-1][i][k]+d[k-1][k][j]。因此，综合上述两种情况，便可以得到Floyd算法的动态转移方程：
d[k][i][j] = min(d[k-1][i][j], d[k-1][i][k]+d[k-1][k][j])（k,i,j∈[1,n]）



	void floyd_original() {
	    for(int i = 1; i <= n; i++)
	        for(int j = 1; j <= n; j++)
	            d[0][i][j] = graph[i][j];
	    for(int k = 1; k <= n; k++) {
	        for(int i = 1; i <= n; i++) {
	            for(int j = 1; j <= n; j++) {
	                d[k][i][j] = min(d[k-1][i][j], d[k-1][i][k] + d[k-1][k][j]);
	            }
	        }
	    }
	}

###Problem3 [微软在线笔试题](http://hihocoder.com/contest/mstest2015april/problem/3)
Code（dijkstra没通过）:

	#include <iostream>
	#include <fstream>
	#include <vector>
	#include <limits.h>
	#include <algorithm>
	
	using namespace std;
	
	int minDist(vector<int>dist, vector<int> visit){
	    int mindist = INT_MAX;
	    int ret = -1;
	    for (int i = 0; i<dist.size(); i++){
	        if (visit[i] == 0 && dist[i]<mindist){
	            ret = i;
	            mindist = dist[i];
	        }
	    }
	    return ret;
	}
	
	int Dijkstra(vector<pair<int, int> > pos,vector<vector<int> > nums, int p, int q){
	    if (p == q){
	        return 0;
	    }
	    int n = nums.size();
	    vector<int> dist(n, INT_MAX);
	    vector<int> visit(n, 0);
	
	    dist[p] = 0;
	    for (int i = 0; i<n; i++){
	        int u = minDist(dist, visit);
	        if (u == q){
	            return dist[q];
	        }
	        visit[u] = 1;
	        for (int i = 0; i<n; i++){
	            if (nums[u][i] == INT_MIN){
	                nums[u][i] = min(abs(pos[i].first - pos[u].first), abs(pos[i].second - pos[u].second));
	            }
	            if (visit[i] == 0 && dist[i]>dist[u] + nums[u][i]){
	                dist[i] = dist[u] + nums[u][i];
	            }
	        }
	    }
	    return dist[q];
	
	}
	
	int main()
	{
	    int n;
	    //ifstream in("input.txt");
	    cin >> n;
	    int x, y;
	    vector<vector<int> > nums(n, vector<int>(n, INT_MIN));
	    vector<pair<int, int> > pos;
	    for (int i = 0; i < n; i++){
	        cin >> x >> y;
	        pos.push_back(pair<int, int>(x, y));
	    }
	    /*for (int i = 0; i<n; i++){
	        for (int j = i + 1; j<n; j++){
	            nums[i][j] = nums[j][i] = min(abs(pos[i].first - pos[j].first), abs(pos[i].second - pos[j].second));
	        }
	    }*/
	    cout << Dijkstra(pos,nums, 0, n - 1) << endl;
	
	    return 0;
	}
##5.	有向图强连通分量
在有向图G中，如果两个顶点间至少存在一条路径，称两个顶点强连通(strongly connected)。如果有向图G的每两个顶点都强连通，称G是一个强连通图。非强连通图有向图的极大强连通子图，称为强连通分量(strongly connected components)。

Tarjan算法是基于对图深度优先搜索的算法，每个强连通分量为搜索树中的一棵子树。搜索时，把当前搜索树中未处理的节点加入一个堆栈，回溯时可以判断栈顶到栈中的节点是否为一个强连通分量。

定义DFN(u)为节点u搜索的次序编号(时间戳)，Low(u)为u或u的子树能够追溯到的最早的栈中节点的次序号。由定义可以得出，

	Low(u)=Min
	{
	   DFN(u),
	   Low(v),(u,v)为树枝边，u为v的父节点
	   DFN(v),(u,v)为指向栈中节点的后向边(非横叉边)
	}
当DFN(u)=Low(u)时，以u为根的搜索子树上所有节点是一个强连通分量。

	void tarjan(int i)
	{
	    int j;
	    DFN[i]=LOW[i]=++Dindex;
	    instack[i]=true;
	    Stap[++Stop]=i;
	    for (edge *e=V[i];e;e=e->next)
	    {
	        j=e->t;
	        if (!DFN[j])
	        {
	            tarjan(j);
	            if (LOW[j]<LOW[i])
	                LOW[i]=LOW[j];
	        }
	        else if (instack[j] && DFN[j]<LOW[i])
	            LOW[i]=DFN[j];
	    }
	    if (DFN[i]==LOW[i])
	    {
	        Bcnt++;
	        do
	        {
	            j=Stap[Stop--];
	            instack[j]=false;
	            Belong[j]=Bcnt;
	        }
	        while (j!=i);
	    }
	}
	void solve()
	{
	    int i;
	    Stop=Bcnt=Dindex=0;
	    memset(DFN,0,sizeof(DFN));
	    for (i=1;i<=N;i++)
	        if (!DFN[i])
	            tarjan(i);
	}
##6.	最小生成树
###Prim算法
设G = (V,E)是连通带权图，V = {1,2,…,n}。构造G的最小生成树Prim算法的基本思想是：

首先置S = {1}，然后，只要S是V的真子集，就进行如下的贪心选择：选取满足条件i ∈S,j ∈V – S,且c[i][j]最小的边，将顶点j添加到S中。这个过程一直进行到S = V时为止。

在这个过程中选取到的所有边恰好构成G的一棵最小生成树。
如下带权图：

![](http://i.imgur.com/pTmYLSC.jpg)

生成过程：

    1 -> 3 : 1
    3 -> 6 : 4
    6 -> 4:  2
    3 -> 2 : 5
    2 -> 5 : 3

### Kruskal算法 ###
给定无向连同带权图G = (V,E),V = {1,2,...,n}。Kruskal算法构造G的最小生成树的基本思想是：

（1）首先将G的n个顶点看成n个孤立的连通分支。将所有的边按权从小大排序。

（2）从第一条边开始，依边权递增的顺序检查每一条边。并按照下述方法连接两个不同的连通分支：

当查看到第k条边(v,w)时，如果端点v和w分别是当前两个不同的连通分支T1和T2的端点是，就用边(v,w)将T1和T2连接成一个连通分支，然后继续查看第k+1条边；如果端点v和w在当前的同一个连通分支中，就直接再查看k+1条边。

这个过程一个进行到只剩下一个连通分支时为止。
此时，已构成G的一棵最小生成树。

Kruskal算法的选边过程：

    1 -> 3 : 1
    4 -> 6 : 2
    2 -> 5 : 3
    3 -> 4 : 4
    2 -> 3 : 5





# 线上： #
## 1.	求连通图的割点（关节点） ##
割点的定义是，如果除去此节点和与其相关的边，有向图不再连通，描述算法。

对于有向图：

	Do  dfs,  record  low[i]  as  the  lowest  vertex  that  can be reached  from  i  and i’s successor
	nodes. For each edge i, if low[i] = i and i is not a leaf in dfs tree, then i is a cut point. The 
	other case is the root of dfs, if root has two or more children ,it is a cut point.

对于无向图：

	// ArticulationPoint.cpp : 定义控制台应用程序的入口点。
	//
	//2015-7-3 21:27:26
	#include <iostream>
	#include <fstream>
	#include <vector>
	#include <limits.h>
	#include <algorithm>
	
	using namespace std;
	
	class Solution {
	public:
	    void getArticulationPoint(int n, vector<vector<int> > &nums){
	        int counter = 1;
	        vector<int> visit(n, 0);
	        vector<int> low(n, INT_MAX);
	        visit[0] = 1;
	        low[0] = 1;
	        int nextnode = nums[0][0];
	        DFS(nums, nextnode, visit, counter, low);
	        for (int i = 0; i < n; i++){
	            if (low[i] == INT_MAX){
	                DFS(nums, i, visit, counter, low);
	            }
	        }
	
	    }
	    void DFS(vector<vector<int> > &nums, int nextnode, vector<int> &visit, int &counter, vector<int> &low){
	        int tempMin;
	        visit[nextnode] = tempMin = ++counter;
	        for (int i = 0; i<nums[nextnode].size(); i++){
	            int node = nums[nextnode][i];
	            if (visit[node] == 0){
	                DFS(nums, node, visit, counter, low);
	                if (low[node] < tempMin){
	                    tempMin = low[node];
	                }
	                if (low[node] >= visit[nextnode]){
	                    cout << "keynode :" << nextnode << endl;
	                }
	            }
	            else{
	                tempMin = min(tempMin, visit[node]);
	            }
	        }
	        low[nextnode] = tempMin;
	        cout <<"node:" << nextnode <<  "  low:" << low[nextnode] << "  visit :" << visit[nextnode] << endl;
	    }
	
	};
	
	int main()
	{
	    ifstream in("input.txt");
	    int n;
	    int x, y;
	    in >> n;
	    vector<vector<int> > nums(n, vector<int>());
	    //edge x--->y
	    while (!in.eof()){
	        in >> x >> y;
	        nums[x].push_back(y);
	    }
	    Solution S;
	    S.getArticulationPoint(n, nums);
	    
	    return 0;
	}
