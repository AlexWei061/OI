~~2018 年 7 月 19 日，某位同学在 NOI Day 1 T1 归程 一题里非常熟练地使用了一个广为人知的算法求最短路。~~

~~然后呢？~~

~~$100 \rightarrow 60；$~~

~~$\text{Ag} \rightarrow \text{Cu}；$~~

~~最终，他因此没能与理想的大学达成契约。~~

~~小 F 衷心祝愿大家不再重蹈覆辙。~~

原题传送门 [luoguP4768 NOI2018T1 归程](https://www.luogu.com.cn/problem/P4768)

# 题目大意
&emsp; 本题的故事发生在魔力之都，在这里我们将为你介绍一些必要的设定。 魔力之都可以抽象成一个 $n$ 个节点、$m$ 条边的无向连通图（节点的编号从 $1$ 至 $n$）。我们依次用 $l,a$ 描述一条边的长度、海拔。

&emsp; 作为季风气候的代表城市，魔力之都时常有雨水相伴，因此道路积水总是不可避免的。由于整个城市的排水系统连通，因此有积水的边一定是海拔相对最低的一些边。我们用水位线来描述降雨的程度，它的意义是：所有海拔不超过水位线的边都是有积水的。

&emsp; Yazid 是一名来自魔力之都的 OIer，刚参加完 ION2018 的他将踏上归程，回到他温暖的家。Yazid 的家恰好在魔力之都的 $1$ 号节点。对于接下来 $Q$ 天，每一天 Yazid 都会告诉你他的出发点 $v$ ，以及当天的水位线 $p$。

&emsp; 每一天，Yazid 在出发点都拥有一辆车。这辆车由于一些故障不能经过有积水的边。Yazid 可以在任意节点下车，这样接下来他就可以步行经过有积水的边。但车会被留在他下车的节点并不会再被使用。 需要特殊说明的是，第二天车会被重置，这意味着：

&emsp; 车会在新的出发点被准备好。

&emsp; Yazid 不能利用之前在某处停放的车。

&emsp; Yazid 非常讨厌在雨天步行，因此他希望在完成回家这一目标的同时，最小化他步

&emsp; 行经过的边的总长度。请你帮助 Yazid 进行计算。

&emsp; **本题的部分测试点将强制在线，具体细节请见【输入格式】和【子任务】。**

&emsp; 单个测试点中包含多组数据。输入的第一行为一个非负整数 $T$，表示数据的组数。对于每一组数据：

&emsp; 第一行 $2$ 个非负整数 $n,m$，分别表示节点数、边数。接下来 $m$ 行，每行 $4$ 个正整数 $u, v, l, a$，描述一条连接节点 $u, v$ 的、长度为 $l$、海拔为 $a$ 的边。 在这里，我们保证 $1 \leq u,v \leq n$。

&emsp; 接下来一行 $3$ 个非负数 $Q, K, S$ ，其中 $Q$ 表示总天数，$K \in \{0, 1\}$ 是一个会在下面被用到的系数，$S$ 表示的是可能的最高水位线。

&emsp; 接下来 $Q$ 行依次描述每天的状况。每行 $2$ 个整数 $v_0; p_0$ 描述一天：这一天的出发节点为 $v = (v_0 + K \times \mathrm{lastans} - 1) \bmod n + 1v=(v 
0$。这一天的水位线为 $p = (p_0 + K \times \mathrm{lastans}) \bmod (S + 1)$。其中 $\mathrm{lastans}$ 表示上一天的答案（最小步行总路程）。特别地，我们规定第 $1$ 天时 $\mathrm{lastans} = 0$。 在这里，我们保证 $1 \leq v_0 \leq n,0 \leq p_0 \leq S$。

# 算法分析

&emsp; 这个题目看起来非常的长啊，但是总结下来就是说每次我们要把走的路径分成两部分（根据水位），一部分开车，另一部分走路，然后要求我们走路的边权和最小。于是我们很容易能够想到一个很朴素的暴力算法：枚举所有可行断点（就是走路和开车的分界点）然后计算每种走法的路程并取最小就是答案了。现在我们考虑如何优化这个算法：

&emsp; 假设当前断点为 $u$。若取 $u$ 是最优的显然要满足一下几个性质：

1. 存在一条从 $v$ 到 $u$ 的路径且在这条路径上都能开车（海拔足够高）
2. 从 $u$ 到 $1$ 的路经长度是最短的（很显然的单元最短路）

&emsp; 所以我们很容易能想到运用 kruskal 重构树来解决这个问题（如果你不知道 kruskal 重构树是什么可以看这里：[kruskal 重构树](https://blog.csdn.net/ID246783/article/details/123446811)）。显然在这里我们根据边的海拔降序排列建立重构树，对于每个询问我们找到**深度最小且海拔大于水位线的节点**。那这显然个节点的**子树中所有的节点**都可以从 $v$ 开车到达（因为子树的海拔要比父亲的大）。

&emsp; 求解**深度最小且海拔大于水位线的节点**可以用倍增来实现。然后就对子树中的所有节点到 $1$ 的最短路（在之前预处理出来就行啦，但是注意不要用 SPFA qwq）比个大小（在树上 dfs）就能得出答案了。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define INF 0x3f3f3f3f
#define MAXN (int)(4e5 + 10)

inline int read(){
    int x = 0; char ch = getchar();
    while(ch < '0' or ch > '9') ch = getchar();
    while(ch >= '0' and ch <= '9') x = (x << 3) + (x << 1) + ch - '0', ch = getchar();
    return x;
}

int T = 0;
int n = 0; int m = 0;
int lastans = 0;
struct node{
    int u, v, l, a, nxt;
    bool operator < (const node &b) const{
        return a > b.a;                      //小根堆
    }
}e[MAXN << 1], tmp[MAXN << 1], edge[MAXN << 1];

int tot, first[MAXN], dis[MAXN];
struct heap{
    int x, dis;
    bool operator < (const heap &b) const{
        return dis > b.dis;
    }
};
int f[MAXN], cnt;

inline void Add(int x, int y, int z){
    edge[++tot].v = y; edge[tot].l = z;
	edge[tot].nxt = first[x]; first[x] = tot;
}

inline void dijkstra(){                    //求出从1到各点的最短路
    priority_queue <heap> q;
    memset(dis, 0x3f, sizeof(dis));
    dis[1] = 0; q.push((heap){1, 0});
    while(!q.empty()){
        heap now = q.top(); q.pop();
        int x = now.x;
        if(dis[x] < now.dis) continue;
        for(int e = first[x]; e; e = edge[e].nxt){
            int y = edge[e].v;
            if(dis[y] > dis[x] + edge[e].l){
                dis[y] = dis[x] + edge[e].l;
                q.push((heap){y, dis[y]});
            }
        }
    }
}

inline int find(int x){
	if(x == f[x]) return f[x];
	else return f[x] = find(f[x]);
}

inline void add(int x, int y){
    edge[++tot].v = y;
	edge[tot].nxt = first[x];
    first[x] = tot;
}

inline void kruskal(){                // 重构树
    sort(e + 1, e + 1 + m);
    for(int i = 1; i <= n; i++) f[i] = i;
    cnt = n; int num = 0;
    for(int i = 1; i <= m; i++){
        int fu = find(e[i].u), fv = find(e[i].v);
        if(fu != fv){
            num++;
            tmp[++cnt].a = e[i].a;
            f[fu] = f[fv] = f[cnt] = cnt;
            add(cnt, fu), add(cnt, fv);
        }
        if(num == n - 1) break;
    }
}

int fa[MAXN][20], dep[MAXN];

inline void dfs(int x, int p){
    dep[x] = dep[p] + 1, fa[x][0] = p;
    for(int i = 1; i <= 19; i++)
        fa[x][i] = fa[fa[x][i - 1]][i - 1];
    for(int i = first[x]; i; i = edge[i].nxt){
        int y = edge[i].v;
        dfs(y, x);
        tmp[x].l = min(tmp[x].l, tmp[y].l);
    }
}

inline int query(int x, int y){             //从x出发往上跳，找到海拔大于y的最小的点
    for(int i = 19; i >= 0; i--)
        if(dep[x] - (1 << i) > 0 && tmp[fa[x][i]].a > y)
            x = fa[x][i];
    return tmp[x].l;
}

inline void solve(){
    kruskal();
    dfs(cnt, 0);
    int q = read(), k = read(), s = read();
    while(q--){
        int x = (k * lastans + read() - 1) % n + 1, y = (k * lastans + read()) % (s + 1);
        printf("%d\n", lastans = query(x, y));
    }
}

inline void init(){
    memset(first, 0, sizeof(first));
    memset(fa, 0, sizeof(fa));
    memset(f, 0, sizeof(f));
    memset(tmp, 0, sizeof(tmp));
    memset(edge, 0, sizeof(edge));
    lastans = tot = 0;
}

int main(){
    T = read();
    while(T--){
        init();
        n = read(), m = read();
        for(int i = 1; i <= m; i++){
            e[i].u = read(), e[i].v = read(), e[i].l = read(), e[i].a = read();
            Add(e[i].u, e[i].v, e[i].l), Add(e[i].v, e[i].u, e[i].l);
        }
        dijkstra();
        for(int i = 1; i <= n; i++) tmp[i].l = dis[i];
        for(int i = n + 1; i <= (n << 1); i++) tmp[i].l = INF;
        memset(first, 0, sizeof(first)), tot = 0;
        solve();
    }
    return 0;
}
```