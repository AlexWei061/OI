# ST表
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

int n = 0; int m = 0;
int a[MAXN] = { 0 };

inline int read(){
	int x = 0, f = 1; char ch = getchar();
	while (ch < '0' || ch > '9') {if (ch == '-') f = -1; ch = getchar();}
	while (ch >= '0' && ch <= '9') {x = x * 10 + ch - 48; ch = getchar();}
	return x * f;
}

int f[MAXN][60] = { 0 };
void prework(){
	for(int i = 1; i <= n; i++){
		f[i][0] = a[i];
	}
	int t = (log(n) / log(2)) + 1;
	for(int j = 1; j < t; j++){
		for(int i = 1; i <= n - (1 << j) + 1; i++){
			f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
		}
	}
}

int query(int l, int r){
	int k = log(r - l + 1) / log(2);
	return max(f[l][k], f[r - (1 << k) + 1][k]);
}

int main(){
	n = read(); m = read();
	for(int i = 1; i <= n; i++){
		a[i] = read();
	}
	
	prework();
	
	for(int i = 1; i <= m; i++){
		int l = read(), r = read();
		printf("%d\n", query(l, r));
	}
	
	return 0;
}
```

