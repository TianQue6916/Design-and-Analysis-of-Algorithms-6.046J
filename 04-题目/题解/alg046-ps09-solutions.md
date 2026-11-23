<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 9 解答（Problem Set 9 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">Massachusetts Institute of Technology（麻省理工学院）<br>Profs. Erik Demaine、Srini Devadas、Nancy Lynch<br>2015 年 5 月 4 日（May 4, 2015）</span>

本习题集于 2015 年 4 月 30 日（周四）晚上 11:59 截止（This problem set is due at 11:59pm on Thursday, April 30, 2015）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 9-1.**</span> 阅读 CLRS 第 35 章（Read CLRS, Chapter 35）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 9-2.**</span> 完成习题 35.2-3（Exercise 35.2-3）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 9-3.**</span> 完成习题 35.4-2（Exercise 35.4-2）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 9-4.**</span> 阅读 Demaine 教授关于固定参数算法（fixed-parameter algorithms）的讲义（Read Prof. Demaine's notes on fixed-parameter algorithms）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 9-1. Knapsack（背包问题）[25 分]**</span>

在 Knapsack 问题（背包问题）中，我们给定一个物品集合（set of items）$A = \{a_1, \ldots, a_n\}$，其中每个 $a_i$ 具有给定的正整数尺寸（size）$s_i$ 和给定的正整数价值（value）$v_i$。我们还给定一个正整数背包容量（knapsack capacity）$B$。假设对每个 $i$ 都有 $s_i \le B$。问题是要找出 $A$ 的一个子集（subset），其总尺寸至多为 $B$，并且总价值最大化。

在本问题中，我们将考虑用于求解 Knapsack 问题的近似算法（approximation algorithms）。

记号（Notation）: 对 $A$ 的任意子集 $S$，我们用 $s_S$ 表示 $S$ 中所有尺寸的总和，用 $v_S$ 表示 $S$ 中所有价值的总和。令 Opt 表示该问题的一个最优解（optimal solution）。

**(a) [5 分]** 考虑如下用于求解 Knapsack 问题的贪心算法（greedy algorithm）Alg1：

按所有物品 $a_i$ 的密度（density）的非递增顺序（non-increasing order）排序，其中密度是价值与尺寸之比（ratio of value to size）$v_i / s_i$。对列表做单趟扫描（single pass），从最高密度到最低密度。对遇到的每个物品，若它仍然放得下（fits），则将其包含；否则将其排除。

证明算法 Alg1 不保证任何常数近似比（constant approximation ratio）。即，对任意正整数 $k$，都存在算法的一个输入，使得算法返回的物品集合的总价值至多为 $v_{\text{Opt}} / k$。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 固定任意 $k$。考虑两物品输入列表 $a_1, a_2$，其中 $v_1 = 2$、$s_1 = 1$、$v_2 = 2k$、$s_2 = 2k$，背包容量 $B = 2k$。由于 $v_1 / s_1 = 2$ 且 $v_2 / s_2 = 1$，Alg1 按顺序 $a_1, a_2$ 考虑物品，包含 $a_1$ 并排除 $a_2$。取得的总价值仅为 2，但可达到的最大总价值是 $2k$。

> <span style="color:#1e8449;">**[note]** </span> 原文此句写作 “v2 = 2k, and sk = 2k”，其中 “sk” 疑为 “s2” 的笔误（指第二个物品的尺寸）；否则该反例无法成立（此例中 $v_2/s_2 = 1$ 正是该构造的关键）。译文按 $s_2 = 2k$ 处理。

---

<!-- ===== PDF p2 ===== -->

**(b) [7 分]** 考虑如下算法 Alg2。

若所有物品的总尺寸 $\le B$，则包含所有物品。否则，按所有物品密度的非递增顺序排序。不失一般性（without loss of generality），假设该排序与物品下标顺序一致。在有序列表中找到最小的下标 $i$，使得前 $i$ 个物品的总尺寸超过 $B$（即 $\sum_{j=1}^{i} s_j > B$，但 $\sum_{j=1}^{i-1} s_j \le B$）。若 $v_i > \sum_{j=1}^{i-1} v_j$，则返回 $\{a_i\}$；否则返回 $\{a_1, \ldots, a_{i-1}\}$。

证明 Alg2 始终给出最优解的一个 2-近似（2-approximation）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 我们知道分数背包问题（fractional knapsack problem）可以用贪心算法求解；分数背包问题的最优解取前 $i-1$ 个物品，并取物品 $i$ 的某个分数 $\alpha$。即分数背包最优解（fractional knapsack optimal solution）$v^{\text{fOpt}}$ 为
>
> ```math
> v^{\text{fOpt}} = \sum_{j=1}^{i-1} v_j + \alpha v_i.
> ```
>
> 由于每个背包问题都是合法的分数背包问题，我们知道 $v_{\text{Opt}} \le v^{\text{fOpt}}$。
>
> 现在考虑 Alg2 输出的解：
>
> ```math
> \max\left(\sum_{j=1}^{i-1} v_j,\ v_i\right) \ge \max\left(\sum_{j=1}^{i-1} v_j,\ \alpha v_i\right) \ge \frac{v^{\text{fOpt}}}{2},
> ```
>
> 因为两个项中至少有一个需要大于 $v^{\text{fOpt}}$ 的一半。因此，我们有
>
> ```math
> v_{\text{Opt}} \le v^{\text{fOpt}} \le 2 \max\left(\sum_{j=1}^{i-1} v_j,\ v_i\right),
> ```
>
> 这表明 Alg2 是一个 2-近似算法（2-approximation algorithm）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 这是一个替代解法（alternate solution）。
>
> 反设存在某个输入实例使得 Alg2 未达到最优解的 2-近似。则对算法中指定的 $i$，$v_i$ 与 $\sum_{j=1}^{i-1} v_j$ 的最大值严格小于 $v_{\text{Opt}} / 2$。即，$v_i$ 与 $\sum_{j=1}^{i-1} v_j$ 都严格小于 $v_{\text{Opt}} / 2$。于是 $\sum_{j=1}^{i} v_j < v_{\text{Opt}}$。我们还知道 $\sum_{j=1}^{i} s_j > B$。
>
> 令 $C = \{a_1, \ldots, a_i\} - \text{Opt}$、$D = \text{Opt} - \{a_1, \ldots, a_i\}$、$E = \{a_1, \ldots, a_i\} \cap \text{Opt}$。
>
> 我们知道 $v_C = \sum_{j=1}^{i} v_j - v_E$，且 $v_D = v_{\text{Opt}} - v_E$。由于 $\sum_{j=1}^{i} v_j < v_{\text{Opt}}$，我们现在有 $v_C < v_D$。我们还知道 $s_C > s_D$，因为 $\sum_{j=1}^{i} s_j > B$ 且 $s_D \le B$。因此，$C$ 的密度严格小于 $D$ 的密度（即 $v_C / s_C < v_D / s_D$）。

---

<!-- ===== PDF p3 ===== -->

**（续）**

> 现在回顾 $\{a_1, \ldots, a_n\}$ 的密度是非递增的。考虑
>
> ```math
> \frac{v_C}{s_C} - \frac{v_i}{s_i} = \frac{\sum_{j \in C}(v_j s_i - v_i s_j)}{\sum_{j \in C} s_i s_j}.
> ```
>
> 我们知道对 $a_j \in C$ 有 $v_j \ge v_i$，从而 $v_j s_i - v_i s_j > 0$。这蕴含 $v_C / s_C \ge v_i / s_i$；对 $v_D / s_D \le v_i / s_i$ 也成立类似的论证。因此，
>
> ```math
> \frac{v_C}{s_C} \ge \frac{v_i}{s_i} \ge \frac{v_D}{s_D}.
> ```
>
> 这与上一段得出的 $v_C / s_C < v_D / s_D$ 相矛盾，故 Alg2 必为 2-近似。

> <span style="color:#1e8449;">**[note]** </span> 原文此处的显示公式在提取文本中较混乱（分子的符号与顺序被颠倒），此处按数学上正确且与结论一致的版本呈现：因 $C \subseteq \{a_1, \ldots, a_i\}$ 中各物品下标 $\le i$，而密度序列非递增，故 $v_j s_i - v_i s_j \ge 0$，从而 $v_C / s_C \ge v_i / s_i$；又因 $D$ 中物品下标 $> i$，故 $v_D / s_D \le v_i / s_i$，最终与 $v_C / s_C < v_D / s_D$ 构成矛盾。

**(c) [5 分]** 令 $A = \{a_1, \ldots, a_n\}$ 为任意排序的输入，$V$ 为任意物品的最大价值；则 $nV$ 是任何解可达到的总价值的一个上界（upper bound）。对每个 $i \in \{1, \ldots, n\}$ 和 $v \in \{1, \ldots, nV\}$，定义 $S_{i,v}$ 为 $\{a_1, \ldots, a_i\}$ 中总价值恰好为 $v$ 的子集的最小总尺寸；若不存在这样的子集，则 $S_{i,v} = \infty$。

给出精确求解 Knapsack 问题的动态规划算法（dynamic programming algorithm）Alg3。具体地，给出计算所有 $S_{i,v}$ 值的递推关系（recurrence），并解释如何用它求解 Knapsack 问题。分析其时间复杂度。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**
>
> $S_{1,v} = s_1$（若 $v_1 = v$），否则 $= \infty$。
>
> 对 $1 \le i \le n-1$：若 $v_{i+1} \le v$，则 $S_{i+1,v} = \min\{S_{i,v},\ s_{i+1} + S_{i,v-v_{i+1}}\}$；否则 $S_{i+1,v} = S_{i,v}$。
>
> 计算所有这些值耗时 $O(n^2 V)$。与动态规划算法的常规做法一样，我们可以在推进过程中加入簿记（bookkeeping）来计算实际的子集。可达到的最大总价值是 $\max\{v : S_{n,v} \le B\}$。最终输出是 $A$ 中总价值为该最大值的子集。
>
> 由于时间复杂度 $O(n^2 V)$ 对 $V$ 线性相关，而 $V$ 在问题输入中以二进制表示，Alg3 是一个伪多项式时间算法（pseudo-polynomial-time algorithm）。

**(d) [8 分]** 最后，我们发展 Alg4，它是 Knapsack 问题的一个完全多项式时间近似方案（Fully Polynomial Time Approximation Scheme, FPTAS）。其思想是使用类似 (c) 部分的精确动态规划算法，但不是使用给定的（可能很大的）物品价值，而是使用给定价值的适当缩放并向下取整（scaled and rounded down）的版本。与 (c) 部分一样，任意排序 $A = \{a_1, \ldots, a_n\}$，并令 $V$ 为任意物品的最大价值。

对任意 $\varepsilon$，$0 < \varepsilon < 1$，Alg4 行为如下：

对每个价值为 $v_i$ 的物品 $a_i$，定义缩放价值（scaled value）$v_i' = \left\lfloor \frac{v_i}{V} \cdot \frac{n}{\varepsilon} \right\rfloor$。

使用这些缩放价值（以及给定的尺寸）运行 Alg3，并输出它返回的物品集合 $C$。

证明 Alg4 是 Knapsack 问题的一个 FPTAS。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**

---

<!-- ===== PDF p4 ===== -->

**（续）**

> 由 (c) 部分，算法的运行时间为 $O\left(n^2 \left\lfloor \frac{V}{V} \cdot \frac{n}{\varepsilon} \right\rfloor\right) = O\left(n^2 \left\lfloor \frac{n}{\varepsilon} \right\rfloor\right)$（因为 $v_i \le V$，缩放价值至多为 $n / \varepsilon$），它关于 $n$ 和 $1/\varepsilon$ 都是多项式的。
>
> 还需证明算法返回的集合 $C$ 具有总价值 $v_C \ge (1 - \varepsilon) v_{\text{Opt}}$。
>
> 令 $K$ 表示 $\varepsilon V / n$，于是每个 $v_i' = \left\lfloor v_i / K \right\rfloor$。由此可知 $K v_i' \ge v_i - K$。考虑 Opt 的所有元素，我们得到 $K v_{\text{Opt}}' \ge v_{\text{Opt}} - nK$。
>
> 现在考虑动态规划步骤返回的集合 $C$。我们有 $v_C \ge K v_C'$。此外，由于集合 $C$ 就缩放价值而言是最优的，我们有 $v_C' \ge v_{\text{Opt}}'$。因此，我们有：
>
> ```math
> v_C \ge K v_C' \ge K v_{\text{Opt}}' \ge v_{\text{Opt}} - nK = v_{\text{Opt}} - \varepsilon V \ge v_{\text{Opt}} - \varepsilon v_{\text{Opt}} = (1 - \varepsilon) v_{\text{Opt}}.
> ```
>
> 这正是所需的。

> <span style="color:#1e8449;">**[note]** </span> 原文此处写 “Let K denote εV”，这与后文 $nK = \varepsilon V$ 相矛盾。由 $v_i' = \left\lfloor (v_i / V)(n / \varepsilon) \right\rfloor$ 反推，正确的取值应为 $K = \varepsilon V / n$（此时缩放价值恰为 $v_i' = \lfloor v_i / K \rfloor$，且 $K \cdot v_i' \ge v_i - K$ 成立），译文按此处理。

> <span style="color:#1e8449;">**[mathtip]** </span> 最后一步用到 $V \le v_{\text{Opt}}$：由于每个物品满足 $s_i \le B$，单件最大价值物品构成的集合本身就是一个可行解（feasible solution），故最优值 $v_{\text{Opt}} \ge V$，从而 $\varepsilon V \le \varepsilon v_{\text{Opt}}$。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 9-2. Fixed-Parameter Algorithms（固定参数算法）[25 分]**</span>

我们考虑锦标赛边翻转问题（Tournament Edge Reversal problem）。定义锦标赛（tournament）为一个有向图 $T = (V, E)$，使得对每对顶点 $u, v \in V$，$(u, v)$ 与 $(v, u)$ 恰好有一个在 $E$ 中。此外，定义环覆盖（cycle cover）为锦标赛 $T$ 的一个有向边集合 $A \subset E$，使得 $T$ 的每个有向环（directed cycle）都至少包含 $A$ 中的一条边。

**(a) [5 分]** 令 $T$ 的极小环覆盖（minimal cycle cover）为具有最少边数的环覆盖。证明将极小环覆盖 $A$ 的所有边翻转会使 $T$ 变为无环锦标赛（acyclic tournament）。（提示（Hint）: $A$ 中的任意边 $e$ 必定是 $T$ 的某个有向环上 $A$ 中的唯一一条边。）

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 令 $T'$ 为新的锦标赛。反设 $T'$ 包含一个环 $C$。令 $F'$ 为 $C$ 中那些通过翻转 $A$ 的边而得到的边集合，令 $F$ 为它们的反转（reversals），它们是 $T$ 中的边。对每条 $e \in F$，令 $C_e$ 为 $e$ 所覆盖的 $T$ 的一个有向环。由提示可知 $e$ 是 $A$ 中同时也在 $C_e$ 内的唯一一条边。
>
> 现在我们可以构造 $T$ 中的一个环 $C'$，它完全由 $T$ 中未被翻转的边组成：包含 $C - F'$ 中所有边的反转（它们是 $T$ 中的合法边），并对每条 $e \in C \cap F'$，包含 $C_e$ 中除 $e$ 以外的所有边。这产生 $T$ 中一个不包含 $A$ 中任何边的环 $C'$，从而构成矛盾。

在 Tournament Edge Reversal 问题中，给定一个锦标赛 $T$ 和一个正整数 $k$，目标（objective）是判定 $T$ 是否存在规模至多为 $k$ 的环覆盖。

**(b) [15 分]** 证明该问题有一个至多 $k^2 + 2k$ 个顶点的核（kernel）。（提示（Hint）: 定义三角形（triangle）为长度为 3 的有向环。考虑一个节点或一条边出现在不同三角形中的次数。）

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**

---

<!-- ===== PDF p5 ===== -->

**（续）**

> (a) 部分蕴含：锦标赛 $T$ 存在规模至多 $k$ 的环覆盖，当且仅当可以通过翻转至多 $k$ 条边的方向将其变为无环锦标赛。我们将用这一刻画（characterization）来构造核。我们给出两条简单的化简规则（reduction rules）：
>
> **规则 1（Rule 1）:** 若一条边 $e$ 包含在 $\ge k+1$ 个三角形中，则翻转 $e$ 并将 $k$ 减 1。
>
> 这条规则是安全的（safe），因为若我们不翻转 $e$，就必须从包含 $e$ 的 $k+1$ 个三角形中每个都至少翻转一条边。因此 $e$ 属于每个规模 $\le k$ 的环覆盖。
>
> **规则 2（Rule 2）:** 若一个顶点 $v$ 不包含在任何三角形中，则从 $T$ 中删除 $v$。
>
> 为理解为何安全，令 $X$ 为满足 $T$ 包含边 $(v, u)$ 的顶点 $u$ 的集合（$v$ 的出向邻居 outgoing neighbors），令 $Y$ 为满足 $T$ 包含边 $(u, v)$ 的顶点 $u$ 的集合（$v$ 的入向邻居 incoming neighbors）。$X$ 与 $Y$ 划分了 $V - \{v\}$ 中的顶点。由于 $v$ 不包含在任何三角形中，从 $X$ 到 $Y$ 没有边。因此 $T$ 中的每个有向环要么完全包含在 $X$ 诱导的子图（induced subgraph）内，要么完全包含在 $Y$ 诱导的子图内。因此，从 $T$ 中删除 $v$ 及其关联边（incident edges）不影响最小环覆盖的规模。
>
> 因此，从实例 $(T; k) = (T_0; k_0)$ 出发，我们反复应用化简规则直到它们不能再应用，得到等价实例（equivalent instances）序列 $(T_1; k_1), (T_2; k_2), \ldots, (T_m; k_m) = (T'; k')$，其中对最终实例 $(T'; k')$ 两条规则都不适用。
>
> **声明（Claim）:** 若 $T'$ 有规模 $\le k'$ 的环覆盖 $A'$，则 $T'$ 至多有 $k'(k' + 2)$ 个顶点。
>
> **声明的证明（Proof of claim）:** 由于规则 2 不适用，$T'$ 的每个顶点都在某个三角形中，而该三角形必然包含 $A'$ 中的某条边。由于规则 1 不适用，对每条边 $e \in A'$，除 $e$ 的两个端点外，至多有 $k'$ 个顶点位于包含 $e$ 的三角形中。由于 $|A'| \le k'$，可知 $T'$ 至多有 $k'(k' + 2)$ 个顶点。
>
> 因此，化简后我们考虑最终实例 $(T', k')$。若 $T'$ 有超过 $k'(k' + 2)$ 个顶点，则它不可能有规模 $\le k'$ 的环覆盖，是一个否定实例（no-instance）。由等价性，$(T, k)$ 也是否定实例。在这种情况下我们返回 no。否则，我们得到所需的至多 $k^2 + 2k$ 个顶点的核。

**(c) [5 分]** 给出 Tournament Edge Reversal 问题的一个 FPT 算法（FPT algorithm）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:**
>
> 给定 Tournament Edge Reversal 问题的实例 $(T, k)$，使用 (b) 部分的算法要么判定 $(T, k)$ 是否定实例，要么获得核 $(T', k')$，其中 $k' \le k$ 且 $T'$ 至多有 $k'(k' + 2)$ 个顶点。在前一种情况下，回答 no。在后一种情况下，运行任意运行时间为 $g(k')$ 的算法（对任意函数 $g$）来求解 $(T', k')$ 上的 Tournament Edge Reversal 问题，并将结果作为答案返回。
>
> 为化简问题，我们必须检查所有三角形，并检查所有边和顶点在三角形中的从属关系（membership）。对顶点而言，检查所有三角形每个顶点耗时 $O(|V|^2)$；对边而言，每条边耗时 $|V|$。因此每一步化简耗时

---

<!-- ===== PDF p6 ===== -->

**（续）**

> $O(|V|^3 + |V||E|)$。化简步骤至多 $|V| + |E|$ 步，因为每一步删除一条边或一个顶点。总计化简耗时 $O(|V|^4 + |V|^3|E| + |V|^2|E| + |V||E|^2)$。由于在锦标赛中 $O(|E|) = O(|V|^2)$，这是 $O(|V|^5)$。为找到最小环覆盖，一个办法是检查 $T$ 中所有可能的边子集。共有 $2^{N(N-1)/2} = 2^{O(k^4)}$ 个边子集（其中 $N = k'(k' + 2)$ 为核中顶点数），对每个子集我们需要运行一个找环算法（cycle finding algorithm），例如 DFS，以确保所有环都被覆盖。因此，最终时间复杂度为 $O(|V|^5) + 2^{O(k^4)}$。

> <span style="color:#1e8449;">**[note]** </span> 原文此处的子集数表达式在提取文本中较混乱（形如 “O(2^{k/2(k/+2)^2})”），此处按数学上正确的版本呈现：核中顶点数 $N = k'(k' + 2)$，锦标赛的边数为 $N(N-1)/2 = O(k'^4)$，故边子集数为 $2^{O(k'^4)} = 2^{O(k^4)}$，与最终复杂度 $2^{O(k^4)}$ 一致。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare<br>http://ocw.mit.edu<br>6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）<br>Spring 2015（2015 春季学期）<br>如需了解引用这些材料的说明或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
