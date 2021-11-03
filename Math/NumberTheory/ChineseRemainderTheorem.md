# 中国剩余定理
&emsp; 设 $m_1, m_2, \cdots, m_n$ 是两两互质的整数，$m = \prod_{i=1}^{n}m_i,\;\; M_i = \frac{m}{m_i}$，$t_i$ 是线性同余方程 $M_it_i \equiv 1 \pmod {m_i}$的一个解。对任意一的 n 个整数 $a_1, a_2, \cdots, a_n$，方程
$$
\begin{cases}
x \equiv a_1 \pmod{m_1}\\
x \equiv a_2 \pmod{m_2}\\
\vdots\\
x \equiv a_n \pmod{m_n}
\end{cases}
$$ 
&emsp;有正整数解，解为：
$$ x = \sum_{i=1}^{n}a_iM_it_i $$

证明：
&emsp; 因为 $M_i$ 为所有 m 除了 $m_i$ 的乘积，所以我们有：
$$ \forall k \neq i, \;\;  a_iM_it_i \equiv 0 \pmod{m_k}$$
&emsp; 又因为：
$$ M_it_i \equiv 1 \pmod{m_i} $$
&emsp; 两边同时乘上 $a_i$ 得：
$$ a_iM_it_i \equiv a_i \pmod {m_i} $$
&emsp;所以带入$x = \sum_{i=1}^{n}a_iM_it_i$原方程成立。所以 x 的一个特解就是：
$$ x = \sum_{i=1}^{n}a_iM_it_i $$
*证毕。*

&emsp;中国剩余定理给出了模数两两互质的线性同余方程组的一个特殊解，而通解我们可以表示为 $x + km, (k \in Z)$。如果要求最小非负整数解，那就用模运算将 x 移动到 [1, m)的区间中来就好了。

----
参考资料：《算法竞赛进阶指南》