# 拉格朗日插值
&emsp; 插值就是有 $n$ 个已知的平面直角坐标系中的点，然后求出一个 $n-1$ 次多项式，这个多项式穿过这些点。

$$ \{ x \} : x_0, x_1, x_2, \cdots,  x_n \\ \{ y \} : y_0, y_1, y_2, \cdots,  y_n $$

&emsp; 然后我们要找到一个 $f(x)$ 穿过这些点。拉格朗日插值给出了一种显然的 $O(n^2)$ 的构造方法，也就是：

$$ f(x) = \sum_{k=0}^n \frac{\prod\limits_{i=0, i \neq k}^n (x-x_i)}{\prod\limits_{i=0, i \neq k}^n (x_k - x_i)} $$

&emsp; 正确性非常显然。