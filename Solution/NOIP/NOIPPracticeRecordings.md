# Data Structure

## segment tree

### [luogu1438 无聊的数列](https://www.luogu.com.cn/problem/P1438)

&emsp; (2022.4.1)

&emsp; 就是区间加等差数列和单点询问。

&emsp; 直接线段树维护差分数组，每次对于一个修改 $[l, r]$，在 $l$ 处加上 $s$（等差数列首相），在 $[l + 1, r]$ 处加上 $d$ （等差数列公差），在 $r + 1$ 处减去 $e$（等差数列末项）。然后每一次询问 $p$求出 $\sum\limits_{i = 1}^pc[i]$ 就好了。

### [P4587 [FJOI2016]神秘数](https://www.luogu.com.cn/problem/P4587)

&emsp; (2022.5.21)

&emsp; 求区间最小的不能被子集合表示出的数。

&emsp; 考虑一个区间 $[l, r]$，对这一段进行一个升序排序，设排序之后的数分别为 $a_l \leq a_{l+1} \leq \cdots \leq a_r$，现在对于 $a_l$ 如果不等于 $1$，那就可以直接输出 $1$。若等于 $1$ 我们考虑一个简单的 $dp$。

&emsp; 现在用 $pos$ 表示当前能表示出的数属于的区间为 $[1, pos]$（很显然这个区间包括了 $1$），对于下一个数 $a_i$，现在有两种情况：

1. 如果 $a_i \leq pos + 1$，那么现在能表示出的数的集合就是 $[1, pos] \bigcup [1 + a_i, pos + a_i] = [1, pos + a_i]$。
2. 如果 $a_i> pos + 1$，那么现在能表示出的数的集合就是 $[1, pos] \bigcup [1 + a_i, pos + a_i]$，显然这个集合中不包含 $pos + 1$，所以答案就是 $pos + 1$。

&emsp; 这样做对于每次询问的时间复杂度是 $O(n\log n)$ 的，所以要考虑优化这个做法。

&emsp; 如果现在的值域是 $[1, pos]$，那么现在的答案就应该是 $pos + 1$。记小于等于 $ans$ 的数的和为 $res$，如果 $res \geq ans$，那么就说明一定有没有选过（选一些数的和为 $ans$）的且小于等于 $ans$ 的数存在，那么现在令 $ans = res + 1$，反过来就说明答案就是 $ans$，直接 $break$ 就好了，注意到 $\sum a_i <  1e9$，所以可以考虑用主席树维护区间值域和。


## BIT

### [luogu8251 丹钓战](https://www.luogu.com.cn/problem/P8253)
&emsp; (2022.3.26) / ~~这个题也可以用主席树过掉qwq~~

&emsp; 很多二元组 $(a_i, b_i)$，按照 $a_i$ 不同和 $b_i$ 递增来维护单调栈，然后每次询问给定一个区间 $[l, r]$ 询问在这个区间里维护单调栈的时候栈中只有一个元素的时刻的个数。

&emsp; 预处理出每个位置 $i$ 入栈前弹栈后的栈顶，记为 $p[i]$。然后问题就能转化为 $[l, r]$ 区间中有多少 $p[i]$ 小于 $l$。因为如果 $p[i]$ 小于 $l$ 就说明在扫到 $i$ 的时候就已经把 $l$ 之后的二元组全部弹出栈了（要不然也不会是栈顶了嘛）。然后就可以把所有询问离线下来然后对于左端点排序，处理出扫到一个左端点的时候左端点的值。然后再关于右端点排序，处理出扫到右端点的时候小于左端点的值，处理可以用树状数组做。

### [luogu1966 火柴排队](https://www.luogu.com.cn/problem/P1966)

&emsp; (2022.4.16)

&emsp; 给两个序列 $\{a_n\}$ 和 $\{b_n\}$，然后定义这两个序列的距离为 $\sum\limits_{i=1}^n(a_i - b_i)^2$。现在给你一个操作，每次操作交换一个序列中相邻的两个数，问最少操作多少次使得两数组的距离最小。

&emsp; 我们考虑展开这个式子：

$$
\begin{aligned}
& \sum_{i = 1}^n (a_i - b_i)^2 \\
= & \sum_{i = 1}^n (a_i^2 +b_i^2 - 2a_ib_i) \\
= & \sum_{i = 1}^n a_i^2 + \sum_{i = 1}^n b_i^2 - 2\sum_{i = 1}^n a_ib_i
\end{aligned}
$$

&emsp; 我们发现 $\sum\limits_{i = 1}^n a_i^2 + \sum\limits_{i = 1}^n b_i^2$ 是不受数列顺序影响的，所以我们有：

$$
\begin{aligned}
\left(\sum_{i = 1}^n (a_i - b_i)^2\right)_{min} 
= & \sum_{i = 1}^n a_i^2 + \sum_{i = 1}^n b_i^2 - 2\left(\sum_{i = 1}^n a_ib_i\right)_{max} \\ 
= & Const_A + Const_B + 2\left(\sum_{i = 1}^n a_ib_i\right)_{max}
\end{aligned}
$$

&emsp; 所以现在就考虑如何最大化 $2\sum_{i = 1}\limits^n a_ib_i$。

&emsp; 这里就有一个结论，就是说如果 $a_i$ 和 $b_i$ 都是升序的时候上面的式子就取到最大值。现在考虑如何证明，考虑微扰法。

&emsp; 假设 $a$ 和 $b$ 已经排好序了，现在答案就是 $\sum\limits_{i = 1}^n a_ib_i$，我们考虑交换 $a_i$ 和 $a_{i +1}$（$a_i < a_{i +1}$）。那么现在的答案就是 $\sum\limits_{i = 1}^n a_ib_i + (a_ib_{i+1} + a_{i+1}b_i) - (a_ib_i + a_{i+1}b_{i+1})$，然后对式子进行化简：

$$
\begin{aligned}
& (a_ib_{i+1} + a_{i+1}b_i) - (a_ib_i + a_{i+1}b_{i+1}) \\
= & a_ib_{i+1} - a_ib_i + a_{i +1}b_i - a_{i +1}b_{i +1} 
= (a_i - a_{i+1})(b_{i +1} - b_i) < 0
\end{aligned}
$$

&emsp; 所以我们发现，如果对这个数列进行微扰就会使答案减小，所以数列在升序的时候答案最大，证毕。

&emsp; 这样之后，我们就是要把两个数列交换成**升序的为止互相对应**就好了（注意，不是真的升序而是升序的位置对应），那就先考虑将两个数组离散化然后构造一个新的数组 $q_i$，其中 $q_{a_i} = b_i$。这样的话如果位置是对应的就应该有 $q_i = i$ 也就是  $q_i$ 升序，那么我们只需要求出 $q$ 的逆序对个数就是这道题要求的答案了。

-----

# Gragh Theory

## kruskal

### [luogu4768 NOI2018T1 归程](https://www.luogu.com.cn/problem/P4768)

&emsp; (2022.3.12)

&emsp; 给一张无向连通图，每个点有一个水位。每天我们要把走的路径分成两部分，一部分开车，另一部分走路（水位低于今天水位的点只能走路），然后要求我们走路的边权和最小（每天的初始位置和水位线不一样）。

&emsp; 假设当前断点为 $u$。若取 $u$ 是最优的显然要满足一下几个性质：

1. 存在一条从 $v$ 到 $u$ 的路径且在这条路径上都能开车（海拔足够高）
2. 从 $u$ 到 $1$ 的路经长度是最短的（很显然的单元最短路）

&emsp; 所以我们很容易能想到运用 kruskal 重构树来解决这个问题（如果你不知道 kruskal 重构树是什么可以看这里：[kruskal 重构树](https://blog.csdn.net/ID246783/article/details/123446811)）。显然在这里我们根据边的海拔降序排列建立重构树，对于每个询问我们找到**深度最小且海拔大于水位线的节点**。那这显然个节点的**子树中所有的节点**都可以从 $v$ 开车到达（因为子树的海拔要比父亲的大）。

&emsp; 求解**深度最小且海拔大于水位线的节点**可以用倍增来实现。然后就对子树中的所有节点到 $1$ 的最短路（在之前预处理出来就行啦，但是注意不要用 SPFA qwq）比个大小（在树上 dfs）就能得出答案了。


### [WOJ10028 贸易](http://192.168.110.25/p/P10028)

&emsp; (2022.4.3)

&emsp; 就是给定一个无向图，然后让你保证每个边都有且仅有 $1$ 的入度。也就是转化成最小生成基环树森林。显然我们只需要把 $kruskal$ 稍微改造一下，也就是换一下判断是否加边的条件就能过掉这个题了：

&emsp; 我们考虑怎样加边来维护基环树森林。判断一条边能否加入的时候，如果这两个端点已经连通了，我们就需要知道这个连通块是否有环，如果没有环就连在一起，并标记他现在有环。如果不连通，那么还是判断两个有没有环。如果都有环就不加边，否则就加边并且标记两边是否有环。

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

## tree

### [NOIP2015 运输计划](https://www.luogu.com.cn/problem/P2680)

&emsp; (2022.5.28)

&emsp; 考虑二分答案，现在的问题就是如何实现 $check(x)$。

&emsp; 考虑找到一条边，使得路径长度大于 $x$ 的所有路径都有这条边，并且减去这条边的边权之后路径长度小于等于 $x$。

&emsp; 所以我们就先用 $lca$ 预处理出所有路径的长度，然后对于每一个 $x$，记录每条边有多少原长超过 $x$ 的路径经过（树上差分，路径覆盖），再遍历一遍整棵树找到有没有满足条件的边就可以了。

------

# Dynamic Programming

## knapsack

### [luogu1734 最大约数和](https://www.luogu.com.cn/problem/P1734)

&emsp; (2022.4.3)

&emsp; 给一个正整数 $S \leq 1000$，要求选出若干个互不相同的正整数，是他们的和不大于 $S$，而且每个数的因数（不包含自身）之和最大。

&emsp; 显然（虽然考试的时候想了很久 qwq）是一个 $0/1$ 背包。物品就是要选出的正整数，容量是 $S$，然后每个的物品的因数之和。显然因数之和可以暴力 $O(S\sqrt{S})$ 预处理出来。所以总的复杂度就是 $O(S^2 + S\sqrt{S})$。

## digital dp

### [luogu P2602 数字计数](https://www.luogu.com.cn/problem/P2602)

&emsp; (2021.10.27)

&emsp; 给定两个正整数 a 和 b，求在 [a,b] 中的所有整数中，每个数码(digit)各出现了多少次。输出包含一行十个整数，分别表示 0∼9 在 [a,b] 中出现了多少次。

&emsp; 乍一看好像是一道大水题，再一看数据范围，$10^{12}$ .....，好吧这题不水。

&emsp; 但是这道题还是很良心的啊，都把 digit 数码在题目中给你写出来了，这不明摆着就是一道数位 DP 的题吗。那我们就来看看能不能把递推式给整出来。 

&emsp; 总思路：我们把从 a 到 b 的所有数字中的各个数码出现的次数转化成这样：1 ~ b 中的所有数字的各个数码出现的次数减去 1 ~ a - 1 中的所有数字的各个数码出现的次数。所以我们现在考虑怎样求出 1 ~ n 的所有数字的各个数码的出现次数。

&emsp; 首先，我们发现这一道题有一个很烦人的东西，叫做 "前导0"，因为有这个东西，所以我们很难直接写出递推式，只能在考虑了没有它的情况之后再减去考虑它之后的那些多出来的值。给没学过数位 DP 的读者们解释一下，"前导0" 是指我们在枚举数位的时候会碰到枚举出来的数是这样的情况：$\overline{0000abc...}$，这些真正的数字前面的 "0" 们我们就叫它们 "前导0"。

&emsp; 首先考虑没有前导 0 的情况，根据我们的 ~~数学常识~~ 我们可以知道，在 0 ~ 9，0 ~ 99，0 ~ 999 ... 这些区间里面，所有数字的出现次数是相同的。不知道这个 ~~c常识~~ 的读者可以试着自己打打表验证一下。如果我们把这些数字出现的次数算出来的话，我们会发现下面这样的规律：

$$
\begin{aligned}
0 \sim 9 &: 1\\
0 \sim 99 &: 20\\
0 \sim 999 &: 300\\
0 \sim 9999 &: 4000\\
0 \sim 99999 &: 50000\\
\vdots
\end{aligned}
$$

&emsp; 这个规律应该是很明显的了，如果我们把所有的 $i$ 位数（含前导0）中隔个数字出现的总次数记为 $f_i$ 那么我们就有 $f_i = 10 \times f_{i-1} + 10^{i-1}$。

&emsp; 既然我们有了这个规律，很自然的我们根据分治的思想可以想到把数字 a 分成不同的部分，我们考虑这样分（假设 $a = \overline{ABCD}$）：从最高位开始分别处理不同的位数，也就是分别处理：$\overline{A000}$，$\overline{0B00}$， $\overline{00C0}$ 和 $\overline{000D}$。

&emsp; 首先我们来讨论如何处理最高位： $\overline{A000}$。因为我们知道 0 ~ 999 之间的每个数字都出现了 $f_3$ 次，所以我们考虑先把这个 $\overline{A000}$ 看成 $\overline{0000} \sim \overline{1000} \sim \overline{2000} \sim \cdots \sim \overline{A000}$。感性的来讲，是不是所有数字都出现了 $A \times f_3$ 次呢？不是的，因为除了这些数字，还要加上首位出现的次数，也就是比 A 小的每个数要多出现 $10^3$ 次。

&emsp; 这应该比较好理解，如果不理解的话我简单的说一下，我们知道 $0 \sim 999$ 中的所有数字的各个数码出现次数相等且都是 $f_3$ 次。那我们是不是可以把这些书写成这样 $\overline{0000}， \overline{0001}，\overline{0002}， \cdots，\overline{0999}$，再看从 $1000 \sim 1999$ 的这些数字，如果忽略最高位 1，那么是不是他们就和 $0 \sim 999$ 一模一样，所以从 $1000 \sim 1999$ 中，所有数字都要出现 $f_3$ 次，且首位 1 会多出现 $10^3$ 次（因为每一个数都多一个 1）。同理：$2000 \sim 2999$ 中的每个数字都出现 $f_3$ 次且数字 2 多出现了 $10^3$ 次。以此类推。

&emsp; 但是这还没完，首位 A 出现的次数没有被统计下来，因为在后面还会有 $\overline{A001}，\overline{A002}，\overline{A003}，\cdots，\overline{ABCD}$，也就是说最高位 A 还要再多出现 $\overline{BCD} + 1$ 次。

&emsp; 把这些因素都考虑进去之后，我们就把首位给处理完了。接着就是用同样的方法来处理抛开最高位之后剩下的最高位了。

&emsp; 当然，我们前面说的都是在一个大前提之下：不考虑前导0。所以现在我们需要把数字 0 出现的次数减去那些前导0 的个数。还是可以利用 "打表大法" 和 "瞪眼观察大法"，我们可以推出前导0的个数等于：$10^{i-1} + 10^{i-2} + \cdots + 10$。然后整道题就愉快地 A 掉了。

&emsp; 综上所述，我们梳理一下求解的流程。
1. 预处理（或打表）出来一个 f 数组，其中 $f_i$ 表示在所有的 i 位数（包含前导0）中各个数码出现的次数。数值上 $f_i = i \times 10^{i-1}$。
2. 循环枚举各个数位，对每个数位进行以下操作（设这个数位是第 i 位上的数码为 A）：每个数码出现的次数加上 $A \times f_{i-1}$，然后比 A 小的数码出现次数再加上 $10^3$，然后 A 出现的次数再加上 $\overline{BCD} + 1$ 次。
3. 数字 0 出现的次数减去 $\sum\limits_{n=1}^{i-1}10^n$ 次。

## range dp

### [luogu5569 SDOI2008 石子合并](https://www.luogu.com.cn/problem/P5569)

&emsp; (2021.11.12)

&emsp; 现在有 $N$ 堆石子排成一排，其中第 $i$ 堆石子的重量为 $A_i$，每次可以选相邻的两堆石子合并起来，然后形成新的石子堆的重量是原来两堆石子的重量和，并且会得到与合并之后的重量相等的分数，求把所有的石子都合并到同一堆的时候的得分的最小值。

&emsp; 显然，如果最初的第 $l$ 堆石子要和第 $r$ 堆石子合并，那么 $l \sim r$ 的石子肯定都被合并过了，因为这样 $l$ 和 $r$ 才可能相邻。所以在所有的时刻的每一堆石子我们可以用一个闭区间 $[l, r]$ 来表示（表示这堆石子是由最初的时刻的第 $l \sim r$ 堆石子合并而来的）。这堆石子的重量就显然是 $\sum\limits_{i=l}^r A_i$。另外，肯定存在一个 $k$ 使得，在 $[l, r]$ 被合并成同一堆之前，有两堆石子 $[l, k]$ 和 $[k+1, r]$。然后这两堆石子合并成了 $[l, r]$。这就意味着两个长度较小的区间上的信息会向一个长度更大的区间上转移，所以我们很自然的用一个区间的长度作为动态转移的**阶段**，并且用区间的左右端点作为 DP 的**状态**（$len = r - l + 1$）。我们记 $F[l, r]$ 表示把最初的 $l \sim r$ 合并成一堆所得到的分数，于是我们有：
$$ F[l, r] = \min_{l \leq k < r}\lbrace F[l, k] + F[k+1, r] \rbrace + \sum_{i=l}^r A_i$$

&emsp; 其中：$\forall l \in [1, N]，F[l, l] = 0$，我们要求的答案就是 $F[1, N]$。然后对于每次查询 $\sum\limits_{i=l}^r A_i$，我们对 $A$ 数组做一个前缀和就好了。

&emsp; 但是这个做法是 $n^2$ 的，对于这个题 40000 的数据范围来说，肯定过不了，这个算法就只能得到 60分。但是因为我今天主要是来学 DP 的，我也不太怎么看得懂这个 GarsiaWachs 算法。所以就这样吧。


### [luogu2893 USACO08FEB Making the Grade G](https://www.luogu.com.cn/problem/P2893)
&emsp; (2021.11.12)

&emsp; 给定一个长度为 $N$ 的序列 $A$ 让你构造一个长度也为 $N$ 的序列 $B$，且 $B$ 非严格单调(不限制单增或单减)，且要最小化 $T = \sum\limits_{i=1}^N \mid A_i - B_i \mid$。

&emsp; 因为这里分成两种情况，一种 $B$ 是非严格单增的，另一种 $B$ 是非严格单减的，所以我们考虑分别对这两种情况进行求解，最后再去一个最小值就好了。下面我们就以 $B$ 是非严格单增来举例子（非严格单减同理）。

&emsp; 在分析的过程中，我们发现一个神奇的现象，在满足最小化 $T$ 的前提下，一定存在一种构造 $B$ 的方案，使得 $B$ 中的所有数值都在 $A$ 中出现过。首先，当 $N = 1$ 的时候这个命题显然成立（最小化 $T$ 之间让 $B_1 = A_1$ 就好了）。之后我们考虑先假设这个命题对 $N = k-1$ 成立，如果能推出这个命对 $N = k$ 成立，在运用数学归纳法，就能证明这个命题对所有 $N$ 都成立了。

&emsp; 现在设命题对 $N = k-1$ 时成立，且此时构造出来的序列是 $B_1 \sim B_{k-1}$。然后我们来分类讨论 $B_{k-1}$ 和 $A_k$ 的大小情况：

1. 如果 $B_{k-1} \leq A_k$，则令 $B_k = A_k$ 则我们就把 $k$ 这里消成了 0，而且还满足单增。并且前面已经构造出最小的了，所以现在肯定也是最小的，此时命题成立。
2. 当 $B_{k-1} > A_k$ 的时候还要分成两种情况，第一种情况是直接令 $B_k = B_{k-1}$ 命题成立。
&emsp; 第二种当这样直接构造不成立的时候就要考虑其他的构造方式了。因为直接令 $B_{k} = B_{k-1}$ 不行了，所以我们考虑下调 $B$ 数列前面的数，因为要向下调整，所以我们前面构造好的数也要向下一起调整。
&emsp; 所以我们发现，我们一定能够找到一个 $j$ 然后重新令 $B_{j}, B_{j+1}, \cdots B_{k}$ 都等于一个新的值 $x$，使得原命题成立。但是为什么要统一下调到 $x$ 呢，难道不存在一种可能使得前面的数下调到比 $x$ 还小使得整个数列的 $T$ 更优吗？显然是不存在的，因为如果可以让前面的更小来满足更优那么这个数就不会在 $[j, k]$ 这一段里，它会在之前构造 $k-1$ 的长度的时候就被调成更小的而满足最优。
&emsp; 这个时候问题就变成了找到一个数 $x$ 证明当 $T' = \sum\limits_{i=j}^k \mid x - A_i \mid$ 时 $x$ 一定在 $A$ 里面出现过。而对于这个式子： $T' = \sum\limits_{i=j}^k \mid x - A_i \mid$，其实就是一个中位数问题。设 $mid$ 是 $A[j\sim k]$ 的中位数，如果 $mid \geq B_{j-1}$，让 $x = mid$ 就能最小化 $T'$。当 $mid < B_{j-1}$ 的时候我们就让 $x = B_{j-1}$ 就能保证最优且单调。而且 $mid$ 和 $B_{j-1}$ 都是 $A$ 中的数值。所以原命题成立。

&emsp; 我们上面已经证明了这个神奇的命题，那下面转移方程就比较容易了。我们设 $F[i]$ 表示对于 $A[1\sim i]$ 完成构造，且 $A[i] = B[i]$ 的时候，$T$ 的最小值。我们仿照 LIS 就能得到下面的动态转移方程：
$$ F[i] = \min_{0 \leq j < i， A[j] \leq A[i]}\lbrace F[j] + c(j+1, i-1) \rbrace $$

&emsp; 其中，我们设 $c(j+1, i-1)$ 表示构造 $B_{j+1}, B_{j+1}, \cdots B_{i-1}$ 的最小代价。也就是构造出 $B_{j+1}, B_{j+1}, \cdots B_{i-1}$，且满足 $A_j \leq B_{j+1} \leq B_{j+1} \leq \cdots \leq B_{i-1} \leq A_i$ 的时候 $\sum\limits_{k=j+1}^{i-1}\mid A_k - B_k \mid$ 的最小值。

&emsp; 现在我们就要想办法求解出 $c(j+1, i-1)$ 等于多少了。根据我们上面的神奇的命题，$B$ 数列一定是由一段一段的 $A$ 数列里面的值组成的，又因为 $i$ 处刚好 $A[i] = B[i]$ 且 $j$ 处也满足 $A[j] = B[j]$，所以我们要构造的中间的一坨东西就是前面一部分是 $A[j]$，后面一部分是 $A[i]$ 的一个序列，然后我们就用一个 $k$ 来枚举分界点就好了。这样的算法的时间复杂度就是 $O(n^3)$，显然过不了。 

&emsp; 然后我们考虑优化一下，一个状态的转移不行，我们就多加一个状态，我们直接把上一次构造的 $B$ 序列的最后一个值也加在状态里面，也就是记 $F[i, j]$ 表示完成对 $A[1\sim i]$ 的构造，且构造出来的 $B$ 中 $B[i] = j$ 时 $T$ 的最小值。那么我们就有：
$$ F[i, j] = \min_{0 \leq k < j} \lbrace F[i-1, k] + \mid A[i] - j \mid \rbrace $$

&emsp; 然后我们可以对 $A$ 进行离散化，把状态的第二维降到 $O(n)$。然而状态转移还是 $O(n)$ 的，所以整个的复杂度也还是 $O(n^3)$ 的。但是我们可以继续优化，我们记 $S(j)$ 表示满足转移条件 $0 \leq k < j$ 的所有 $k$ 的集合。我们发现，在每一次枚举 $j$ 的时候这个决策集合只增不减，所以在找 $k$ 的时候我们就不用每次都枚举 $k$，直接用 $j$ 来代表 $k$ 就可以了，这样我们就可以做到 $O(1)$ 的转移。

### [luogu7914 括号匹配](https://www.luogu.com.cn/problem/P7914)

&emsp; (2021.11.15)

&emsp; 首先是区间 DP。那我们设 $F_{l, r,k}$ 表示从 $l$ 到 $r$ 的合法序列数量。但是这道题有个很烦的事情就是，有很多种状态它都是合法的，所以我们要对每一种转移做讨论。所以我们对于几种不同的转移把 $F$ 扩展成 3 维，它们分别代表一下的含义（S 表示全是 * 的字符串，A 表示任意合法字符串）：

1. $F_{l, r, 0}$：表示这样：S，全是 ' * ' 的序列
2. $F_{l, r, 1}$：表示这样：(S) 的序列，两头都是括号且这两头的括号匹配的序列
3. $F_{l, r, 2}$：表示这样：(A)S(A)...(A)S 的序列，左边以括号开头，有边以 * 结尾的序列
4. $F_{l, r, 3}$：表示这样："(A)S(A)S...(A)" 的序列，左边以括号开头，有边以括号结尾的序列
5. $F_{l, r, 4}$：表示这样："S(A)S...(A)" 的序列，左边以 * 开头，有边以括号结尾的序列
6. $F_{l, r, 5}$：表示这样："S(A)S...S" 的序列，左边以 * 开头，有边以 * 结尾的序列

&emsp; 然后就是转移，我们定义一个 bool 类型的函数 canMatch(i, j)，返回第 $i$ 位和第 $j$ 为能否匹配成一对括号。然后下面是转移方程：

$$
\begin{aligned}
& F_{l, r, 0} \quad 直接特判就好了... \\
& F_{l, r, 1} = (F_{l+1, r-1, 0} + F_{l+1, r-1, 2} + F_{l+1, r-1， 3} + F_{l+1, r-1, 4})[canMatch(l, r)] \\
& F_{l, r, 2} = \sum_{i=l}^{r-1} F_{l, i, 3} F_{l+1, r, 0} \\
& F_{l, r, 3} = F_{l, r, 1} + \sum_{i=l}^{r-1} (F_{l, i, 2} + F_{l, i, 3})F_{i+1, r, 1} \\
& F_{l, r, 4} = \sum_{i=l}^{r-1}(F_{l, i, 4} + F_{l, i, 5})F_{i+1, r, 1} \\
& F_{l, r, 5} = F_{l, r, 0} + \sum_{i=l}^{r-1}F_{l, i, 4}F_{i+1, r, 0}
\end{aligned}
$$

&emsp; 最后，答案就是$F_{1, n, 3}$。

## 状压 dp

### 前置芝士

1. 取出整数 $n$ 二进制下的第 $k$ 位 ： $(n >> k) \& 1$
2. 取出整数 $n$ 而今之下的 $0 \sim k$ 位：$n \& ((1 << k) - 1)$
3. 把整数 $n$ 在二进制下第 $k$ 位取反：$n\; xor \; (1 << k)$
4. 把整数 $n$ 在二进制下第 $k$ 位赋为 $1$：$n | (1 << k)$
5. 把整数 $n$ 在二进制下第 $k$ 位赋为 $0$：$n \& (\sim(1 <<k))$

### [luogu1433 吃奶酪](https://www.luogu.com.cn/problem/P1433)

&emsp; (2022.4.16)

&emsp; 求最短哈密顿路径。

&emsp; 不能直接暴力，会超时。而且目前这玩意儿没有多项式解法，所以我们考虑状压 $dp$。我们用状态压缩处理点集中是否走过的信息，然后我们设 $f_{i, j}$ 表示点集为 $i$，目前在 $j$ 时的最短路径。

&emsp; 在起点的时候显然有 $f_{1, 0} = 0$，最终目标时 $f_{(1 << n) - 1, n - 1}$。在任意时刻我们有：

$$ f_{i, j} = \min_{k = 0}^{n - 1}\{ f_{i \; xor \; (1 << j), k} + w_{k, j} \} $$

&emsp; 然后就做完了

-----

# Math

## expectation and posibility

### 一些前置芝士

#### 概率
$$
\begin{aligned}
& P(A\cup B) = P(A) + P(B) - P(A \cap B) \\
& P(B-A) = P(B) - P(A) \qquad (P(A) \leq P(B)) \\
& P(B-A) = P(B) - P(A \cap B) \qquad (\forall A, B) \\
& P(A\mid B) = \frac{P(A \cap B)}{P(B)} \\
& P(AB) = P(A) \times P(B\mid A) = P(B) \times P(A\mid B) \quad (A,B 有关联) \\ 
\end{aligned}
$$

##### 全概率公式
$$ P(A) = \sum_{i=1}^n P(A\mid H_i) \times P(H_i) $$

##### 贝叶斯定理
$$ P(A\mid B) = \frac{P(A)P(B\mid A)}{P(B)} $$

#### 期望
$$
\begin{aligned}
& E(x) = \sum_i p_ix_i \\
& E(C) = C \quad E(Cx) = CE(x) \quad E(x+y) = E(x) + E(y) \\ 
& E(xy) = E(x)E(y) \qquad (x,y相互独立)
\end{aligned}
$$

##### 全期望公式
$$ E(Y) = E(E(Y \mid X)) = \sum_i P(x=x_i)E(Y \mid X = x_i) $$

### [luogu1291 SHOI2002百事世界杯之旅](https://www.luogu.com.cn/problem/P1291)

&emsp; “……在 $2002$ 年 $6$ 月之前购买的百事任何饮料的瓶盖上都会有一个百事球星的名字。只要凑齐所有百事球星的名字，就可参加百事世界杯之旅的抽奖活动，获得球星背包，随声听，更可赴日韩观看世界杯。还不赶快行动！”

&emsp; 你关上电视，心想：假设有 $n$ 个不同的球星名字，每个名字出现的概率相同，平均需要买几瓶饮料才能凑齐所有的名字呢？

&emsp; 设 $E_{i, j}$ 表示总共有 i 个，还有 j 个没有抽到的期望。,我们考虑下一次抽取：有 $\frac k n$ 的概率抽到我们还没有的，有 $\frac{n-k}n$ 的概率抽到我们已经抽到的，于是我们可以得到以下的递推式：
$$ E_{n, k} = \frac k n E_{n,k-1} + \frac{n-k}n E_{n, k} + 1 $$

&emsp; 然后稍微移项化简一下，就会变成这样：
$$
\begin{aligned}
& E_{n, k} = \frac k n E_{n,k-1} + \frac{n-k}n E_{n, k} + 1 \\
& E_{n, k} - \frac{n-k}n E_{n, k} = \frac k n E_{n,k} = \frac k n E_{n,k-1} + 1 \\ 
& E_{n, k} = E_{n, k-1} + \frac n k
\end{aligned}
$$

&emsp; 又因为我们知道 $E_{1,1} = 1$,，所以我们得到通项公式：
$$ E(x) = n\sum_{i=1}^n\frac 1 i $$

### 骰子

&emsp; (2021.11.3)

&emsp; 一个骰子，扔 n 次的点数之和大于 x 的概率。

&emsp; 首先我们考虑算出扔 n 次点数之和大于 x 的方案数，最后再除以 $6^n$ 即可。我们设 $f_{i, j}$ 表示扔了 i 次，点数之和为 j 的方案数。我们就有：
$$ f_{i, j} = \sum_{k=1}^6 f_{i-1,j-k} $$

&emsp; 所以整个算法就是先 DP 然后最后的答案就是：

$$ ans = \frac{\sum\limits_{i = x}^{6n}f_{n,i}}{6^n} $$

### OX 序列
&emsp; (2022.2.5)

&emsp; 给定序列，每个位置是 $o$ 的概率为 $p_i$，为 $x$ 的概率是 $1 - p_i$。对于一个 $ox$ 序列，连续 $a$ 长度的 $o$ 会得到 $a^3$ 的收益，问最终得到的 $ox$ 序列的期望收益是多少。

&emsp; 我们设 $f_i$ 为某一种具体的放置方法中已经放到第 $i$ 位的收益。$x_i$ 表示某一种具体的放置方法中从最后一位开始连续的 $o$ 的长度。那么我么需要求解的就是 $E(f_n)$。首先我们很容易知道一个关于 $f$ 的递推式：

$$ f_i = f_{i-1} + (x_{i-1} + 1)^3 - x_{i-1}^3 = f_{i-1} + 3x_{i-1}^2 + 3x_{i-1} + 1
 $$

&emsp; 那么：

$$ E(f_i) = E(f_{i-1}) + 3E(x_{i-1}^2) + 3E(x_{i-1}) + 1 $$

&emsp; 化成这样之后，我们就可以发先如果我们求出 $3E(x_{i-1}^2) + 3E(x_{i-1})$，这就是一个关于 $E(f)$ 的递推式。就可以直接求出来。所以我们考虑怎样求出 $E(x_{i})$ 和 $E(x_{i}^2)$。首先处理一个简单一点的：

$$
\begin{aligned}
E(x_{i}) = ?
\end{aligned}
$$

&emsp; 要知道这个我们首先需要知道 $x_i$ 的递推式：

$$
\begin{cases}
第i位o, \; 概率为p_i, \; x_i = x_{i-1}+1 \\ 第i位x, \; 概率为1-p_i, \; x_i = 0
\end{cases}
$$

&emsp; 那么：

$$
\begin{aligned}
E(x_i) = p_i \times E(x_{i-1} + 1
) + E(1-p_i) \times 0 = p_iE(x_{i-1}) + p_i
\end{aligned}
$$

&emsp; 这样之后我们也可以用递推解决 $E(x_i)$ 的值。然后考虑 $E(x_i^2)$ 的值。首先：


$$
\begin{cases}
第i位o, \; 概率为p_i, \; x_i^2 = (x_{i-1}+1)^2 = x_{i-1}^2 + 2x_{i-1} + 1 \\ 第i位x, \; 概率为1-p_i, \; x_i^2 = 0
\end{cases}
$$

&emsp; 那么：

$$
\begin{aligned}
E(x_i^2) = & p_iE(x_{i-1}^2 + 2x_{i-1} + 1) + (1-p_i) \times 0 \\
= & p_i E(x_{i-1}^2) + 2p_iE(x_{i-1}
) + p_i
\end{aligned}
$$

&emsp; 显然这也是一个递推式，那我们就可以 $O(n)$ 求出最终答案了。

### [luogu4316 绿豆蛙的归宿](https://www.luogu.com.cn/problem/P4316)

&emsp; (2022.4.8)

&emsp; 给定一张 $m$ 条边 $n$ 个点的，起点为 $1$ 终点为 $n$ 的有向无环图。现在在这张图上走，到达一个顶点的时候（假设这个点的度为 $k$）可以选择一条路径离开这个点，并且走向每条边的概率是 $\frac 1k$。问你从 $1$ 到 $n$ 的期望路径总长。

&emsp; 显然现在给的是一张 $DAG$。然后考虑期望概率 $dp$。我们设 $f_i$ 表示 $i$ 点到终点的期望路径总长。显然我们要求的答案就是 $f_1$，且有 $f_n = 0$。然后就是 $dp$ 方程了

&emsp; 因为我们知道了终点的状态的值，所以我们考虑逆推，我们就能得到：

$$ f_x = \sum_{(x, y)\in G_x}\frac{f_y + w_{(x, y)}}{|G_x|} $$

&emsp; 转移的过程就可以通过连反向边然后沿着拓扑序走一遍转移就行了。时间复杂度就是 $O(n + m)$。

## number theory

### 一些前置芝士

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

#### 埃氏筛
&emsp; 埃氏筛的思想很简单，就是从前往后，遇到一个质数就把所有后面小于 n 的这个质数的倍数全都标记为合数。后面遇到他们就直接跳过。

##### 筛质数

&emsp; 按照上面的思想直接模拟就好了，时间复杂度 $O(n \log \log n)$：
```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100100

int v[MAXN] = { 0 };              // 合数标记 
void primes(int n){
	memset(v, 0, sizeof(v));
	for(int i = 2; i <= n; i++){
		if(v[i]) continue;
		cout << i << endl;
		for(int j = 1; j <= n / i; j++) v[i * j] = 1;
	}
}

int main(){
	primes(100);
	return 0;
}
```

##### 筛欧拉函数

&emsp; 利用欧拉函数的计算式和埃氏筛，能在 $O(n \log n)$ 的时间内筛出欧拉函数的值。就是首先初始化所有的 phi[i] = 1，然后再枚举从 2 到 n 的每个数，用埃氏筛找出质数，然后把后面质数的倍数根据计算式计算就好了：

$$ \varphi(n) = n \prod_{p | n} \frac {p-1}p $$

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100100

int phi[MAXN] = { 0 };
void euler(int n){
	for(int i = 1; i <= n; i++) phi[i] = i;
	for(int i = 2; i <= n; i++){
		if(phi[i] == i)
			for(int j = i; j <= n; j += i)
				phi[j] = phi[j] / i * (i - 1);
	}
}

int main(){
	euler(20);
	for(int i = 1; i <= 20; i++){
		cout << "phi[" << i << "] = " << phi[i] << endl;
	}
	return 0;
}
```

##### 筛莫比乌斯函数

&emsp; 首先把所有的 mu 初始化为 1，接下来对于每一个质数 p 把 mu[p] 变成 -1。并扫描 p 的倍数们看他们能否被 $p^2$ 整除，如果可以 mu[x] = 0，如果不行 mu[x] = -mu[x]。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100100

int v[MAXN] = { 0 };
int mu[MAXN] = { 0 };
void mobius(int n){
	memset(v, 0, sizeof(v));
	for(int i = 2; i <= n; i++) mu[i] = 1;
	for(int i = 2; i <= n; i++){
		if(v[i]) continue;
		mu[i] = -1;
		for(int j = 2 * i; j <= n; j += i){
			v[j] = 1;
			if((j / i) % i == 0) mu[j] = 0;
			else mu[j] *= -1;
		}
	}
}

int main(){
	mobius(20);
	for(int i = 1; i <= 20; i++){
		cout << "mu[" << i << "] = " << mu[i] << endl;
	}
	return 0;
}
```

#### 线性筛

&emsp; 顾名思义，这玩意儿能在线性的时间内筛出质数。具体的做法是优化一下埃氏筛，在埃氏筛里面，一个合数可能被很多个质数标记过，比如 12 就被 2 和 3 同时标记过。这样就会降低我们的效率。线性筛的想法就是让所有的合数只有一种被筛出来的方式。也就是被它的最小质因子筛出来。具体做法如下：

1. 枚举 $2 \sim n$ 的所有数，考虑用一下方式维护一个数组 v（表示这个数的最小质因子）。
2. 如果 v[i] = i，那么这个是是质数，把它存下来。
3. 扫描不大于 v[i] 的所有质数 p，令 v[ip] = p。


##### 筛质数

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100100

int v[MAXN] = { 0 };
int prime[MAXN] = { 0 };
int primes(int n){
	memset(v, 0, sizeof(v));
	int cnt = 0;                                                 // 质数的个数
	for(int i = 2; i <= n; i++){
		if(!v[i]){ 
			v[i] = i; prime[++cnt] = i; 
		}
		for(int j = 1; j <= cnt; j++){                           // 枚举所有质数 
			if(prime[j] > v[i] or prime[j] > n / i) break;       // 质数比它大或者超过范围 
			v[i * prime[j]] = prime[j];                          // 更新最小质因子 
		}
	}
	return cnt;
}

int main(){
	int num = primes(100);
	for(int i = 1; i <= num; i++){
		cout << prime[i] << endl;
	}
	return 0;
}
```

##### 筛欧拉函数

&emsp; 这就要用到几个欧拉函数的性质了：

1. 如果 $p \mid n$ 且 $p^2 \mid n$ 则有 $\varphi(n) = {\varphi(\frac np)} \times p$
2. 如果 $p \mid n$ 且 $p^2 \nmid n$ 则有 $\varphi(n) = \varphi(\frac np) \times (p-1)$

&emsp; 我们发现这两个判断和线性筛的两个判断很类似，所以我们可以利用线性筛，从 $\varphi(\frac np)$ 递推到 $\varphi(n)$
```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100100

int v[MAXN] = { 0 };
int phi[MAXN] = { 0 };
void euler(int n){
	memset(v, 0, sizeof(v));
	int cnt = 0;
	for(int i = 2; i <= n; i++){
		if(!v[i]){
			v[i] = i; prime[++cnt] = i;
			phi[i] = i - 1;
		}
		for(int j = 1; j <= cnt; j++){
			if(prime[j] > v[i] or prime[j] > n / i) break;
			v[i *prime[j]] = prime[j];
			if(i % prime[j]) phi[i * prime[j]] = phi[i] * (prime[j] - 1);
			else phi[i * prime[j]] = phi[i] * prime[j];
		}
	}
}

int main(){
	euler(20);
	for(int i = 1; i <= 20; i++){
		cout << "phi[" << i << "] = " << phi[i] << endl;
	}
	return 0;
}
```

### [luogu2185 SDOI2008 仪仗队](https://www.luogu.com.cn/problem/P2158)

&emsp; (2021.11.7)

有一个点阵（方阵），你站在左下角，问你能总共看到几个点。然后我们可以发现，如果我们将左下角的点放在坐标轴 原点，能被看见的点的横纵坐标一定是互质的（除了两个和它相邻的点）。所以我们要求解的问题的答案就是：
$$ ans = 2 + \sum_{i=1}^n \sum_{j=1}^n [gcd(i, j) = 1] $$

&emsp; 然后可以对这个式子进行化简：
$$
\begin{aligned}
ans-2 = & \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} [gcd(i, j) = 1] \\
= & \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} \varepsilon(gcd(i, j)) = \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} \sum_{d \mid gcd(i, j)}\mu(d) \\ 
= & \sum_{d = 1}^{n-1} \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} [d \mid gcd(i, j)]\mu(d) = \sum_{d = 1}^{n-1} \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} [d \mid i \wedge d \mid j]\mu(d) \\
= & \sum_{d=1}^{n-1} \mu(d) \sum_{i=1}^{n-1} \sum_{j=1}^{n-1} [d \mid i \wedge d	 \mid j] \\
= & \sum_{d=1}^{n-1} \mu(d)  \left\lfloor \frac {n-1}d \right\rfloor^2
\end{aligned}
$$

&emsp; 然后 $\mu(d)$ 可以用埃氏筛预处理然后 $O(1)$ 查询。后面的 $\left\lfloor \frac nd \right\rfloor^2$ 显然也是可以 $O(1)$ 查询的，那么总的复杂度就是 $O(n\log n + n)$

### [P2522 [HAOI2011]Problem b](https://www.luogu.com.cn/problem/P2522)

&emsp; (2021.11.7)

&emsp; 对于给出的 n 个询问，每次求出有多少个数对 $(x, y)$ 满足 $a \leq x \leq b, c \leq y \leq d$ 且 $gcd(x, y) = k$。

&emsp; 所以这个题的答案就是：
$$ ans = \sum_{i = a}^b \sum_{j = c}^d [gcd(i, j) = k] $$

&emsp; 这个式子就和上面那个题的试着长得很像了，但是这个上下界看着还是有点不舒服，因为我们想把它转化成下界为 1 的求和，于是我们利用容斥原理，转化一下问题：
$$
\begin{aligned}
ans = & \sum_{i = a}^b \sum_{j = c}^d [gcd(i, j) = k]  \\
= & \sum_{i = 1}^b \sum_{j = 1}^d [gcd(i, j) = k] - \sum_{i = 1}^{a-1} \sum_{j = 1}^d [gcd(i, j) = k] - \sum_{i = 1}^b \sum_{j = 1}^{c-1} [gcd(i, j) = k] + \sum_{i = 1}^{a-1} \sum_{j = 1}^{c-1} [gcd(i, j) = k]
\end{aligned} 
$$

&emsp; 于是问题就被转化成求 4 个和上道题差不多的式子。由上一道题我们知道：
$$
\begin{aligned}
& \sum_{i = 1}^n \sum_{j = 1}^m [gcd(i, j) = k]\\
= & \sum_{i = 1}^n \sum_{j = 1}^m \varepsilon\left( \frac{gcd(i, j)}{k} \right)= \sum_{i = 1}^n \sum_{j = 1}^m \sum_{p | gcd(i, j) / k} \mu(p) = \sum_{p = 1}^{min(n, m)}\sum_{i = 1}^n \sum_{j = 1}^m \left[ p \Big| \frac{gcd(i, j)}{k} \right]\mu(p) \\
= &\sum_{p = 1}^{min(n, m)}\mu(p) \sum_{i = 1}^n \sum_{j = 1}^m \left[ p \Big| \frac{gcd(i, j)}{k} \right] = \sum_{p = 1}^{min(n, m)}\mu(p) \sum_{i = 1}^n \sum_{j = 1}^m \left[ p \Big| \frac{i}{k} \wedge p \Big| \frac{j}{k} \right] = \sum_{p = 1}^{min(n, m)}\mu(p) \sum_{i = 1}^n \sum_{j = 1}^m \left[ kp \mid i \wedge kp \mid j \right] \\
= & \sum_{p = 1}^{min(n, m)}\mu(p) \left\lfloor \frac{n}{kp} \right\rfloor \left\lfloor \frac{m}{kp} \right\rfloor
\end{aligned}
$$

&emsp; 然后我们只需要写一个函数来处理这个，然后每次询问调用 4 次就好了。计算这个式子的时间复杂度是 $O(min(n, m) \log min(n, m) + min(n, m))$，所以如果我还要处理 $5 \times 10^4$ 组这样的数据，就会超时，直接计算能拿 30 分。

&emsp; 下面我们考虑怎么样优化处理这个式子的时间。首先看看这个式子的样子，他长得是不是就很像整除分块，那就直接整除分块计算这个式子，复杂度就被降到了 $O(\sqrt{min(n, m)})$，·这样一来就能过了。

### [luogu2480 古代猪文](https://www.luogu.com.cn/problem/P2480)

&emsp; (2022.4.2)

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

## linear algebra

### [Power of Matrix](https://www.luogu.com.cn/problem/UVA11149)
&emsp; (2022.4.30)

&emsp; 给一个 $n \times n$ 的矩阵 $A$，给定一个整数 $k$ 求：

$$ ans = \sum_{i = 1}^k A_i $$

&emsp; 我们将这个式子看成递推也就是：

$$ F_k = \sum_{i = 1}^k A^i $$

&emsp; 其中 $F_0 = O = \begin{bmatrix}0 & 0 & \cdots&  0 \\ 0 & 0 & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & 0 \end{bmatrix}$，$F_1 = A$，然后我们可以得到矩阵版的递推关系：

$$
\begin{bmatrix}
F_k & E
\end{bmatrix} = 
\begin{bmatrix}
F_{k - 1} & E
\end{bmatrix} \times
S = 
\begin{bmatrix}
F_0 & E
\end{bmatrix} \times S^{k}
$$

&emsp; 上面的式子中的 $E$ 表示单位矩阵，也就是：

$$
E = \begin{bmatrix}1 & 0 & \cdots&  0 \\ 0 & 1 & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & 1 \end{bmatrix}
$$

&emsp; 使用单位矩阵是为了更方便的写出 $S$ 来，我们设：

$$ S = 
\begin{bmatrix}
a & b \\ c & d
\end{bmatrix}
$$

&emsp; 其中 $a, b, c, d$ 都是 $n \times n$ 的矩阵，$S$ 是 $2n \times 2n$ 的矩阵，这样才能满足上面的递推关系。我们根据上面的关系又能得出：

$$
\begin{aligned}
& \begin{bmatrix} F_k & E \end{bmatrix} = \begin{bmatrix} F_{k - 1} & E \end{bmatrix} \times \begin{bmatrix} a & b \\ c & d \end{bmatrix} \\
\rightarrow & \quad F_k = F_{k - 1} \times a + c \; , \;E \; = F_{k - 1} \times b + d
\end{aligned}
$$

&emsp; 又因为我们有：

$$ F_k = F_{k - 1} + A^k $$

&emsp; 就能得到：

$$ F_{k - 1} + A^k = F_{k - 1} \times a + c \; , \; E = F_{k - 1} \times b + d $$

&emsp; 带入 $k = 1$，得到：

$$ F_0 + A = F_0 \times a +c \; , \; E = F_0 \times b + d $$

&emsp; 带入 $k = 2$ 得到：

$$ F_1 + A^2 = F_1 \times a + c \; , \; E = F_1 \times b + d $$

&emsp; 然后就能得到：

$$ a = A \; , \; b = O \; , \; c = A \; , \; d = E $$

&emsp; 也就是：

$$ S = \begin{bmatrix} A & O \\ A & E \end{bmatrix}, S^k = \begin{bmatrix} A^k & O \\ \sum\limits_{i = 1}^k A^i & E \end{bmatrix} $$

&emsp; 也就是说 $S^k$ 的左下角就是答案。

### [球形空间产生器](https://www.luogu.com.cn/problem/P4035)

&emsp; (2022.4.30)

&emsp; 给一个 $n \in [1, 10]$ 维空间中的 $n + 1$ 个点的坐标，求出过这 $n +1$ 个点的 $n$ 维空间上的 "球" 的球心的坐标。

&emsp; 我们发现对于球心的坐标： $(x_1, x_2, x_3, \cdots , x_n)$，我们都有：

$$ \sum_{j = 0}^n (a_{i, j} - x_i)^2 = Const $$

&emsp; 其中 $a_{i, j}$ 第 $i$ 个点的第 $j$ 维坐标。我们发现这个方程是一个 $n$ 元 $2$ 次方程组，总共有 $n + 1$ 个方程，不是一个线性方程，但是我们可以通过一些变换将它变成线性方程然后通过高斯消元来求解。具体方法我们将相邻的两个方程作差：

$$
\begin{aligned}
&\sum_{j = 1}^n (a_{i, j}^2 - a_{i +1, j}^2 - 2x_i(a_{i, j} - a_{i +1, j})) = 0, \quad (i \in [1, n], i \in Z) \\
\rightarrow &\sum_{j = 1}^n2(a_{i, j} - a_{i +1, j})x_j = \sum_{j = 1}^n(a_{i, j}^2 - a_{i +1, j}^2), \quad (i \in [1, n], i \in Z)
\end{aligned}
$$

&emsp; 现在我们发现这就是一个现线性的方程组了，也就是我们高斯消元这个增广矩阵：

$$
\begin{bmatrix}
2(a_{1, 1} - a_{2, 1}) & 2(a_{1, 2} - a_{2, 2}) & \cdots & 2(a_{1, n} - a_{2, n}) & \sum\limits_{j = 1}^n (a_{1, j}^2 - a_{2, j}^2) \\
2(a_{2, 1} - a_{3, 1}) & 2(a_{2, 2} - a_{3, 2}) & \cdots & 2(a_{2, n} - a_{3, n}) & \sum\limits_{j = 1}^n (a_{2, j}^2 - a_{3, j}^2) \\
\vdots & \vdots & \ddots & \vdots & \vdots \\
2(a_{n, 1} - a_{n +1, 1}) & 2(a_{n, 2} - a_{n +1, 2}) & \cdots & 2(a_{n, n} - a_{n +1, n}) & \sum\limits_{j = 1}^n (a_{n, j}^2 - a_{n +1, j}^2)
\end{bmatrix}
$$

&emsp; 就能得到答案了。

------

# Construct

## Divisible subset

&emsp; (2021.11.12)

&emsp; 给定一个长度为 $n$ 的 multiset，找到一个非空子集，满足子集中的元素的和能被 $n$ 整除，或者判断这样的集合不存在。

&emsp; 首先，对这个数组做一个模 $n$ 的前缀和，如果我们找到了前缀和中有不同的两个位置相等，即 $S_l = S_r$，那么我们就找到了一个区间 $[l, r]$，这个区间内的所有数的和模 $n$ 得 0。即这个区间内的所有数的和是 $n$ 的倍数。那么我们就找到了这个子集。现在就要判定能不能找到 $S_l = S_r$，因为我们对所有的 $S_i$ 都对 $n$ 取模，所以 $S_i \in [0, n-1]$，总共有 $n$ 种取值，而我们的前缀和数组一共有 $n+1$ 个值（因为我们把 $S_0 = 0$ 也放在前缀和里面，不然的话 $n = 5，a = \lbrace3, 3, 3, 3, 3\rbrace$，这组数据就会被 hack 掉）。根据抽屉原理，必定有两个数值是相同的。

## Hack it
&emsp; (2021.11.12)

&emsp; 令 $f(x)$ 表示 $x$ 在十进制下各位数字的和。给定一个整数 $a$，让你构造一个整数 $a$ 使得：
$$ T = \sum_{i = l}^r f(i) \equiv 0 \pmod a \qquad a\in[1, 10^{18}]， l, r\in[1, 10^{200}] $$

&emsp; 我们发现如果找到一个 $k$，使得 $10^k >> x$，那么 $f(x + 10^k) = f(x) + 1$。现在我们考虑一个区间：$[1, 10^k]$，我们把这个区间的第一个数字 $1$ 加上一个 $10^k$，这个区间就变成了： $[2, 10^k + 1]$，而这个区间的 $T$ 加了 1。我们再把操作之后的区间的第一个数字加上 $10^k$，这个区间就变成了：$[3, 10^k+2]$，这个区间的 $T$ 又在原来的基础上加了 1。所以如果我们想让这个区间的 $T$ 加上 $x$，那么我们就让 $[1, 10^k]$，变成 $[1+x, 10^k + x]$ 就好了。

&emsp; 知道了这个，我们就可以考虑先用数位 DP（不会数位 DP 也可以手推 qwq），把 $[1\sim 10^k]$ 的 $T$ 算出来，再把区间整体向右平移 $a - T$ 就好了，也就是把区间变成 $[1+a-T, 10^k+a-T]$。

&emsp; 现在来讲一下如何手推 $[1, 10^k]$ 的 $T$。我们首先可以算出 $[1, 10^k-1]$ 的 $T$，然后再加上 1 就好了。那么首先可以手算出 $T([1, 9]) = 45$。然后看 $T([1, 99])$，我们考虑算每一位的贡献，个位数的贡献每轮都从 1 到 9，然后一共有 10 轮，所以是 45 * 10，然后是十位数的贡献，十位数从 1 到 9 每个数出现 10 次，所以也是 45 * 10，总共为 45 * 20 = 900。然后是 $T([0, 999])$，同理，先算百位数的贡献，是 100 * 45.。然后是个位和十位的贡献，因为我们已经算出来了 $T([0, 99])$，所以我们可以考虑把十位个个位放在一起考虑，每次都是从 0 到 99，总共轮了 10 次，所以就是 10 * $T([1, 99])$，加起来就是 300 * 45 = 13500。

&emsp; 好了，现在我们找到了规律，也就是：
$$ T([1, 10^k]) = 45k \times 10^{k-1} + 1 $$

&emsp; 然后我们就可以解决这道题了。

## A Problem Concerning LCS

&emsp; (2021.11.12)

&emsp; 题目大意：给定一个仅有 "A"，"T"，"C"，"G" 构成的长度为 $n, \; n \leq 10^6$ 的字符串 $a$。要求构造一个长度同为 $n$ 字符串 $b$，使得 $a，b$ 的 LCS 最短。

&emsp; 你多写几个字符串出来你就会发现，满足条件的 $b$ 显然就是全部都是 "ATCG" 中的其中一个字母，然后这个字母就是 $a$ 串中出现次数最小的，我们可以证明这个结论。假设 $a$ 中出现次数最少的是 "A"，那么显然 "A" 的出现次数（我们设为 $x$）小于等于 $\frac n4$。如果全用 "A" 来构造 $b$ 串，那么 LCS 的长度就是 $x$。假设存在$b'$ 使得它与 $a$ 的 LCS 小于长度 $x$，那么 $b'$ 中所有字母的出现次数都要小于 $\frac n4$。所以 $b'$ 的长度小于 $n$，但是 $b'$ 的长度又等于 $n$，矛盾了，所以不存在这样的 $b'$。

## Bags and Coins

&emsp; (2021.11.12)

&emsp; 题目大意：有 $s$ 个硬币，有 $n$ 个包。一个包可以放在其他包里面，可以多层嵌套。如果拿出一个硬币必须打开第 $i$ 个包，那我们就说第这个硬币在第 $i$ 个包里。现在已知第 $i$ 个包里有 $a_i$ 个硬币，构造一种满足上述条件的方案。$1 \leq n, s, a_i \leq 70000$

&emsp; 可以把一个包a 里面的包b 看成是包a 是包b 的父节点。于是我们可以把整个嵌套结构看成一颗树。于是我们想到一颗最特别的树，也就是一条链。但是我们发现一个问题 $\max\limits_{i=1}^n a_i$ 可能小于 $s$，也就是说如果只有一条链的话，有些硬币可能就没有被放进包里。于是我们试着构造多条链，使得这些链的根节点所包含的硬币数的和等于总硬币数 $s$ 就好了。

## Stack Machine Programmer

&emsp; (2022.11.12)

&emsp; 题目大意：有一个计算机，它只能存储整数，它只有一个栈作为存储器，它支持一下好几种操作：

1. NUM X：将一个非负整数数 $X$ 压入栈。
2. POP：弹出栈顶的数。
3. INV：将栈顶的数取相反数。
4. DUP：将栈顶的数复制一份再压入栈中。
5. SWP：交换栈顶的两个数。
6. ADD：将栈顶的两个数弹出，并将它们俩的和压入栈。
7. SUB：将栈顶的两个数弹出，并将它们俩的差压入栈。
8. MUL：将栈顶的两个数弹出，并将它们俩的积压入栈。
9. DIV：将栈顶的两个数弹出，并将它们俩的商的整数部分压入栈。
10. MOD：将栈顶的两个数弹出，并将它们俩相除的余数压入栈。

&emsp;  一个程序会按照顺序执行所有命令，给定在这个 Stack Machine 上一个程序的 $n, \; (n \leq 5)$ 组输入和输出（每组输入输出为两个整数），要求你构造一个程序能够使得给定的输入经过这个程序的处理能输出给定的输出（程序中只能使用上面给出的所有操作）。对于每个输入输出 $(I, O)$，$0 \leq I \leq 10 \quad 0 \leq O \leq 20$，要求你构造的程序的长度不得超过 $10^5$。

&emsp; 经过观察，发现这个计算机里面其实有基本上所有的多项式需要的运算（加，减，乘，除...）。所以题目就被转化成构造一个多项式$f(x)$，使得每组 $f(I) = O$ 都成立。然后拉格朗日插值就好了。


## [luogu8252 讨论](https://www.luogu.com.cn/problem/P8252)

&emsp; (2022.3.31)

### 构造

&emsp; 大概就是给 $n$ 个集合，每个集合 $k_i$ 个元素，然后判断这些集合中有没有满足有交且不包含的。如果有输出 $YES$ 和这两个集合的编号，如果没有就输出 $NO$。

&emsp; 我们维护一个 $last[i]$ 数组表示当前枚举到的人中会做 $i$ 题的最后一个人的编号（其实就是染色（后面会说） qwq）。然后我们通过一些分析就能得出一些神奇的性质。

&emsp; 首先，如果我们在枚举第 $now$ 个人会做的题目 $p$ 时，发现 $last_p = 0$。那么说明 $now$ 就是枚举到的人中第一个会做 $p$ 这道题的人。如果是这样就比较好处理，我们只需要找到一个在前面找一个与 $i$ 有交的人就好了（因为有题目 $p$ 的存在所以前面的人不可能包含 $now$）。

&emsp; 那么第二种情况 $last_p$ 有值，现在的问题就是要求 $last_p$ 和 $now$ 不能是包含关系。我们为了方便，记 $x = last_p$。然后我们继续枚举其他题，发现有另一道题的 $last_{p'} \neq x$。那我们把现在的 $last_{p'}$ 也记录下来记为 $y$。现在我们就有了两个不同的人，他们**都满足与 $now$ 有题目交集。** 

&emsp; 现在我们找到 $x$ 和 $y$ 中 $k$ 较小的那个（假设 $k[x] > k[y]$）。因为排序，所以 $y$ 的枚举的顺序肯定在 $x$ 之后，又因为 $last_{p} = x \neq y$，所以 $y$ 肯定不会做题 $p$，又因为 $now$ 会做 $p$，**所以 $now$ 和 $y$ 肯定不是包含关系**，那么久满足了这道题的要求。那我们就输出 $now$ 和 $y$ 就搞定了。

### 染色

&emsp; 前面的构造没看懂没关系，我们来看看更容易理解的一种算法（其实和构造是差不多等效的），看完染色再回去看构造应该就能有更深的理解 qwq。首先对所有人按照所对应的集合的 $size$ 从大到小排序，然后从头到尾扫一遍，然后执行以下操作：

1. 对于每一个人的集合中的题将他们染成同一个颜色。
2. 如果当前这个人的题目集合跨越了两个颜色，那么我们就找到了要讨论的人。

&emsp; 我们对所有人进行排序是为了保证之后扫到的人不可能包含前面扫到的人的集合。按照这个算法进行在找到能讨论的两个人之前染色的情况应该是只有包含和不交两种情况，像这样：

![在这里插入图片描述](/Alex/OI/pic/NOIonline221.png)

&emsp; 然后我们找到了一个能讨论的集合包含了两种颜色，就像这样：

![在这里插入图片描述](/Alex/OI/pic/NOIonline222.png)

&emsp; 就直接找然后输出就做完了。

---------

# Search

## A*

&emsp; (2021.10.15)

### [luogu1379 八数码难题](https://www.luogu.com.cn/problem/P1379)

#### 判断可行

&emsp; $3 \times 3$ 的九宫格，其中 $8$ 个格子中有 $1 \sim 8$ 的 $8$ 个数字。很显然这 $8$ 个数字有很多种填写方法。我们有一个操作，就是九宫格中没有填数字的方格和旁边的数字交换。现在我们给出两个状态，然后我们要判断两种状态是否能通过若干个操作互相到达，如果能互相到达就输出操作次数最少的一种方法的操作步数。

&emsp; 首先判断能否相互到达，八数码游戏的两个局面可达，当且仅当把两个局面写成一维形式的时候，逆序对个数的奇偶性相同。证明很简单，分为两种情况。第一种情况：我们左右移动空格位置的时候，一维形式数列不变，所以逆序对奇偶性不变。第二种情况：我们上下移动空格位置的时候，在一维形式上的表现就是某个数与它前（后）面的 2 个数进行位置互换。此时我们又要分两种情况讨论。
1. 与它进行位置交换的两个数都比它大（小）那么逆序对数量就减少（增加）2。逆序对数量奇偶性不变。
2. 与他进行交换的两个数一个比他大，另一个比他小，那么逆序对的数量加一再减一就没有变化，所以逆序对数量奇偶性不变。

&emsp; 然后我们就可以对于两次给出的局面维护一个树状数组求出两次的逆序对数量（也可以用归并排序求逆序对）然后判断奇偶性是否相同就好了。

#### 步数最小

&emsp; 然后，首先利用上述方法判断是否可解，若问题有解，那我们就采用 A* 算法搜索出一种移动部步数最小的方案。我们令评估函数为当前位置和目标位置有几个数的位置不相同，即：
$$ f(state) = \sum_{i=1}^{9} (state_{xi} ==  goal_{xi} \; and \; state_{yi} == goal_{yi}) \; ? \; 0 \; : \; 1 $$
&emsp; 然后每次我们用当前步数 g(state) 加上 f(state) 来进行扩展，最终状态第一次被从堆中取出时就是最优解。

---------

# Other

## [luogu1102 数对](https://www.luogu.com.cn/problem/P1102)

&emsp; (2022.4 8)

&emsp; 给你一个长度为 $n \in [1, 2e5]$ 的数列（数列中的数 $a_i \in [1, 2^{30}]$）和一个整数 $C$，然后让你找出这个数列中所有位置不同的两个数 $A, B$。并统计满足 $A - B = C$ 的数对的个数。

&emsp; 挺水的一道题。我们把条件转化一下，把 $A - B = C$ 转化成 $A - C = B$。然后我们只用建立一个 $map$，$m[a[i]]$ 表示 $a[i]$ 在 $\{a\}$ 中出现的次数。然后 $ans = \sum\limits_{i = 1}^nm[a[i] - C]$。就搞定了。

## [luogu2671 求和](https://www.luogu.com.cn/problem/P2671)

&emsp; (2022.4.9)

&emsp; 现在有一个数组，每个数三个值 $(x, y, c)$。分别表示下标，值和颜色。如果我们找到一个三元组 $(i, j, k)$ 满足 $j$ 是 $i, k$ 的等差中项且下标分别为 $i$ 和 $k$ 的所对应的颜色相同，那么这个三元组就对答案有 $(x_i + x_k) \times (y_i + y_k)$ 的贡献。求总答案。

&emsp; 首先我们发现 $j$ 其实没啥用，这里只是规定了 $i$ 和 $k$ 必须奇偶性相同。所以我们考虑单独把能对答案产生贡献的块放到一起。也就是按颜色为第一关键字，$x$ 的奇偶性按第二关键字排序。然后分别处理每一块对答案的贡献，然后再加起来就可以了。

&emsp; 这里以处理第一块对答案的贡献为例（假设第一块的长度为 $k$）。那么这块的贡献就是：

$$
\begin{aligned}
& \sum_{i = 1}^k\sum_{j = i + 1}^k(x_i + x_j)(y_i +y_j) \\
= & \sum_{i=1}^k\sum_{j=i +1}^k(x_iy_i + x_jy_j + x_iy_j + x_jy_i) \\
= & \sum_{i = 1}^k \left( \sum_{j = i +1}^k x_iy_i + \sum_{j = i +1}^k x_jy_j + \sum_{j = i +1}^kx_iy_j + \sum_{j = i +1}^kx_jy_i \right) \\
= &\sum_{i = 1}^k \left( (k - i)x_iy_i + \sum_{j = i +1}^kx_jy_j + x_i\sum_{j = i +1}^ky_j + y_i\sum_{j = i +1}^kx_j \right) \\
\end{aligned}
$$

&emsp; 然后我们发现只要我们预处理出 $x_i$、$y_i$ 和 $x_iy_i$ 的前缀和，我们就能 $O(1)$ 处理我们用很大的括号括起来的那一坨式子。这样整个的复杂度就是 $O(k)$ 的。

## 单调队列

### [WOJ4744 最佳序列](http://192.168.110.251/problempage.php?problem_id=4744)

&emsp; (2022.4.3)

&emsp; 给定一个长度为 $n$ 的数组  $a$。给定 $L, R$，求所有满足长度大于等于 $L$ 小于等于 $R$ 的 $a$ 的子区间的平均值的最大。

&emsp; 先二分答案，也就是二分平均值，然后每个数减去这个平均值，这个时候所有和大于 $0$ 的区间的平均值就肯定大于二分的值。

&emsp; 统计减去平均值的后序列的前缀和，问题就变成了前缀和在 $[i - R, i - L]$ 区间内是否存在一个数 $s_j$ 使得 $s_i > s_j$，然后就是单调队列优化就能做到 $O(n)$。

### [luogu2216 理想正方形](https://www.luogu.com.cn/problem/P2216)

&emsp; (2022.4.9)

&emsp; 给你一个 $a \times b$ 的二维数组（$a, b \in[2, 1e3]$）。然后再给你一个 $n \leq a, b$，让你在这个二维数组中找到一个 $n \times n$ 的矩形使得这个矩形中的最大值减这个矩形中的最小值是最小的。

&emsp; 很显然这是一个 $RMQ$ 的问题，因为 $a$ 和 $b$ 不大，而且 $n$ 是确定的，所以我们考虑跑 $a + b$ 遍单调队列来处理这道题，具体做法如下：

&emsp; 我们以 $luogu$ 上的样例为例 $n = 2$，我们先横着跑单调队列求出每一行连续 $n$ 个中的最大值。

$$
\begin{matrix}
1 & 2 & 5 & 6 \\
0 & 17 &16 & 0 \\
16 & 17 & 2 & 1 \\
2 & 10 & 2 & 1 \\
1 & 2 & 2 & 2 \\
\end{matrix}
\quad \to \quad
\begin{matrix}
2 & 5 & 6 \\
17 &17 & 16 \\
17 & 17 & 2 \\
10 & 10 & 2 \\
2 & 2 & 2 \\
\end{matrix}
$$

&emsp; 然后再竖着跑单调队列求出每一列中连续 $n$ 个数的最大值：

$$
\begin{matrix}
2 & 5 & 6 \\
17 &17 & 16 \\
17 & 17 & 2 \\
10 & 10 & 2 \\
2 & 2 & 2 \\
\end{matrix}
\quad \to \quad
\begin{matrix}
17 &17 & 16 \\
17 & 17 & 16 \\
17 & 17 & 2 \\
10 & 10 & 2 \\
\end{matrix}
$$

&emsp; 同理，我们对最小值也这样处理：

$$
\begin{matrix}
1 & 2 & 5 & 6 \\
0 & 17 &16 & 0 \\
16 & 17 & 2 & 1 \\
2 & 10 & 2 & 1 \\
1 & 2 & 2 & 2 \\
\end{matrix}
\quad \to \quad
\begin{matrix}
1 & 2 & 5 \\
0 &16 & 0 \\
16 & 2 & 1 \\
2 & 2 & 1 \\
1 & 2 & 2 \\
\end{matrix}
\quad \to \quad
\begin{matrix}
0 &2 & 0 \\
0 & 2 & 0 \\
2 & 2 & 1 \\
1 & 2 & 1 \\
\end{matrix}
$$

&emsp; 然后将跑出来的两个矩阵相减，找到差矩阵中的最小值即为所求：

$$
\begin{matrix}
17 &17 & 16 \\
17 & 17 & 16 \\
17 & 17 & 2 \\
10 & 10 & 2 \\
\end{matrix} \quad - \quad
\begin{matrix}
0 &2 & 0 \\
0 & 2 & 0 \\
2 & 2 & 1 \\
1 & 2 & 1 \\
\end{matrix} \quad = \quad
\begin{matrix}
17 & 15 & 16 \\
17 & 15 & 16 \\
15 & 15 & 1 \\
9 & 8 & 1 \\
\end{matrix}
$$

&emsp; 答案就是 $1$

## 启发式合并

### [luogu3201 HNOI2009 梦幻布丁](https://www.luogu.com.cn/problem/P3201)

&emsp; (2022.4.11)

&emsp; 初始给以一个长度为 $n$ 的数列，这个数列的每个数表示一个颜色，然后给你 $m$ 个操作，每次操作或是查询现在颜色的总段数（例如 $1, 2, 2, 1$ 就是有 $3$ 段颜色）或是给定 $x, y$ 表示将颜色 $x$ 的数全部变为 $y$ 颜色。

&emsp; 显然这道题就是一个链表的启发式合并，我们维护很多链表，每个链表中存出同一种颜色的数。然后每一次修改就是一次启发式合并，对于询问我们可以在修改的时候处理出来，具体来说就是：如果（假设 $x$ 是元素较少的那个集合） $x$ 中的一个元素旁边有有 $y$ 中的元素的话那么 $ans$ 就应该减 $1$。

&emsp; 这样虽然看起来很暴力，但是时间复杂度就是启发式合并的时间复杂度也就是 $O(n\log n)$ 的。