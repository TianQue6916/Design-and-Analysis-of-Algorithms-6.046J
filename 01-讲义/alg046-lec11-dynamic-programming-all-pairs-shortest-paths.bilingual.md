<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 11（Lecture 11）：全对最短路径（All-Pairs Shortest Paths）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

**引言（Introduction）**：可以用不同类型算法求解全对最短路径问题：
- 动态规划（Dynamic programming）
- 矩阵乘法（Matrix multiplication）
- Floyd-Warshall 算法
- Johnson 算法（Johnson's algorithm）
- 差分约束（Difference constraints）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**单源最短路径（Single-source shortest paths）**</span>

- 给定有向图 $G = (V, E)$、顶点 $s \in V$ 与边权 $w : E \to \mathbb{R}$
- 求 $\delta(s, v)$，等于最短路径权重 $s \to v$，对所有 $v \in V$（若沿途有负权环（negative weight cycle）则为 $-\infty$，若无路径则为 $\infty$）

| 情形（Situation） | 算法（Algorithm） | 时间（Time） |
|:---|:---|:---|
| 无权（$w = 1$） | BFS | $O(V + E)$ |
| 非负边权 | Dijkstra | $O(E + V \lg V)$ |
| 一般 | Bellman-Ford | $O(VE)$ |
| 无环图（DAG） | 拓扑排序 + 一次 B-F | $O(V + E)$ |

上述结果都是**目前已知最好**的。我们用**斐波那契堆（Fibonacci heaps）**让 Dijkstra 达到 $O(E + V \lg V)$ 的界。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**全对最短路径（All-pairs shortest paths）**</span>

- 给定带边权图 $G = (V, E, w)$
- 对所有 $u, v \in V$ 求 $\delta(u, v)$

🎥 *Devadas 在视频中[预告 Johnson 算法]*："So a better general case algorithm is called Johnson's algorithm. That will be the last algorithm we cover today. And it achieves this bound, which is the same as running Dijkstra V times."（翻译：一个更好的通用情形算法叫 Johnson 算法，这是我们今天讲的最后一个算法。它达到的这个界，与运行 V 次 Dijkstra 相同。）——Johnson 算法在一般（含负权但不含负权环）图上达到「V 次 Dijkstra」的界，是单源方法中最优的。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 单源最短路径与全对最短路径的区别是「一次求一个源 vs 一次求所有源」。表格里四种单源算法的复杂度来自 6.006：BFS（无权）、Dijkstra（非负，配二叉堆 $O(E \lg V)$、配斐波那契堆 $O(E + V \lg V)$）、Bellman-Ford（一般情形，可处理负权，$O(VE)$）、DAG 上拓扑排序 + 单遍 Bellman-Ford 松弛（$O(V+E)$）。「斐波那契堆」是这一讲的伏笔——它让 Dijkstra 达到最优界，但实现复杂（你在 6.006 只学二叉堆版）；它是摊还分析（讲次 5）的实际应用：斐波那契堆的 decrease-key 摊还 $O(1)$。全对最短路径的朴素思路就是「把单源算法跑 $V$ 次」——下一页会分析这个思路的复杂度并发现它的局限。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**全对最短路径（续）**</span>

求解全对最短路径（APSP）问题的一个简单方法是：对图中的 $V$ 个顶点，**每个顶点都运行一次单源最短路径算法**。

| 情形（Situation） | 算法（Algorithm） | 时间（Time） |
|:---|:---|:---|
| $E = \Theta(V^2)$、无权（$w=1$） | $\lvert V \rvert \times$ BFS | $O(VE) = O(V^3)$ |
| 非负边权 | $\lvert V \rvert \times$ Dijkstra | $O(VE + V^2 \lg V) = O(V^3)$ |
| 一般 | $\lvert V \rvert \times$ Bellman-Ford | $O(V^2E) = O(V^4)$ |
| 一般 | Johnson 算法 | $O(VE + V^2 \lg V) = O(V^3)$ |

这些结果（除第三个外）也**已是目前已知最好**的——不知道如何击败「$|V| \times$ Dijkstra」。

**求解 APSP 的算法**：注意下面所有算法都假设 $w(u, v) = \infty$ 若 $(u, v) \notin E$。

**动态规划，尝试 1（Dynamic Programming, attempt 1）**
1. **子问题（Sub-problems）**：$d^{(m)}_{uv}$ = 用 $\le m$ 条边的最短 $u \to v$ 路径的权重
2. **猜测（Guessing）**：最后一条边（last edge）是什么（$x, v$）？
3. **递推（Recurrence）**：

```math
d^{(m)}_{uv} = \min_{x \in V} \left( d^{(m-1)}_{ux} + w(x, v) \right)
```

```math
d^{(0)}_{uv} = \begin{cases} 0 & \text{若 } u = v\\ \infty & \text{否则} \end{cases}
```

4. **拓扑排序（Topological ordering）**：对 $m = 0, 1, 2, \ldots, n-1$：对 $u$ 与 $v \in V$：
5. **原始问题（Original problem）**：若图不含负权环（由 Bellman-Ford 分析），则最短路径是简单的 ⇒ $\delta(u, v) = d^{(n-1)}_{uv} = d^{(n)}_{uv} = \cdots$

**时间复杂度（Time complexity）**：在这个 DP 中，共有 $O(V^3)$ 个子问题。每个子问题需 $O(V)$ 时间求解，因为要考虑 $V$ 种可能选择。这给出总运行时间 $O(V^4)$。

注意：这**不比**「$|V| \times$ Bellman-Ford」更好。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 「DP 尝试 1」是「**按边数限制路径**」的思路：$d^{(m)}_{uv}$ 允许用至多 $m$ 条边。子问题个数 $O(V^3)$（$m$ 从 0 到 $n-1$，$u,v$ 各 $V$ 个），每个要遍历 $x$ 找最后一条边（$O(V)$），所以 $O(V^4)$——**并不比**「跑 $V$ 次 Bellman-Ford」更好（讲义原文措辞）。这个尝试的意义在于**揭示 DP 的第一步往往不够好**：子问题选择「边数上界」导致每子问题要试 $O(V)$ 个前驱。改进方向在下一页：要么用「平方松弛」减少层数（矩阵乘法），要么换一种子问题定义（Floyd-Warshall 用「中间点集合」）。这个「子问题定义决定复杂度」的教训，是本讲反复出现的主题——**DP 的复杂度 = 子问题个数 × 每子问题时间**，两个因子都能通过重新设计子问题来优化。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**自底向上松弛（Bottom-up via relaxation steps）**</span>

```
1  for m = 1 to n by 1
2      for u in V
3          for v in V
4              for x in V
5                  if duv > dux + dxv
6                      duv = dux + dxv
```

在上述伪代码中，我们省略上标，因为**更多的松弛永远不会有害**（more relaxation can never hurt）。

注意我们可以把松弛步骤改成 $d^{(m)}_{uv} = \min_x ( d^{\lceil m/2 \rceil}_{ux} + d^{\lceil m/2 \rceil}_{xv} )$。这个改动会把总运行时间降到 $O(n^3 \lg n)$。（学生建议）

> <span style="color:#7f8c8d;">[说明] 学生建议的改进是「分治式松弛」：把「每次 +1 条边」改成「每次 +⌈m/2⌉ 条边」（即把路径从中点拆成两半）。这正是下一页「反复平方（repeated squaring）」的雏形，只不过矩阵乘法视角会把它讲得更清楚。</span>

**矩阵乘法（Matrix multiplication）**

回忆标准矩阵乘法的任务：给定 $n \times n$ 矩阵 $A$ 与 $B$，计算 $C = A \cdot B$，使得 $c_{ij} = \sum_{k=1}^{n} a_{ik} \cdot b_{kj}$。
- 标准算法 $O(n^3)$
- Strassen 算法 $O(n^{2.807})$
- Coppersmith-Winograd 算法 $O(n^{2.376})$
- Vassilevska Williams 算法 $O(n^{2.3728})$

**与最短路径的联系（Connection to shortest paths）**
- 定义 $\oplus = \min$ 与 $\otimes = +$
- 那么 $C = A \otimes B$ 产生 $c_{ij} = \min_k (a_{ik} + b_{kj})$
- 定义 $D^{(m)} = (d^{(m)}_{ij})$、$W = (w(i, j))$、$V = \{1, 2, \ldots, n\}$

由上述定义，我们看到 $D^{(m)}$ 可以表示为 $D^{(m-1)} \otimes W$。换言之，$D^{(m)}$ 可以表示为 $W$ 与自身「圆乘法」（circle-multiplication，$\otimes$）$m$ 次。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 「最短路径 = 广义矩阵乘法」是「**代数化（algebraification）**」的经典：把 $(+, \times)$ 换成 $(\min, +)$，普通矩阵乘法的「内积-求和」就变成「逐项取 $\min$ 的路径拼接」。为什么这个替换有意义？因为普通矩阵乘法 $c_{ij} = \sum_k a_{ik} b_{kj}$ 对应「从 $i$ 到 $j$ 经过中间点 $k$ 的『所有路径和』」，而 $(\min, +)$ 版本对应「从 $i$ 到 $j$ 经过 $k$ 的『最短路径』」——**同一套组合结构，换一个运算就得到不同的语义**。这个 $(\min, +)$ 半环（semiring）视角是算法统一性的体现：你在 18.065 学过的矩阵乘法、正则表达式的 Kleene 代数、概率推断的和-积算法（sum-product），全是这个「换运算的矩阵乘法」思想。注意「$D^{(m)} = D^{(m-1)} \otimes W$」正是「路径长 +1」的矩阵化，而「重复平方」利用结合律把 $O(n)$ 次乘法降到 $O(\lg n)$ 次——但 $(\min, +)$ 运算不支持减法（不能用 Strassen），所以每步仍是 $O(n^3)$。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**矩阵乘法算法（Matrix multiplication algorithm）**</span>

- $n - 2$ 次乘法 ⇒ $O(n^4)$ 时间（仍不好）
- **反复平方（Repeated squaring）**：

```math
W \to W^2 \to (W^2)^2 \to \cdots \to W^{2^{\lceil \lg n \rceil}} = W^{n-1} = (\delta(i, j))
```

若没有负权环。该算法的时间复杂度为 $O(n^3 \lg n)$。

**我们不能用 Strassen 等算法**，因为我们的新乘法和加法运算**不支持取负（negation）**。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**Floyd-Warshall：动态规划，尝试 2（Floyd-Warshall: Dynamic Programming, attempt 2）**</span>

1. **子问题（Sub-problems）**：$c^{(k)}_{uv}$ = 最短 $u \to v$ 路径的权重，其中间顶点（intermediate vertices）$\in \{1, 2, \ldots, k\}$
2. **猜测（Guessing）**：最短路径是否使用顶点 $k$？
3. **递推（Recurrence）**：

```math
c^{(k)}_{uv} = \min\left( c^{(k-1)}_{uv},\ c^{(k-1)}_{uk} + c^{(k-1)}_{kv} \right)
```

```math
c^{(0)}_{uv} = w(u, v)
```

4. **拓扑排序（Topological ordering）**：对 $k$：对 $u$ 与 $v \in V$：
5. **原始问题（Original problem）**：$\delta(u, v) = c^{(n)}_{uv}$。**负权环** $\Leftrightarrow$ 负的 $c^{(n)}_{uu}$

**时间复杂度（Time complexity）**：这个 DP 同样包含 $O(V^3)$ 个子问题。但这次每个子问题只需 $O(1)$ 时间求解，所以总运行时间为 $O(V^3)$。

**自底向上松弛（Bottom up via relaxation）**

```
1  C = (w(u, v))
2  for k = 1 to n by 1
3      for u in V
4          for v in V
5          if cuv > cuk + ckv
6              cuv = cuk + ckv
```

和之前一样，我们选择忽略下标（省略上标）。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Floyd-Warshall 是 DP 子问题设计从「按边数」到「按中间点集合」的经典转变：$c^{(k)}_{uv}$ 允许路径使用**编号不超过 $k$** 的中间顶点。这个定义的妙处在于递推只有两个分支（用不用顶点 $k$），**每子问题 $O(1)$ 时间**——所以 $O(V^3)$ 子问题 × $O(1)$ = $O(V^3)$，比「尝试 1」的 $O(V^4)$ 整整少一个因子。「用不用 $k$」的二分推理：若最优路径不用 $k$，就是 $c^{(k-1)}_{uv}$；若用 $k$，则路径被 $k$ 分成两段 $u \to k$ 与 $k \to v$，各自只用 $\le k-1$ 的中间点（因为 $k$ 作为中间点只出现一次，简单路径）。这个「**引入新顶点，问用不用它**」的增量式子问题设计，是区间/集合 DP 的通用模板——与最长回文的「区间收缩」、最优 BST 的「枚举根」并列的第三种结构。Wiki 查证：Floyd-Warshall 也被称为 Roy-Warshall / WFI（Warshall-Floyd-Ingerman），$\Theta(V^3)$ 时间与 $\Theta(V^2)$ 空间，1962 年由 Floyd 发表。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**Johnson 算法（Johnson's algorithm）**</span>

1. 找函数 $h : V \to \mathbb{R}$，使得对**所有** $u, v \in V$ 有 $w_h(u, v) = w(u, v) + h(u) - h(v) \ge 0$，或确定存在负权环。
2. 对每个源顶点 $s \in V$，在 $(V, E, w_h)$ 上运行 Dijkstra ⇒ 对所有 $u, v \in V$ 得到 $\delta_h(u, v)$。
3. 已知 $\delta_h(u, v)$ 后，很容易计算 $\delta(u, v)$。

<span style="color:#2471a3;">**[claim]**</span> 声明（Claim）：$\delta(u, v) = \delta_h(u, v) - h(u) + h(v)$

**证明（Proof）**：看图 $G$ 中任意 $u \to v$ 路径 $p$。
- 设 $p$ 为 $v_0 \to v_1 \to v_2 \to \cdots \to v_k$，其中 $v_0 = u$ 且 $v_k = v$。

```math
\begin{aligned}
w_h(p) &= \sum_{i=1}^{k} w_h(v_{i-1}, v_i)\\
&= \sum_{i=1}^{k} \left[ w(v_{i-1}, v_i) + h(v_{i-1}) - h(v_i) \right]\\
&= \sum_{i=1}^{k} w(v_{i-1}, v_i) + h(v_0) - h(v_k)\\
&= w(p) + h(u) - h(v)
\end{aligned}
```

- 因此**所有** $u \to v$ 路径的权重都改变同一个**偏移量（offset）** $h(u) - h(v)$，这意味着**最短路径被保留**（只是有偏移）。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Johnson 算法的核心是「**用重加权（reweighting）把负权变成非负**」：边 $(u,v)$ 的新权重 $w_h(u,v) = w(u,v) + h(u) - h(v)$。证明里的「**望远镜求和（telescoping sum）**」是全部魔力所在——路径上的 $h(v_{i-1}) - h(v_i)$ 逐项相消，只留下首尾 $h(u) - h(v)$，所以**每条路径都偏移同一个量**，相对优劣不变（最短路径不变）。这个「**给每个顶点加个势能 $h$，用势能差调整边权**」的技巧极其通用：后续的最小费用流（Edmonds-Karp 正是用同款重加权处理负费用）、Suurballe 算法（找两条最小总长不相交路径）里都会再见到它。关键洞察：负权只影响 Dijkstra 的正确性（它假设贪心选择最优），而重加权让我们「换一套权重」在非负图上跑 Dijkstra，再「还原」真实距离——**换坐标系解题，再换回来**。

---

<span style="color:#c0392b">**Johnson 算法（续）**</span>

**如何找到 $h$？**

我们知道

```math
w_h(u, v) = w(u, v) + h(u) - h(v) \ge 0
```

这等价于

```math
h(v) - h(u) \le w(u, v)
```

对所有 $(u, v) \in V$。这称为**差分约束系统（system of difference constraints）**。

<span style="color:#2471a3;">**[theorem]**</span> 定理（Theorem）：若 $(V, E, w)$ 有负权环，则上述差分约束系统**无解**。

---

<!-- ===== PDF p6 ===== -->

**证明（Proof）**：设 $v_0 \to v_1 \to \cdots \to v_k \to v_0$ 是一个负权环。反设差分约束系统有解，记为 $h$。

这给出如下方程组：

```math
h(v_1) - h(v_0) \le w(v_0, v_1)
```

```math
h(v_2) - h(v_1) \le w(v_1, v_2)
```

```math
\cdots
```

```math
h(v_k) - h(v_{k-1}) \le w(v_{k-1}, v_k)
```

```math
h(v_0) - h(v_k) \le w(v_k, v_0)
```

把所有方程相加得：

```math
0 \le w(\text{cycle}) < 0
```

这显然不可能。

由此我们可以得出结论：若图 $(V, E, w)$ 有负权环，则上述差分约束系统无解。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 负权环 ⇒ 差分约束无解的证明是一个**「求和-相消-矛盾」**的经典套路：把环上的不等式逐项相加，左边的 $h(v_i)$ 全部两两相消（又是望远镜求和！），剩下 $0 \le w(\text{cycle})$——但负权环意味着 $w(\text{cycle}) < 0$，直接矛盾。这个证明与 Johnson 主证明遥相呼应：**同一个望远镜技巧，一个用于「证明重加权保最短路径」，一个用于「证明负权环破坏可解性」**。直觉层面：如果存在负权环，沿着环走可以让「势能差」无限减小，而差分约束要求所有 $h(v)-h(u) \le w(u,v)$ 同时成立——环上的势能差「绕一圈回到自己」必须是 0，却被迫 $\le$ 负值，自相矛盾。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#c0392b">**Johnson 算法（续）**</span>

<span style="color:#2471a3;">**[theorem]**</span> 定理（Theorem）：若 $(V, E, w)$ **无**负权环，则我们能找到差分约束的一个解。

**证明（Proof）**：向 $G$ 添加一个新顶点 $s$，并对所有 $v \in V$ 添加权重为 0 的边 $(s, v)$。
- 显然，这些新边不会给图引入任何新的负权环。
- 添加这些新边保证现在**至少存在一条从 $s$ 到 $v$ 的路径**。这意味着对所有 $v \in V$，$\delta(s, v)$ 是有限的。
- 我们现在断言 $h(v) = \delta(s, v)$。这由**三角不等式（triangle inequality）**立得：

```math
\delta(s, u) + w(u, v) \ge \delta(s, v) \iff \delta(s, v) - \delta(s, u) \le w(u, v) \iff h(v) - h(u) \le w(u, v)
```

**时间复杂度（Time complexity）**
1. 第一步涉及从 $s$ 运行 Bellman-Ford，需 $O(VE)$ 时间。还要支付对所有边**重加权（reweight）**的预处理成本（$O(E)$）

> <span style="color:#7f8c8d;">[说明] 本页与下一页是 Johnson 算法的复杂度分解与差分约束的应用。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 定理「无负权环 ⇒ 差分约束有解」的构造性证明是「**把最短路径距离当解**」：加一个超级源 $s$（0 权边连到所有顶点），跑 Bellman-Ford 得到 $\delta(s, \cdot)$，然后断言 $h(v) = \delta(s, v)$ 就是所求。为什么成立？因为 $\delta(s, \cdot)$ 天然满足三角不等式 $\delta(s,u) + w(u,v) \ge \delta(s,v)$，整理后恰好是差分约束 $h(v) - h(u) \le w(u,v)$。这个「**最短路径距离就是差分约束的解**」的双向对偶（下一页的应用会反过来：用 Bellman-Ford 解差分约束系统）是 Johnson 算法最优雅的部分——**重加权问题的解恰好由最短路径计算给出**。注意这个证明也说明了 Johnson 第 1 步用 Bellman-Ford 的深层原因：它既检测负权环（无解），又能在无环时给出 $h$。

---

<!-- ===== PDF p8 ===== -->

<span style="color:#c0392b">**Johnson 算法（续）与应用**</span>

2. 然后对图中的 $V$ 个顶点各运行一次 Dijkstra；这一步的总时间复杂度为 $O(VE + V^2 \lg V)$
3. 然后需要对每对最短路径**重加权还原**，需 $O(V^2)$ 时间

**该算法的总运行时间为 $O(VE + V^2 \lg V)$。**

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**应用（Applications）**</span>

Bellman-Ford 能在 $O(VE)$ 时间内**求解任意差分约束系统**（或报告其无解），其中 $V = $ 变量数、$E = $ 约束数。

一个练习是证明：Bellman-Ford **最小化** $\max_i x_i - \min_i x_i$。

这有以下应用：
- **实时编程（Real-time programming）**
- **多媒体调度（Multimedia scheduling）**
- **时间推理（Temporal reasoning）**

例如，你可以用差分约束**限制一个事件的持续时间**：$LB \le t_{\mathrm{end}} - t_{\mathrm{start}} \le UB$；或**限制事件之间的间隔**：$0 \le t_{\mathrm{start2}} - t_{\mathrm{end1}} \le \varepsilon$；或**同步事件**：$|t_{\mathrm{start1}} - t_{\mathrm{start2}}| \le \varepsilon$ 或 $0$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 差分约束的应用展示了「**算法问题 ⇄ 现实约束**」的双向翻译：把「变量差 $x_j - x_i \le c$」的约束系统翻译成图（约束 $x_j - x_i \le c$ 对应边 $(i, j)$ 权重 $c$），用 Bellman-Ford 求最短路得到一组满足全部约束的赋值。时间推理是经典场景：事件开始/结束时间是变量，「A 在 B 之后至多 $\varepsilon$」是差分约束，Bellman-Ford 解出的最短路径距离给出**最紧凑可行的时间表**。这个「**变量差约束 → 最短路**」的归约（reduction）是算法设计中「把陌生问题翻译成熟知问题」的典范——你在后续的线性规划（LEC 15）、调度问题里会看到更多这类归约。CSAPP 的实时系统中「任务必须满足的时序约束」也正是这个模型。

---

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
