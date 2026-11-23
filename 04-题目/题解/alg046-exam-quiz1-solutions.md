<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Quiz 1 解答（Quiz 1 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">麻省理工学院（Massachusetts Institute of Technology）· 2015 年 3 月 16 日（March 16, 2015）· Profs. Erik Demaine, Srini Devadas, and Nancy Lynch</span>

考试须知（Instructions）：

- 直到指示你开始之前，不要打开本测验册（quiz booklet）。请先阅读所有说明。
- 本测验包含 7 道大题，每题含多个小题。你有 120 分钟去赢得 120 分。
- 本测验册共 12 页，含本页。
- 本测验为闭卷（closed book）。允许使用一张双面 letter 纸（$8\frac{1}{2} \times 11''$）或 A4 速查表（crib sheet）。不允许使用计算器或可编程设备。手机必须收好。
- 不要把时间浪费在推导我们已经学过的结论上，直接引用课堂上的结果即可。
- 当本测验要求你「给出算法（give an algorithm）」时，用英文或伪代码（pseudocode）描述你的算法，并对正确性（correctness）和运行时间给出简短论证。除非有助于把你的解释讲得更清楚，否则无需提供图示或示例。
- 不要在任一题目上花费过多时间。通常，一题的分值即提示你该在上面花多少分钟。
- 请写出你的过程，因为会给予部分分（partial credit）。评分不仅看答案的正确性，也看你表达的清晰度。请保持整洁。
- 祝好运（Good luck）！

问题目录（Problem Table）：

| 题号 Problem | 标题 Title | 分值 Points | 小题数 Parts | 得分 Grade | 签名 Initials |
|---|---|---|---|---|---|
| 1 | True or False（判断题） | 40 | 10 |  |  |
| 2 | Fast Fourier Transform（快速傅里叶变换） | 5 | 1 |  |  |
| 3 | Yellow Brick Road（黄砖路） | 10 | 1 |  |  |
| 4 | Amortized Analysis（摊还分析） | 15 | 1 |  |  |
| 5 | Verifying Polynomial Multiplication（验证多项式乘法） | 15 | 4 |  |  |
| 6 | Dynamic Programming（动态规划） | 15 | 2 |  |  |
| 7 | Median of Sorted Arrays（有序数组的中位数） | 20 | 3 |  |  |
| Total（合计） |  | 120 |  |  |  |

姓名（Name）:

<!-- ===== PDF p2 ===== -->

**（续）**

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 1. True or False（判断题）**</span> <span style="color:#7f8c8d;">[40 points]（40 分）(10 parts)（10 小题）</span>

对下列每个命题圈出 T 或 F，以表示该命题为真（true）或假（false），并简要说明原因。

**(a)** T F [4 points]（4 分）当所有区间大小相等时，基于最早开始时间（earliest start time）的贪心算法（greedy algorithm）总能选出最大数量的兼容区间（compatible intervals）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 正确（True）。该算法与最早结束时间（earliest finish time）算法等价。

**(b)** T F [4 points]（4 分）加权区间调度（weighted interval scheduling）问题可以用动态规划（dynamic programming）在 $O(n \log n)$ 时间内求解。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 正确（True）。该算法在复习课（recitation）中讲过。

**(c)** T F [4 points]（4 分）如果我们将数组分成每组 3 个元素，找出每组的中位数，递归找出这些中位数的中位数，再划分（partition）并递归，那么我们可以得到线性时间的中位数查找（median-finding）算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。$T(n) = T(n/3) + T(2n/3) + O(n)$ 并不能解出 $T(n) = O(n)$。数组必须分成每组至少 5 个元素，才能得到线性时间算法。

**(d)** T F [4 points]（4 分）如果在分治（divide-and-conquer）凸包（convex-hull）算法中使用显而易见的 $\Theta(n^2)$ 合并算法，总体时间复杂度将是 $\Theta(n^2 \log n)$。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。时间复杂度将满足递推式 $T(n) = 2T(n/2) + \Theta(n^2)$，根据主定理（Master Theorem）解出为 $\Theta(n^2)$。

**(e)** T F [4 points]（4 分）Van Emde Boas 排序（先插入所有数字、找出最小值，然后反复调用 SUCCESSOR（后继））可以用来在 $O(\lg u \cdot \lg \lg \lg u)$ 时间内对 $n = \lg u$ 个数字排序。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。插入树中然后再找出所有后继将花费 $n \lg \lg u$ 时间，换算成 $u$ 即为 $\lg u \cdot \lg \lg u$。

<!-- ===== PDF p3 ===== -->

**（续）**

**(f)** T F [4 points]（4 分）对 0 到 $u - 1$ 之间的 $n$ 个整数，Van Emde Boas 树支持在最坏情况 $O(\lg \lg u)$ 时间内完成后继（successor）查询，且只使用 $O(n)$ 空间。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。我们要么使用 $\Theta(u)$ 空间，要么采用随机化（randomization）。

**(g)** T F [4 points]（4 分）在摊还分析（amortized analysis）的势能法（potential method）中，势能（potential energy）绝不应为负。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 正确（True）。

**(h)** T F [4 points]（4 分）利用线性时间中位数查找使最坏情况运行时间为 $O(n \log n)$ 的快速排序（quicksort）算法需要 $\Theta(n)$ 辅助空间。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。它可以用 $O(\log n)$ 辅助空间实现。

**(i)** T F [4 points]（4 分）在跳表（skip list）中查找以高概率（with high probability）花费 $\Theta(\log n)$ 时间，但也可能以非零概率花费 $\Omega(2^n)$ 时间。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 正确（True）。跳表可能以非零概率具有任意高度，具体取决于它的随机选择。
>
> 另解（Alternative solution）：错误（False）。我们可以把跳表的高度限制为 $O(n)$ 或 $O(\lg n)$，从而获得 $O(n)$ 的最坏情况代价。
>
> 常见错误 1（Common mistake 1）：我们至少遍历每个元素一次。（错误，因为还需要「向上攀爬」跳表。）
>
> 常见错误 2（Common mistake 2）：最坏情况是没有任何元素被提升（promoted），或者所有元素都被提升到同一层，此时跳表退化为链表（linked list）。

**(j)** T F [3 points]（3 分）下列哈希函数族（collection of hash functions）$H = \{h_1, h_2, h_3\}$ 是全域的（universal），其中每个哈希函数按下表把键的全域 $U = \{A, B, C, D\}$ 映射到范围 $\{0, 1, 2\}$：

| $x$ | A | B | C | D |
|---|---|---|---|---|
| $h_1(x)$ | 1 | 0 | 1 | 1 |
| $h_2(x)$ | 0 | 1 | 0 | 1 |
| $h_3(x)$ | 2 | 2 | 1 | 0 |

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 错误（False）。A 和 C 以概率 $2/3$ 碰撞（collide）。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 2. Fast Fourier Transform (FFT)（快速傅里叶变换）**</span> <span style="color:#7f8c8d;">[5 points]（5 分）(1 part)（1 小题）</span>

Ben Bitdiddle 正试图用 FFT 相乘两个多项式。在这个平凡的例子中，Ben 令 $a = (0, 1)$、$b = (0, 1)$，两者都表示 $0 + x$，并计算：

```math
A = F(a) = B = F(b) = (1, -1)
```

```math
C = A \ast B = (1, 1)
```

```math
c = F^{-1}(C) = (1, 0)
```

于是 $c$ 表示 $1 + 0 \cdot x$，这显然是错的。请用一句话指出 Ben 的错误；无需计算。（Ben 发誓他正确地计算了 FFT $F$ 和逆 FFT（inverse FFT）$F^{-1}$。）

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 结果多项式（resulting polynomial）的次数（degree）为 2，所以 Ben 需要用零填充（pad）$a$ 和 $b$。（或者说 Ben 至少需要 3 个采样点（samples）来做 FFT。）
>
> 下面是正确计算（解答中不作要求）。令 $a = b = (0, 1, 0, 0)$，则
>
> ```math
> A = F(a) = B = F(b) = (1, -i, -1, i)
> ```
>
> ```math
> C = A \ast B = (1, -1, 1, -1)
> ```
>
> ```math
> c = F^{-1}(C) = (0, 0, 1, 0)
> ```
>
> 它表示 $x^2$。令 $a = b = (0, 1, 0)$ 也是可以的。
>
> 常见错误 1（Common mistake 1）：$A \ast B$ 应当是卷积（convolution）。
>
> 常见错误 2（Common mistake 2）：Ben 应当反转 $b$。
>
> 这两种错误都混淆了卷积、多项式乘法与 FFT 计算之间的关系。如果直接计算卷积（不用 FFT），需要反转第二个向量。FFT 提供了计算卷积和多项式乘法（这两者是同一回事）的更快方式。使用 FFT 时，应当对两个原始向量做 FFT（无需反转）。然后，在做完 FFT 之后，只需要做逐元素相乘（element-wise multiplication，而非卷积），而这一点 Ben 是做对了的。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 3. Yellow Brick Road（黄砖路）**</span> <span style="color:#7f8c8d;">[10 points]（10 分）(1 part)（1 小题）</span>

Gale 教授正在开发一个名为「Yellow Brick Road（黄砖路）」的新 Facebook 应用，用于维护用户的动态时间线（timeline），这里表示为 $n$ 个（不变的）事件按时间排序的列表 $e_0, e_1, \ldots, e_{n-1}$。（在 Facebook 中事件永远不会被删除，而且就本题而言，也不必担心插入操作。）该应用允许用户把事件 $e_i$ 标记为黄色（yellow，重要）或灰色（grey，不重要）；初始时所有事件都是灰色。该应用还允许用户跳到当前屏幕上的事件 $e_i$（它可能是黄色或灰色）之后的下一个黄色事件。更形式化地说，你必须支持以下操作：

1. MARK-YELLOW(i)（标黄）：把 $e_i$ 标记为黄色。
2. MARK-GREY(i)（标灰）：把 $e_i$ 标记为灰色。
3. NEXT-YELLOW(i)（下一黄色）：找出满足 $e_j$ 为黄色的最小 $j > i$。

针对本题给出你能想到的最快数据结构，按最坏情况时间衡量。你的数据结构越快越好。

提示（Hint）：以你在 6.006 或 6.046 中学过的某个数据结构作为构建块（building block）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 初始化需要 $O(n \lg(\lg n))$ 时间，把所有黄色元素插入一棵 vEB（Van Emde Boas）树 $V$。
>
> 更重要的是，每个操作花费 $O(\lg \lg n)$ 时间。当用户请求 MARK-YELLOW(i) 时，调用 $V.\text{insert}(i)$，花费 $O(\lg \lg n)$ 时间；当用户请求 MARK-GREY(i) 时，调用 $V.\text{delete}(i)$，花费 $O(\lg \lg n)$ 时间；当用户请求 NEXT-YELLOW(i) 时，调用 $V.\text{successor}(i)$，花费 $O(\lg \lg n)$ 时间。
>
> 另一种较慢的解法是用 AVL 树替代 vEB 树，各操作运行时间为 $O(\lg n)$。
>
> 常见错误 1（Common mistake 1）：声称各操作花费 $O(\lg \lg u)$。全域（universe）大小恰为 $n$，而 $u$ 这一项并未定义。
>
> 常见错误 2（Common mistake 2）：把黄色和灰色元素都插入同一数据结构，却没有增加辅助信息（augmentation）来记录子节点内是否存在黄色元素。NEXT-YELLOW 在最坏情况下可能花费 $O(n)$。
>
> 常见错误 3（Common mistake 3）：用跳表记录所有黄色元素。某些操作会以高概率花费 $O(\lg n)$，但最坏情况为 $O(n)$。
>
> 常见错误 4（Common mistake 4）：使用限制为 2 层的跳表、双向链表（doubly linked list）或哈希表（hash table）。某些操作最坏情况为 $O(n)$。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 4. Amortized Analysis（摊还分析）**</span> <span style="color:#7f8c8d;">[15 points]（15 分）(1 part)（1 小题）</span>

设计一个数据结构来维护由 $n$ 个不同整数组成的集合 $S$，支持以下两种操作：

1. INSERT(x, S)（插入）：把整数 $x$ 插入 $S$。
2. REMOVE-BOTTOM-HALF(S)（移除下半部分）：从 $S$ 中移除最小的 $n/2$ 个整数。

描述你的算法，并给出两种操作的最坏情况时间复杂度。然后进行摊还分析（amortized analysis），使 INSERT(x, S) 的摊还时间为 $O(1)$，REMOVE-BOTTOM-HALF(S) 的摊还时间为 0。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 用单向链表（singly linked list）存储这些整数。实现 INSERT(x, S) 时，把新整数追加到链表末尾，这花费 $\Theta(1)$ 时间。实现 REMOVE-BOTTOM-HALF(S) 时，用课上讲过的中位数查找算法（median finding algorithm）找到中位数，然后再次遍历链表，删除所有小于等于该中位数的数字，这花费 $\Theta(n)$ 时间。
>
> 假设 REMOVE-BOTTOM-HALF(S) 的运行时间以 $cn$ 为界，其中 $c$ 为某常数。作摊还分析时，取 $\Phi = 2cn$ 作为我们的势能函数（potential function）。因此，一次插入的摊还代价为 $1 + \Delta\Phi = 1 + 2c = \Theta(1)$。REMOVE-BOTTOM-HALF(S) 的摊还代价为 $cn + \Delta\Phi = cn + (-2c \times \frac{n}{2}) = 0$。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 5. Verifying Polynomial Multiplication（验证多项式乘法）**</span> <span style="color:#7f8c8d;">[15 points]（15 分）(4 parts)（4 小题）</span>

本题将探讨如何检验两个多项式的乘积。具体地，给定三个多项式：

```math
p(x) = a_n x^n + a_{n-1} x^{n-1} + \cdots + a_0
```

```math
q(x) = b_n x^n + b_{n-1} x^{n-1} + \cdots + b_0
```

```math
r(x) = c_{2n} x^{2n} + c_{2n-1} x^{2n-1} + \cdots + c_0
```

我们要检验是否对一切 $x$ 都有 $p(x) \cdot q(x) = r(x)$。通过 FFT，我们可以直接计算 $p(x) \cdot q(x)$ 并在 $O(n \log n)$ 时间内检验。而我们这里的目标是借助随机化（randomization）达到 $O(n)$ 时间。

**(a)** [5 points]（5 分）设计一个 $O(n)$ 时间的随机化算法，用于检验是否 $p(x) \cdot q(x) = r(x)$，且满足以下性质：

1. 若两边相等，算法输出 YES（是）。
2. 若两边不相等，算法以至少 $1/2$ 的概率输出 NO（否）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 随机选取一个值 $a \in [1, 4n]$，检查是否 $p(a)q(a) = r(a)$。若两边相等，算法输出 YES，否则输出 NO。对这三个次数为 $O(n)$ 的多项式求值需要 $O(n)$ 时间。因此算法总运行时间为 $O(n)$。

**(b)** [2 points]（2 分）证明你的算法满足性质 1。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 若 $p(x) \cdot q(x) = r(x)$，则对任意输入，两边都会求值得到相同的结果。


<!-- ===== PDF p8 ===== -->

**（续）**

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 5. Verifying Polynomial Multiplication（验证多项式乘法）**</span> <span style="color:#7f8c8d;">[15 points]（15 分）(4 parts)（4 小题）· 接 p7 页</span>

**(c)** [3 points]（3 分）证明你的算法满足性质 2（Property 2）。

提示（Hint）：回忆代数基本定理（Fundamental Theorem of Algebra）：一个 $d$ 次多项式（至多）有 $d$ 个根。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $s(x) = r(x) - p(x) \cdot q(x)$ 是一个 $2n$ 次多项式，因此至多有 $2n$ 个根。于是
>
> ```math
> \Pr\{s(a) = 0\} \le \frac{2n}{4n} = \frac{1}{2}
> ```
>
> 因为 $a$ 是从大小为 $4n$ 的集合中随机选取的。

**(d)** [5 points]（5 分）设计一个随机化算法（randomized algorithm）来检验是否 $p(x) \cdot q(x) = r(x)$，要求其正确概率至少为 $1 - \varepsilon$。用 $n$ 和 $1/\varepsilon$ 分析你的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 我们把 (a) 部分的算法运行 $m$ 次，当且仅当（if and only if）所有答案都输出 YES 时输出 YES。换句话说，我们通过重复来放大（amplify）成功概率。
>
> 我们的检验以至少 $1 - \left(\frac{1}{2}\right)^m$ 的概率正确工作。因此我们需要
>
> ```math
> \left(\frac{1}{2}\right)^m \le \varepsilon \quad \Rightarrow \quad m \ge \lg \frac{1}{\varepsilon}
> ```

---

<!-- ===== PDF p9 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 6. Dynamic Programming（动态规划）**</span> <span style="color:#7f8c8d;">[15 points]（15 分）(2 parts)（2 小题）</span>

Child 教授（Prof. Child）要用自家菜园里的食材做饭，菜园是一个有 $n$ 行 $m$ 列的网格（grid）。每个格子 $(i, j)$（$1 \le i \le n$，$1 \le j \le m$）里长着一种食材，其美味值（tastiness）为正数 $T_{i,j}$。Child 教授不喜欢「照本宣科（by the book）」地烹饪。为了准备晚餐，她会站在某个格子 $(i, j)$ 处，从相对于该格子的每一个象限中各取一种食材。她菜肴的美味度是她所选的四种食材美味值的乘积。请帮 Child 教授找出一个 $O(nm)$ 的动态规划算法，以最大化她菜肴的美味度。

这里相对于格子 $(i, j)$ 的四个象限定义如下：

1. top-left（左上象限）= 所有满足 $a < i$、$b < j$ 的格子 $(a, b)$ 的集合；
2. bottom-left（左下象限）= 所有满足 $a > i$、$b < j$ 的格子 $(a, b)$ 的集合；
3. top-right（右上象限）= 所有满足 $a < i$、$b > j$ 的格子 $(a, b)$ 的集合；
4. bottom-right（右下象限）= 所有满足 $a > i$、$b > j$ 的格子 $(a, b)$ 的集合。

由于 Child 教授需要四个象限都非空（non-empty），她只能站在满足 $1 < i < n$ 且 $1 < j < m$ 的格子 $(i, j)$ 上。

**(a)** [10 points]（10 分）定义 $TL_{i,j}$ 为格子 $(i, j)$ 左上象限中的最大美味值：$TL_{i,j} = \max\{T_{a,b} \mid 1 \le a \le i,\ 1 \le b \le j\}$。请找出一个动态规划算法，在 $O(nm)$ 时间内计算所有 $1 < i < n$、$1 < j < m$ 的 $TL_{i,j}$。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 在尝试计算 $TL_{i,j}$ 时，我们看到最大值可能就在格子 $(i, j)$ 处。如果不是，它必然位于从 $(1, 1)$ 到 $(i, j-1)$ 的矩形中，或从 $(1, 1)$ 到 $(i-1, j)$ 的矩形中，或同时位于两者之中。这三种重叠的情形覆盖了我们所需的矩形。于是我们有：
>
> ```math
> TL_{i,j} = \max\{T_{i,j},\ TL_{i-1,j},\ TL_{i,j-1}\}
> ```
>
> 对于基本情况（base cases），我们可以对所有有效的 $i$ 和 $j$ 直接设 $TL_{0,j} = TL_{i,0} = 0$。每个状态的 DP 值可在 $O(1)$ 时间内算出。共有 $nm$ 个状态，所以我们的算法为 $O(nm)$。

<!-- ===== PDF p10 ===== -->

**（续）**

**(b)** [5 points]（5 分）利用 (a) 部分的想法，得到一个 $O(nm)$ 算法来找出最美味的一道菜。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 在 (a) 部分我们计算了左上象限的区间最大值（range maximum）。我们可以类似地定义其他三个象限的区间最大值。设
>
> ```math
> BL_{i,j} = \max\{T_{a,b} \mid i \le a \le n,\ 1 \le b \le j\}
> ```
>
> ```math
> TR_{i,j} = \max\{T_{a,b} \mid 1 \le a \le i,\ j \le b \le m\}
> ```
>
> ```math
> BR_{i,j} = \max\{T_{a,b} \mid i \le a \le n,\ j \le b \le m\}
> ```
>
> 其中每一个都可以用与 $TL$ 类似的方法在 $O(nm)$ 时间内计算。
>
> 要计算 Child 教授站在格子 $(i, j)$（$1 < i < n$ 且 $1 < j < m$）时能做出的最美味菜肴，我们现在只需要计算乘积 $TL_{i-1,j-1} \cdot BL_{i+1,j-1} \cdot TR_{i-1,j+1} \cdot BR_{i+1,j+1}$，并选出最大乘积。这可以在 $O(nm)$ 时间内完成。

---

<!-- ===== PDF p11 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 7. Median of two sorted arrays（两个有序数组的中位数）**</span> <span style="color:#7f8c8d;">[20 points]（20 分）(3 parts)（3 小题）</span>

找到有序数组（sorted array）的中位数很容易：返回中间元素即可。但如果你给定两个有序数组 $A$ 和 $B$，大小分别为 $m$ 和 $n$，并且想找出 $A$ 和 $B$ 中所有数字的中位数，那该怎么办呢？你可以假设 $A$ 和 $B$ 不相交（disjoint）。

**(a)** [3 points]（3 分）给出一个运行时间为 $\Theta(m + n)$ 的朴素（naïve）算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 合并（merge）这两个有序数组（花费 $O(m + n)$ 时间），然后用线性时间选择（linear-time selection）找出中位数。

**(b)** [10 points]（10 分）若 $m = n$，给出一个运行时间为 $\Theta(\lg n)$ 的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 选取 $A$ 的中位数 $m_1$ 和 $B$ 的中位数 $m_2$。若 $m_1 = m_2$，返回 $m_1$。若 $m_1 > m_2$，移除 $A$ 的后半部分和 $B$ 的前半部分。于是我们得到两个大小为 $n/2$ 的子数组。重复上述过程，直到两个数组都小于某个常数。$m_1 < m_2$ 的情形是对称的（symmetric）。

<!-- ===== PDF p12 ===== -->

**（续）**

**(c)** [7 points]（7 分）对任意 $m$ 和 $n$，给出一个运行时间为 $O(\lg(\min\{m, n\}))$ 的算法。别在这道题上花太多时间！

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 不失一般性（without loss of generality），假设 $|A| = m > n = |B|$。我们可以安全地移除元素 $A[0 : \frac{m-n}{2}]$ 和 $A[\frac{m+n}{2} : m - 1]$，因为这些元素中任何一个都不可能是 $A + B$ 的中位数。经过这一过程后，我们得到两个大小约为 $n$ 的数组。然后我们可以运行 (b) 部分的算法。复杂度为 $\Theta(\lg(\min\{m, n\}))$。

<!-- ===== PDF p13 ===== -->

<span style="color:#7f8c8d;">MIT 开放课程（MIT OpenCourseWare）· http://ocw.mit.edu · 6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）· 2015 春季学期（Spring 2015）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms</span>
