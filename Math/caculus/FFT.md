# FFT
&emsp; FFT 也就是快速傅里叶变换，这玩意儿应该很多人都听过，这个就是利用卷积来快速解决多项式乘积问题的一个算法。时间复杂度是 $O(n \log n)$

## 简要的说明
&emsp; 很显然，如果直接暴力地计算两个多项式的乘积的话，那么时间复杂度肯定是 $O(n^2)$ 的，如果不知道为什么的话，看完下面这个例子肯定也就说知道了：
$$
\begin{aligned}
& (3x^3 + 7x^2 + x + 2)(6x^3 + 3x + 1)\\
= &6x^3 (3x^3 + 7x^2 + x + 2) + 3x(3x^3 + 7x^2 + x + 2) + (3x^3 + 7x^2 + x + 2)\\
= & 18x^6 + 42x^5 + 6x^4 + 12x^3 + 9x^4 + 21x^3 + 3x^2 + 6x + 3x^3 + 7x^2 + x + 2 \\
= & 18x^6 + 42x^5 + 15x^4 + 10x^2 + x^3 + 7x + 2
\end{aligned}
$$

### 多项式系数表达
&emsp; 在这里我们其实是用到的系数表达来表示一个多项式，啥意思呢，就是说我们用的是一个多项式每一个不同次数项前面的系数来表达这个多项式。比如说一个多项式：
$$ F(x) = 2x^3 + 3x^2 + 7x + 1 $$

&emsp; 我们就用一个数列 $\lbrace x_n \rbrace = \lbrace 1, 7, 3, 2 \rbrace$ 来唯一确定这一个多项式。很显然，我们用 n 个数就能确定一个最高次项是 n-1 次的多项式。如果我们记 $F[i]$ 表示 $F(x)$ 的 $i$ 次项系数，那么：
$$ F(x) = \sum_{i=0}^n F[i]x^i $$

&emsp; 如果我们用点值表达来表示两个个多项式 $A(x)$ 和 $B(x)$，我们记他们的乘积为 $C(x)$。如果：
$$ A(x) = \sum_{i=0}^n A[i]x^i \\ B(x) = \sum_{i=0}^mB[x]x^i $$

&emsp; 那么：
$$ C[k] = \sum_{i=0}^k A[i]B[k-i] $$

&emsp; 这个其实也就是加法卷积。




### 多现实的点值表达
&emsp; 首先，我们先明确一点：一个 $n$ 次多项式一定能由平面上相互不同的 $n+1$ 个点来唯一确定（关于这方面的东西，我以后会写一个关于拉格朗日插值的文章）。这个其实很好证明，感性理解一下就是用待定系数法解方程，n + 1 个未知数一定需要 n + 1 个方程才能唯一确定一组解。

&emsp; 然后我们就可以很自然的引出我们的系数表达了，也就是把这个多项式变成一个函数，并把它画在平面直角坐标系上，然后在它的图像上取出 n + 1 个互不相同的点，并且把它们横纵坐标记录下来。这些横纵坐标们就是这个多项式的点值表达，也就是写成这样。
$$ (x_0, y_0), (x_1. y_1), (x_2, y_2), \cdots (x_n, y_n) $$

&emsp; 那么如果我们用点值表达式来进行多项式的乘法运算的话，我们只需要 $O(n)$ 的时间就能计算出来了。就比如我们在平面的 x 轴上取出 n + 1 个点，并写出多项式（为了方便这俩多项式的最高次项系数都为 n） $F(x)$ 和 $G(x)$ 的点值表达，分别为：
$$ (x_0, y_0), (x_1. y_1), (x_2, y_2), \cdots (x_n, y_n) \\ (x_0, y'_0), (x_1. y'_1), (x_2, y'_2), \cdots (x_n, y'_n) $$

&emsp; 那么他们的乘积 $H(x)$ 的点值表达显然就是：
$$ (x_0, y_0 \cdot y'_0), (x_1. y_1 \cdot y'_1), (x_2, y_2 \cdot y'_2), \cdots (x_n, y_n \cdot y'_n) $$

&emsp; 但是有一个问题，我们的乘积的最高次项显然应该是 $2n$ ，那么乘积的点值表达就应该有 $2n+1$ 个点才能唯一确定这个多项式。那现在怎么办呢？很简单，反正这些点都是你随便取的，再多取 $n$ 个点不就好了。也就是说我们在平面的 x 轴上取出 $2n + 1$ 个点，并写出多项式（为了方便这俩多项式的最高次项系数都为 n） $F(x)$ 和 $G(x)$ 的点值表达，分别为：
$$ (x_0, y_0), (x_1. y_1), (x_2, y_2), \cdots (x_{2n}, y_{2n}) \\ (x_0, y'_0), (x_1. y'_1), (x_2, y'_2), \cdots (x_{2n}, y'_{2n}) $$

&emsp; 然后就能正确写出连个多项式的乘积的点值表达式了，也就是这样：
$$ (x_0, y_0 \cdot y'_0), (x_1. y_1 \cdot y'_1), (x_2, y_2 \cdot y'_2), \cdots (x_{2n}, y_{2n} \cdot y'_{2n}) $$

## 思想
&emsp; 在了解了多项式的点值表达和系数表达之后，我们就可以大致知道 FFT 到底是干怎么工作的了。就是这样：

1. 将系数表达式转化成点值表达 (DFT)
2. 利用点值表达解出乘积的点值表达
3. 用点值表达再转回系数表达 (IDFT(DFT 的逆运算))

&emsp; 从一个多项式的系数表达确定其点值表达的过程称为**求值**。而求值运算的逆运算 (也就是从一个多项式的点值表达确定其系数表达) 被称为**插值**。

&emsp; 但是如果我们用暴力的方法来 **求值** 和 **插值**，求值的复杂度就是 $O(n^2)$，而插值就更惨，利用高斯消元解方程的复杂度是 $O(n^3)$。所以我们就需要一些神奇的算法（DFT 和 IDFT）来帮助我们求值和插值。

&emsp; 提醒：下面的内容需要有一定的复数相关的知识才能看懂。
## DFT
&emsp; DFT 中文名称为离散傅里叶变换（为什么是离散呢，因为他把积分符号换成了求和符号 qwq）。这个东西就能解决我们求值的问题，并且能把复杂度降到 $O(n \log n)$。

&emsp; 但是这个东西是怎么做到的呢？我们先来看看我们如果用暴力的做法是怎样求值的。首先，我们选定 2n + 1 个横坐标，然后每次 $O(n)$ 计算每一个横坐标对应的纵坐标。然而，这些横坐标都是我们自己选定的，对于大多数正常人来说，这些坐标的值肯定就被选成了这样：
$$ 1, 2, 3, 4, 5, 6, 7, 8, 9, \cdots, 2n+1 $$

&emsp; 这时，傅里叶大佬横空出世并对我们说：不！我们不能带入这些没意思的数，我们要选一些其他 ~~更毒瘤~~ 的数往里面代入！这些数是什么呢？这些数就是单位根！！

### 单位根(复数)
&emsp; 好了，让我们现在来介绍一下单位根是个什么鬼东西。复数的单位根其实也就是在复平面上的单位圆上的点，些就是说，它们都可以写成 $\cos a + i \sin a$ 的样子（不知道为什么的同学可以先去看看关于虚数和复数的基本知识）。

&emsp; 之后，我们人为的把复平面上的单位圆像切蛋糕一样划分成 n 等份，这样每一个圆上的分界点就是一个单位根。举个例子，如果我们把单位圆八等份，首先第一个单位根，记做 $\omega_8^0$，也就是实数轴与单位圆的交点。然后沿着单位圆逆时针走 $\frac {\pi}{4}$ 就到达了第二个单位根（八等份嘛）记做 $\omega_8^1$。同理继续逆时针转，就会以此得到 $\omega_8^2, \omega_8^3, \omega_8^4, \cdots \omega_8^7$。

&emsp; 正因为如此单位根也可以写成这样：
$$ \omega_n^k = e^{\frac{ik}{n}2\pi} = \cos\left(\frac{k}{n}2\pi\right) + i\sin\left(\frac kn 2\pi\right) $$

### FFT 分治
&emsp; 了解了什么事单位根，我们就继续往下说。傅里叶不仅要把一些神奇的数带入多项式，而且他还要一掌把这个多项式按照奇偶拍成两部分。
$$
\begin{aligned}
& F(x) = a_0 + a_1 x + a_2 x^2 + a_3 x^3 + a_4 x^4 + \cdots\\
& F_L(x) = a_0 + a_2 x + a_4 x^2 + a_6 x^3 + a_8 x^4 + \cdots\\
& F_R(x) = a_1 + a_3 x + a_5 x^2 + a_7 x^3 + a_9 x^4 + \cdots
\end{aligned}
$$

&emsp; 通过观察，我们可以发现：
$$ F(x) = F_L(x^2) + xF_R(x^2) $$

&emsp; 这个性质非常重要，这也是 FFT 分治的关键的地方。现在因为我们已经确定了要代入单位根，我们就代一个进去算一下（**注意：下面的所有 k 都小于 n / 2**）：
$$
\begin{aligned}
F(\omega_n^k) = & F_L((\omega_n^k)^2) +\omega_n^kF_R((\omega_n^k)^2) 
= F_L(\omega_{n/2}^k) +\omega_n^kF_R(\omega_{n/2}^k)
\end{aligned}
$$

&emsp; 再带一个：
$$
\begin{aligned}
F(\omega_{n}^{k+n/2}) = & F_L((\omega_{n}^{k+n/2})^2) + \omega_{n}^{k+n/2}F_R((\omega_{n}^{k+n/2})^2) \\
= & F_L(\omega_{n}^{2k+n}) + \omega_{n}^{k+n/2}F_R(\omega_{n}^{2k+n})\\
= & F_L(\omega_{n}^{2k}) + \omega_{n}^{k+n/2}F_R(\omega_{n}^{2k}) \\
= & F_L(\omega_{n/2}^{k}) + \omega_{n}^{k+n/2}F_R(\omega_{n/2}^{k}) = F_L(\omega_{n/2}^{k}) - \omega_{n}^{k}F_R(\omega_{n/2}^{k})
\end{aligned}
$$

&emsp; 看一下我们算出来的两个式子：
$$
\begin{aligned}
F(\omega_n^k) = & F_L(\omega_{n/2}^k) +\omega_n^kF_R(\omega_{n/2}^k) \\
F(\omega_{n}^{k+n/2}) = & F_L(\omega_{n/2}^{k}) -\omega_{n}^{k}F_R(\omega_{n/2}^{k})
\end{aligned}
$$

&emsp; 我们可以发现，如果我们知道了 $F_L(x)$ 和 $F_R(x)$ 在 $\omega_{n/2}^0, \omega_{n/2}^1, \cdots, \omega_{n/2}^{n/2-1}$ 的点值表达的话。我们用上面的第一个式子，就能算出 $F(x)$ 在 $\omega_n^0, \omega_n^1, \cdots, \omega_n^{n/2-1}$ 的点值表达。并且通过第二个式子就能得到 $F(x)$ 在 $\omega_n^{n/2}, \omega_n^{n/2+1}, \cdots, \omega_n^{n-1}$ 的点值表达。而且这样算出来的复杂度是 $O(n)$ 的，就跑的飞快。

&emsp; 但是现在问题又来了，我们怎么计算 $F_L(x)$ 和 $F_R(x)$ 的点值表达呢？很简单嘛，这两个问题是和原问题性质相同规模减半的子问题嘛，那我们就直接分治就好了。那么我们总的复杂度就降到了 $O(n \log n)$

&emsp; 这是我们用了复数值带入之后的结果，如果我们想用整数值带入的话，在计算 $F(2)$ 的时候，我们就需要的是 $F_L(4)$ 和 $F_R(4)$ 的点值，就不能分治了，所以复杂度降不下去。

## IDFT
&emsp; 顾名思义，IDFT 也就是 DFT 的逆运算，也就是插值的过程。我们现在考虑一下该如何来实现插值。

### 一个小意外
&emsp; 生活中难免碰到一些粗心大意的地方，就比如说我们的小 k 同学在写求值的步骤中不小心把已经求出来的值又用几乎同样的步骤算了一遍（只是把 $\omega_n^{ik}$ 变成了 $\omega_n^{-ik}$），也就是算出了求值的求值。小 k 同学就惊奇的发现，现在输出出来的结果居然和原多项式的系数每个都是 n 倍的关系。小 k 同学现在想证明这个结论是否正确。

### 证明
&emsp; 我们可以通过脑补得到，我们在对一个多项式进行 DFT 的时候其实是这样一个过程（其中 $a_i$ 是原数列中的值，$b_i$ 求出来的数列中的值）：
$$ b_k = \sum_{i = 0}^{n-1} a_i \omega_n^{ik} $$



&emsp; 这个应该很容易理解吧，毕竟 $F(x) = \sum\limits_{i = 0}^{n-1} a_ix^i$ 嘛。然后我们再对 b 数列做一遍 DFT（但是把 $\omega_n^{ik}$ 变成了 $\omega_n^{-ik}$），得到一个 c 数列：
$$ c_k = \sum_{i=0}^{n-1}b_i \omega_n^{-ik} $$



&emsp; 然后继续算：
$$
\begin{aligned}
c_k = & \sum_{i=0}^{n-1}b_i \omega_n^{ik} = \sum_{i = 0}^{n-1}\omega_n^{-ik} \sum_{j=0}^{n-1} a_j\omega_n^{ij} = \sum_{j = 0}^{n-1} \sum_{i = 0}^{n-1} \omega_n^{i(j-k)}a_j
\end{aligned}
$$

&emsp; 然后从这里开始需要分类讨论了，首先第一种 $k + j = 0$ 的情况（也就是 $j = k$ 的情况）：这种情况下对总的值的贡献就是：
$$ \sum_{i=0}^{n-1} \omega_n^0a_k = na_k $$

&emsp; 第二种情况 $j \neq k$ 的时候，我们令 $q = j - k$，那么这种情况下每一个 j 的贡献就是：
$$ \sum_{i=0}^{n-1}\omega_n^{ip}a_{k+p} = \omega_n^p \times a_{k+p}\sum_{i = 0}^{n-1}\omega_n^i $$

&emsp; 其中利用等比数列求和公式得到：
$$ \sum_{i=0}^{n-1}\omega_n^i = \frac{\omega_n^n - \omega_n^0}{\omega_n^1 - 1} = 0 $$

&emsp; 所以上面的一大坨式子也等于 0 。即：
$$ \sum_{i=0}^{n-1}\omega_n^{ip}a_{k+p} = 0 $$

&emsp; 所以真正做出贡献的只有 $j = k$ 的时候算出来的 $na_k$，所以我们就证得 $c_k = na_k$。小 k 同学的疑惑也就解开了qwq。

### 又是 FFT
&emsp; 写到这里，读者们应该也能想到接下来该怎么做了，利用上面的结论：
$$
\begin{aligned}
G[k] = & \sum_{i = 0}^{n - 1}\omega_n^{ik}F[k] \\
nF[k] = & \sum_{i = 0}^{n - 1}\omega_n^{-ik}G[k]
\end{aligned}
$$

&emsp; 对于已经求出来的乘积的点值表达再来一遍 FFT，不过这次带入的是 $\omega_n^{0}, \omega_n^{-1}, \omega_n^{-1}, \cdots, \omega_n^{-n+1}$ 而已。

## 代码

&emsp; 原题传送门：[luogu:P3803 ](https://www.luogu.com.cn/problem/P3803)

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1 << 22
#define eps 1e-6
#define pi acos(-1.0)

int n = 0; int m = 0;
complex<double> a[MAXN], b[MAXN];

void FFT(complex<double> *a, int n, int inv){
	if(n == 1) return;
	int mid = n / 2;
	complex<double> Al[mid + 1], Ar[mid + 1];
	for(int i = 0; i <= n; i += 2){
		Al[i / 2] = a[i];
		Ar[i / 2] = a[i + 1];
	}
	FFT(Al, mid, inv);
	FFT(Ar, mid, inv);
	complex<double> w0(1, 0), wn(cos(2 * pi / n), inv * sin(2 * pi / n));
	for(int i = 0; i < mid; i++, w0 *= wn){
		a[i] = Al[i] + w0 * Ar[i];
		a[i + n / 2] = Al[i] - w0 * Ar[i];
	}
}

int main(){
	scanf("%d%d", &n, &m);
	for (int i = 0; i <= n; ++i){ double x; scanf("%lf", &x); a[i].real(x); }
    for (int i = 0; i <= m; ++i){ double x; scanf("%lf", &x); b[i].real(x); }
    int len = 1 << max((int)ceil(log2(n + m)), 1);
    FFT(a, len, 1);
	FFT(b, len, 1);
	for(int i = 0; i <= len; i++) a[i] *= b[i];
	FFT(a, len, -1);
	for (int i = 0; i <= n + m; ++i) printf("%.0f ", a[i].real() / len + eps);
    return 0;
}
```

## 总结
&emsp; 快速傅里叶变换求多项式乘法的步骤如下：

1. DFT，FFT 分治 $O(n\log n)$ 求出两个多项式的点值表达。
2. 对应点相乘 $O(n)$ 得到乘积的点值表达。
3. IDFT（再来一遍 FFT）得到乘积的系数表达。

&emsp; 完结撒花！