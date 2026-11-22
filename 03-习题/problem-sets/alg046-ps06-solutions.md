<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 6 解答（Problem Set 6 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">2015 年 3 月 30 日（March 30, 2015）· 麻省理工学院（Massachusetts Institute of Technology）· 授课教授（Profs.）：Erik Demaine、Srini Devadas 与 Nancy Lynch</span>

<span style="color:#7f8c8d;">本习题集截止时间为 2015 年 4 月 2 日（周四）晚上 11:59（This problem set is due at 11:59pm on Thursday, April 2, 2015）。</span>

---

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-1**</span>：阅读 CLRS（《算法导论》）第 25 章（Read CLRS, Chapter 25）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-2**</span>：完成习题（Exercise）25.3-2。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-3**</span>：完成习题（Exercise）25.3-5。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-4**</span>：阅读 CLRS 第 23 章（Read CLRS, Chapter 23）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-5**</span>：完成习题（Exercise）23.2-4。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 6-6**</span>：完成习题（Exercise）23.2-5。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 6-1. Dynamic and Bounded-Hop All-Pairs Shortest Paths（动态与有界跳数全对最短路径）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

本问题探讨课堂上与 CLRS 中讲到的全对最短路径（All-Pairs Shortest Paths，APSP）算法的一些扩展。本问题的所有部分均假设权重为非负实数（nonnegative real weights）。我们假设顶点编号为 $1, 2, \ldots, n$。

加权有向图（weighted directed graphs）可用于对通信网络（communication networks）建模，而节点之间的最短距离（shortest distances，即最短路径权重 shortest-path weights）可用于为消息建议路由（routes）。然而，大多数通信网络是动态的（dynamic），即边的权重可能随时间变化。因此，拥有一种修改距离估计值（distance estimates）以反映这些变化的方式是有用的。

**(a)** <span style="color:#7f8c8d;">[6 points]（6 分）</span> 给出一个高效算法，它给定一个加权有向图 $G = (V, E, W)$、$G$ 的一个正确距离矩阵 $D$、相应的前驱矩阵（predecessor matrix）$\Pi$，以及一个三元组（triple）$(i, j, r)$（其中 $i, j \in V$，$r$ 为非负实数），把 $D$ 与 $\Pi$ 修改为反映将 $w_{i,j}$ 改为 $r$ 的影响。

你的算法需要处理三种情形：$r = w_{i,j}$、$r < w_{i,j}$ 与 $r > w_{i,j}$。对每种情形，分析你的算法。（注：其中一种情形的你最坏情况运行时间（worst case running time）可能不会优于 $O(V^3)$。）

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 算法在这三种情形下应有不同的表现：
>
> 1. $r = w_{i,j}$。无需任何改动，因为不会有任何最短路径发生变化。

<span style="color:#7f8c8d;">6.046J/18.410J</span>

<!-- ===== PDF p2 ===== -->

**（续）**

> 2. $r < w_{i,j}$。在此情形下，我们可能改善某些顶点对的距离：对每一对顶点 $(x, y)$，$x \ne y$，置
>
> ```math
> d_{x,y} = \min(d_{x,i} + r + d_{j,y},\ d_{x,y}).
> ```
>
> min 表达式中的两项含义如下：
>
> - 第一项对应于从 $x$ 到 $y$ 的新最短路径包含边 $(i, j)$ 的情形。
> - 第二项对应于从 $x$ 到 $y$ 的新最短路径不包含边 $(i, j)$ 的情形，此时最短路径保持不变。
>
> 注意，在第一种情形下，$d_{x,i}$ 与 $d_{j,y}$ 的值不会因这次调整而改变，因为边 $(i, j)$ 不可能被包含在从 $x$ 到 $i$ 的最短路径中，也不可能被包含在从 $j$ 到 $y$ 的最短路径中。
>
> 重新计算 $D$ 的总时间为 $O(V^2)$（因为我们需要考虑所有顶点对）。所需的额外总空间为 $O(1)$。
>
> 为调整前驱矩阵 $\Pi$，我们只需考虑那些 $d_{x,y}$ 值被减小的矩阵项 $\pi_{x,y}$。对其中每一项，我们考虑两种情形：若 $y = j$，则 $\pi_{x,y} = i$；否则 $\pi_{x,y} = \pi_{j,y}$。注意，在第二种情形下，$\pi_{j,y}$ 不会因这次调整而改变，因为 $(i, j)$ 不会出现在从 $j$ 到 $y$ 的最短路径中。
>
> 调整 $\Pi$ 的总时间为 $O(V^2)$，且只需 $O(1)$ 额外空间。
>
> 3. $r > w_{i,j}$。在此情形下，我们可能恶化某些节点对的距离。遗憾的是，这似乎要求从零开始（from scratch）为所有节点重新计算最佳距离与父节点（parents）。$D$ 与 $\Pi$ 可以使用 Floyd-Warshall 算法在 $O(V^3)$ 时间内重新计算。

**(b)** <span style="color:#7f8c8d;">[4 points]（4 分）</span> 给出一个加权有向图 $G = (V, E, W)$、一个距离矩阵 $D$ 与一个三元组 $(i, j, r)$ 的例子，使得任何为反映将 $w_{i,j}$ 改为 $r$ 而修改 $D$ 的算法都必须花费 $\Omega(V^2)$ 时间。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 设 $G$ 为具有 $n$ 个顶点 $1, 2, \ldots, n$、边为 $(i, i+1)$（对所有 $i$）的线图（line graph）。假设所有这些有向边的权重均为 1。所有其他权重均为 $\infty$。设 $e = (n, 1, 1)$。则对所有满足 $i < j$ 的 $i, j$，$d_{j,i}$ 在修改前为 $\infty$，但在修改后为正整数。这意味着 $D$ 中必须有 $\Omega(n^2)$ 个元素发生变化。

现在假设我们为问题新增一个约束——对我们想要考虑的路径中的跳数（hops，即边数 edges）设一个上界 $h$。更形式化地说，给定加权有向图（weighted digraph）$G = (V, E, W)$ 和一个正整数 $h$，$0 \le h \le n - 1$，我们希望产生一个距离矩阵 $D$，给出至多 $h$ 跳路径的最短距离。

<!-- ===== PDF p3 ===== -->

**（续）**

**(c)** <span style="color:#7f8c8d;">[5 points]（5 分）</span> 改编 Floyd-Warshall 算法以求解有界跳数 APSP 问题。就其图参数与 $h$ 分析其复杂度。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** Floyd-Warshall 算法在 $|V|$ 次外层循环执行中按顺序、一次一个地引入中间顶点（intermediate vertices）（见 CLRS 第 695 页的代码）。在该循环的每一次执行中，它考虑所有顶点编号对 $(i, j)$。每一对只需 $O(1)$ 时间，因为它只涉及检查与比较 $D$ 中的三个条目。
>
> 当我们加上「完整路径必须至多由 $h$ 跳组成」这一约束时，第 7 行的比较变得更加复杂。它必须考虑把 $h$ 跳分配给路径两部分的全部不同方式——新顶点 $k$ 之前的部分与 $k$ 之后的部分。因此，我们定义 $d^{k,h}_{i,j}$ 为从顶点 $i$ 到顶点 $j$ 的最短路径的权重，其中所有中间顶点都在集合 $1, \ldots, k$ 中，且加上「路径至多由 $h$ 跳组成」的约束。$d^{k,h}_{i,j}$ 现在可以表示为
>
> ```math
> d^{k,h}_{i,j} = \min\!\left(\{d^{k-1,h}_{i,j}\} \cup \{d^{k-1,h_0}_{i,k} + d^{k-1,h-h_0}_{k,j} \mid 0 \le h_0 \le h\}\right).
> ```
>
> 子问题的总数现在为 $O(V^3 \cdot h)$，求解每个子问题所需时间为 $O(h)$，这意味着该算法的总运行时间为 $O(V^3 \cdot h^2)$。就空间需求而言，在计算 $k$ 的结果时，我们需要 $k - 1$ 的距离信息以及 $h$ 的所有取值，因此空间复杂度现在为 $O(V^2 \cdot h)$。

**(d)** <span style="color:#7f8c8d;">[5 points]（5 分）</span> 改编第 25.1 节的矩阵乘法策略以求解有界跳数 APSP 问题。就其图参数与 $h$ 分析其复杂度。尽量获得对 $h$ 的对数依赖（logarithmic dependence）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 在第 25.1 节的描述中，矩阵 $L^{(k)}$ 包含使用至多 $k$ 跳路径的最短距离；具体地，$l^k_{i,j}$ 是从 $i$ 到 $j$、由至多 $k$ 跳组成的一条路径的最短距离。因此，$L^{(0)}$ 是主对角线（main diagonal）为 0、其余各处为 $\infty$ 的矩阵，并且对每个 $k$，$L^{(k+1)} = L^{(k)} \cdot W$，其中此处的矩阵乘法使用 min 与 $+$ 来代替通常的 $+$ 与 $\times$。我们的目标是产生 $L^{(h)}$。
>
> 若 $h = 0$，则答案就是主对角线为 0、其余各处为 $\infty$ 的矩阵。现在假设 $h \ge 1$。
>
> 我们不能直接使用第 689–690 页的反复平方（successive squaring）策略，因为若 $h$ 不是 2 的幂，那会「越过」（overshoot）$h$。不过，我们可以通过使用 $h$ 的二进制表示 $h = h_l h_{l-1} \cdots h_1$ 来获得对 $h$ 的对数依赖。由于 $h \ge 1$，我们有 $h_l = 1$。从 $L^{(h_l)} = L^{(1)} = L^{(0)} \cdot W = W$ 开始。然后对 $j = l - 1, \ldots, 1$，如下计算 $L^{(h_l \cdots h_j)}$：若 $h_j = 0$，则 $L^{(h_l \cdots h_j)} = (L^{(h_l \cdots h_{j+1})})^2$；否则 $L^{(h_l \cdots h_j)} = (L^{(h_l \cdots h_{j+1})})^2 \cdot W$。最终答案即为 $L^{(h_l \cdots h_1)}$。

<!-- ===== PDF p4 ===== -->

**（续）**

> 矩阵计算的总数为 $O(\lg h)$，每次计算耗时 $O(V^3)$，因此总时间复杂度为 $O(V^3 \lg h)$。注意，这比第 (c) 部分的改进版 Floyd-Warshall 算法更好，这很有意思，因为原始版（vanilla）Floyd-Warshall 在标准全对最短路径问题（All-pair Shortest Path problem）上表现更好。空间复杂度为 $O(V^2)$。

**(e)** <span style="color:#7f8c8d;">[5 points]（5 分）</span> 最后，考虑有界跳数 APSP 问题的一个动态版本。设计一个算法，其输入为：

1. 一个加权有向图 $G = (V, E, W)$；
2. 一个跳数 $h$，$0 \le h \le n - 1$；
3. 一个正确的距离矩阵 $D$，给出至多 $h$ 跳的最短距离，以及可能对求解本问题有用的附加距离信息；以及
4. 一个三元组 $(i, j, r)$，其中 $i, j \in V$，$r$ 为非负实数。

你的算法应修改 $D$ 以反映将 $w_{i,j}$ 改为 $r$ 的影响，并且还应更新你所添加的任何附加距离信息。与第 (a) 部分一样，你的算法需要处理三种情形：$r = w_{i,j}$、$r < w_{i,j}$ 与 $r > w_{i,j}$。对每种情形，就图参数与 $h$ 分析你的算法的复杂度。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 附加距离信息可以是第 (d) 部分解答中定义的整组矩阵 $L^{(k)}$，$0 \le k \le h$。于是 $D = L^{(h)}$。所有这些都可以使用一系列与矩阵 $W$ 的连续乘法，在 $O(V^3 \cdot h)$ 时间内从零开始计算。
>
> 考虑如何修改第 (a) 部分的解答。$r = w_{i,j}$ 的情形仍然不涉及任何改动，而 $r > w_{i,j}$ 的情形涉及在全部矩阵中重新计算所有距离，时间代价为 $O(V^3 \cdot h)$。
>
> 在 $r < w_{i,j}$ 的情形下，我们必须为所有矩阵中的全部有序对 $(x, y)$ 重新计算距离；因此，我们必须重新计算 $V^2 \cdot h$ 个条目。对每个 $x, y$，我们定义如下：
>
> ```math
> l^{(k)}_{x,y} = \min\!\left(\{l^{(k)}_{x,y}\} \cup \{l^{(k')}_{x,i} + r + l^{(k-k'-1)}_{j,y} \mid 0 \le k' \le k - 1\}\right).
> ```
>
> 重新计算每个条目所需的时间代价为 $O(k)$，即 $O(h)$。因此总时间代价为 $O(V^2 \cdot h^2)$。空间复杂度为 $O(V^2 \cdot h)$。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 6-2. Minimum Spanning Trees with Unique Edge Weights（具有唯一边权的最小生成树）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

考虑一个无向图 $G = (V, E)$ 及其权重函数 $w$，$w$ 提供非负实数值权重（nonnegative real-valued weights），且所有边的权重互不相同（different）。

<!-- ===== PDF p5 ===== -->

**（续）**

**(a)** <span style="color:#7f8c8d;">[5 points]（5 分）</span> 证明在给定的唯一性（uniqueness）假设下，$G$ 具有唯一的最小生成树（Minimum Spanning Tree，MST）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 为导出矛盾（contradiction），假设 $G$ 有两个不同的 MST，记为 $T_1$ 与 $T_2$。设 $e$ 为恰好出现在 $T_1$、$T_2$ 二者之一中的权重最小的边——注意，由于树 $T_1$ 与 $T_2$ 不同，这样的边必然存在。不失一般性（without loss of generality），假设 $e$ 在 $T_1$ 中而不在 $T_2$ 中。
>
> 然后构造一个新图 $T_2'$，它由 $T_2$ 加上 $e$ 组成。观察可知，$T_2'$ 现在含有一个环（cycle）。由于 $T_1$ 不可能包含整个环，环中必然存在某条边 $e' \ne e$ 不在 $T_1$ 中。由于我们假设 $e$ 是恰好出现在两棵树的某一棵中的权重最小的边，且 $e'$ 在 $T_2$ 中而不在 $T_1$ 中，我们看到 $w(e') > w(e)$。（我们在此使用了唯一权重假设来得到严格不等式（strict inequality）。）
>
> 然后从 $T_2'$ 中删除 $e'$，得到一棵新的生成树 $T_2''$，其权重比 $T_2$ 更小。这与我们最初「$T_2$ 是一个 MST」的假设矛盾。

接下来的三个部分各概述一个针对具有唯一边权的图的 MST 算法。在每种情况下，说明这是否是一个正确的 MST 算法。若是，请给出证明、一个更详细的高效算法描述以及分析。若不是，请给出一个具体的反例（counterexample）。（我们省略这些部分的分值，因为我们将对算法分配更多分值、对反例分配较少分值。）

**(b)** <span style="color:#c0392b;">[Batched Edge-Addition MST]（批量加边 MST）</span>

该算法维护一个已知属于 MST 的边集 $A$。初始时，$A$ 为空。算法分阶段（phases）运行；在每个阶段中，它向 $A$ 添加一批（batch）一条或多条边。阶段持续进行，直到我们得到一个生成树（spanning tree）。

具体地，在每个阶段中，算法执行如下操作：对由 $A$ 形成的森林中的每个分量树（component tree）$C$，识别跨越 $C$ 与其余分量之间切割（cut）的最轻边（lightest weight edge）$e_C$。在确定所有分量树的这些边之后，把全部 $e_C$ 边作为一批一次性加入 $A$。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 这确实能产生一个 MST。
>
> 我们可以通过对阶段数进行归纳（induction）来证明，集合 $A$ 始终是唯一 MST 的一个子集。对于基础情形（base case），集合 $A$ 不含任何边，它平凡地（trivially）是唯一 MST 的一个子集。
>
> 对于归纳步骤（inductive step），考虑某个阶段开始时的一个边集 $A$。由我们的归纳假设（inductive hypothesis），集合 $A$ 是唯一 MST 的一个子集。我们要证明在同一阶段结束时，通过向 $A$ 添加某些边而构造出的集合 $A'$ 仍是唯一 MST 的一个子集。
>
> 对由 $A$ 形成的森林中的每个分量 $C$，由 CLRS 推论 23.2（Corollary 23.2），最轻的边是某个 MST 的一部分。由于只有一个 MST，所有被选出的边都是同一个唯一 MST 的一部分。因此，当我们把它们全部加入后，在该阶段结束时得到的新集合 $A'$ 仍是唯一 MST 的一个子集。


<!-- ===== PDF p6 ===== -->

**（续）**

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）**：（承接 Problem 6-2 中 (b) Batched Edge-Addition MST（批量加边最小生成树）的解答）
>
> **Detailed algorithm（详细算法）**：我们可以用面向顶点的 Union-Find（并查集）集合结构来实现该算法。对于结构中的每个 component（连通分量），我们维护一条边列表，其中的每条边都恰好有一个端点在分量内。初始时 $A$ 为空，我们为每个顶点创建一个单独的集合，使用 Recitation 3（复习课 3）中描述的 MAKESET（建集）操作。我们用 $O(E \log E)$ 的时间按边权升序（ascending order）对边排序。对于每个单顶点分量，我们计算一条与其关联（incident）的边的独立列表。这可以通过对已排序的边表做一次线性扫描（linear pass）来完成。
>
> 在每个 phase（阶段），我们执行如下步骤：
>
> - 对每个分量 $C$，通过查看其边列表的第一个条目找出最轻的关联边（lightest incident edge）。这需要 $O(1)$ 时间。所有这些边都被加入 $A$。
> - 对每条加入 $A$ 的边，在其两个端点所关联的分量之间执行一次 union（合并）操作。
> - 现在，我们需要更新每个合并后分量的边列表。这可以用 Merge sort（归并排序）的 two-finger algorithm（双指算法）完成。这些边列表每个有 $O(E_i)$ 个元素，因此合并步骤需要 $O(E_i)$ 时间。（这里我们假设分量 $i$ 有 $E_i$ 条边，并且不失一般性（WLOG），对任意分量 $i$ 和 $j$，$E_i > E_j$。）在这个合并步骤中，我们需要删除所有现在属于同一分量的边；这可以通过对合并列表中每条边调用 FIND（查找）完成——合并列表中边的总数是 $O(E_i)$，且每次 FIND 操作的时间复杂度为 $O(\alpha(V))$，这意味着该合并步骤的总时间复杂度为 $O(E_i(1+\alpha(V)))$。
>
> 注意，每次迭代中每个分量上合并操作的最坏情况时间复杂度为 $O(E_i(1+\alpha(V)))$，因此每次迭代的最坏情况时间复杂度为 $O\left(\sum_i E_i(1+\alpha(V))\right) = O(E(1+\alpha(V)))$。还要注意，最坏情况下迭代的总次数为 $O(\log V)$（因为在每个阶段分量的数量至少减少为原来的一半），从而总的最坏情况时间复杂度为 $O(E \log V (1+\alpha(V)))$。
>
> 所有单独边列表的总长度为 $O(E)$，用于跟踪每个顶点属于哪个分量的 Union-Find 数据结构其空间复杂度为 $O(V)$，这意味着除了大小为 $O(E)$ 的列表 $A$ 之外，该算法的总空间复杂度为 $O(V + E)$。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**(c) [Divide-and-Conquer MST]（分治最小生成树）**</span>

该算法使用一种简单的 Divide-and-Conquer（分治）策略：把顶点集合 $V$ 任意划分为两个不相交（disjoint）的集合 $V_1$ 和 $V_2$，每个集合的大小约为 $V/2$。定义图 $G_1 = (V_1, E_1)$，其中 $E_1$ 是 $E$ 中两个端点都在 $V_1$ 内的边组成的子集。类似地定义 $G_2 = (V_2, E_2)$。

递归地求出 $G_1$ 和 $G_2$ 各自的（唯一的）MST（最小生成树），记为 $T_1$ 和 $T_2$。然后找出跨越顶点集合 $V_1$ 与 $V_2$ 之间割（cut）的那条（唯一的）最轻边，将其加入以构成最终的生成树（spanning tree）$T$。

<!-- ===== PDF p7 ===== -->

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 不正确（Incorrect）。一个简单的反例（counterexample）是四节点环形图（ring graph），节点为 $a, b, c, d$，边权为 $w(a,b) = 1,\ w(b,c) = 11,\ w(c,d) = 2,\ w(d,a) = 12$。
>
> ```text
>     a --1-- b
>     |       |
>    12      11
>     |       |
>     d --2-- c
> ```
>
> 该图可以被切分为 $V_1 = \{a, d\}$ 和 $V_2 = \{b, c\}$。那么两半的 MST 权重分别为 12 和 11。加入最轻的割边（连接 $a$ 与 $b$、权重为 1 的边）后得到一棵权重为 $11 + 12 + 1 = 24$ 的生成树，这显然是次优的（sub-optimal）（边 $(a,b)$、$(b,c)$ 和 $(c,d)$ 组成的生成树权重为 14）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**(d) [Cycle-Breaking MST]（破圈最小生成树）**</span>

该算法分 phase（阶段）运行。在每个阶段，算法首先找出图中 simple cycles（简单环）的某个非空（nonempty）子集。然后识别每个环上的最重边（heaviest edge），并删除所有这些重边。不断重复各阶段，直到我们得到一棵生成树（spanning tree）。

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 该算法确实能得到 MST。设 $T$ 表示 $G$ 的唯一 MST。我们首先陈述并证明下面的 claim（声明）。
>
> **Claim（声明）**：对于 $G$ 中每个简单环 $C$，环 $C$ 上的最重边不在 $T$ 中。
>
> **Proof of Claim（声明的证明）**：为推出矛盾，假设某个简单环 $C$ 的最重边在 $T$ 中。由于 $T$ 不可能包含整个环 $C$，环 $C$ 中必存在另一条不在 $T$ 中的边 $e' \neq e$。于是我们可以通过在 $T$ 中加入边 $e'$ 并删除边 $e$，构造出一棵新树 $T'$。$T'$ 也是一棵生成树，且其权重严格小于（strictly less than）$T$ 的权重，这与我们假设 $T$ 是 MST 相矛盾。
>
> **End of proof of Claim（声明证明完毕）**
>
> 现在考虑算法在第 $i$ 个阶段被删除的任意一条边 $e$。在第 $i$ 阶段开始时，边 $e$ 必是图 $G$ 的某个简单环 $C$ 的最重边。但环 $C$ 也是原图 $G$ 的一个简单环，所以被删除的边 $e$ 也是 $G$ 的某个简单环上的最重边。因此由 Claim（声明）可知，边 $e$ 不在 $T$ 中。
>
> 于是，该算法只删除不在 $T$ 中的边。由于最终得到的图是一棵生成树，且包含 $T$，所以它必然等于 $T$。

<!-- ===== PDF p8 ===== -->

> <span style="color:#2471a3;">**[solution]**</span> **Detailed algorithm（详细算法）**：我们可以如下实现该算法：
>
> ```text
> CYCLEBREAKINGMST(G)
> 1   while |G.E| > |V| − 1
> 2       C = FINDCYCLE(G)
> 3       e = FINDMAXIMUMEDGE(C)
> 4       G = G − e
> 5   return G
> ```
>
> 这里，FINDCYCLE（找环）找出图 $G$ 中的某个环并返回它——这可以用 Depth First Search（DFS，深度优先搜索）实现；DFS 让我们能在 $O(E)$ 时间内找出图 $G$ 中的回边（back edges）。一旦我们得到环，就可以在 $O(V)$ 时间内找出其中的最大权重边（因为简单环至多有 $V$ 条边）；然后我们从图 $G$ 中删除这条边，并在缩减后的图上重复上述步骤，直到图中不再有环。
>
> 该算法的总运行时间复杂度为 $O(E \cdot (E - V)) = O(E^2)$。
>
> 该算法的总空间复杂度为 $O(E)$，因为我们只需要记录图中的一条简单环，而简单环在最坏情况下有 $O(E)$ 条边。
>
> **Remark（注）**：注意在上述实现中，我们每次从图中删除一条边。我们通常可以通过在单次迭代中删除多条边来做得更好（单次迭代可以识别出多个环）。不过，这样做能否改进最坏情况（worst-case）时间复杂度尚不清楚。

<!-- ===== PDF p9 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**　http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J　Design and Analysis of Algorithms（算法设计与分析）**</span>

<span style="color:#7f8c8d;">**Spring 2015（2015 春季学期）**</span>

<span style="color:#7f8c8d;">关于引用这些材料的说明或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms</span>
