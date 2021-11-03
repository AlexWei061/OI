# 字典树
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MAXN 100100

/* Trie 树维护查找前缀数量  */

int n = 0; int m = 0;

int tot = 0;
char str[MAXN];
int trie[MAXN][30] = { 0 };
int ed[MAXN] = { 0 };

void buildTrie(char str[]){
	int p = 0;
	int len = strlen(str);
	for(int i = 0; i < len; i++){
		if(!trie[p][str[i] - 'a']){
			trie[p][str[i] - 'a'] = ++tot;
		}
		p = trie[p][str[i] - 'a'];
	}
	ed[p]++;
}

int queryTrie(char str[]){
	int p = 0;
	int ans = 0;
	int len = strlen(str);
	for(int i = 0; i < len; i++){
		if(!trie[p][str[i] - 'a']){
			return 0;
		}
		p = trie[p][str[i] - 'a'];
		ans += ed[p];
	}
	return ans;
}

int main(){
	scanf("%d%d", &n, &m);                       // n 个单词 m 次询问 
	for(int i = 1; i <= n; i++){
		scanf("%s", &str);
		bulidTrie(str);
	}

	for(int i = 1; i <= m; i++){
		scanf("%s", &str);
		printf("%d\n", queryTrie(str));
	}
	return 0;
}
```