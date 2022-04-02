# Data Structure

## segment tree

### [luogu1438 无聊的数列](https://www.luogu.com.cn/problem/P1438)

&emsp; (2022.4.1)

&emsp; 就是区间加等差数列和单点询问。

&emsp; 直接线段树维护差分数组，每次对于一个修改 $[l, r]$，在 $l$ 处加上 $s$（等差数列首相），在 $[l + 1, r]$ 处加上 $d$ （等差数列公差），在 $r + 1$ 处减去 $e$（等差数列末项）。然后每一次询问 $p$求出 $\sum\limits_{i = 1}^pc[i]$ 就好了。

## BIT

### [luogu8251 丹钓战](https://www.luogu.com.cn/problem/P8253)
&emsp; (2022.3.26) / ~~这个题也可以用主席树过掉qwq~~

&emsp; 很多二元组 $(a_i, b_i)$，按照 $a_i$ 不同和 $b_i$ 递增来维护单调栈，然后每次询问给定一个区间 $[l, r]$ 询问在这个区间里维护单调栈的时候栈中只有一个元素的时刻的个数。

&emsp; 预处理出每个位置 $i$ 入栈前弹栈后的栈顶，记为 $p[i]$。然后问题就能转化为 $[l, r]$ 区间中有多少 $p[i]$ 小于 $l$。因为如果 $p[i]$ 小于 $l$ 就说明在扫到 $i$ 的时候就已经把 $l$ 之后的二元组全部弹出栈了（要不然也不会是栈顶了嘛）。然后就可以把所有询问离线下来然后对于左端点排序，处理出扫到一个左端点的时候左端点的值。然后再关于右端点排序，处理出扫到右端点的时候小于左端点的值，处理可以用树状数组做。

-----

# Gragh Theory

## bipartite graph

### 一些前置芝士

&emsp; 在二分图 $G =(V, E)$ 中，规定边集 $M \in E$ 表示最大匹配，边集 $F \in E$ 表示最小边覆盖， 点集 $S_1 \in V$ 表示最大独立集，$S_2 \in V$ 表示最小顶点覆盖。

1. 边覆盖表示任意定边至少是 $F$ 中某条边的端点的边集。
2. 独立集表示两两互不相连的定点集合 $S_1$
3. 顶点覆盖表示任何一条边都至少有一个端点在 $S_2$ 中。

&emsp; 然后我们就有：

$$
\begin{aligned}
|M| +|F| & = |V| \\
|S_1| +|S_2| & = |V| \\
|M| & = |S_2|
\end{aligned}
$$

&emsp; 注意前两个式子在任意图中都成立，第三个式子只在二分图中成立。所以在二分图中，我们还能得到：

$$ |M| + |S_1| = |V| $$

### [luogu3386 二分图最大匹配](https://www.luogu.com.cn/problem/P3386)

&emsp; (2022.4.2)

&emsp; 板子题，直接 $bfs$ 找增广路然后每次取反直到找不到为止就是最大匹配了（匈牙利）。


### [luogu4304 攻击装置](https://www.luogu.com.cn/problem/P4304)

&emsp; (2022.4.2)

&emsp; 给一个 $n \times n$ 的中国象棋棋盘，然后给定一些地方可以放置一个 $马$，然后求能在棋盘上放置的最多的马的数量，并保证这些马不会相互共计（马走 "日" 大家应该都知道吧 qwq）。

&emsp; 考虑构图，如果两个点之间马可以相互到达就连一条边，那么就是要求这个无向图的最大独立集 $S_1$。但是一般图的最大独立集是一个 $NPC$ 问题，所以我们考虑证明我们构造出来的图是一个二分图。

&emsp; 如果我们对这个棋盘进行染色，像这样：

![在这里插入图片描述](/Alex/OI/pic/PR.png)

&emsp; 我们就能发现能连边的肯定都是不同颜色的两个点，所以是一个二分图，就能二分图最大匹配了。然后我们在二分图中又有：

$$ |S_1| = |V| - |M| $$

&emsp; 然后就做完了。

## web flow

## tarjan

------

# Dynamic Programming

-----

# Math

## 一些前置芝士

&emsp; 欧拉定理：
$$ \forall gcd(a, n) = 1 \rightarrow a^{\varphi(n)} \equiv 1 \pmod n $$

&emsp; 欧拉定理的推论：

$$ \forall gcd(a, n) = 1 \rightarrow a^{b \mod \varphi(n)} \equiv 1 \pmod n $$

&emsp; 费马小定理：

$$ \forall p 是质数 \rightarrow a^{p - 1} \equiv \pmod p $$

&emsp; exgcd()：

```cpp
int exgcd(int a, int b, int &x, int &y){
	if(b == 0) { x = 1; y = 0; return a; }
	int d = exgcd(b, a % b, x, y);
	int z = x; x = y; y = z - y * (a / b);
	return d;
}
```

&emsp; 中国剩余定理：

$$
(i:1 \to n) \;\;\;
\begin{cases}
x \equiv a_i \pmod {m_i} \\
m = \prod\limits_{i = 1}^nm_i \\
M_i = \frac{m}{m_i} \\
M_it_i \equiv 1 \pmod {m_i} 
\end{cases}
\rightarrow x = \sum_{i=1}^na_iM_it_i 
$$

&emsp; $Lucas$ 定理（把 $n$ 和 $m$ 表示成 $p$ 进制数，然后每一位计算组合数再乘起来）：

$$ p 是质数 \rightarrow C_n^m \equiv C_{n\mod p}^{m\mod p} \times C_{\frac np}^{\frac mp} \pmod p $$

## [luogu2480 古代猪文](https://www.luogu.com.cn/problem/P2480)

&emsp; (2022.4.3)

&emsp; 给你 $q, n \in [1, 10^9]$，算 $q^{\sum\limits_{d | n}C_n^d} \mod p$, 其中 $p = 999911659$。

&emsp; 如果 $q = p$，那么上式结果为 $0$。否则，因为 $p$ 是质数，所以 $gcd(q, n)$ 互质。所以根据欧拉定理的推论，我们就能得到：

$$ q^{\sum\limits_{d | n}C_n^d} \equiv q^{\sum\limits_{d | n}C_n^d \mod (p-1)} \pmod p $$

&emsp; 所以我们只需要知道怎么计算 $\sum\limits_{d|n}C_n^d \mod (p-1)$ 就可以了。经过观察我们发现 $p - 1 = 2 \times 3 \times 4679 \times 35617$，所以它是一个 $square-free-number$（就是所有质因子的指数都是 $1$ 的数 qwq）。所以在这里我们可以枚举 $n$ 的因数 $d$ 然后用 $Lucas$ 定理来计算 $C_n^d \mod (p-1)$，然后分别计算出 $\sum\limits_{d|n}C_n^d$ 对 $2, 3, 4679, 35617$ 四个质数取模的结果。我们把他们记为 $a_1, a_2, a_3, a_4$。

&emsp; 然后我们解出下面这个方程组的根 $x$ 就是 $\sum\limits_{d|n}C_n^d \mod (p-1)$ 的最小非负整数解。

$$
\begin{cases}
x \equiv 2 \pmod {a_1} \\
x \equiv 3 \pmod {a_2} \\
x \equiv 4679 \pmod {a_3} \\
x \equiv 35617 \pmod {a_4}
\end{cases}
$$

&emsp; 解这个方程很显然就是中国剩余定理。然后我们就用快速幂求 $q^x$ 就是答案了。

------

# Construct

## [luogu8252 讨论](https://www.luogu.com.cn/problem/P8252)

&emsp; (2022.3.31)

### 构造

&emsp; 大概就是给 $n$ 个集合，每个集合 $k_i$ 个元素，然后判断这些集合中有没有满足有交且不包含的。如果有输出 $YES$ 和这两个集合的编号，如果没有就输出 $NO$。

&emsp; 我们维护一个 $last[i]$ 数组表示当前枚举到的人中会做 $i$ 题的最后一个人的编号（其实就是染色（后面会说） qwq）。然后我们通过一些分析就能得出一些神奇的性质。

&emsp; 首先，如果我们在枚举第 $now$ 个人会做的题目 $p$ 时，发现 $last_p = 0$。那么说明 $now$ 就是枚举到的人中第一个会做 $p$ 这道题的人。如果是这样就比较好处理，我们只需要找到一个在前面找一个与 $i$ 有交的人就好了（因为有题目 $p$ 的存在所以前面的人不可能包含 $now$）。

&emsp; 那么第二种情况 $last_p$ 有值，现在的问题就是要求 $last_p$ 和 $now$ 不能是包含关系。我们为了方便，记 $x = last_p$。然后我们继续枚举其他题，发现有另一道题的 $last_{p'} \neq x$。那我们把现在的 $last_{p'}$ 也记录下来记为 $y$。现在我们就有了两个不同的人，他们**都满足与 $now$ 有题目交集。** 

&emsp; 现在我们找到 $x$ 和 $y$ 中 $k$ 较小的那个（假设 $k[x] > k[y]$）。因为排序，所以 $y$ 的枚举的顺序肯定在 $x$ 之后，又因为 $last_{p} = x \neq y$，所以 $y$ 肯定不会做题 $p$，又因为 $now$ 会做 $p$，**所以 $now$ 和 $y$ 肯定不是包含关系**，那么久满足了这道题的要求。那我们就输出 $now$ 和 $y$ 就搞定了。

## 染色

&emsp; 前面的构造没看懂没关系，我们来看看更容易理解的一种算法（其实和构造是差不多等效的），看完染色再回去看构造应该就能有更深的理解 qwq。首先对所有人按照所对应的集合的 $size$ 从大到小排序，然后从头到尾扫一遍，然后执行以下操作：

1. 对于每一个人的集合中的题将他们染成同一个颜色。
2. 如果当前这个人的题目集合跨越了两个颜色，那么我们就找到了要讨论的人。

&emsp; 我们对所有人进行排序是为了保证之后扫到的人不可能包含前面扫到的人的集合。按照这个算法进行在找到能讨论的两个人之前染色的情况应该是只有包含和不交两种情况，像这样：

![在这里插入图片描述](/Alex/OI/pic/NOIonline221.png)

&emsp; 然后我们找到了一个能讨论的集合包含了两种颜色，就像这样：

![在这里插入图片描述](/Alex/OI/pic/NOIonline222.png)

&emsp; 就直接找然后输出就做完了。