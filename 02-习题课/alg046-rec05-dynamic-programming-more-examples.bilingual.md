<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 5（复习课 5）：Dynamic Programming: More Examples（动态规划：更多例子）**</span> <span style="color:#7f8c8d;">（Massachusetts Institute of Technology · March 6, 2015 · Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**矩形块（Rectangular blocks）**</span>

给定 $n$ 个矩形三维积木（rectangular three-dimensional blocks）组成的集合，其中积木 $B_i$ 的长度为 $l_i$、宽度为 $w_i$、高度为 $h_i$（均为实数）。利用这些积木的任意子集（any subset），求尽可能高的积木塔的最大高度（maximum height），并使得该塔满足如下约束：

1. 积木不可旋转（Blocks cannot be rotated）：长度始终指东西方向（east-west），宽度始终指南北方向（north-south），高度始终指上下方向（up-down）。
2. 只有当 $l_i \le l_j$ 且 $w_i \le w_j$ 时，积木 $B_i$ 才能叠放在积木 $B_j$ 之上，也就是说，积木 $B_i$ 底面的两个维度都不大于积木 $B_j$ 的对应维度。

<span style="color:#00838f;">**子问题定义（Sub-problem definition）**</span>

首先我们假设积木 $\{1, 2, \ldots, n\}$ 已按长度、再按宽度的非递增顺序（non-increasing order）排列——注意，这一步作为预处理（pre-processing）很容易完成，耗时 $O(n \log n)$。基于这一积木排序，我们定义 $H[i]$ 为以积木 $B_i$ 为塔顶的最高塔的高度（height）。

<span style="color:#00838f;">**递归公式（Recursive formulation）**</span>

我们看到 $H[i]$ 可表示为：

```math
H[i] = h_i + \max_{\substack{j < i \\ l_j \ge l_i,\ w_j \ge w_i}} H[j]
```

若上述最大化过程中不存在任何相容的积木（compatible box），则 max 项等于 0。
这里的基准情形（base case）为 $H[1] = h_1$。
在这个问题中，我们是在塔顶积木（此处即积木 $B_i$）之下的所有合法积木中进行猜测（guessing）。
最终答案即为 $\max_i H[i]$。

<span style="color:#00838f;">**运行时间分析（Runtime analysis）**</span>

这里的子问题总数为 $O(n)$，求解每个子问题所需的总时间为 $O(n)$，这意味着该算法中动态规划部分的运行时间（running time）为 $O(n^2)$。
注意，将积木按长度、再按宽度的非递增顺序排序需要 $O(n \log n)$ 时间，因此该算法的总运行时间为 $O(n \log n + n^2) = O(n^2)$。

> <span style="color:#1e8449;">**[note]** </span> 这个「积木塔」问题与经典的 box stacking（叠箱）问题同构，本质上是一种带约束的最长递增子序列（longest increasing subsequence）式动态规划：将积木按长度、宽度排序之后，$H[i]$ 只依赖于所有满足 $l_j \ge l_i$ 且 $w_j \ge w_i$ 的 $j < i$ 所对应的 $H[j]$。注意题目额外规定积木不可旋转；若允许旋转，则需把每块积木按三种朝向展开成三块候选，再套用同一框架。整个算法分两阶段：$O(n \log n)$ 的排序预处理加上 $O(n^2)$ 的动态规划，后者占主导地位，这也是本讲的第一个例子所展示的「排序 + 线性 DP 子结构」通用套路。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**统计布尔加括号方案数（Counting Boolean Parenthesizations）**</span>

给定一个由符号 True（真）、False（假）、AND（与）、OR（或）和 XOR（异或）组成的字符串形式的布尔表达式，统计对该表达式加括号（parenthesize）使其求值为 True 的方案数。
例如，对 True AND False XOR True 加括号使其求值为 True 的方案共有 2 种。

<span style="color:#00838f;">**子问题定义（Sub-problem definition）**</span>

设 $T[i, j]$ 为对字符串 $S[i : j]$ 加括号、使索引 $i$ 与 $j$ 之间的表达式求值为 True 的方案数，$F[i, j]$ 为对字符串 $S[i : j]$ 加括号、使索引 $i$ 与 $j$ 之间的表达式求值为 False 的方案数。此处索引 $i$ 和 $j$ 均为包含端点（inclusive）。

<span style="color:#00838f;">**递归公式（Recursive formulation）**</span>

于是，给定 $Tot[i, j] = T[i, j] + F[i, j]$，我们看到 $T[i, j]$ 和 $F[i, j]$ 可由如下递归公式表达：

```math
T[i, j] = \sum_{k=i}^{j-1} \begin{cases}
T[i, k] \cdot T[k+1, j] & \text{if } S[k] = \text{AND}\\
Tot[i, k] \cdot Tot[k+1, j] - F[i, k] \cdot F[k+1, j] & \text{if } S[k] = \text{OR}\\
T[i, k] \cdot F[k+1, j] + F[i, k] \cdot T[k+1, j] & \text{if } S[k] = \text{XOR}
\end{cases}
```

```math
F[i, j] = \sum_{k=i}^{j-1} \begin{cases}
Tot[i, k] \cdot Tot[k+1, j] - T[i, k] \cdot T[k+1, j] & \text{if } S[k] = \text{AND}\\
F[i, k] \cdot F[k+1, j] & \text{if } S[k] = \text{OR}\\
T[i, k] \cdot T[k+1, j] + F[i, k] \cdot F[k+1, j] & \text{if } S[k] = \text{XOR}
\end{cases}
```

对介于 1 到 $n$ 之间的所有 $i$，我们有以下基准情形（base cases）：
- 若 $S[i] = \text{True}$，则 $T[i, i] = 1$；若 $S[i] = \text{False}$，则 $T[i, i] = 0$。
- 若 $S[i] = \text{True}$，则 $F[i, i] = 0$；若 $S[i] = \text{False}$，则 $F[i, i] = 1$。

最终答案为 $T[1, n]$（在整段字符串 $S[1 : n]$ 都被使用的条件下，最终值取到 True 的方案总数）。
此处，我们是对 $i$ 与 $j$ 之间子串表达式的最外层括号（outermost parentheses）位置进行猜测。

<span style="color:#00838f;">**运行时间分析（Runtime analysis）**</span>

这里的子问题总数为 $O(n^2)$，求解每个子问题所需的时间为 $O(n)$，因此该算法的总运行时间（runtime complexity）为 $O(n^3)$。

> <span style="color:#1e8449;">**[note]** </span> 该问题是经典的 counting Boolean parenthesizations（统计布尔加括号方案数）DP 题（源自 MIT 6.006 的 DP 练习册，也是算法课上的常见例题），与矩阵链乘法（matrix-chain multiplication）共享同一类「区间 DP（interval DP）」框架：子问题按子串区间 $[i, j]$ 划分，共 $O(n^2)$ 个，每个需枚举分割点 $k$，故总复杂度为 $O(n^3)$。可以验证文中的例子：对 True AND False XOR True 加括号，仅有 (True AND False) XOR True 与 True AND (False XOR True) 两种方式，两者都求值为 True。关键在于必须同时维护 $T$ 与 $F$ 两张表——例如要算 AND 情形下「求值为真」，就需要两侧都为真，而要算 OR 情形下的真值则需要用总数减去两侧都为假，单凭一张真值表无法合并这些信息。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**找零（Make change）**</span>

给定一个数值 $N$，若我们要为 $N$ 美分找零（make change），且面值为 $S = \{S_1, S_2, \ldots, S_m\}$ 的每种硬币都有无限供应（infinite supply），那么凑出总额 $N$ 所需的最少硬币数是多少？为简单起见，假设 $S_1 > S_2 > \ldots > S_m$。

<span style="color:#00838f;">**子问题定义（Sub-problem definition）**</span>

设 $C[p]$ 为使用面值为 $S_1, S_2, \ldots, S_m$ 的硬币为 $p$ 美分找零所需的最少硬币数。

<span style="color:#00838f;">**递归公式（Recursive formulation）**</span>

若 $p > 0$，

```math
C[p] = \min_{i : S_i \le p} \left( C[p - S_i] + 1 \right)
```

基准情形为 $C[0] = 0$。
最终答案为 $C[N]$。
在这个公式中，我们是对最优配置（optimal configuration）中（最后一枚）硬币采用哪种面值 $S_i$ 进行猜测。

<span style="color:#00838f;">**运行时间分析（Runtime analysis）**</span>

这里的子问题总数为 $O(N)$，求解每个子问题所需的总时间为 $O(m)$，因此该算法的总运行时间（runtime complexity）为 $O(mN)$。
注意，该运行时间是伪多项式时间（pseudo-polynomial）（与背包问题（Knapsack）类似）。

> <span style="color:#1e8449;">**[note]** </span> 该问题即 coin change（硬币找零）的「最小硬币数」版本，也可看作完全背包（unbounded knapsack，每种物品不限数量）的变体，因此它同样被归为伪多项式时间（pseudo-polynomial）：$O(mN)$ 关于数值 $N$ 是多项式，但关于输入的规模 $\log N$ 却是指数级，这与 0/1 背包的时间复杂度分析如出一辙。若硬币面值构成 canonical coin system（正则硬币体系，例如美元的 1、5、10、25 美分），贪心法恰好能给出最优解，但对面值任意的集合必须依赖动态规划，这正是本讲把它作为「猜测最后一枚硬币面值」的示范的原因。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**其他问题（Other problems）**</span>

以下问题留给读者自行练习（the following problems are left as exercises for practice）：

<span style="color:#1e8449;">**[exercise]**</span> **Warmup（热身）.** A robot starts from the top left corner $(1, 1)$ of a $M \times N$ grid. The goal of the robot is to reach right bottom $(M, N)$. At each step the robot can make one of the two choices — move one cell right, move one cell bottom. Write a function which takes $M$ and $N$ as arguments and returns the total number of paths the robot can take to reach its destination.

> <span style="color:#1e8449;">**[mathtip]** </span> 提示：从 $(1,1)$ 走到 $(M,N)$ 恰好需要 $(M-1)$ 次向下、$(N-1)$ 次向右，共 $M+N-2$ 步，因此路径总数为组合数 $\binom{M+N-2}{M-1}$；若用动态规划求解，则递推为 $dp[i][j] = dp[i-1][j] + dp[i][j-1]$，基准情形 $dp[1][1] = 1$。

<span style="color:#1e8449;">**[exercise]**</span> **Text neatness（文本整齐排版）.** Consider an input text consisting of $n$ words, each of lengths $l_1, l_2, \ldots, l_n$ characters. We want to print this text as neatly as possible, with the restriction that each line can hold only a maximum of $M$ characters. The goal here is to minimize the sum over all lines except the last, of the cubes of the numbers of extra space characters at the end of each line. The number of extra characters at the end of a line that consists of words $i$ through $j$ is $M - j + i - \sum_{k=i}^{j} l_k$.

> <span style="color:#1e8449;">**[mathtip]** </span> 提示：这是经典的 text justification（文本对齐）问题。可令 $cost(i, j)$ 表示把单词 $i$ 到 $j$ 放在同一行的罚分，即行末多余空格数的立方（最后一行除外），再用前缀和快速计算 $\sum_{k=i}^{j} l_k$ 后做区间型动态规划。

> <span style="color:#1e8449;">**[note]** </span> 该题对应 CLRS 习题 15-4「Printing neatly（整齐打印）」。原文中「额外空格数」的公式在求和符号前印的是「+」，但按定义核对应为「−」：一行容纳单词 $i$ 到 $j$ 时，行内字符总数为 $\sum_{k=i}^{j} l_k + (j - i)$（其中 $j - i$ 是相邻单词之间的空格数），故行末多余空格数 $= M - \left(\sum_{k=i}^{j} l_k + (j-i)\right) = M - j + i - \sum_{k=i}^{j} l_k$。此处按正确的数学形式给出该公式。求解时通常设 $dp[j]$ 为排好前 $j$ 个单词的最小总罚分，对每个可行的行 $i..j$ 进行转移，复杂度为 $O(n^2)$（配合行宽限制 $M$ 可进一步剪枝）。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare（麻省理工学院开放课程） · http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）· Spring 2015（2015 年春季）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
