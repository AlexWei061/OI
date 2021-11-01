# 关于圆周率
## 圆周率为什么是一个定值
&emsp; 我们都知道，圆周率是一个圆的周长与直径的比值，我们在这里介绍一种利用数列极限来证明圆周率收敛于 $\pi$。

&emsp; 设单位圆内接正 $n$ 边形的周长为 $L_n$ ，则 $L_n = nsin\frac{180^\circ}{n}$。数列 $L_n$ 应该收敛与单位元的半周长，及圆周率 $\pi$ 。现在我们来严格证明这一点。

&emsp; 证明：令 $t = \frac{180^\circ}{n(n+1)}$，则当 $n \geq 3$ 的时候，$nt \leq 45^\circ$，且
$$ \tan nt = \tan \left((n-1)t + t\right) = \frac{\tan (n-1)t + \tan t}{1-\tan (n-1)t \tan t} \geq \tan(n-1)t + \tan t$$
&emsp; 同理：
$$ \tan (n-1)t \geq \tan(n-2)t + \tan t，\tan(n-2)t \geq \tan(n-3)t + \tan t，\cdots $$
&emsp; 所以：
$$ \tan nt \geq \tan (n-1)t + \tan t \geq \tan (n-2)t + 2\tan t \geq \cdots \geq n\tan t $$
&emsp; 故
$$
\begin{aligned}
\sin (n+1)t = & \sin nt \cos t + \cos nt \sin t \\
= & \sin nt \cos t \times \left( \frac{\cos nt \sin t}{\sin nt \cos t} \right) = \sin nt \cos t\times\left(1 +  \frac{\tan t}{\tan nt} \right)\\ \\ 
\because \tan nt \geq n \tan &t \rightarrow \frac{tant}{\tan nt} \leq \frac{\tan t}{n\tan t} = \frac{1}{n} \\ \\
\therefore sin(n+1)t &\leq \sin nt \cos t \times (1 + \frac{1}{n}) = \sin nt \cos t \times \frac{n+1}{n} \leq \frac{n+1}{n}\sin nt \\ \\
\therefore n \sin (n+1)t& \leq (n+1)\sin nt
\end{aligned}
$$

&emsp; 因此，当 $n \geq 3$ 的时候：
$$ L_n = n \sin \frac{180^\circ}{n} \leq (n+1)\sin\frac{180^\circ}{n+1} = L_{n+1} $$

&emsp; 又因为，单位圆内接正 $n$ 边形的面积：

$$
\begin{aligned}
S_n = n\sin\frac{180^\circ}{n} \cos\frac{180^\circ}{n} < 4 \\ \\ 
\therefore n\sin \frac{180^\circ}{n} < \frac{4}{\cos\frac{180^\circ}{n}}
\end{aligned}
$$

&emsp; 所以当 $n \geq 3$ 的时候

$$ L_n  = n\sin \frac{180^\circ}{n} < \frac{4}{\cos \frac{180^\circ}{n}} \leq \frac{4}{\cos 60^\circ} = 8 $$

&emsp; 综上所述，数列 $\lbrace n \sin \frac{180^\circ}{n} \rbrace$ 单调增且有上界，所以它是收敛的，这个极限就是圆周率 $\pi$，即：
$$ \lim_{n \to \infty}n \sin \frac{180^\circ}{n} = \pi $$

## 圆周率的一种无穷级数展开
$$
\begin{aligned}
& \tan \frac \pi 4 = 1 \rightarrow \pi = 4 \arctan(1) \\ \\
\end{aligned}
$$

&emsp; 令 $f(x) = \arctan(x)$，则 $f'(x) = \frac 1 {1+x^2}$。令 $g(t) = \frac 1 {1+t}$，则：

$$
\begin{aligned}
g^{(n)}(t) = (-1)^n \cdot n! (t + 1)^{-n-1} \quad \therefore g^{(n)}(0) = (-1)^n \cdot n!
\end{aligned}
$$
&emsp; 把 $g(t)$ 在零点泰勒展开：

$$ g(t) = \sum_{n = 0}^{\infty} (-1)^n \cdot t^n $$

&emsp; 所以：
$$ f'(x) = \frac 1 {1 + x^2} = g(x^2) = \sum_{n=0}^{\infty} (-1)^n \cdot x ^{2n} $$

&emsp; 对这个式子求定积分得到原函数：

$$
\begin{aligned}
f(x) - f(0) = &f(x) = \int_0^x \sum_{n=0}^{\infty}(-1)^n \cdot z^{2n}dz \\
= &\sum_{n=0}^{\infty} (-1)^n \int_0^x z^{2n}dz = \sum_{n=0}^\infty (-1)^n \cdot \frac{x^{2n+1}}{2n+1} \\ \\
\therefore f(1) = \sum_{n=0}^\infty (-1)^n &\cdot \frac{1}{2n+1}
\end{aligned}
$$
&emsp; 综上所述：
$$ \pi = 4 \times (1 - \frac 1 3 + \frac 1 5 - \frac 1 7 + \frac 1 9 - \cdots) $$