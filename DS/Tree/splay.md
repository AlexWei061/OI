# Spaly

&emsp; 众所周知，Splay 是一种平衡二叉查找树（~~不要告诉我你不知道二叉查找树是什么qwq~~ 不知道什么是二叉查找树的看过来： [关于二叉查找树](https://blog.csdn.net/ID246783/article/details/121114692)）。在这篇东西的最后我们也解释了为什么我们需要用到平衡二叉查找树而不是直接查找，这里就不再赘述了，所以现在直接引入正题吧。

### 旋转操作

&emsp; 未来解决普通二叉查找树被卡成层数过深而时间复杂度升高的问题我们需要引入这样一个旋转操作。这个操作的本质就是在不改变这棵树的中序遍历的前提下让这颗树的层数减小。具体来说是这样做的（对于左边的那棵树：$R$ 是 $y$ 的祖先 $y$ 是 $x$ 的父节点，$A$ 和 $B$ 分别为 $x$ 的左右子树，$C$ 是 $y$ 的右子树）：

![在这里插入图片描述](/Alex/OI/pic/Spaly1.png)


&emsp; ~~这个图是不是特别好懂呢qwq。~~ 现在我们来解释一下这个神奇的操作吧：顾名思义，旋转就是把一个节点的父节点旋转下来变成这个节点的子节点。但是如果直接旋转下来的话这棵树就变成了一颗三叉树了（下面是如果直接转下来的图）：

![在这里插入图片描述](/Alex/OI/pic/Spaly2.png)
&emsp; 这样搞显然是不行的，所以我们为了保证这棵树还具有二叉查找树的性质，我们就需要把 $x$ 原来的右子树 $B$ 给从 $x$ 上取下来，并且接到 $y$ 的左子树上（这样接的目的是保证这个棵树中序遍历不变，读者可以自己写一下旋转后和旋转前的中序遍历验证一下），如下图：

![在这里插入图片描述](/Alex/OI/pic/Spaly3.png)
&emsp; 这样我们的旋转操作就完成啦。

&emsp; 然后我们来看看代码吧，其中：

1. $fa[x]$ 表示 $x$ 的父亲节点。
2. $ch[x][0]$ 就表示 $x$ 的左儿子，$ch[x][1]$ 表示 $x$ 的右儿子。
3. get(x) 是个 bool 型函数，如果 $x$ 是他父节点的左儿子则返回 0，是右儿子则返回 1。
4. isr(x) 表示询问 $x$ 是不是这棵树的根。
5. up(x) 就是上传节点信息（这里不用管这个玩意儿啦~~）

```cpp
inline void rot(int x){
	int y = fa[x]; int z = fa[y]; int k = get(x);                                    // 先创建变量
	if(!isr(y)) ch[z][get(y)] = x; fa[x] = z;                                        // 把 x 连在 z 下边 (和 y 同侧)
	ch[y][k] = ch[x][k ^ 1]; fa[ch[x][k ^ 1]] = y;                                   // 把 x 的一颗子树接下来接到 y 下面
	ch[x][k ^ 1] = y; fa[y] = x; up(y); up(x);                                       // 把 y 变成 x 的子节点
}
```

&emsp; 大家对照这上面第一张旋转操作的图再一句一句得看应该就懂了。

### Splay

&emsp; splay 顾名思义就是伸展的意思而在代码里面函数 $splay(x)$ 就是用刚才的 $rotate$ 操作将 $x$ 一路旋转道根节点的操作。但是我们不能直接 ~~单旋上去~~ 一个 $while$ 循环一直转上去：

```cpp
inline void spl(int x) { while(!isr(x)) rot(x); }
```

&emsp; 就是说这样是绝对不行的，因为这样的时间复杂度不对（至于具体什么原因，等会儿证明时间复杂度的时候会详细说明）。所以我们需要引入双旋的操作来实现把 $x$ 转到根的功能。双旋的操作要根据 $x$ 和他的父亲和他的父亲的父亲的位置关系来进行操作，一共分为三种情况：

1. $x$ 的父亲就是根节点 $R$：
 
&emsp; 在这种情况中，我们就只需要转一下 $x$ 就行啦。

![在这里插入图片描述](/Alex/OI/pic/Spaly4%20.png)


1. $x$ 和 $x$ 的父亲 $y$ 和 $x$ 的父亲的父亲 $z$ 在同一侧：

&emsp; 这种情况下，我们要先转一下 $y$ 再转一下 $x$。

![在这里插入图片描述](/Alex/OI/pic/Spaly5.png)


1. $x$ 和 $x$ 的父亲 $y$ 和 $x$ 的父亲的父亲 $z$ 在不同侧：

&emsp; 在这种情况下，就是说直接转两次 $x$ 就行了。

![在这里插入图片描述](/Alex/OI/pic/Spaly6.png)
&emsp; 对，就是这样。那么我们来看看代码吧：

```cpp
inline void spl(int x){
	while(!isr(x)){                                                                           // 直到把 x 转到根
		int y = fa[x];
		if(!isr(y)) rot(get(x) ^ get(y) ? x : y);                                             // 三种情况一句写完 qwq
		rot(x);
	}
}
```
&emsp; 然后 Splay 的其他操作方法就和普通平衡二叉树是类似的，只不过再插入和删除操作中要对当前节点做一遍 Splay 就搞定啦~~~

### 时间复杂度证明

##### 势能分析
&emsp; 再说这个东西之前，我们首先要知道一个东西叫做复杂度的势能分析：

&emsp; 在某些数据结构中我们往往很难估计第 $i$ 次操作的时间 $t_i$。所以我们要引入势能分析的概念：

1. 设 $\varphi_i$ 表示第 $i$ 次操作后数据结构的势能值
2. 记录 $a_i = t_i + \varphi_i - \varphi_{i-1}$ 表示第 $i$ 次操作的均摊时间

&emsp; 假设进行了 $m$ 次操作，那么总时间就是(很好理解的式子qwq)：

$$ T = \sum_{i=1}^m t_i = \left(\sum_{i=1}^m a_i\right) + \varphi_0 - \varphi_m	$$

&emsp; 所以我们只要直到了 $a_i$ 和 $\varphi_i$ 就能知道实际上的总时间了。

##### Splay 的势能分析s
&emsp; 在 splay 分析中，我们选取这样一个函数：

&emsp; 设当前状态下，节点 $x$ 的势能值 $F(x) = \log_{2}size_x$， 其中 $size_x$ 表示这个节点为根的子树的大小。

&emsp; 设当前状态下，整个 splay 的势能 $\varphi = \sum\limits_{i=1}^n F(i)$。

&emsp; 那么我们接下来需要证明的就是：

$$
\begin{aligned}
a_i & \leq 3(F(x') - F(x)) + 1 \\
& = O(\log_2\frac{n}{size_x}) = O(log_2 n)
\end{aligned}
$$

&emsp; 其中 $F(x)$ 和 $F(x')$ 分别表示 splay(x) 前和后的势能。因为旋转过后的 $x$ 已经到根了，所以就有

$$ F(x') = \log_2 n $$

&emsp; 然后就有：

$$ F(x') - F(x) = \log_2n - \log_2size_x = \log_2\frac{n}{size_x} $$

&emsp; 然后我们只需要分情况写出三种旋转情况的 $a_i$ 的值都小于等于$3(F(x') - F(x)) + 1$ 就能证明双旋 splay 的复杂度是 $\log$ 级别的了。

1. 第一种的证明：

![在这里插入图片描述](/Alex/OI/pic/Spaly4%20.png)

&emsp; 第一种情况时间就只转了一次，势能变化也很简单，显然只有被转的节点 $x$ 和他的爸爸 $R$ 的势能变了。所以我们就能得到：

$$
\begin{aligned}
a_{zig} & = t_i + \varphi_i - \varphi_{i-1} \\
& = 1 + F(x') + F(R') - F(x) - F(R) \\
& = 1 + F(R') - F(x) \leq 3(F(x') - F(x)) + 1
\end{aligned}
$$

&emsp; 显然在上图中 $F(x')$ 和 $F(R)$ 是相等的。所以最后一个等式是相等的。

1. 然后是第二种情况的证明：

![在这里插入图片描述](/Alex/OI/pic/Spaly5.png)
&emsp; 第二种情况也就值转了 2 次，势能变化就有点麻烦了，多了一个 $z$ 的势能发生了变化。所以我们得到：

$$
\begin{aligned}
a_{zig-zig} & = t_i + \varphi_i - \varphi_{i-1} \\
& = 2 + F(x') + F(y') + F(z') - F(x) - F(y) - F(z) \\
& = 2  + F(y') + F(z') - F(x) - F(y)
\end{aligned}
$$

&emsp; 观察上面的图我们发现 $F(x')$ 显然等于 $F(z)$。所以最后一个等式成立。然后现在这个式子看着这个 2 是不是非常的难受，所以我们继续观察看看能不能处理掉这个 2。于是我们注意到：

$$
\begin{aligned}
F(x) + F(z') - 2F(x') = log_2 \frac{size_x \cdot size_{z'}}{size_{x'}^2} \leq log_2\frac14 = -2
\end{aligned}
$$

&emsp; 这个式子如果看不懂的话就数一下上图的点数算算每个点的 size 然后带到式子里看一下就应该能懂了qwq（其实就是我懒得解释）。	

&emsp; 然后带回前面的式子：

$$ a_{zig-zig} \leq 2F(x') + F(y') - 2F(x) - F(y) \leq 3(F(x') - F(x)) + 1 $$

1. 第三种情况的证明：

![在这里插入图片描述](/Alex/OI/pic/Spaly6.png)

&emsp; 时间仍然是转了两次，是能也仍然是 $x,y,z$ 都发生了变化，那么：

$$
\begin{aligned}
a_{zig-zag} 	& = t_i + \varphi_i - \varphi_{i-1} \\
& = 2 + F(x') + F(y') + F(z') - F(x) - F(y) - F(z) \\
& = 2  + F(y') + F(z') - F(x) - F(y)
\end{aligned}
$$

&emsp; 然后仍然是注意到：

$$
\begin{aligned}
F(x) + F(z') - 2F(x') = log_2 \frac{size_x \cdot size_{z'}}{size_{x'}^2} \leq log_2\frac14 = -2
\end{aligned}
$$

&emsp; 然后仍然是两个式子进行合并：

$$ a_{zig-zag} \leq 2F(x') + F(y') - 2F(x) - F(y) \leq 3(F(x') - F(x)) + 1 $$

&emsp; 综上所述，三种情况中我们都有：

$$ a_i \leq 3(F(x') - F(x)) + 1 = O(\log_2 n) $$

&emsp; 所以 $a_i = \log_2n$，所以：

$$ T = \left(\sum_{i=1}^n a_i\right) + \varphi_0 - \varphi_m = O(m\log_2n) + \varphi_0 - \varphi_m $$

&emsp; 又因为 $0 \leq \varphi \leq n\log_2 n$，所以 $\varphi_0 - \varphi_m = O(n\log_2n)$，所以：

$$ T = O(m\log_2n) + O(n\log_2n) = O((n + m)\log_2n) $$

&emsp; 于是双旋 splay 的时间复杂度就是 $O((n + m)\log_2n)$ 了。
