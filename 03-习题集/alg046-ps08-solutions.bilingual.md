<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 8 解答（Problem Set 8 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">Massachusetts Institute of Technology（麻省理工学院） · 2015 年 4 月 29 日 · Profs. Erik Demaine, Srini Devadas, and Nancy Lynch</span>

<span style="color:#7f8c8d;">本习题集原定截止时间：2015 年 4 月 24 日（星期五）晚上 11:59。</span>

---

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-1.**</span> 阅读 CLRS 第 29 章（Chapter 29）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-2.**</span> 练习 29.2-2（Exercise 29.2-2）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-3.**</span> 练习 29.2-4（Exercise 29.2-4）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-4.**</span> 阅读 CLRS 第 34 章（Chapter 34）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-5.**</span> 练习 34.2-8（Exercise 34.2-8）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 8-6.**</span> 练习 34.3-5（Exercise 34.3-5）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 8-1. A Simple Simplex Example（一个简单的单纯形示例）**</span> <span style="color:#7f8c8d;">[25 分]</span>

考虑一个由两个变量 $x_1$ 和 $x_2$ 组成的 linear program（线性规划，LP），它们满足以下三个约束条件（constraints）：

```math
\begin{aligned}
x_1 + x_2 &\le 10 \\
x_2 &\ge 4x_1 - 20 \\
x_1 + 3x_2 &\le 24 \\
x_1, x_2 &\ge 0
\end{aligned}
```

目标是最大化 objective function（目标函数）$p = 4x_1 + x_2$ 的值。

**(a)** <span style="color:#7f8c8d;">[5 分]</span> 画出可行域（feasible region）的示意图。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 参见图 1（Figure 1）。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#7f8c8d;">**图 1（Figure 1）：** 该 LP 问题的可行域（feasible region）。图片致谢 Finite mathematics & Applied calculus（http://www.zweigmedia.com）。</span>

**(b)** <span style="color:#7f8c8d;">[5 分]</span> 把给定的 LP 写成标准形（standard form），并把该标准形表示转换为松弛形（slack form）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**
>
> **标准形（Standard form）：** 最大化 $p = 4x_1 + x_2$，约束条件为：
>
> ```math
> \begin{aligned}
> x_1 + x_2 &\le 10 \\
> 4x_1 - x_2 &\le 20 \\
> x_1 + 3x_2 &\le 24 \\
> x_1, x_2 &\ge 0
> \end{aligned}
> ```
>
> **松弛形（Slack form）：** 引入三个新变量 $x_3, x_4, x_5$。
>
> 最大化 $p = 4x_1 + x_2$，约束条件为：
>
> ```math
> \begin{aligned}
> x_3 &= 10 - x_1 - x_2 \\
> x_4 &= 20 - 4x_1 + x_2 \\
> x_5 &= 24 - x_1 - 3x_2 \\
> x_1, \ldots, x_5 &\ge 0
> \end{aligned}
> ```

**(c)** <span style="color:#7f8c8d;">[10 分]</span> 用 Simplex（单纯形法）求解所得的松弛形 LP。指出你选择的 pivots（枢轴/主元），并给出由此得到的修改后的 LP 以及相继的可行解（successive feasible solutions）。在你的（a）部分示意图上标出这些相继的解。

---

<!-- ===== PDF p3 ===== -->

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 从 $x_1 = x_2 = 0$、$x_3 = 10$、$x_4 = 20$、$x_5 = 24$ 出发，即解 $(0, 0, 10, 20, 24)$，此时 objective function（目标函数）值为 $p = 0$。
>
> 我们为 pivoting（选主元）选择的第一个 nonbasic variable（非基本变量）可以是 $x_1$ 或 $x_2$，因为两者在目标函数中的系数都是正的。为具体起见，我们选择 $x_2$。随着 $x_2$ 增大，$x_3$ 和 $x_5$ 的值会减小。起限制作用（limiting）的约束是 $x_5$ 对应的那个：我们只能把 $x_2$ 增大到 8，因为再多就会使 $x_5$ 变为负值。我们把 $x_2$ 与 $x_5$ 互换：对第三个约束解出 $x_2$，得到 $x_2 = 8 - \frac{1}{3}x_1 - \frac{1}{3}x_5$。然后代入，得到如下新的 LP：
>
> 最大化 $p = 8 + \frac{11}{3}x_1 - \frac{1}{3}x_5$，约束条件为：
>
> ```math
> \begin{aligned}
> x_3 &= 2 - \frac{2}{3}x_1 + \frac{1}{3}x_5 \\
> x_4 &= 28 - \frac{13}{3}x_1 - \frac{1}{3}x_5 \\
> x_2 &= 8 - \frac{1}{3}x_1 - \frac{1}{3}x_5 \\
> x_1, \ldots, x_5 &\ge 0
> \end{aligned}
> ```
>
> 把 non-basic variables（非基本变量）$x_1$ 和 $x_5$ 置为 0，并计算其余变量，得到一个新的解 $(0, 8, 2, 28, 0)$。此时目标函数的值为 $p = 8$。
>
> 现在唯一可选的 pivot（主元）是 $x_1$，因为 $x_5$ 在目标函数中的系数为负。随着 $x_1$ 增大，$x_2, x_3, x_4$ 的值都会减小。起限制作用的约束是 $x_3$ 对应的那个；我们只能把 $x_1$ 增大到 3。我们把 $x_1$ 与 $x_3$ 互换，对第一个约束解出 $x_1$，得到 $x_1 = 3 - \frac{2}{3}x_3 + \frac{1}{2}x_5$。我们得到如下新的 LP：
>
> 最大化 $p = 19 - \frac{11}{2}x_3 + \frac{3}{2}x_5$，约束条件为：
>
> ```math
> \begin{aligned}
> x_1 &= 3 - \frac{3}{2}x_3 + \frac{1}{2}x_5 \\
> x_4 &= 15 + \frac{13}{2}x_3 - \frac{5}{2}x_5 \\
> x_2 &= 7 + \frac{1}{2}x_3 - \frac{1}{2}x_5 \\
> x_1, \ldots, x_5 &\ge 0
> \end{aligned}
> ```
>
> 把 $x_3$ 和 $x_5$ 置为 0，得到一个新的解 $(3, 7, 0, 15, 0)$。此时目标函数的值为 $p = 19$。
>
> 接下来，我们在 $x_5$ 上选主元。增大 $x_5$ 会使 $x_4$ 和 $x_2$ 减小，其中起限制作用的约束是 $x_4$ 对应的那个。我们把 $x_5$ 与 $x_4$ 互换，得到 $x_5 = 6 + \frac{13}{5}x_3 - \frac{2}{5}x_4$。我们得到如下新的 LP：

> <span style="color:#1e8449;">**[note]** </span> 原 PDF 此处将新解误作 $(3, 7, 0, 14, 0)$，已更正为 $(3, 7, 0, 15, 0)$：由松弛变量定义 $x_4 = 20 - 4x_1 + x_2 = 20 - 12 + 7 = 15$，且上述松弛形 LP 中 $x_4 = 15 + \frac{13}{2}x_3 - \frac{5}{2}x_5$ 在 $x_3 = x_5 = 0$ 时取值为 15（不影响后续迭代结果）。

---

<!-- ===== PDF p4 ===== -->

**（续）**

> 最大化 $p = 28 - \frac{8}{5}x_3 - \frac{3}{5}x_4$，约束条件为：
>
> ```math
> \begin{aligned}
> x_1 &= 6 - \frac{1}{5}x_3 - \frac{1}{5}x_4 \\
> x_5 &= 6 + \frac{13}{5}x_3 - \frac{2}{5}x_4 \\
> x_2 &= 4 - \frac{4}{5}x_3 + \frac{1}{5}x_4 \\
> x_1, \ldots, x_5 &\ge 0
> \end{aligned}
> ```
>
> 把 $x_3$ 和 $x_4$ 置为 0，得到一个新的解 $(6, 4, 0, 0, 6)$，此时目标函数值为 28。到这一步，已不可能再进行任何选主元操作（它们对应的系数在目标函数中均为负）。因此，最优解（optimal solution）为 $x_1 = 6$、$x_2 = 4$。
>
> 在上述过程中，我们从基本解（basic solution）$(0, 0)$ 出发（即 $x_1 = 0$、$x_2 = 0$），逐步改进我们的估计，经过 $(0, 8)$、$(3, 7)$，最终到达最终解 $(6, 4)$。在图 1 中，这对应于从原点出发沿顺时针方向（clockwise direction）遍历白色区域的四个角（corners）。

**(d)** <span style="color:#7f8c8d;">[5 分]</span> 给出（b）部分标准形 LP 的对偶 LP（dual LP），并给出它的最优值（optimal value）。（提示：利用（c）部分的解答。）

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**
>
> （b）部分的标准形 LP 为：最大化 $p = 4x_1 + x_2$，约束条件为：
>
> ```math
> \begin{aligned}
> x_1 + x_2 &\le 10 \\
> 4x_1 - x_2 &\le 20 \\
> x_1 + 3x_2 &\le 24 \\
> x_1, x_2 &\ge 0
> \end{aligned}
> ```
>
> 如 CLRS 第 880 页所述，对偶 LP 使用新变量 $y_1, y_2, y_3$。该 LP 为：最小化 $10y_1 + 20y_2 + 24y_3$，约束条件为：
>
> ```math
> \begin{aligned}
> y_1 + 4y_2 + y_3 &\ge 4 \\
> y_1 - y_2 + 3y_3 &\ge 1 \\
> y_1, y_2, y_3 &\ge 0
> \end{aligned}
> ```
>
> 由 LP duality（LP 对偶性，定理 29.10，Theorem 29.10），该 LP 的最小值为 28。该值在 $y_1 = \frac{8}{5}$、$y_2 = \frac{3}{5}$、$y_3 = 0$ 处取得。你可以手工求出这个解，也可以通过考察（c）部分解答中最终的松弛形 LP，并利用第 882 页的公式 (29.91) 得到它。

> <span style="color:#1e8449;">**[note]** </span> 原 PDF 将最优对偶解误作 $y_1 = \frac{5}{8}$、$y_2 = \frac{5}{3}$、$y_3 = 0$（代入对偶目标得 $10 \cdot \frac{5}{8} + 20 \cdot \frac{5}{3} = \frac{475}{12} \ne 28$，不满足最优性），已更正为 $y_1 = \frac{8}{5}$、$y_2 = \frac{3}{5}$、$y_3 = 0$（由互补松弛性（complementary slackness）解得，此时 $10y_1 + 20y_2 = 16 + 12 = 28$，与对偶定理一致）。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 8-2. NP-Completeness（NP 完全性）**</span> <span style="color:#7f8c8d;">[25 分]</span>

在本问题中，你将证明几个 decision problems（判定问题）的 NP-completeness（NP 完全性）。为证明 NP-hardness（NP 难度），你可以从课堂上或 CLRS 中已被证明是 NP-complete（NP 完全）的任何问题出发进行归约（reduce）。

**(a)** <span style="color:#7f8c8d;">[5 分]</span>

令 TRIPLE-SAT 表示如下 decision problem（判定问题）：给定一个 Boolean formula（布尔公式）$\varphi$，判定 $\varphi$ 是否至少有三个互不相同的 satisfying assignments（满足赋值）。证明 TRIPLE-SAT 是 NP-complete（NP 完全）的。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 为说明 TRIPLE-SAT 属于 NP（in NP），对任意输入公式 $\varphi$，我们只需猜测三个互不相同的赋值，并验证它们是否满足 $\varphi$。
>
> 为说明 TRIPLE-SAT 是 NP-hard（NP 难）的，我们把 SAT 归约到它。令 $\varphi$ 表示某个 SAT 问题的输入布尔公式，并设 $\varphi$ 中的变量集合为 $X = \{x_1, \ldots, x_n\}$。我们按如下方式构造一个关于新变量集合 $X'$ 的布尔公式 $\varphi'$ 的 TRIPLE-SAT 问题：
>
> - $X' = \{x_1, \ldots, x_n, y, z\}$。
> - $\varphi' = \varphi$。
>
> 现在我们断言：$\varphi$ 是可满足的（satisfiable），当且仅当 $\varphi'$ 至少有 3 个满足赋值。若 $\varphi$ 可满足，则我们可以为任意一个特定的赋值补上 $\{y, z\}$ 的 4 种可能取值组合中的任意一种，从而总共给出至少 4 个满足赋值。另一方面，若 $\varphi$ 不可满足，则 $\varphi'$ 也不可满足。

**(b)** <span style="color:#7f8c8d;">[10 分]</span> 在 Problem Set 1（习题集 1）中，我们考虑过如何在某个街道网络的部分顶点处开设甜甜圈店（donut shops），该网络被建模为任意的无向图（undirected graph）$G = (V, E)$。每个顶点 $u$ 有一个非负整数值 $p(u)$，它描述在 $u$ 处开设一家店所能获得的潜在利润（potential profit）。两家店不能设在相邻的顶点。当时的问题是设计一个算法，输出一个子集 $U \subseteq V$，使总利润 $\sum_{u \in U} p(u)$ 最大化。毫无疑问，你找到的算法的时间复杂度在图参数上是指数级的（exponential）。现在我们来看看这是为什么。

定义 DONUT 为如下 decision problem（判定问题）：给定一个无向图 $G = (V, E)$，给定一个从顶点 $u \in V$ 到非负整数利润 $p(u)$ 的映射 $p$，并给定一个非负整数 $k$，判定是否存在一个子集 $U \subseteq V$，使得 $U$ 中任意两个顶点在 $G$ 中都不是邻居（neighbors），并且 $\sum_{u \in U} p(u) \ge k$。证明 DONUT 是 NP-hard（NP 难）的。（提示：尝试从 3SAT 归约。）

另外，解释为什么这意味着：如果存在一个多项式时间算法（polynomial-time algorithm）来求解原问题，即输出一个使总利润最大化的子集 $U$，那么 $P = NP$。


<!-- ===== PDF p6 ===== -->

**（续）**

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）**：（承接上页 Problem 8-2 (b) 的题目——证明 DONUT（甜甜圈店选址）判定问题是 NP-hard 的，并解释其对 P = NP 的蕴含关系）
>
> 设 $\varphi = C_1 \wedge C_2 \wedge \cdots \wedge C_m$ 为某个 3SAT 问题的输入公式，其中每个 clause（子句）$C_c$ 含有从 $\{x_i, \bar{x}_i \mid 1 \le i \le n\}$ 中选出的三个 literal（字面量）。我们如下构造一个 DONUT 问题 $(G, p, k)$。
>
> $G$ 的顶点集 $V$ 为 $\{v_{c,j} \mid 1 \le c \le m,\ 1 \le j \le 3\}$，其中 $v_{c,j}$ 对应子句 $C_c$ 中的第 $j$ 个字面量。我们给每个顶点 $v_{c,j}$ 贴上标签 $x_i$ 或 $\bar{x}_i$，具体取子句 $C_c$ 第 $j$ 个位置上出现的那个。$G$ 的边集 $E$ 有两类：
>
> - 对每个子句 $C_c$，在其各字面量所对应的每对顶点之间连一条边，即对 $j_1 \ne j_2$，在 $v_{c,j_1}$ 与 $v_{c,j_2}$ 之间连边。
> - 对每个 $i$，在每一对「一个标为 $x_i$、另一个标为 $\bar{x}_i$」的顶点之间连一条边。
>
> 函数 $p$ 把所有顶点都映到 1。阈值（threshold）$k$ 等于 $m$。我们断言：$\varphi$ 是可满足的（satisfiable），当且仅当 DONUT 问题 $(G, p, k)$ 的总利润（total profit）可以达到至少 $k$。
>
> 首先，假设 $\varphi$ 是可满足的。那么存在某个把变量映射到 $\{\text{true}, \text{false}\}$ 的真值赋值（truth assignment）$A$。$A$ 必须使每个子句中至少一个字面量为真；对每个子句，选择与这样一个字面量对应的顶点加入集合 $U$。由于共有 $m$ 个子句，这恰好得到 $m = k$ 个顶点，所以总利润为 $k$。此外，我们断言 $U$ 不可能包含 $G$ 中两个相邻的顶点（neighboring vertices）。用反证法，假设 $u, v \in U$ 且 $(u, v) \in E$。那么边 $(u, v)$ 必属于上述两类边之一。但 $u$ 与 $v$ 不可能对应同一子句中的字面量，因为每个子句我们只选择一个顶点。同时 $u$ 与 $v$ 也不可能对同一 $i$ 分别标为 $x_i$ 和 $\bar{x}_i$，因为 $A$ 不可能同时使一个变量及其否定（negation）为真。由于这两种可能都无法成立，$U$ 不可能包含两个相邻顶点。于是 $U$ 对 DONUT 问题 $(G, p, k)$ 取得了 $k$ 的总利润。
>
> 反过来，假设存在 $U \subseteq V$ 满足 $|U| \ge k = m$，且 $U$ 中不含 $G$ 中的任意一对相邻点。由于 $U$ 不含相邻点，它不可能包含来自同一子句的两个顶点。因此必有 $|U| = m$，即每个子句恰好选一个顶点。现在为变量定义一个真值赋值 $A$：若某个标为 $x_i$ 的顶点在 $U$ 中，则 $A(x_i) = \text{true}$；若某个标为 $\bar{x}_i$ 的顶点在 $U$ 中，则 $A(x_i) = \text{false}$。对其余变量，真值可以任意指定。又因为 $U$ 不含相邻点，$U$ 不可能包含两个标签互相矛盾的顶点，所以赋值 $A$ 是良定义的（well-defined）。$A$ 在每个子句中通过把对应于 $U$ 中某顶点的那个字面量置为真，从而使所有子句都被满足。因此，$A$ 满足 $\varphi$。
>
> 对于最后一问，假设存在一个多项式时间算法（polynomial-time algorithm）求解原问题，即输出使总利润最大化的子集 $U$。那么这个算法可以轻松改造成 DONUT 的多项式时间算法：对任意 $(G, p, k)$，只需运行所假设的算法并得到最优子集 $U$，然后若 $k \le |U|$ 则输出 true，否则输出 false。由于我们已经证明 DONUT 是 NP-hard 的，这就意味着 P = NP。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**(c)**</span> <span style="color:#7f8c8d;">[10 points]（10 分）</span> 假设我们有一台机器（machine）和一组任务（tasks）$a_1, a_2, \ldots, a_n$。每个任务 $a_j$ 在机器上需要 $t_j$ 单位的处理时间，产生利润 $p_j$，并且有一个截止时间（deadline）$d_j$。这里 $t_j$、$p_j$ 和 $d_j$ 均为非负整数（nonnegative integers）。机器一次只能处理一个任务。并非所有任务都必须运行，但一旦某个任务开始运行，它必须不被中断地运行，并且必须在截止时间前完成。

对某个任务子集的调度（schedule）描述了该子集中每个任务何时开始运行。调度必须遵守上述约束。调度的利润是该调度中各任务 $a_j$ 的所有 $p_j$ 值之和。

该问题是要为某个任务子集产生一个能返回最大可能利润的调度。把此问题表述为一个判定问题（decision problem），并证明它是 NP-complete 的（NP-complete）。在证明时，你可以从课堂上或 CLRS 中已证明为 NP-complete 的任意问题归约（reduce）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 定义 SCHED 为如下判定问题：给定 $(T, P, D, k)$，其中 $T$、$P$、$D$ 分别是序列 $\{t_j\}$、$\{p_j\}$ 和 $\{d_j\}$，每个长度为 $n$，判定是否存在子集 $I \subseteq \{1, \ldots, n\}$ 以及 $I$ 的一个排序（ordering）$i_1, i_2, \ldots, i_m$，使得：
>
> 1. 对每个 $j$，$\sum_{l=1}^{j} T(i_l) \le D(i_j)$。也就是说，前 $j$ 个被执行任务的运行时间之和不超过第 $j$ 个任务的截止时间。这意味着按给定顺序运行时，所有任务都能赶上截止时间。
>
> 2. $\sum_{l=1}^{m} P(i_l) \ge k$。也就是说，总利润至少为 $k$。
>
> 为说明 SCHED 属于 NP（in NP），给定实例 $(T, P, D, k)$，我们可以猜测一个任务子集和一组开始时间（start times），并验证它满足调度（schedule）的所有约束，且能在给定时间 $k$ 内完成。
>
> 为说明 SCHED 是 NP-hard 的，我们可以从 CLRS 第 1097 页定义的 SUBSET-SUM（子集和问题）归约。给定 SUBSET-SUM 的一个实例 $(S, t)$，其中 $|S| = n$，把 $S$ 的元素任意排序为 $s_1, \ldots, s_n$。我们如下构造 SCHED 的一个实例 $(T, P, D, k)$：令 $T = P = \{s_1, \ldots, s_n\}$（按此顺序），令 $D$ 为长度为 $n$、每个位置都等于 $t$ 的序列，并令 $k = t$。我们断言：SUBSET-SUM 问题 $(S, t)$ 的答案是「是」（"yes"），当且仅当 SCHED 问题 $(T, P, D, k)$ 的答案是「是」。
>
> 首先，假设 SUBSET-SUM 问题 $(S, t)$ 的答案是「是」。那么存在 $S$ 的一个子集 $S'$，其元素之和恰好为 $t$。令 $I$ 为 $S'$ 的各元素在序列 $s_1, \ldots, s_n$ 中的下标集合，并将 $I$ 按递增顺序（increasing order）排列为 $i_1, i_2, \ldots, i_m$。于是所有任务的运行时间之和恰好为 $t$，即 $\sum_{l=1}^{m} T(i_l) = t$。这意味着所有任务都能赶上各自的截止时间。此外，总利润恰好为 $\sum_{l=1}^{m} P(i_l) = t = k$。因此，SCHED 问题 $(T, P, D, k)$ 的答案是「是」。
>
> 反过来，假设 SCHED 问题 $(T, P, D, k)$ 的答案是「是」。那么存在 $\{1, \ldots, n\}$ 的子集 $I$ 以及 $I$ 的一个排序 $i_1, i_2, \ldots, i_m$，使得：

<!-- ===== PDF p8 ===== -->

**（续）**

> 1. 对每个 $j$，$\sum_{l=1}^{j} T(i_l) \le D(i_j)$。
>
> 2. $\sum_{l=1}^{m} P(i_l) \ge k$。
>
> 由于所有截止时间都等于 $k = t$，这等价于说：
>
> 1. $\sum_{l=1}^{m} T(i_l) \le t$。
>
> 2. $\sum_{l=1}^{m} P(i_l) \ge t$。
>
> 因为每个 $T(i_j) = P(i_j)$，这说明 $\sum_{l=1}^{m} T(i_l) = \sum_{l=1}^{m} P(i_l) = t$。现在令 $S'$ 为 $S$ 中与 $I$ 内下标相对应的子集，即 $S' = \{s_i \mid i \in I\}$。那么 $S'$ 的元素之和恰好为 $t$。因此，SUBSET-SUM 问题 $(S, t)$ 的答案是「是」。

---

<!-- ===== PDF p9 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**　http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**　Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">如需了解如何引用这些材料或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
