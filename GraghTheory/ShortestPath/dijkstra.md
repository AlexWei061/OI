# dijkstra + 堆优化
&emsp; bfs（把bfs中用的队列改成优先队列） 然后对于每个"两边之和小于第三边的三角形"（如下图）更新到的路径最小值即可。
![在这里插入图片描述](/Alex/OI/pic/dijkstra.png)

```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100
#define MAXM 2 * MAXN

int n = 0; int m = 0;

int tot = 0;
int first[MAXN] = { 0 };
int   nxt[MAXM] = { 0 };
int    to[MAXM] = { 0 };
int value[MAXM] = { 0 };

void add(int x, int y, int weight){
	nxt[++tot] = first[x];
	first[x] = tot;
	to[tot] = y;
	value[tot] = weight;
}

int  dis[MAXN] = { 0 };
bool vis[MAXN] = { 0 };
priority_queue<pair<int, int> > q;

void dijkstra(int node){
	memset(dis, 0x3f, sizeof(dis));
	memset(vis, 0, sizeof(vis));
	dis[node] = 0;
	q.push(make_pair(0, node));
	while(!q.empty()){
		int u = q.top().second;
		if(vis[u]){
			continue;
		}
		vis[u] = 1;
		for(int e = first[u]; e; e = nxt[e]){
			int v = to[e];
			if(dis[v] > dis[u] + value[e]){
				dis[v] = dis[u] + value[e];
				q.push(make_pair(-dis[v], v));
			}
		}
	}
}

int main(){
	scanf("%d%d", &n, &m);
	for(int i = 1; i <= m; i++){
		int x, y, w;
		scanf("%d%d%d", &x, &y, &w);
		add(x, y, w); add(y, x, w);
	}
	
	dijkstra(1);
	
	for(int i = 1; i <= n; i++){
		printf("%d ", dis[i]);
	}
	puts("");
	
	return 0;
}
```
