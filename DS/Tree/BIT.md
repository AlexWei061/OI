# 树状数组

```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

int n = 0;
int a[MAXN] = { 0 };
int c[10 * MAXN] = { 0 };

inline int lowbit(int x){
	return x & -x;
}

void add(int index, int val){
	for(; index <= n; index += lowbit(index)){
		c[index] += val;
	}
}

int query(int x){              // 查询 1 ~ x 的和 
	int ans = 0;
	for(; x; x -= lowbit(x)){
		ans += c[x];
	}
	return ans;
}

int main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
		add(i, a[i]);
	}
	
	printf("%d\n", query(10));
	
	return 0;
}
```
## 树状数组+离散化求逆序对
```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
#define MAXN 1001000

int n = 0; int m = 0;
int a[MAXN] = { 0 };
int b[MAXN] = { 0 };
int c[MAXN] = { 0 };

void discrete(){
	sort(b + 1, b + n + 1);
	m = unique(b + 1, b + n + 1) - b - 1;
}

int query(int x){
	return lower_bound(b + 1, b + m + 1, x) - b;
}

int lowbit(int x){
	return x & -x;
}

void add(int index, int val){
	for(; index <= n; index += lowbit(index)){
		c[index] += val;
	}
}

ll ask(int x){
	ll ans = 0;
	for(; x; x -= lowbit(x)){
		ans += c[x];
	}
	return ans;
}

signed main(){
	scanf("%d", &n);
	for(int i = 1; i <= n; i++){
		scanf("%d", &a[i]);
		b[i] = a[i];
	}
	
	discrete();
	
	ll ans = 0;
	for(int i = n; i >= 1; i--){
		int k = query(a[i]);
		ans += ask(k - 1);
		add(k, 1);
	}
	
	printf("%lld\n", ans);
	return 0;
}
```

