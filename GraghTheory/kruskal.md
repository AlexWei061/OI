# kruskal
&emsp; 贪心的思想：把所有边按照边权大小排序，然后从小到大依次选边，并把这个边所连接的两个点放入并查集中，如果找到这这条边所连接的两个店已经在并查集里面了，那就直接跳过这一条边。
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 10010
#define MAXM MAXN * (MAXN - 1)

/* 最小生成树 */

int n = 0, m = 0;

struct Tedge{
	int x; int y;
	int value;
}edge[MAXM];
bool comp(Tedge a, Tedge b){
	return a.value < b.value;
}

int parent[MAXN] = { 0 };
int  rrank[MAXN] = { 0 };

void init(){
	for(int i = 1; i <= n; i++){
		parent[i] = -1;
		rrank[i]  =  0;
	}
}

int findRoot(int x){
	while(parent[x] != -1){
		x = parent[x];
	}
	return x;
}

void unionVertices(int x, int y){
	int xRoot = findRoot(x);
	int yRoot = findRoot(y);
	if(xRoot != yRoot){
		if(rrank[xRoot] > rrank[yRoot]){
			parent[yRoot] = xRoot;
		}
		else if(rrank[xRoot] < rrank[yRoot]){
			parent[xRoot] = yRoot;
		}
		else{
			parent[xRoot] = yRoot;
			rrank[yRoot]++;
		}
	}
}

int chosen[MAXM] = { 0 };
int tot = 0;
void kruskal(){
	sort(edge, edge+m, comp);
	for(int i = 0; i < m; i++){
		Tedge e = edge[i];
		if(findRoot(e.x) != findRoot(e.y)){
			unionVertices(e.x, e.y);
			chosen[i] = 1;
			tot += e.value;
		}
	}
}

int main(){
	scanf("%d%d", &n, &m);

    init();
	for(int i = 0; i < m; i++){
		scanf("%d%d%d", &edge[i].x, &edge[i].y, &edge[i].value);
	}

	kruskal();

	for(int i = 0; i < m; i++){
		if(chosen[i]){
			printf("x = %d y = %d v = %d\n", edge[i].x, edge[i].y, edge[i].value);
		}
	}
	printf("total value = %d\n", tot);

    return 0;
}
```
