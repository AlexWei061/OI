# T1 丹钓栈

&emsp; ~~名字很屑的一道题~~

&emsp; 传送门 [NOIonline 2022 T1](https://www.luogu.com.cn/problem/P8251)

## 15 pts

&emsp; 应该都会 $15$ 分暴力吧，就直接模拟就好了。

```cpp
// 15 分暴力
#include<bits/stdc++.h>
using namespace std;
#define in read()
#define MAXN 500500

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int n = 0; int q = 0;
struct Tnode{
	int a; int b;
}arr[MAXN];

int top = 0;
Tnode stk[MAXN] = { 0 };
void solve1(int l, int r){
	top = 0; int ans = 0;                        // set empty
	for(int i = l; i <= r; i++){
		bool howAdd = 0;
		for(; top; top--){
			int x = stk[top].a; int y = stk[top].b;
			if(arr[i].a != x and arr[i].b < y){
				stk[++top] = arr[i]; howAdd = 1; break;
			}
		}
		if(!howAdd) stk[++top] = arr[i];
		if(top == 1) ans++;
	}
	cout << ans << '\n'; 
}

int main(){
	n = in;  q = in;
	for(int i = 1; i <= n; i++) arr[i].a = in;
	for(int i = 1; i <= n; i++) arr[i].b = in;
	for(int i = 1; i <= q; i++) { int l = in; int r = in; solve1(l, r); }
	return 0;
}
```

## 30 pts

&emsp; 然后我们考虑如何优化，我们看到对于每一个询问，我们可以预处理出一个数组 $ans[l][r]$ 表示 $区间 [l, r]$ 的答案。具体做法如下（$[expr]$ 表示判断 $expr$ 这个表达式是否为真，若为真返回 $1$，否咋返回 $0$）：

1. 初始值 $ans[i][i] = 1$
2. 枚举左端点（清空栈），然后将左端点入栈。
3. 枚举右端点，每次在栈没空的时候如果符合条件就一直弹出栈顶。然后 $ans[i][j] = ans[i][j-1] + [top = 0]$，并把右端点入栈。

&emsp; 这样就能比上面的直接模拟要快一点点，能捞 $30$ 分。

```cpp
// 30 分暴力
#include<bits/stdc++.h>
using namespace std;
#define in read()
#define MAXN 5050

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int n = 0; int q = 0;
struct Tnode{
	int a; int b;
	bool operator < (const Tnode &x) const { return (a != x.a) and (b < x.b); }       // 能入栈的条件 
	bool operator >= (const Tnode &x) const { return !((*this) < x); }                // 能入栈的条件取反 
	bool operator == (const Tnode &x) const { return a == x.a; }
}arr[MAXN];

int ans[MAXN][MAXN] = { 0 };
int top = 0;
int stk[MAXN] = { 0 };
void solve1(){
	for(int i = 1; i <= n; top = 0, i++){
		ans[i][i] = 1; stk[++top] = i;
		for(int j = i + 1; j <= n; j++){
			while(top and arr[j] >= arr[stk[top]]) top--;
			ans[i][j] = ans[i][j - 1] + (top == 0); stk[++top] = j;
		}
	}
}

int main(){
	n = in;  q = in;
	for(int i = 1; i <= n; i++) arr[i].a = in;
	for(int i = 1; i <= n; i++) arr[i].b = in;
	solve1();
	while(q--) { int l = in; int r = in; cout << ans[l][r] << '\n'; }
	return 0;
}
```

## 40 pts

&emsp; 考场上没想出来怎样继续优化，那就来看看那两组特殊数据怎么做吧。对于 $b_i = n - i + 1$ 的这一组数据的做法是比较好想的。在这组数据中， $b$ 肯定是单减的，所以对于每一个二元组肯定都满足条件，所以我们只用考虑 $a$ 的限制对答案的贡献了。我们记一个后缀树组 $suf[i]$ 表示从 $i$ 开始连续的 $a$ 相等的二元组的个数，用如下方法维护 $suf$ 数组：

1. 倒序循环处理 $suf$
2. 如果相邻的两个二元组的 $a$ 相等，即 $arr_i.a = arr_{i+1}.a$，那么 $suf_i = suf_{i+1} + 1$
3. 其余的情况 $suf_i = 1$

&emsp; 这样处理之后，对于每一个询问 $suf_l$ 就是答案。

```cpp
// 40 分暴力
#include<bits/stdc++.h>
using namespace std;
#define in read()
#define MAXN 5050
#define MAXM 500500

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int n = 0; int q = 0;
struct Tnode{
	int a; int b;
	bool operator < (const Tnode &x) const { return (a != x.a) and (b < x.b); }       // 能入栈的条件 
	bool operator >= (const Tnode &x) const { return !((*this) < x); }                // 能入栈的条件取反 
	bool operator == (const Tnode &x) const { return a == x.a; }
}arr[MAXM];

int ans[MAXN][MAXN] = { 0 };
int top = 0;
int stk[MAXN] = { 0 };
void solve1(){
	for(int i = 1; i <= n; top = 0, i++){
		ans[i][i] = 1; stk[++top] = i;
		for(int j = i + 1; j <= n; j++){
			while(top and arr[j] >= arr[stk[top]]) top--;
			ans[i][j] = ans[i][j - 1] + (top == 0); stk[++top] = j;
		}
	}
	while(q--) { int l = in; int r = in; cout << ans[l][r] << '\n'; }
}

void solvea(){
	return;
}

int suf[MAXM] = { 0 };
void solveb(){
	suf[n] = 1;
	for(int i = n - 1; i; i--){
		if(arr[i] == arr[i + 1]) suf[i] = suf[i + 1] + 1;
		else suf[i] = 1;
	}
	while(q--) { int l = in; int r = in; cout << suf[l] << '\n'; }
}

int main(){
	n = in;  q = in;
	bool flaga = 1; bool flagb = 1;
	for(int i = 1; i <= n; i++) if((arr[i].a = in) != i) flaga = 0;
	for(int i = 1; i <= n; i++) if((arr[i].b = in) != (n - i + 1)) flagb=0;
	if(flaga) solvea();
	else if(flagb) solveb();
	else {
		if(n <= 5000) solve1();
		else{
			
		}
	}
	return 0;
}
```

## 50 pts
&emsp; 现在看看 $a_i = i$ 这两组能不能做。

## 正解

&emsp; &emsp; 预处理出每个位置 $i$ 入栈前弹栈后的栈顶，记为 $p[i]$。然后问题就能转化为 $[l, r]$ 区间中有多少 $p[i]$ 小于 $l$。因为如果 $p[i]$ 小于 $l$ 就说明在扫到 $i$ 的时候就已经把 $l$ 之后的二元组全部弹出栈了（要不然也不会是栈顶了嘛）。然后就可以把所有询问离线下来然后对于左端点排序，处理出扫到一个左端点的时候左端点的值。然后再关于右端点排序，处理出扫到右端点的时候小于左端点的值，处理可以用树状数组做。看代码：

```cpp
#include<bits/stdc++.h>
using namespace std;
#define in read()
#define MAXN 500500

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int n = 0; int q = 0;
int top = 0;
int stk[MAXN] = { 0 };
struct Tnode{
	int a; int b;
	bool operator < (const Tnode &x) const { return (a != x.a) and (b < x.b); }       // 能入栈的条件 
	bool operator >= (const Tnode &x) const { return !((*this) < x); }                // 能入栈的条件取反 
	bool operator == (const Tnode &x) const { return a == x.a; }
}arr[MAXN];

struct BIT{
	int t[MAXN << 2];
	
	inline int lowbit(int x) { return x & -x; }
	void clear() { memset(t, 0, sizeof(t)); }
	void add(int x, int val){
		for(; x <= n; x += lowbit(x)) t[x] += val;
	}
	int query(int x){
		int ans = 0;
		for(; x; x -= lowbit(x)) ans += t[x];
		return ans;
	}
}T;

struct Tq{
	int l; int r;	
}que[MAXN];
int p[MAXN] = { 0 };
int b[MAXN] = { 0 };           // 处理左端点时 b[i] = h[que[i].l]
int h[MAXN] = { 0 };           // 处理左端点时 h[que[i].l] = i
int ans[MAXN] = { 0 };
void solve(){
	stk[++top] = 1; p[1] = 1; T.add(1, 1); 
	for(int i = 2; i <= n; i++){
		while(stk[top] and (arr[i] >= arr[stk[top]])) top--;
		p[i] = stk[top] + 1; stk[++top] = i;
	}
	for(int i = 1; i <= q; i++) { que[i].l = in; que[i].r = in; }            // 把询问离线下来 
	for(int i = 1; i <= q; i++) b[i] = h[que[i].l], h[que[i].l] = i;
	for(int i = 1; i <= n; i++){
		for(int j = h[i]; j; j = b[j]) ans[j] -= T.query(que[j].l);
		T.add(p[i], 1);
	}
	T.clear(); memset(h, 0, sizeof(h));
	for(int i = 1; i <= q; i++) b[i] = h[que[i].r], h[que[i].r] = i;
	for(int i = 1; i <= n; i++){
		T.add(p[i], 1);
		for(int j = h[i]; j; j = b[j]) ans[j] += T.query(que[j].l);
	}
	for(int i = 1; i <= q; i++) cout << ans[i] + 1 << '\n';
}

int main(){
	n = in;  q = in;
	for(int i = 1; i <= n; i++) arr[i].a = in;
	for(int i = 1; i <= n; i++) arr[i].b = in;
	solve();
	return 0;
}
```

## 番外 （100 pts）

&emsp; 考完之后机房大佬 DSW 和 FJN 说可以用莫队卡过去。对于右端点的扩展显然是 $O(1)$ 的，然后左端点的扩展只需要二分找到第一个把这个点弹出去点就可以了。于是这样做的时间复杂度就是 $O(n\sqrt{n}\log n)$。然后加上 ~~亿点点~~ 卡常的技巧就能 $A$ 掉这个题了。

```cpp
// 以下是机房大佬 dsw 的代码
#include<bits/stdc++.h>
#define Maxn 500100
using namespace std;
namespace DEBUG {
	inline void cerr_out(){cerr<<'\n';}
	template<typename Head,typename... Tail>
	inline void cerr_out(Head H,Tail... T){cerr<<' '<<H,cerr_out(T...);}
	void debug_out() { cerr << '\n'; }
	template <typename Head, typename... Tail>
	void debug_out(Head H, Tail... T) { cerr << ' ' << H, debug_out(T...); }
#define debug(...) cerr << '[' << #__VA_ARGS__ << "]:", debug_out(__VA_ARGS__)
} using namespace DEBUG;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int,int> pii;
#define getchar() (S==T&&(T=(S=B)+fread(B,1,1<<15,stdin),S==T)?EOF:*S++)
namespace get_out
{
	char B[1<<20],*S=B,*T=B;
	char op;
	inline void read_(int &x)
	{
		x=0;
		int fi(1);
		op=getchar();
		while((op<'0'||op>'9')&&op!='-') op=getchar();
		if(op=='-') op=getchar(),fi=-1;
		while(op>='0'&&op<='9') x=(x<<1)+(x<<3)+(op^48),op=getchar();
		x*=fi;
		return;
	}
	inline void read_(long long &x)
	{
		x=0;
		int fi(1);
		op=getchar();
		while((op<'0'||op>'9')&&op!='-') op=getchar();
		if(op=='-') op=getchar(),fi=-1;
		while(op>='0'&&op<='9') x=(x<<1)+(x<<3)+(op^48),op=getchar();
		x*=fi;
		return;
	}
	inline void read_(double &x)
	{
		x=0.0;
		float fi(1.0),sm(0.0);
		op=getchar();
		while((op<'0'||op>'9')&&op!='-') op=getchar();
		if(op=='-') op=getchar(),fi=-1.0;
		while(op>='0'&&op<='9') x=(x*10.0)+(op^48),op=getchar();
		if(op=='.') op=getchar();
		int rtim=0;
		while(op>='0'&&op<='9') sm=(sm*10.0)+(op^48),++rtim,op=getchar();
		while(rtim--) sm/=10.0;
		x+=sm,x*=fi;
		return;
	}
	inline void read_(string &s)
	{
		char c(getchar());
		s="";
		while(!isgraph(c)&&c!=EOF) c=getchar();
		for(;isgraph(c);c=getchar()) s+=c;
	}
	inline void read_(char &c)
	{
		for(c=op;c==' '||c=='\n'||c=='\r'||c=='\t';c=getchar());
		op=c;
	}
	
	template<typename Head,typename ...Tail>
	inline void read_(Head& h,Tail&... t)
	{read_(h),read_(t...);}
	
	inline void write_(){}
	inline void postive_write(unsigned x)
	{
		if(x>9) postive_write(x/10);
		putchar(x%10|'0');
	}
	inline void postive_write(unsigned long long x)
	{
		if(x>9) postive_write(x/10);
		putchar(x%10|'0');
	}
	inline void postive_write(int x)
	{
		if(x>9) postive_write(x/10);
		putchar(x%10|'0');
	}
	inline void postive_write(long long x)
	{
		if(x>9) postive_write(x/10);
		putchar(x%10|'0');
	}
	inline void postive_write(short x)
	{
		if(x>9) postive_write(x/10);
		putchar(x%10|'0');
	}
	inline void write_(const char* ss) {while(*ss) putchar(*ss++);}
	inline void write_(char c) {putchar(c);}
	inline void write_(string s) {for(unsigned i=0;i<s.size();++i) putchar(s[i]);}
	inline void write_(short x)
	{
		if(x<0) putchar('-'),postive_write(-x);
		else postive_write(x);
	}
	inline void write_(int x)
	{
		if(x<0) x=-x,putchar('-');
		postive_write(x);
	}
	inline void write_(long long x)
	{
		if(x<0) x=-x,putchar('-');
		postive_write(x);
	}
	inline void write_(unsigned x) {postive_write(x);}
	inline void write_(ull x) {postive_write(x);}
	
	template<typename Head,typename ...Tail>
	inline void write_(Head h,Tail ...t) {write_(h),write_(t...);}
}
using get_out::read_;
using get_out::write_;
vector<int>cnt[Maxn<<1];
int n,F,m,fa[Maxn<<1];
int a[Maxn<<1],b[Maxn<<1],ans_[Maxn<<1];
struct ques
{
	int l,r,id;
	int bl;
}q[Maxn<<1];
inline bool cmp(ques &a,ques &b)
{
	return a.bl!=b.bl?a.bl<b.bl:a.r<b.r;
}
int main()
{
	read_(n,m);
	F=pow(n,0.45)+5;
	for(register int i=1;i<=n;i++) read_(a[i]);
	for(register int i=1;i<=n;i++) read_(b[i]);
	for(register int i=1,k;i<=n;i++)
	{
		cnt[i].emplace_back(-0x7ffffff);
		k=i-1;
		while(k&&((a[k]==a[i])||((a[k]^a[i])&&b[k]<=b[i])))
			k=fa[k];
		cnt[fa[i]=k].emplace_back(i);
	}
	for(register int i=1;i<=m;i++)
	{
		read_(q[i].l,q[i].r);
		q[i].id=i,q[i].bl=q[i].l/F;
	}
	sort(q+1,q+m+1,cmp);
//	cerr<<clock();
	int l=1,r=1,ans=1;
	for(register int i=1;i<=m;i++)
	{
		while(q[i].r>r)
			ans+=(fa[++r]<l?1:0);
		while(q[i].l<l)
		{
			l--,ans++;
			vector<int>::iterator t1=upper_bound(cnt[l].begin(),cnt[l].end(),l);
			vector<int>::iterator t2=upper_bound(cnt[l].begin(),cnt[l].end(),r)-1;
			if(t1!=cnt[l].end()&&t2!=cnt[l].begin())
				ans-=(t2-t1+1);
		}
		while(q[i].r<r)
			ans-=(fa[r--]<l?1:0);
		while(q[i].l>l)
		{
			vector<int>::iterator t1=upper_bound(cnt[l].begin(),cnt[l].end(),l);
			vector<int>::iterator t2=upper_bound(cnt[l].begin(),cnt[l].end(),r)-1;
			if(t1!=cnt[l].end()&&t2!=cnt[l].begin())
				ans+=(t2-t1+1);
			l++,ans--;
		}
		ans_[q[i].id]=ans;
	}
	for(register int i=1;i<=m;i++) write_(ans_[i],'\n');
}
```

&emsp; 

# T2 讨论

&emsp; 传送门 [NOIonline 2022 T2](https://www.luogu.com.cn/problem/P8252)

&emsp; 这道题 $30$ 分暴力非常好拿，众所周知，直接暴力模拟的时间复杂度是 $O(n^2k)$，但是如果你使用神秘的 $STL$ $bitset$ 的话，复杂度就变成了 $O(\frac{n^3}{\omega})$，其中 $\omega = 64$。就能过掉前三个点就是 $1000$ 的点。

```cpp
// 30 分暴力
#include<bits/stdc++.h>
using namespace std;
#define in read()
#define MAXN 5050

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int T = 0; int n = 0;
bitset<MAXN> b[MAXN], e, o;

int main(){
	T = in;
	while(T--){
		n = in; bool flag = 0;
		for(int i = 1; i <= n; i++){
			b[i].reset(); int k = in;
			while(k--) b[i][in] = 1;
		}
		for(int i = 1; i <= n and (!flag); i++)
			for(int j = 1; j <= n; j++){
				o = (b[i] | b[j]);
				if(((b[i] & b[j]) != e) and ((b[i] != o) and (b[j] != o))){
					puts("YES"); cout << i << ' ' << j << '\n'; flag = 1; break;
				}
			}
		if(!flag) puts("NO");
	}
	return 0;
}
```

# T3 如何正确排序
&emsp; 直接暴力模拟有 $10$ 分。

```cpp
// 10 分暴力
#include<bits/stdc++.h>
using namespace std;
#define int long long               // 注意要开 long long
#define in read()
#define MAXN 3030
#define INFI 1 << 30

inline int read(){
	int x = 0; char c = getchar();
	while(c < '0' or c > '9') c = getchar();
	while('0' <= c and c <= '9'){
		x = x * 10 + c - '0'; c = getchar();
	}
	return x;
}

int m = 0; int n = 0;
int a[MAXN][MAXN] = { 0 };
int f[MAXN][MAXN] = { 0 };

signed main(){
	m = in; n = in;
	for(int i = 1; i <= m; i++)
		for(int j = 1; j <= n; j++)
			a[i][j] = in;
	for(int i = 1; i <= n; i++)
		for(int j = 1; j <= n; j++){
			int mm = INFI; int mx = 0;
			for(int k = 1; k <= m; k++){
				mm = min(mm, a[k][i] + a[k][j]);
				mx = max(mx, a[k][i] + a[k][j]);
			}
			f[i][j] = mm + mx;
		}
	int ans = 0;
	for(int i = 1; i <= n; i++)
		for(int j = 1; j <= n; j++)
			ans += f[i][j];
	cout << ans << '\n';
	return 0;
}
```