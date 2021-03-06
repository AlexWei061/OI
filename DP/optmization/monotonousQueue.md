# 单调队列优化
## 一道题
&emsp; 我们来看这道神奇的题：[CF372C Watching Fireworks is Fun](https://www.luogu.com.cn/problem/CF372C)

&emsp; 题目大意是，一个城镇有 $n$ 个区域,从左到右编号为 1 到 $n$ ,每个区域之间距离 1 个单位距离。现在城市要举行一个烟花节目，一共有 $m$ 个烟花要放，每一个烟花给定一个放的地点 $a_1$，放的时间 $t_i$ 和一个参数 $b_i$，如果此时你所处再 $x$ 区域，那么你看到这个烟花就会产生 $b_i - \mid a_i - x \mid$ 的和 $happy$ 值。每个单位时间内，你可以移动不超过 $d$ 的距离，你的初始位置是任意的（初始时刻为 1）。请算出你通过一定的移动能获得的最大的 $happy$ 值。

&emsp; 我们先来分析一下，一看这道题长得就很想一道动态规划的题，所以我们考虑一个非常朴素的 DP，计 $F_{i, j}$ 表示在第 $i$ 个烟花的时候，你的位置是 $j$ 的最大 $happy$ 值。那么我们有：
$$ F_{i, j} = \max_{k \in [j-d\Delta t, j+d \Delta t]}\lbrace F_{i-1, k} + b[i] - \mid a_i - j \mid \rbrace \quad (\Delta t = t[i]-t[i-1]) $$

&emsp; 我们要的答案就是 $\max\limits_{i=1}^n\{ F_{i,m} \}$，~~朴素的 DP 方程还是很好推的qwq。~~ 那么我们这样做出来的时间复杂度就是 $O(mn^2)$，显然时间上是过不了所有数据的，而且空间上开一个这样的数组： $F[300][150000]$ 也是存不下的。这时候我们就要考虑怎么优化一下算法了。

## 优化
&emsp; 我们稍微把刚才的方程变一下形，就变成了这样：
$$ F_{i, j} = \max_{k \in [j-d\Delta t, j+d\Delta t]}\lbrace F_{i-1, k} \rbrace + b[i] - \mid a_i - j \mid \quad (\Delta t = t[i]-t[i-1]) $$

&emsp; 我们会发现，后面那一坨东西其实根我们在转移的时候枚举的 $k$ 是无关的，所以我们把这个东西提出来放到后面。这样的话我们就能发现，其实 $F_{i, j}$ 的值只和 $\max\limits_{k\in[j-d\Delta t,j+d\Delta t]}\{F_{i-1, k}\}$ 有关系。一个很自然的想法就是说用线段树来维护这个区间最大值，这样时间复杂度就是 $O(nm\log n)$ 了。但是再一看数据范围，还是过不了。所以我们只能想办法把这玩意儿优化成 $O(1)$ 的。

### 单调队列
&emsp; 那我们来考虑如何实现 $O(1)$ 查询这个东西，如果做过 **滑动窗口** [luoguP1886 滑动窗口](https://www.luogu.com.cn/problem/P1886) 这道题的同学肯定就能想到利用单调队列来解决这个问题。

&emsp; 具体的做法也很简单。我们发现，每次我们查询的区间的长度其实都是一样的，只是每次查询最值的时候整个查询区间会向右移一格（就像一个长度一定的窗口在数组上每次往右移一格一样）。这样的话就可以很好地利用单调队列完成这个操作。我们以这样一个数列来举例(我们假设每次查询的区间长度为3)： $\{ 3, 7, 2, 5, 4, 6, 1, 9, 8 \}$

&emsp; 第一次查询：如下图，我们用 $h$ 代表队列的头指针，$t$ 代表队列的尾指针。然后窗口中现在只有一个数 3，所以队列里面就只有一个数 3。这次查询的最大值就是队首 3。

![在这里插入图片描述](/Alex/OI/pic/MQDP1.png)

&emsp; 第二次查询：窗口中已经有了两个数 3 和 7，7 > 3，所以最大值一定是 7，我们就把 3 从队首直接出队，然后把 7 从队尾入队。这次查询的最大值就是队首 7。

![在这里插入图片描述](/Alex/OI/pic/MQDP2.png)


&emsp; 第三次查询，窗口里有 3 个数 3、7、2 了，新增了一个数 2，因为 2 < 7 所以它不可能是最大值，但是它有可能称为后面询问的最大值（如果它后面的两个值都比它小那他就是下下次询问的最大值）。所以我们把它从队尾入队。这次查询的最大值仍然是队首 7。

![在这里插入图片描述](/Alex/OI/pic/MQDP3.png)



&emsp; 第四次查询，窗口里的数是 7、2、5，新增进来的数 5 > 2 所以 2 已经不可能成为最大值了，于是我们把 2 从队尾出队，5 < 7 但是它有可能成为后面询问中的最大值，所以我们把 5 从队尾入队。本次查询的最大值还是队首 7。

![在这里插入图片描述](/Alex/OI/pic/MQDP4.png)


&emsp; 第五次询问，窗口里的数分别为 2、5、4。7 已经不在窗口里了，所以把 7 从队首出队，然后看 4，4 < 5 它不是现在的最大值，但是有机会成为最大，所以我们把 4 从队尾入队。然后这次查询的最大值就是队首 5。

![在这里插入图片描述](/Alex/OI/pic/MQDP5.png)


&emsp; 第六次查询，窗口里的数是 5、4、6，这一次不在窗口里的数是 2，因为它本来就不在队列里，所以就不用管它了，然后新增进来的数 6 > 4，所以 4 不可能是最大了，就把 4 从队尾出队。然后再比较 6 > 5，所以 5 也不可能是最大值了，所以把 5 从队尾出队，然后把 6 从队尾入队。这次查询的最大值就是队首 6。

![在这里插入图片描述](/Alex/OI/pic/MQDP6.png)


&emsp; 第七次查询，现在在窗口里的数为 4、6、1，这次出去的是 5，5 本来就不在队列里，所以不管，然后新加进来的数是 1，1 < 6 所以它不是最大值，但是他可能成为以后询问中的最大，所以把 1 从队尾入队。这次查询的最大值是队首 6。

![在这里插入图片描述](/Alex/OI/pic/MQDP7.png)


&emsp; 第八次查询，窗口里的数为 6、1、9，这次离开窗口的数是 4，然而 4 本来就不在队列里，所以不管它，然后新加进来的数是 9，9 > 1，所以 1 已经不可能是最大了，所以把 1 从队尾出队。然后继续比较 9 > 6，所以 6 也不可能是最大，所以把 6 从队尾出队，然后把 9 从队尾入队。这次询问的最大值是队首 9。

![在这里插入图片描述](/Alex/OI/pic/MQDP8.png)


&emsp; 第九次询问，窗口里的数是 1、9、8，离开窗口的是 6，队列里没有 6，所以不管它。然后新增进来的是数 8，8 < 9 所以它不是现在的最大但是可能成为后面几次询问的最大，所以把 8 从队尾入队。这次询问的最大值是队首 9。

![在这里插入图片描述](/Alex/OI/pic/MQDP9.png)


&emsp; 第十次询问，现在窗口里两个数 9、8，离开窗口的数是 1，但它不在队列里，所以不管。然后没有新增进来的数了，所以队列不变，这次询问的最大值是队首 9。

![在这里插入图片描述](/Alex/OI/pic/MQDP10.png)


&emsp; 最后一次询问，窗口里只剩下一个数 8，离开窗口的数是 9，他在队列里，所以把它从队首出队，然后这次询问的最大值就是队首 8。

![在这里插入图片描述](/Alex/OI/pic/MQDP11.png)

&emsp; 现在让我们来总结一下上面维护单调队列的的流程：

1. 处理这次查询离开查询区间的数，如果它本来就不在队列里，那就不管，如果它在队列里，就让它从队首出队。
2. 然后处理新加进来的数 $x$，如果 $x >$ 队尾的数，那么让队尾的数出队，然后继续让 $x$ 和队尾的数进行比较，如果队尾的数还是比它小，那就继续让队尾的数出队，直到队尾的数比它大或者队列为空。然后就是如果 $x <$ 队尾是数，那么就让 $x$ 从队尾入队。
3. 每次查询的最大值就是队首的数。

### 滚动数组
&emsp; 现在时间复杂度的问题已经解决了，那么还有空间复杂度的问题，我们开一个这样的数组： $F[300][150000]$ 显然是不现实的，所以我们再仔细看看我们的方程;
$$ F_{i, j} = \max_{k \in [j-d\Delta t, j+d\Delta t]}\lbrace F_{i-1, k} \rbrace + b[i] - \mid a_i - j \mid \quad (\Delta t = t[i]-t[i-1]) $$

&emsp; 我们发现，$F_{i, j}$ 的值只和 $F_{i-1,k}$ 的值有关系 ~~（其实前面已经发现过一遍了qwq）~~，也就是说在计算到 $F_{i,x}$ 的时候，我们只需要前一行的数据二从 $F_{1, k}$ 到 $F_{i-2,k}$ 的所有数据已经都没有用了，我们完全可以不用浪费一些空间来专门存储它们。所以我们参考一下把二维背包转化成一维背包的做法。只用一个 $F[150000]$ 来存储转移需要的信息。但是还有个问题，就是我们也要维护 $F$ 数组上一行的单调队列，所以我们必须存有 $F$ 数组上一行的全部信息，而不是像背包一样直接把某一部分信息给覆盖掉。所以我们开一个 $F[2][150000]$ 就可以了。每次新的 $F_{i,k}$ 只要存在和上一行不同的行数中就可以了。

## 代码
&emsp; 有时间再写吧qwq。