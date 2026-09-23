<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 9（复习课 9）：Complexity: Approximations（复杂度：近似算法）**</span> <span style="color:#7f8c8d;">（April 22, 2015 · 2015 春季学期（Spring 2015）· Massachusetts Institute of Technology（麻省理工学院），Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

<span style="color:#7f8c8d;">本讲内容：Approximation Algorithms: Traveling Salesman Problem（近似算法：旅行商问题）——用 minimum spanning tree（最小生成树）与 Christofides 算法为 metric TSP（度量 TSP）构造近似解，并证明其近似比。</span>

在本节 recitation（复习课）中，我们将研究 Traveling Salesman Problem（旅行商问题，TSP）：给定一个 undirected graph（无向图）$G(V, E)$，其中每条边 $(u, v) \in E$ 带有非负整数费用 $c(u, v)$，找出具有最小费用的 Hamiltonian cycle（哈密顿回路）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Metric TSP（度量 TSP）**</span>

TSP 是一个 NP-complete（NP 完全）问题，因此不存在已知的高效解法（efficient solution）。事实上，对于一般的 TSP 问题，除非 $P = NP$，否则不存在好的 approximation algorithm（近似算法）。不过，对于 metric TSP（度量 TSP），存在一个已知的 2-approximation（2 近似）算法。在 metric TSP 中，费用函数满足 triangular inequality（三角不等式）：

```math
c(u, w) \le c(u, v) + c(v, w) \quad \forall u, v, w \in V.
```

这同时意味着任意 shortest path（最短路径）也满足三角不等式：$d(u, w) \le d(u, v) + d(v, w)$。即便加上这一约束，metric TSP 仍然是 NP-complete（NP 完全）问题。

> <span style="color:#1e8449;">**[note]**</span> 为什么一般 TSP 没有近似算法？关键就在于三角不等式。若费用函数不满足三角不等式，可以把 Hamiltonian cycle（哈密顿回路）判定问题归约进来：对图中存在的边设费用 1、不存在的边设一个极大的费用，那么任何具有常数近似比的算法都能区分「存在哈密顿回路」与「不存在哈密顿回路」两种情况，从而判定哈密顿回路问题，这与 $P = NP$ 等价。因此在 $P \neq NP$ 的前提下，一般 TSP 连常数近似比都无法保证。而度量空间中的最短路径天然满足三角不等式，这正是 metric TSP 能够拥有近似算法的根本原因。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　MST Approximation Algorithm（MST 近似算法）**</span>

当你从 Hamiltonian cycle（哈密顿回路）中移除一条边时，得到的就是一棵 spanning tree（生成树）。我们已知如何高效地求出 minimum spanning tree（最小生成树，MST）。利用这一想法，我们为最小权重的 Hamiltonian cycle（哈密顿回路）构造一个近似算法。

该算法如下：找出 $G$ 以某个节点 $r$ 为根的 minimum spanning tree（最小生成树）$T$。令 $H$ 为从 $r$ 出发对 $T$ 进行 pre-order tree walk（前序树游走）时访问到的顶点列表。返回按 $H$ 的顺序访问各顶点的回路。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.1　Approximation Ratio（近似比）**</span>

现在我们将证明基于 MST 的近似算法是 metric TSP 问题的 2-approximation（2 近似）算法。令 $H^*$ 为图 $G$ 的最优 Hamiltonian cycle（哈密顿回路），并令 $c(R)$ 表示 $R$ 中所有边的总权重。此外，对顶点列表 $S$，令 $c(S)$ 表示按 $S$ 中出现的顺序访问 $S$ 中全部顶点所需边的总权重。

<span style="color:#2471a3;">**[lemma]**</span> <span style="color:#00838f;">**Lemma 1（引理 1）**</span>　$c(T)$ 是 $c(H^*)$ 的一个 lower bound（下界），即 $c(T) \le c(H^*)$。

> <span style="color:#2471a3;">**[proof]**</span> **证明（Proof）：** 从 $H^*$ 中移除任意一条边，得到的就是一棵 spanning tree（生成树）。因此，minimum spanning tree（最小生成树）的权重必然小于 $H^*$ 的权重。

<span style="color:#2471a3;">**[lemma]**</span> <span style="color:#00838f;">**Lemma 2（引理 2）**</span>　对所有 $S' \subset S$，都有 $c(S') \le c(S)$。

<!-- ===== PDF p2 ===== -->

**（续）**

> <span style="color:#2471a3;">**[proof]**</span> **证明（Proof）：** 考虑 $S' = S - \{v\}$。不失一般性（WLOG），假设顶点 $v$ 是从 $S$ 的一个子序列 $u, v, w$ 中被移除的。那么在 $S'$ 中，我们得到的是 $u \to w$ 而非 $u \to v \to w$。由 triangular inequality（三角不等式），我们知道 $c(u, w) \le c(u, v) + c(v, w)$。因此 $c(S)$ 是 non-increasing（非递增）的，且对所有 $S' \subset S$ 都有 $c(S') \le c(S)$。

考虑按 pre-order（前序）方式遍历树时所执行的 walk（游走）$W$。该游走恰好经过每条边两次，即 $c(W) = 2c(T)$。我们还知道，从 $W$ 中去掉重复顶点就得到 $H$。由 Lemma 1（引理 1），我们知道 $c(T) \le c(H^*)$；由 Lemma 2（引理 2），我们知道 $c(H) \le c(W)$。综合起来，我们得到

```math
c(H) \le c(W) = 2c(T) \le 2c(H^*).
```

> <span style="color:#1e8449;">**[note]**</span> 这个基于 MST 的 2-近似算法通常被称为 double-tree（双树）方法，因为 pre-order walk（前序游走）中每条边恰好被走两次，代价正好为 $2c(T)$。它给出的近似比 2 在 1976 年被 Christofides 改进到 $\frac{3}{2}$，此后四十余年里 $\frac{3}{2}$ 一直是 metric TSP 的最佳已知近似比，直到 2020 年 Karlin、Klein 与 Oveis Gharan 证明了近似比为 $\frac{3}{2} - \varepsilon$ 的算法（即改进至严格小于 $\frac{3}{2}$）。值得注意的是，讲义中「去除重复顶点」这一步正是利用 metric 条件下的三角不等式来消除绕路，这是整份分析的关键所在；没有三角不等式，这条游走就无法可靠地缩短。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**3　Christofides Algorithm（Christofides 算法）**</span>

通过略微修改 MST（最小生成树），我们可以改进上述 MST 算法。将图的 Euler tour（欧拉回路）定义为访问图中每条边恰好一次的回路（tour）。

与之前一样，找出 $G$ 以某个节点 $r$ 为根的最小生成树 $T$。计算所有 odd degree（奇度）顶点的最小费用 perfect matching（完美匹配）$M$，并将 $M$ 加入 $T$ 以得到 $T'$。令 $H$ 为 $T'$ 的 Euler tour（欧拉回路）中去除重复顶点后得到的顶点列表。返回按 $H$ 的顺序访问各顶点的回路。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**3.1　Approximation Ratio（近似比）**</span>

我们将证明 Christofides 算法是 metric TSP 问题的 $\frac{3}{2}$-approximation（3/2 近似）算法。我们首先注意到，$T' = T \cup M$ 的 Euler tour（欧拉回路）存在，因为所有顶点的度数都为偶数（even degree）。现在我们给出匹配 $M$ 的费用上界。

<span style="color:#2471a3;">**[lemma]**</span> <span style="color:#00838f;">**Lemma 3（引理 3）**</span>　$c(M) \le \frac{1}{2} c(H^*)$。

> <span style="color:#2471a3;">**[proof]**</span> **证明（Proof）：** 考虑仅由 $T$ 的奇度顶点构成的 TSP 的最优解 $H'$。我们可以通过每隔一条边取一条的方式，将 $H'$ 拆成两个 perfect matching（完美匹配）$M_1$ 和 $M_2$。因为 $M$ 是最小费用完美匹配，我们知道 $c(M) \le \min(c(M_1), c(M_2))$。此外，由于 $H'$ 只访问图的一个子集，故 $c(H') \le c(H^*)$。因此 $2c(M) \le c(H') \le c(H^*)$，即 $c(M) \le \frac{1}{2}c(H^*)$。

$T'$ 的 Euler tour（欧拉回路）的费用为 $c(T) + c(M)$，因为它恰好访问所有边一次。与之前一样（Lemma 1，引理 1），我们知道 $c(T) \le c(H^*)$。结合 Lemma 3（引理 3）与 Lemma 1（引理 1），我们得到 $c(T) + c(M) \le c(H^*) + \frac{1}{2}c(H^*) = \frac{3}{2}c(H^*)$。最后，去除重复顶点会通过 triangular inequality（三角不等式）进一步降低费用。因此，

```math
c(H) \le c(T') = c(T) + c(M) \le \frac{3}{2}c(H^*).
```

> <span style="color:#1e8449;">**[note]**</span> Christofides 算法的关键步骤是对所有奇度顶点求最小费用 perfect matching（完美匹配）。由 handshaking lemma（握手引理），图中奇度顶点的个数必为偶数，故完美匹配一定存在；而最小费用完美匹配可用 Edmonds 的 blossom algorithm（花算法）在多项式时间内求解。给 MST 补上奇度顶点的最小费用匹配后，所有顶点的度数都变为偶数，因而 $T'$ 一定存在 Euler tour（欧拉回路）。引理 3 的本质在于：奇度顶点子集上的最优回路 $H'$ 可拆成两个完美匹配，其中较优的那个至多只有 $H'$ 一半的费用，故匹配 $M$ 的费用不超过 $\frac{1}{2}c(H^*)$。这套「生成树 + 奇度顶点最小完美匹配」的组合正是 Christofides 能同时优于 double-tree 方法（近似比 2）的原因，也是教材 CLRS 第 35 章 Approximation Algorithms 中 metric TSP 一节的核心内容。

<!-- ===== PDF p3 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare（麻省理工学院开放课程）</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms），Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
