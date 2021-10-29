# SPFA
&emsp; 思路和 dijkstra 很像。
&emsp; [Dijkstra地址：https://github.com/AlexWei061/OI/blob/main/GraghTheory/dijkstra.md](https://github.com/AlexWei061/OI/blob/main/GraghTheory/dijkstra.md)
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 10010
#define MAXM MAXN * (MAXN-1)

int n = 0, m = 0, s = 0;

int tot = 0;
int first[MAXN] = { 0 };
int   nxt[MAXM] = { 0 };
int    to[MAXM] = { 0 };
int value[MAXM] = { 0 };

void add(int x, int y, int v){
	nxt[++tot] = first[x];
	first[x] = tot;
	to[tot] = y;
	value[tot] = v;
}

int  dis[MAXN] = { 0 };
bool isV[MAXN] = { 0 };
int    q[MAXM] = { 0 };
int head = 0; int tail = 0;

void SPFA(int node){
	memset(dis, 0x3f, sizeof(dis));
	memset(isV, 0, sizeof(isV));
	dis[node] = 0; isV[node] = 1;
	q[++tail] = node;
	while(head < tail){
		int u = q[++head];
		isV[u] = 0;
		for(int e = first[u]; e; e = nxt[e]){
			int v = to[e];
			if(dis[u] + value[e] < dis[v]){
				dis[v] = dis[u] + value[e];
				if(!isV[v]){
					isV[v] = 1;
					q[++tail] = v;
				}
			}
		}
	}
}

int main(){
	scanf("%d%d%d", &n, &m, &s);
	for(int i = 0; i < m; i++){
		int u = 0, v = 0, w = 0;
		scanf("%d%d%d", &u, &v, &w);
		add(u, v, w);
		add(v, u, w);
	}

	SPFA(s);

	for(int i = 1; i <= n; i++){
		printf("%d ", dis[i]);
	}
	puts("");
	
	return 0;
}
```
