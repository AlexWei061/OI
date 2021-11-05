# 组合计数

## 加法原理
&emsp; 若完成一件事的方法有 $n$ 类，其中第 $i$ 类方法包括 $a_i$ 种不同的方法，且这些方法互不重合。那么完成这件事共有 $a_1 + a_2 + a_3 + \cdots + a_n$种不同的方法。

----
## 乘法原理
&emsp; 若完成一件事需要 $n$ 个步骤。其中第 $i$ 个步骤有 $a_i$ 种不同的完成方法，且这些步骤互不干扰，则完成这件事共有 $a_1 \times a_2 \times a_3 \times \cdots \times a_n$种不同的方法。

----
## 排列数
&emsp; 从 $n$ 个不同的元素中 ***依次*** 取出 $m$ 个元素排成一列，产生的不同排列的数量为：

$$ A_n^m(也可以记为 P_n^m) = \frac{n!}{(n-m)!} = n \times (n-1) \times (n-2) \times \cdots \times (n-m+1) $$

---
## 组合数
&emsp; 从 $n$ 个不同的元素种取出 $m$ 个组成一个集合（***不考虑顺序***），产生的不同集合数量为：

$$ C_n^m = \frac{n!}{m!(n-m)!} = \frac{n \times (n-1) \times (n-2) \times \cdots \times (n-m+1)}{m \times (m-1) \times (m-2) \times \cdots \times 2 \times 1} $$

### 组合数的性质：
1. $C_n^m = C_n^{n-m}$
2. $C_n^m = C_{n-1}^m + C_{n-1}^{m-1}$
3. $C_n^0 + C_n^1 + C_n^2 + \cdots + C_n^n = 2^n$

### 证明性质2：
&emsp; 从 $n$ 个元素中取出 $m$ 个组成一个集合的两类方法：取 $n$ 号元素、不取 $n$ 号元素。若取 $n$ 号元素，则在剩余的 $n-1$ 个元素中选出 $m-1$ 个，有 $C_{n-1}^{m-1}$ 种取法。若不取 $n$ 号元素，在在剩余的 $n-1$ 个元素种选出 $m$个，有 $C_{n-1}^m$种取法。根据加法原理，性质二成立。

### 组合数的求法：
&emsp; 求 $C_n^m \mod{p}$

1. 直接递推求出 $n! ， m!^{-1}\mod{p} 和 (n-m)!^{-1}\mod{p}$ 时间复杂度 $O(nlogn)$
2. 利用性质2递推 $C_n^m = C_{n-1}^m + C_{n-1}^{m-1}$时间复杂度 $O(n)$
3. 若在计算阶乘的过程中在 $fac[]$ 数组中记录下 $k!$，在 $fac\_inv[]$ 数组中记录下 $k!^{-1} \mod{p}$ 就可以做到 $O(nlogn)$ 预处理 $O(1)$ 查询

$$C_n^m \mod{p} = \bigg[ (fac[n]\mod{p}) \times (fac\_inv[m]\mod{p}) \times (fac\_inv[n-m]\mod{p}) \bigg] \mod{p} $$

----

## 二项式定理：

$$ (a+b)^n = \sum_{k=0}^n C_n^k a^k b^{n-k} $$

### 证明：
&emsp; 当 $n = 1$时，$(a+b)^1 = C_n^0 a^0 b^1 + C_n^1 a^1 b^0 = a + b$ 成立。
&emsp; 假设当 $n = m$ 时命题成立，当 $n = m + 1$ 时：

$$ 

\begin{aligned}

&(a+b)^n \\

=&(a+b)^{m+1} = (a+b)(a+b)^m = (a+b)\sum_{k=0}^m C_m^k a^k b^{m-k} \\
 
= &\sum_{k=0}^{m} C_m^k a^{k+1} b^{m-k} + \sum_{k=0}^{m} C_m^k a^k b^{m-k+1}  

= \sum_{k=1}^{m+1} C_m^{k-1} a^k b^{m-k+1} + \sum_{k=0}^{m} C_m^k a^k b^{m-k+1} \\

= &\sum_{k=0}^{m+1} (C_m^{k-1} + C_m^k) a^k b^{m-k+1}

= \sum_{k=0}^{m+1} C_{m+1}^{k} a^k b^{m+1-k} \\

= &\sum_{k=0}^{n}C_n^k a^k b^{n-k} \\

\end{aligned}

$$
证毕

----

## 多重集的排列数
&emsp; 多重集或多重集合是数学中的一个概念，是集合概念的推广。在一个集合中，相同的元素只能出现一次，因此只能显示出有或无的属性。在多重集之中，同一个元素可以出现多次。设 $S = \lbrace n_1 \cdot a_1, n_2 \cdot a_2, ..., n_k \cdot a_k \rbrace$ 是由 $n_1$ 个 $a_1$，$n_2$ 个 $a_2$，$\cdots$，$n_k$ 个 $a_k$，组成的多重集，记 $n = n_1 + \cdots + n_k$。 S 的全排列个数为：

$$ \frac{n!}{n_1! n_2! \cdots n_k!} $$

----

## 多重集的组合数
&emsp; 设 $S = \lbrace n_1 \cdot a_1, n_2 \cdot a_2, \cdots, n_k \cdot a_k \rbrace$ 是由 $n_1$ 个 $a_1$，$n_2$ 个 $a_2$，$\cdots$，$n_k$ 个 $a_k$，组成的多重集，设整数 $r \leq n_i (\forall i \in [1, k])$。从 $S$ 中取出 $r$ 个元素组成一个多重集（不考虑元素的顺序）产生的不同多重集的数量为：

$$ C_{k+r-1}^{k-1} $$

### 证明：
&emsp; 原问题等价于统计下列集合的数量：$\lbrace x_1 \cdot a_1, x_2 \cdot a_2, \cdots , x_k \cdot a_k \rbrace$，其中 $\sum_{i=1}^k x_i = r$ 并且 $x_i \leq n_i$。故原问题等价于 $r$ 个 $0$， $k-1$ 个 $1$ 构成的全排类数—— $k-1$ 个 $1$ 把 $r$ 个 $0$ 分成了 $k$ 组。每组 $0$ 的个数就是对应的 $x_i$。而多重集 $\lbrace r \cdot 0, (k-1) \cdot 1 \rbrace$ 的全排列数为：

$$\frac{(r+k-1)!}{r!(k-1)!} = C_{k+r-1}^r = C_{k+r-1}^{k-1}$$

证毕

----

## Lucas 定理
&emsp; 若 $p$ 是质数，则对于任意整数 $1 \leq m \leq n$，有：

$$ C_n^m \equiv C_{n \mod{p}}^{m \mod{p}} \times C_{n/p}^{m/p} \pmod{p} $$

&emsp; 也就是说把 $n$ 和 $m$ 表示成 $p$ 进制数，对于 $p$ 进制下的每一位分别计算组合数，最后再乘起来。证明需要的知识超出了我们的学习范围。

&emsp; Lucas 的证明及其运用：https://oi-wiki.org/math/lucas/

----

## Catalan 数列

&emsp; 给定 $n$ 个 $0$ 和 $n$ 个 $1$，他们按照某种顺序组成一个长度为 $2n$ 的数组。满足任意前缀中 $0$ 的数量不小于 $1$ 的数量的序列的数量为：

$$ Cat_n = \frac{C_{2n}^n}{n+1} $$

### 证明：
&emsp; 令  $n$ 个 $0$ 和 $n$ 个 $1$ **随意** 排成一个长度为 $2n$ 的序列 $S$。存在一个最小位置 $2p+1 \in [1, 2n]$，使得 $S[2p+1]$ 中有 $p$ 个 $0$ 、$p+1$ 个 $1$。而把 $S[2p+2 \sim 2n]$ 中的所有数字取反后，包含 $n-p-1$ 个 $0$ 和 $n-p$ 个 $1$。于是我们得到了由 $n-1$ 个 $0$ 和 $n+1$ 个 $1$ 排成的序列。

&emsp; 同理，令  $n-1$ 个 $0$ 和 $n+1$ 个 $1$ **随意** 排成一个长度为 $2n$ 的序列 $S'$。也存在一个最小的位置 $2p' + 1$，使得 $S'[1 \sim 2p' + 1]$ 中有 $p'$ 个 $0$，$p'+1$ 个 $1$。把 $S'$ 后面剩下的部分取反，就得到了由 $n$ 个 $0$ 和 $n$ 个 $1$ 组成的、存在一个前缀 $0$ 比 $1$ 多的序列。

&emsp; 因此，一下两种序列构成一个双射：

1. 由 $n$ 个 $0$ 和 $n$ 个 $1$ 组成的、存在一个前缀 $0$ 比 $1$ 多的序列。
2. $n-1$ 个 $0$ 和 $n+1$ 个 $1$ 组成的序列。

&emsp; 根据组合数的定义，后者显然有 $C_{2n}^{n-1}$ 种。

&emsp; 综上所述：由 $n$ 个 $0$ 和 $n$ 个 $1$ 组成的，满足任意前缀中 $0$ 的数量不小于 $1$ 的数量的序列的数量为：

$$ Cat_n = C_{2n}^n - C_{2n}^{n-1} = \frac{(2n)!}{n!n!} - \frac{(2n)!}{(n-1)!(n+1)!} = \frac{C_{2n}^n}{n+1} $$

证毕


我们也可以从另一个角度来推导 $Cat_n$ :

&emsp; 考虑在一个凸 $(n+2)$ 边形中，我们可以画出 $(n-1)$  条互不相交的对角线将多边形分为 $n$ 个三角形，所有满足条件的方案数为 $Cat_n$

&emsp; 考虑 $(n+2)$ 边的多边形的任意一条边，我们这条边为基边，考虑这条边所以在的三角形，这个三角形会把凸 $(n+2)$ 边形分成两份，设其中的一份有 $n$ 条边，即它能被分割成 $k$ 个三角形，那么另一部分有 $(n - k + 1)$ 条边，即能被分成 $(n - 1 - k)$ 个三角形。根据乘法原理，这样的方案数为 $Cat_n = Cat_k \times Cat_{n-1-k}$ ，显然 $k$ 可以从 $0$ 一直变化到 $n-1$ 故：

$$ Cat_n = \sum_{k=0}^{n-1} Cat_n \times Cat_{n-k-1} $$

再根据一些构造函数的知识，就可以推导出前文所讲的公式:

$$ Cat_n = \frac{1}{n+1} C_{2n}^{n} $$

### 关于 Catalan 有关的推论：
&emsp; 只要满足以上递推式或通项公式的模型都能用通项公式直接求出所有方案个数:

1. $n$ 个左括号和 $n$ 个右括号的合法括号序列数量为 $Cat_n$。
2. $1, 2, 3, \cdots, n$经过一个栈，形成的合法输出栈序列的数量为 $Cat_n$。
3. $n$ 个节点构成的不同二叉树的数量为 $Cat_n$。
4. 在平面直角坐标系中，每一步只能向上或向右走，从 $(0, 0)$，走到 $(n, n)$ 并且除两个端点外不接触直线 $y = x$ 的路线数量为 $2Cat_{n-1}$

----