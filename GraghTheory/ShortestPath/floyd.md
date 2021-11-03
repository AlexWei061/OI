# Floyd
$$ f_{i,j} =  min\lbrace f_{i, j} , f_{i, k} + f_{k, j} \rbrace (k \in [1, n])$$
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 1010

/* 

图中最短路 f[i][j] = gragh[i][j]
                  = min{f[i][k] + f[k][j], f[i][j]}

*/

int n = 0;
int gragh[MAXN][MAXN] = { 0 };
int     f[MAXN][MAXN] = { 0 };

void floyd(){
	for(int k = 1; k <= n; k++){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= n; j++){
				f[i][j] = min(f[i][j], f[i][k]+f[k][j]);
			}
		}
	}
}

int main(){
	scanf("%d", &n);
	memset(gragh, 0x3f, sizeof(gragh));                             // 10 ^ 9
	memset(f, 0x3f, sizeof(f));
	for(int i = 1; i <= n; i++){
		for(int j = 1; j <= n; j++){
			scanf("%d", &gragh[i][j]);
			f[i][j] = gragh[i][j];
		}
	}

	floyd();

	int x = 0, y = 0;
	scanf("%d%d", &x, &y);
	printf("%d\n", f[x][y]);

	return 0;
}

/*

作用 :
1. f[i][j] 求出图中任意两节点 i, j 的最小路径
2. 求有向图的连通块 求除图中任意一点是否可以到达另外一点

1.
int f[MAXN][MAXN] = { 0 };
初始值 : memset(f, 0x3f, sizeof(f)); 
void floyd(){
	for(int k = 1; k <= n; k++){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= n; j++){
				f[i][j] = min(f[i][j], f[i][k]+f[k][j]);
			}
		}
	}
}

2.
bool f[MAXN][MAXN] = { 0 };
初始值 : memset(f, 0, sizeof(f));
void floyd(){
	for(int k = 1; k <= n; k++){
		for(int i = 1; i <= n; i++){
			for(int j = 1; j <= n; j++){
				f[i][j] = f[i][j] or (f[i][k] and f[k][j]);
			}
		}
	}
}

*/
```
