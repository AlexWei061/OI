# 二次剩余

&emsp; 前置芝士：[原根和阶的口胡笔记](https://blog.csdn.net/ID246783/article/details/124514022?spm=1001.2014.3001.5502)

&emsp; 传送门：[P5491 模板 二次剩余](https://www.luogu.com.cn/problem/P5491)

## 题目大意

&emsp; ~~二次剩余俗称模意义开根~~，给出 $n$ 和 $p$ 解方程：

$$ x^2 \equiv n \pmod p $$

&emsp; **我们保证 $p$ 是一个奇素数**

## 定义

&emsp; 对于 $n$ 和 $p$ 来说如果 $\exist x$ 使得 $x^2 \equiv n \pmod p$ 就称 $n$ 是 模 $p$ 的二次剩余。

## 解的数量

&emsp; 我们考虑二次剩余 $n$，$x^2 \equiv n$ 有多少解。我们假设有很多组解，其中两个不同的解 $x_0$ 和 $x_1$。我们就有 $x_0^2 \equiv x_1^2$，我们移一下项就能得到：

$$ (x_0 - x_1)(x_0 + x_1) \equiv 0 $$

&emsp; 又因为 $p$ 是奇素数，而且有 $x_1 \neq x_0$，所以只有可能是 $x_0 + x_1 \equiv 0$，也就是说不相等的两个解一定为相反数。而且又因为 $p$ 是奇素数所以两个互为相反数的数不可能相等（因为奇偶性的问题）。

&emsp; 推到这里我们还能知道，任意一对相反数都对应一个二次剩余，而且这些二次剩余两两不同，也就是说二次剩余的数量恰好为 $\frac{p - 1}{2}$，其他的就都是非二次剩余了。

## 判断 $n$ 是否为二次剩余

&emsp; 怎样快速判断一个 $n$ 是不是一个二次剩余呢？我们知道费马小定理：

$$ n^{p - 1} \equiv 1\pmod p $$

&emsp; 因为 $p$ 是奇素数，所以：

$$ n^{2\frac{p - 1}{2}} - 1 \equiv 0 $$

&emsp; 也就是说 $n^{\frac{p - 1}{2}}$ 就是 $1$ 开根的结果，又因为 $1$ 开根只有可能是 $1$ 或者 $-1$，所以 $n^{\frac{p - 1}{2}}$ 只可能等于 $1$ 或 $-1$。

&emsp; 现在我们假设 $n$ 是二次剩余，那么就有 $n^{\frac{p - 1}{2}} \equiv (x^2)^{\frac{p - 1}{2}} \equiv x^{p - 1}$，然后现在我们分情况讨论。

1. $n^\frac{p - 1}{2} \equiv 1$ 的时候，将 $n$ 写成 $n = g^k$，其中 $g$ 是模 $p$ 的原根，那么就是 $g^{k\frac{p - 1}{2}} \equiv 1$，又因为 $g$ 是原根，所以有 $g^{\varphi(p)} \equiv 1$，所以就是 $\varphi(p) | k\frac{p - 1}{2}$，也就是 $p - 1 | k \frac{p - 1}{2}$。那么 $k$ 就一定是一个偶数。我们令 $x = g^{\frac k2}$，就是 $n$ 开根之后的结果，说明 $n$ 是二次剩余。所以我们发现 $n^{\frac{p - 1}{2}} \equiv 1$ 和 $n$ 是二次剩余是等价的。
2. 因为 $n^{\frac{p - 1}{2}}$ 不是 $1$ 就是 $-1$，所以 $n^{\frac{p - 1}{2}} \equiv - 1$ 与 $n$ 是非二次剩余等价。


## Cipolla 定理

&emsp; 对于解方程 $x^2 \equiv n \pmod p$，我们随机化找到一个 $a$ 满足 $a^2 - n$ 是非二次剩余，因为二次剩余的个数差不多是 $\frac{p}{2}$，所以我们期望 $2$ 次就能找到这样的 $a$。

&emsp; 接下来定义 $i ^2 \equiv a^2 - n$。这时候有人就要问了，不是说 $a^2 - n$ 是非二次剩余吗，怎么来的 $i^2$ 呢。我们类比实数推广到复数的思想，直接定义这样的一个 $i$。然后就可以将所有数写成 $A + Bi$，其中 $A, B$ 都是模 $p$ 意义之下的数，这就类似于实部和虚部。现在我们要解方程之前还要证明几个引理：

1. $i^p \equiv -i$
2. $(A + B)^p \equiv A^p + B^p$
3. $(a +i)^{p + 1} \equiv n$

&emsp; 第一个很好证，证明过程如下：

$$
\begin{aligned}
i^p \equiv i(i^2)^{\frac{p - 1}{2}} \equiv i(a^2 - n)^{\frac{p - 1}{2}} \equiv -i
\end{aligned}
$$

&emsp; 第二个考虑多项式定理展开，因为 $p$ 是质数，所以除了 $C_p^0$ 和 $C_p^p$ 之外的所有组合数分子上的阶乘都没法消去，再模个 $p$ 那就都是 $0$ 了，所以剩下来的就是 $C_p^0A^0B^p + C_p^pA^pB^0 = A^p + B^p$。

&emsp; 最后一个：

$$
\begin{aligned}
(a + i)^{p +1} \equiv (a^p + i^p)(a +i) \equiv (a^p - i)(a + i)
\end{aligned}
$$

&emsp; 又由费马小定理得到：$a^{p - 1} \equiv 1 \pmod p$，所以 $a^p \equiv p$，代入上式：

$$ (a + i)^{p + 1} \equiv (a - i)(a + i) \equiv a^2 - i^2 \equiv n $$

&emsp; 现在三个引理证完了，那么方程的解也就很显然了，我们要解的数满足 $x^2 \equiv n$，我们又有：$(a+i)^{p+1}\equiv n$，所以 $x = \sqrt{(a+i)^{p+1}} = (a+i)^{\frac{p+1}{2}}$，另一个解就是他的相反数。

&emsp; 现在还有一个问题，我们现在解出来的东西只有在 “虚部” 为 $0$ 的时候才是一个正常的数，如果不为零的话我们上面的推导就没什么意义了。但幸运的是，我们可以证明 $(a + i)^{\frac{p + 1}{2}}$ 的 "虚部" 一定为 $0$。

&emsp; 假设 $\exist (A + Bi)^2 \equiv n \land B \neq 0$，现在把这个式子展开：

$$ A^2 + B^2i^2 + 2ABi \equiv n $$

&emsp; 也就是说（$i^2 \equiv a^2 - n$）：

$$ A^2 + B^2(a^2 - n) - n \equiv -2ABi $$

&emsp; 我们发现左边的式子虚部为 $0$，那么右边的式子虚部一定也为 $0$，也就是说 $AB \equiv 0$，又因为 $B \neq 0$，那么就有 $A \equiv 0$，也就是说 $(Bi)^2 \equiv n$。在写的好看一点：

$$ i^2 \equiv nB^{-2} $$

&emsp; 因为 $B^2$ 是一个二次剩余（应该很显然吧），那么 $nB^{-2}$ 显然也是一个二次剩余，那么这就与 $i^2$ 不是二次剩余矛盾了，所以 $\nexists (A + Bi)^2 \equiv n \land B \neq 0$，也就是说虚部一定为 $0$。

&emsp; 现在我们来总结一下，对于方程 $x^2 \equiv n \pmod p$，其中 $p$ 是奇素数，来说我们有：

1. $n^{\frac{p - 1}{2}} \equiv 1$ 与 $n$ 是一个二次剩余等价。
2. $n^{\frac{p - 1}{2}} \equiv -1$ 与 $n$ 不是一个二次剩余等价。
3. 解方程时算随机化找到一个 $i^2 \equiv a^2 - n$ 是一个非二次剩余。（找到这个东西的期望次数是 $2$）。
4. 然后答案就是 $(a + i)^{\frac{p + 1}{2}}$ 和它的相反数。
