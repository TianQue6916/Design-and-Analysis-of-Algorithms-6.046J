<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 12（Lecture 12）：贪心算法与最小生成树（Greedy Algorithms and Minimum Spanning Tree）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- 最优子结构（Optimal Substructure）
- 贪心选择性质（Greedy Choice Property）
- Prim 算法（Prim's algorithm）
- Kruskal 算法（Kruskal's algorithm）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**定义（Definitions）**</span>

回忆：贪心算法（greedy algorithm）反复做出**局部最优**的选择或决策，但**忽略未来的影响**（ignores the effects of the future）。

- **树（tree）**是连通的、无环的图。
- 图 $G$ 的**生成树（spanning tree）**是 $G$ 的边的一个子集，它构成一棵树且包含 $G$ 的所有顶点。
- 最后，**最小生成树问题**：给定无向图 $G = (V, E)$ 与边权 $W : E \to \mathbb{R}$，求**权重最小**的生成树 $T$：$\min_{T} \sum_{e \in T} w(e)$。

**朴素算法（A naive algorithm）**：显然的 MST 算法是计算每棵树的权重，返回权重最小的树。不幸的是，这在最坏情况下可能需要**指数时间**。考虑下面的例子：

如果取图的上方两条边，最小生成树可以由「连接中间顶点到左、右顶点的左/右边的任意组合」构成。因此在最坏情况下，可能存在**指数多个**生成树。

相反，我们考虑用贪心算法和动态规划算法求解 MST。我们将看到，贪心算法可以在**近线性时间**（nearly linear time）内求解 MST。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> MST 问题的定义值得逐词确认：**生成树**是「连通所有顶点 + 无环」的边子集，所以一棵 $n$ 顶点图上的任意生成树都有恰好 $n-1$ 条边（树的性质）；**最小生成树**在所有生成树里挑总权重最小的。Wiki 查证：更一般地，非连通图有「最小生成森林（minimum spanning forest）」——每个连通分量的 MST 之并。MST 是贪心算法最经典的温床：本讲的「最优子结构 + 贪心选择性质」双属性，正是你能用贪心解决它、且能证明正确的理论依据。朴素「枚举所有树」之所以指数级，是因为完全图上的生成树数是 $n^{n-2}$（Cayley 公式，你在 6.042J 图论部分学过）——枚举不可行，必须靠结构。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**贪心算法的性质（Properties of Greedy Algorithms）**</span>

能用贪心算法解决的问题有两个主要性质：
- **最优子结构（Optimal Substructure）**：问题的最优解**包含**子问题的最优解
- **贪心选择性质（Greedy choice property）**：局部最优的选择导致全局最优解

🎥 *Devadas 在视频中[介绍贪心两大性质]*："One of them is called optimal substructure. ... And the other is called the greedy choice property. Optimal substructure should be a familiar idea, because it's essentially an encapsulation [of the DP principle]."（翻译：其中一个叫「最优子结构」，另一个叫「贪心选择性质」。最优子结构应该是熟悉的概念，因为它本质上是对 DP 原则的封装。）——注意「最优子结构」正是 DP 的基石（讲次 10），贪心与 DP 共享它；两者的分水岭在于「贪心选择性质」——贪心每一步不必回头试所有选择。

我们可以看看这些性质如何应用于 MST 问题。

**MST 的最优子结构（Optimal substructure for MST）**

考虑一条边 $e = \{u, v\}$，它是某个 MST 的边。那么我们可以通过**合并**顶点 $u$ 和 $v$ 来**收缩（contract）** $e$，创造一个新顶点。于是任何与顶点 $u$ 或 $v$ 相邻的边都与这个新顶点相邻；若 $u$ 和 $v$ 有共同的邻居，该过程可能产生**多重边（multiedge）**。我们通过「在两个边之间创建一条**权重最小**的单一边」来解决多重边问题。

这引导出下面的引理：

<span style="color:#2471a3;">**[lemma]**</span> **引理 1（Lemma 1）**：若 $T'$ 是 $G/e$ 的最小生成树，则 $T' \cup \{e\}$ 是 $G$ 的 MST。

**证明（Proof）**：设 $T^{*}$ 是 $G$ 的含边 $e$ 的 MST。那么 $T^{*}/e$ 是 $G' = G/\{e\}$ 的生成树。由定义，$T'$ 是 $G'$ 的 MST。因此 $T'$ 的总权重小于等于 $T^{*}/e$ 的总权重，即 $w(T') \le w(T^{*}/e)$。于是

```math
w(T) = w(T') + w(e) \le w(T^{*}/e) + w(e) = w(T^{*})
```

> <span style="color:#7f8c8d;">[说明] 原文末行 OCR 有笔误（$w(T)=w(T')+w(e)$ 应为 $w(T' \cup \{e\})$），语义是「$T' \cup \{e\}$ 的权重 ≤ MST 权重，故它也是 MST」。</span>

该陈述可作为**动态规划算法**的基础：猜测一条属于 MST 的边、收回（retract）该边、递归。最后，解除收缩（decontract）并把 $e$ 加入 MST。引理保证该算法正确。然而，该算法需要指数时间，因为「要猜哪条边构成 MST」有指数多种可能。

**通过去掉猜测过程**，我们让算法变成多项式时间。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 最优子结构的证明是「**收缩-拼接**」论证：若 $e = \{u,v\}$ 属于某个 MST，把 $u, v$ 合并成一个顶点（边 $e$ 变成「内部边」），原问题 $G$ 的最优解就对应缩小图 $G/e$ 的最优解加回 $e$。这正是 DP 需要的「子问题最优 → 原问题最优」结构。但本讲的关键洞察是：**DP 在此不划算**——因为「猜哪条边属于 MST」有指数种选择（这正是贪心 vs DP 的分野：DP 猜「哪条边」，贪心猜「哪一步」）。这个「先想 DP、再发现贪心更快」的叙事在算法课上很常见：**DP 提供正确性基线，贪心提供效率**。注意收缩时多重边取最小权重的处理——如果 $u, v$ 有共同邻居 $w$，那么 $(u,w)$ 与 $(v,w)$ 收缩后都变成「新顶点-$w$」边，保留较轻的那条（因为重的那条可被轻者替换而不产生环，由交换论证/剪切粘贴保证其不在 MST 中）。这是你 6.042J 图论里「边收缩」概念的算法化。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**贪心选择性质（Greedy Choice Property）**</span>

MST 问题可以用贪心算法求解，因为**局部最优解也是全局最优解**。这一事实由 MST 的**贪心选择性质**描述，其正确性证明通过贪心证明常用的「**剪切粘贴（cut and paste）**」论证给出。

<span style="color:#2471a3;">**[lemma]**</span> **引理 2（Lemma 2，MST 的贪心选择性质）**：对图 $G = (V, E, w)$ 中的任意**割（cut）** $(S, V \setminus S)$，任何**最轻的跨越边（least-weight crossing edge）** $e = \{u, v\}$（$u \in S$ 且 $v \notin S$）属于 $G$ 的某个 MST。

**证明（Proof）**：首先，考虑 $G$ 的一个 MST $T$。那么 $T$ 有一条从 $u$ 到 $v$ 的路径。因为 $u \in S$ 且 $v \notin S$，该路径有某条边 $e' = \{u', v'\}$ 也跨越该割。于是 $T' = T \setminus \{e'\} \cup \{e\}$ 是 $G$ 的生成树，且 $w(T') = w(T) - w(e') + w(e)$。但 $e$ 是跨越 $(S, V \setminus S)$ 的最轻边，所以 $w(e) \le w(e')$，故 $w(T') \le w(T)$。因此 $T'$ 也是一个 MST。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 贪心选择性质的证明是「**剪切粘贴（cut and paste）**」模板的完美示范——这也是你在讲次 1 区间调度里第一次见到的论证法的「图论版」。推理链条：① 任意 MST $T$ 中，$u$ 到 $v$ 的路径必然跨越割（因为 $u, v$ 分居割两侧），所以路径上至少有一条跨越边 $e'$；② 用最轻跨越边 $e$ **替换** $e'$（剪切 $e'$、粘贴 $e$），得到新生成树 $T'$；③ 因为 $e$ 不重于 $e'$，$T'$ 不重于 $T$，所以 $T'$ 也是 MST。这个「**找到一条可替换的边，用更轻的边换掉它，证明权重不增**」的论证，是「贪心选择正确」的通用证明骨架——它不依赖任何先验，只依赖「割」的跨越性质。注意割（cut）是图论的核心概念：把顶点分成两部分 $(S, V \setminus S)$，「跨越边」就是连接两部分的边；MST 的贪心本质是「**每个割的最轻边都必须被选（或至少有一个 MST 选它）**」。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**Prim 算法（Prim's Algorithm）**</span>

现在，我们可以把最优结构与贪心选择性质的洞见应用到**多项式时间**的贪心算法上，来求解最小生成树问题。

**Prim 算法伪代码（Prim's Algorithm Pseudocode）**

```
1  在 V \ S 上维护优先队列 Q，其中 v.key = min{ w(u, v) | u ∈ S }
2  Q = V
3  选择任意起始顶点 s ∈ V，s.key = ∅
4  for v in V \ {s}
5      v.key = ∞
6  while Q 非空
7      u = Extract-Min(Q)，把 u 加入 S
8      for v ∈ Adj[u]
9          if v ∈ Q 且 v ∉ S 且 w(u, v) < v.key:
10             v.key = w(u, v)（通过 Decrease-Key 操作）
11             v.parent = u
12 return { {v, v.parent} | v ∈ V \ {s} }
```

在上述伪代码中，我们选择一个任意起始顶点，尝试**依次减小到所有顶点的距离**。在尝试找到连接所有顶点的最低权重边后，返回我们的 MST。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Prim 算法是「**从单个顶点长出一棵树**」的贪心：始终维护已选顶点集 $S$ 和一棵当前树 $T_S$，每一步都选「从 $S$ 跨出到 $V \setminus S$ 的最轻边」加入。这与 Dijkstra 算法（单源最短路）的骨架几乎相同——区别只在 `v.key` 的语义：Dijkstra 存「从源到 $v$ 的最短距离」，Prim 存「从当前树 $S$ 到 $v$ 的最轻边权」。两者的正确性都依赖「割的贪心选择性质」：Prim 每一步选的边都是「跨越 $S$ 与 $V \setminus S$ 的最轻边」，由引理 2 它在某个 MST 里。Wiki 查证：Prim 算法 1930 年由捷克数学家 **Vojtěch Jarník** 提出，1957 年由 Robert C. Prim、1959 年由 Dijkstra 各自重新发现，因此也叫 **Jarník / Prim-Dijkstra（DJP）算法**——又一个「同算法多名字」的历史案例。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**Prim 算法（续）**</span>

**正确性（Correctness）**：我们用下面的不变量证明 Prim 算法的正确性。
1. $v \notin S \Rightarrow v.\mathrm{key} = \min\{ w(u, v) \mid u \in S \}$
2. $S$ 内的树 $T_S \subseteq $ $G$ 的某个 MST。

第一个不变量由上面的算法第 8 步得出。第二个不变量的证明如下：

**证明（Proof）**：归纳假设 $T_S \subseteq$ MST $T^{*}$。那么 $S \to S' \cup \{e\}$，其中 $e$ 是跨越割 $(S, V \setminus S)$ 的最轻边。于是我们可以贪心地**剪切粘贴** $e$，这意味着我们可以修改 $T^{*}$ 使其包含 $e$ 而不移除 $T_S$，因为 $T_S$ 的边**不跨越割**。因此 $T_S \cup \{e\} = T_{S'} \subseteq T^{*}$。

因此，Prim 算法总是添加权重最低的边，逐步构建一棵**始终是某个 MST 子集**的树，并返回正确结果。

**运行时间（Runtime）**

Prim 算法运行时间为

```math
O(V) \cdot T_{\text{Extract-Min}} + O(E) \cdot T_{\text{Decrease-Key}}
```

$O(E)$ 项来自：第 8 步重复的次数等于图中相邻顶点数之和，由**握手引理（handshaking lemma）**等于 $2|E|$。

算法实际运行时间随实现优先队列所用数据结构的不同而变化。下表描述不同优先队列实现下的运行时间：

| 优先队列（Priority Queue） | $T_{\text{Extract-Min}}$ | $T_{\text{Decrease-Key}}$ | 总计（Total） |
|:---|:---|:---|:---|
| 数组（Array） | $O(V)$ | $O(1)$ | $O(V^2)$ |
| 二叉堆（Binary heap） | $O(\lg V)$ | $O(\lg V)$ | $O(E \lg V)$ |
| 斐波那契堆（Fibonacci heap）[CLRS ch.19] | $O(\lg V)$（摊还 amortized） | $O(1)$（摊还） | $O(E + V \lg V)$ |

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Prim 的复杂度分析是「**算清楚每个操作被调用多少次**」的示范：`Extract-Min` 恰被执行 $O(V)$ 次（每个顶点出队一次），`Decrease-Key` 被调用的总次数是「所有邻接关系扫描」的总和，由握手引理（所有顶点的度之和 $= 2|E|$）知为 $O(E)$ 次。于是总时间 $= V \times$ ExtractMin + $E \times$ DecreaseKey。优先队列的选择决定了每项成本：数组版 $V \times V + E \times 1 = O(V^2)$（稠密图友好）；二叉堆版 $V \lg V + E \lg V = O(E \lg V)$；斐波那契堆把 Decrease-Key 摊还到 $O(1)$（讲次 5 摊还分析的实际应用），得 $O(E + V \lg V)$（稀疏图友好）。**稠密选数组、稀疏选堆**——这个「按图密度选数据结构」的权衡，是你在后续算法里反复遇到的工程判断。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**Kruskal 算法（Kruskal's Algorithm）**</span>

Kruskal 算法是求解 MST 问题的另一算法。它通过**取全局最轻的边并收缩它**来构造 MST。

**Kruskal 算法伪代码（Kruskal's Algorithm Pseudocode）**

```
1  在 Union-Find 结构中维护「已加入 MST 的连通分量」T
2  初始化 T = ∅
3  for v in V
4      Make-Set(v)
5  按权重对 E 排序
6  for e = (u, v) ∈ E（按权重递增顺序）:
7      if Find-Set(u) ≠ Find-Set(v):
8          把 e 加入 T
9          Union(u, v)
```

**正确性（Correctness）**：我们用下面的不变量证明 Kruskal 算法的正确性。

<span style="color:#2471a3;">**[claim]**</span> **声明 3（Claim 3）**：目前的树 $T \subseteq$ MST $T^{*}$。

**证明（Proof）**：我们给出归纳证明。先假设目前的树 $T \subseteq T^{*}$（归纳假设）。当我们把边 $e$ 加入某个连通分量 $C_1$ 与 $C_2$ 之间时，我们在割 $(C_1, V \setminus C_1)$ 上使用**贪心选择性质**（因为 $e$ 连接 $C_1$ 与 $C_2$，它跨越割 $(C_1, V \setminus C_1)$；原讲义写作 $(C_1, V \setminus C_2)$ 记号不严谨，这里按正确割修正）。因此我们在**不移除 $T$** 的情况下加入了边，新的「目前之树」仍然是 MST $T^{*}$ 的子集。

**运行时间（Runtime）**：Kruskal 算法的总运行时间为

```math
T_{\text{sort}}(E) + O(V) \cdot T_{\text{Make-Set}} + O(E)(T_{\text{Find}} + T_{\text{Union}}) = O(E \lg E + E \alpha(V))
```

我们注意到，对 Union-Find 数据结构，$T_{\text{Make-Set}}$ 是 $O(1)$，$T_{\text{Find}} + T_{\text{Union}}$ 摊还 $O(\alpha(V))$。

此外，若所有权重都是整数权重、或所有权重都在区间 $[0, E^{O(1)}]$ 内，那么排序步骤的运行时间为 $O(E)$（用计数排序 Counting Sort 或类似算法），此时 Kruskal 算法的运行时间会**优于 Prim 算法**。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Kruskal 与 Prim 的对比值得记住：**Prim 从「点」长树（每次并入一个顶点），Kruskal 从「边」并森林（每次加入一条不产生环的最轻边）**。Kruskal 的正确性用「连通分量割」：一条边 $e$ 连接两个不同的分量 $C_1, C_2$，则 $e$ 跨越割 $(C_1, V \setminus C_2)$，且因为是「全局递增序里第一条连接它们的边」，$e$ 是该割的最轻跨越边——由贪心选择性质它在某个 MST 里。实现依赖**并查集（Union-Find）**：`Make-Set` $O(1)$、`Find`/`Union` 摊还 $O(\alpha(V))$（$\alpha$ 是反阿克曼函数，增长极慢，实用中 ≤ 4）——这是你在 6.006 学过的并查集的摊还分析（讲次 5 的收费法在并查集上的体现）。注意「计数排序让排序变 $O(E)$」的细节：当权重是有限范围的整数时，排序可以突破比较排序的 $\Omega(E \lg E)$ 下界——这呼应了你在 CSAPP/6.006 里学过的「整数排序可以比比较排序快」。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b">**其他 MST 算法（Other MST Algorithms）**</span>

目前，最快的 MST 算法是**随机化算法**，期望运行时间为 $O(V + E)$。该算法由 **Karger、Klein 与 Tarjan** 于 **1993 年**提出。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Karger-Klein-Tarjan（1993）的 $O(V+E)$ 期望时间 MST 算法是「随机化 + 收缩」的杰作：它**反复随机采样边来识别「几乎肯定不属于 MST」的边并丢弃**，再用「收缩 + 递归」缩小图，最终期望线性时间。注意它是**期望**线性——最坏情形仍可能超线性，这正是随机化算法的特征（讲次 6 的「期望 vs 最坏」）。这个算法把本讲的两个核心思想——贪心选择性质（识别必然在/不在 MST 的边）与边收缩（最优子结构）——结合随机化发挥到极致。此后又有确定性近线性算法（如 Chazelle 的 $O(E \, \alpha(E,V))$），但期望线性仍是 MST 的理论里程碑。对绝大多数实际场景，Prim + 二叉堆或 Kruskal + 并查集已经足够快，$O(V+E)$ 是理论边界的展示。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
