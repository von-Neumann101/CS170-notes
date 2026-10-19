本节的内容也是一种 [[Linear Programming]]
# 单纯形算法补充
原问题：在 $Ax\le b,x\ge 0$ 的条件下 $\max c^Tx$
对偶问题：在 $A^Ty\le c,y\ge 0$ 的条件下 $\min b^Ty$
如果二者任何一个有有界解，那么他们的解是相同的。这称为**强对偶性**

单纯形算法中“可行集中找一个顶点，然后再找其相邻顶点”，有两个问题：一个顶点和相邻顶点。我们要怎么做？

多胞形 $Ax\le b,x\ge 0,x\in\mathbb R^n$ 的顶点意味着选择 $n$ 个不等式，准确地解一个 $n\times n$ 的线性方程组

问题一：
加入一个变量 $t\in[0,1]$，形成一个新的问题：
在 $Ax\le tb,x\ge 0$ 的条件下 $\operatorname*{max}_{(\mathbf x,t)}t$
这实际上是一种齐次化：
$$
\underbrace{\begin{pmatrix}A&-b\end{pmatrix}}_{\text{新的系数矩阵}} \underbrace{\begin{pmatrix}x\\t\end{pmatrix}}_{\text{扩展变量}} \le 0 
$$
当 $t=0$ 时，约束就是 $Ax\le 0$，那么 $x=0, t=0$ 一定是一个可行顶点
当 $t=1$ 时，约束就是 $Ax\le b$，**这是原问题**
所以，我们**只要对这个线性规划问题运行单纯形算法**（因为这个算法是在顶点上移动的），一定能取到 $t_{\text{max}}$（如果这个线性规划的解存在的话），而这时给出的 $\mathbf x$ 就是一个顶点

问题二：
如果两个顶点恰好有一个不等式不同，那么他们相邻
$m$ 个不等式，$n$ 个未知量，那么一共有 $\tbinom{m}{n}$ 个顶点（组合爆炸）

# 对偶性
**Example**：
假设 $A$ 是方阵，最优解 $x=A^{-1}b$，那么 $c^Tx=(c^TA^{-1})b=y^Tb$
我们成功的消去了 $A$，不过实际上 $A$ 不一定是方阵。以[[Linear Programming#^fa8a76|上节的例子举例]]：
$$
\begin{bmatrix}
1 & 0\\
0 & 1\\
1 & 1
\end{bmatrix}
\begin{bmatrix}
x_1\\
x_2
\end{bmatrix}
\le
\begin{bmatrix}
200\\
300\\
460
\end{bmatrix}
\\\\
\begin{bmatrix}
0 & 1\\
1 & 1
\end{bmatrix}
\begin{bmatrix}
x_1\\
x_2
\end{bmatrix}
=
\begin{bmatrix}
360\\
400
\end{bmatrix}
$$
$x=A_v^{-1}b_v$，我们要得到对偶解的答案 $y^Tb=c^Tx=c^TA_vb_v=(c^TA_v)b_v$. 接下来只要明白如何把 $b$ 变成 $b_v$ 就行，$y^T$ 也容易求解了
# Max Flow
## Network Flows in graphs
**Example**：
水流从起点流向终点，途中的边权代表每个路径通过的最大水量：
![[流网络图：三种路径与流量示意.png|513]]

**定义**：给定一个有向图 $G=(V,E)$ 和边的容量 $v(e)\ge 0$，流向量 $f\in\mathbb R^{m\times 1}$ 每条边对应一个值，是其分量。满足 $0\le f(e)\le v(e)$，且流入等于流出（除了源点和汇点）的约束，最大化流 $\text{Val}(f)=\sum_{e(s,\cdot)}f(e)$
这显然是一个线性规划（线性，规划）

先不考虑使用单纯形算法，从一些可能具有结构的算法开始：
- Greedy：
  从 $s$ 开始运行 DFS，如果没有到 $t$ 的路径就结束，否则让 $z$ 为路径上的最小容量，沿着路径发送 $z$ 量的流，并把这条路径的流量减去 $z$
  一共要进行 $m$ 次 DFS（这是因为一条路径最多用 1 次），时间复杂度为 $O((m+n)m)$
  不过这个算法并不能达到最优（上面的图就是一个反例"flow = 20"）
- Ford-Fulkerson：在贪心的基础上进行改进
  整体还是上面的贪心算法，但是只要每次发送的 flow 的大小非 0，那么就反向发一条相同大小的 flow，构建一个新图。
  ![[Pasted image 20261018160534.png|474]]
  由于每次找到一条路径就能使得流量至少增加 $1$ （假设容量为整数），最多有总流量算法停止，所以运行时间为：$O((m+n)U)$，其中 $U$ 是总流量
  而且，如果流量不是整数（是无理数），可能会需要无限多步以收敛
  可以想象到，当边的容量极度不均匀（差异极大），这个算法会倒来倒去
- 把上的算法的 DFS 改成 BFS 使得复杂度变为 $O(m^2n)$ 或 $O(n^2m)$
  
