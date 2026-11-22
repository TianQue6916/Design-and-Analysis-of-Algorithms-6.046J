<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 8（复习课 8）：Complexity: More Reductions（复杂度：更多归约）**</span> <span style="color:#7f8c8d;">（April 10, 2015 · 2015 春季学期（Spring 2015）· Massachusetts Institute of Technology（麻省理工学院），Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

<span style="color:#7f8c8d;">本讲内容：NP-Complete Problems（NP 完全问题）——承接 Lec 16（Complexity: P, NP, NP-completeness, Reductions），通过更多归约实例演示如何证明新问题的 NP 完全性。</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Definitions（定义）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**P（多项式时间可判定类）:**</span> 所有 decision problems（判定问题）$D$ 的集合，满足存在一个 polynomial time algorithm（多项式时间算法）$A$ 使得 $A(x) = D(x)$。我们把 polynomial time algorithm 视为 "efficient"（高效的）。

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**NP（非确定性多项式时间可验证类）:**</span> 所有 decision problems $D$ 的集合，满足存在一个 polynomial time verification algorithm（多项式时间验证算法）$V$，使得对一切输入 $x$，$D(x) = 1$ 当且仅当存在一个 polynomial-length（多项式长度的）"certificate"（证书）$y$，满足 $V(x, y) = \text{True}$。

为了比较求解不同问题的 "hardness"（难度），我们使用 reductions（归约）！其想法是：如果我有两个问题 $A$ 和 $B$，并且我能证明可以通过一个求解 $B$ 的 black box（黑盒）来求解 $A$，那么我就能用「求解 $B$ 的难度，加上把 $B$ 的一个解转化为 $A$ 的一个解所需的工作量」来理解求解 $A$ 的难度。

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**"Karp"-Reduction（Karp 归约）:**</span> 设 $A : X \to \{0, 1\}$ 与 $B : Y \to \{0, 1\}$ 是 decision problems。若存在一个函数 $R : X \to Y$，把 $A$ 的输入变换为 $B$ 的输入，使得 $A(x) = B(R(x))$，则称 $A$ 是 polytime reducible to $B$（多项式时间可归约到 $B$ 的），或称 "$A$ reduces to $B$"（$A$ 归约到 $B$），记为 $A \propto B$。下面这幅图展示了这种归约如何导出一个直接建立在 $B$ 的算法之上、求解 $A$ 的算法。

求解 $A$ 不比求解 $B$ 更难。换言之，如果求解 $B$ 是 "easy"（容易的）（即 $B \in P$），那么求解 $A$ 也是容易的（$A \in P$）。等价地，如果 $A$ 是 "hard"（困难的），那么 $B$ 也是困难的。给定求解 $B$ 的算法，我们就能轻松地构造出求解 $A$ 的算法。

> <span style="color:#7f8c8d;">（原文此处有一幅归约示意图：$A$ 的输入 $x$ 经函数 $R$ 变换为 $B$ 的输入 $R(x)$，交给求解 $B$ 的算法，再把结果作为 $A(x)$ 的输出。图片统一后处理，此处不插入。）</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**NP-Hard（NP 困难）:**</span> 一个 decision problem $D$ 是 NP-Hard 的，如果 NP 中的所有问题 $Q$ 都可以在多项式时间内归约到它（对所有 $Q \in NP$ 有 $Q \propto D$）。也就是说，给定一个求解 NP-Hard 问题 $D$ 的高效算法，我们就可以为 NP 中的任何问题构造出高效算法。

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**NP-Complete（NP 完全）:**</span> 一个 decision problem $D$ 是 NP-Complete 的，如果它属于 NP 且是 NP-Hard 的。

> <span style="color:#1e8449;">**[note]**</span> 复习课采用的是 Karp reduction（Karp 归约），它要求在多项式时间内把 $A$ 的每个实例变换成 $B$ 的一个实例，且 yes/no 答案完全保持一致（$A(x) = B(R(x))$）。与之相对的是条件更宽松的 Cook reduction（Cook 归约），后者允许在求解过程中任意多次调用 $B$ 的 oracle（预言机）。整个 NP 完全性大厦的奠基是 Cook-Levin Theorem（Cook–Levin 定理，1971 年）：它证明 Boolean satisfiability（布尔可满足性，SAT）是 NP 完全的；此后的归约链（SAT → 3SAT → Clique → Vertex Cover / Hamiltonian Cycle 等）全靠 Karp 归约的传递性一层层铺开。归约方向很容易记反，务必注意：要证明新问题「难」，应当从已知的难问题归约到新问题（对所有 $Q \in NP$ 有 $Q \propto D$），即新问题必须「难得住」NP 里的每一个问题，而不是反过来。这套内容与 CLRS 第 34 章（NP-Completeness）的 34.1–34.3 节一一对应。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　Reducing Hamiltonian Cycle to Hamiltonian Path（把哈密顿回路归约到哈密顿路径）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Hamiltonian Cycle（哈密顿回路）:**</span> 给定有向图 $G = (V, E)$，是否存在一条恰好访问每个顶点一次的 cycle（回路）？

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Hamiltonian Path（哈密顿路径）:**</span> 给定有向图 $G = (V, E)$，是否存在一条恰好访问每个顶点一次的 path（路径）？

已知 Hamiltonian Cycle 是 NP-Complete 的，我们据此证明 Hamiltonian Path 也是 NP-Complete 的。

**1. 证明 Hamiltonian Path $\in$ NP**

要证明这一点，我们需要证明存在一个 verifier（验证器）$V(x, y)$。设 $x = G$ 是一个 "yes"（是）输入，设 $y$ 是一条满足条件的路径 $P$。

我们可以验证该路径恰好遍历每个顶点一次，然后检查路径上的每一条边都是图中的边。naive（朴素地）验证每个顶点恰好被遍历一次需要 $O(n^2)$ 时间，检查路径中的每条边都在图中需要 $O(nm)$ 时间。

**2. 证明 Hamiltonian Path $\in$ NP-Hard**

我们通过给出 Hamiltonian Cycle 到 Hamiltonian Path 的一个 Karp-reduction（Karp 归约）来证明这一点。

(a) 给定 Hamiltonian Cycle 的一个实例 $G$，任选一个节点 $v$，把它 split（拆分）成两个节点 $v'$ 和 $v''$。所有指向 $v$ 的有向边现在都以 $v'$ 为端点，所有从 $v$ 出发的边则改从 $v''$ 出发。我们称这个新图为 $G'$。该变换至多需要 $O(E)$ 时间。

(b) 若 $G$ 中存在 Hamiltonian Cycle，则 $G'$ 上存在 Hamiltonian Path。我们可以把 $G$ 上环的边用作 $G'$ 上的路径，但我们的路径必须从 $v''$ 出发、在 $v'$ 结束。

(c) 若 $G'$ 上存在 Hamiltonian Path，则 $G$ 中存在 Hamiltonian Cycle。由于没有边进入 $v''$，也没有边离开 $v'$，该路径必然从 $v''$ 开始、在 $v'$ 结束。因此，一旦把 $v'$ 和 $v''$ 重新合并，我们就可以把 $G'$ 上的路径用作 $G$ 上的环。

**3.** 这就证明了 Hamiltonian Cycle 在多项式时间内归约到 Hamiltonian Path，也就是说 Hamiltonian Path 至少和 Hamiltonian Cycle 一样难，因此 Hamiltonian Path 是 NP-Complete 的。

> <span style="color:#1e8449;">**[note]**</span> Hamiltonian Cycle 与 Hamiltonian Path 都是 CLRS 第 34 章列出的经典 NP 完全问题，也是 Karp 1972 年开创性论文《Reducibility Among Combinatorial Problems》所列 21 个 NP 完全问题中的成员。本页「拆点」技巧的核心思想是：把一个顶点 $v$ 拆成两个「半顶点」，一个只接收入边（$v'$）、一个只发出出边（$v''$），从而打破「环」的闭合性，迫使任何遍历所有顶点的路径恰好从一个半顶点出发、在另一个半顶点终止——环与路径由此一一对应。这个归约只做 local modification（局部修改），几乎不改变图的结构，与后面 3DM → 4-Partition 那种需要精细数值编码的归约形成鲜明对比。这里讨论的是有向图版本；无向图情形也有同样结论（把每条无向边替换为两条反向边即可）。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**3　Reducing 3-Dimensional Matching to 4-Partition（把三维匹配归约到四划分）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**3DM（三维匹配，3-Dimensional Matching）:**</span> 给定三个两两不相交的集合 $X, Y, Z$（每个集合含 $n$ 个元素），以及三元组集合 $T \subset X \times Y \times Z$，是否存在一个子集 $S \subset T$，使得每个元素 $a \in X \cup Y \cup Z$ 恰好出现在某个 $s \in S$ 中？

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**4-Partition（四划分）:**</span> 给定 $n$ 个整数 $\{a_1, a_2, \ldots, a_n\}$，能否把它们划分成 $n/4$ 个各含 4 个元素的子集，使得每个子集的和都等于同一个值 $t = \frac{A}{n/4} = \frac{4A}{n}$（其中 $A$ 为所有整数之和）？每个整数满足 $a_i \in (\frac{t}{5}, \frac{t}{3})$（这保证每个子集恰好含 4 个元素）。

> <span style="color:#1e8449;">**[mathtip]**</span> 这里 $n$ 是整数的个数（必须是 4 的倍数），分成 $n/4$ 组、每组恰好 4 个数。若所有整数之和为 $A$，则每组的目标和满足 $(n/4) \cdot t = A$，即 $t = \frac{A}{n/4} = \frac{4A}{n}$——这正是原文中 $t = A/n$ 与附近那个被拆行丢失的「$4$」（分母的 $n/4$）的含义。约束 $a_i \in (t/5, t/3)$ 的作用是「尺寸锁定」：任意 3 个数之和 $< 3 \cdot (t/3) = t$，任意 5 个数之和 $> 5 \cdot (t/5) = t$，因此任何和为 $t$ 的子集必然恰好含 4 个数，杜绝了「3 个数或 5 个数凑成 $t$」的歧义。

已知 3DM 是 NP-Complete 的，我们据此证明 4-Partition 是 NP-Complete 的。

**1. 证明 4-Partition $\in$ NP**

我们可以通过检查每个划分中的元素是否求和等于 $t$、且没有元素被使用超过一次，来验证一个可能的解。朴素验证至多需要 $O(n^2)$ 时间。

**2. 证明 4-Partition $\in$ NP**

> <span style="color:#1e8449;">**[mathtip]**</span> 原文此处写的是「Show 4-Partition $\in$ NP」，与第 1 步完全重复，属于印刷笔误。从整体结构看（第 1 步验证 $\in$ NP，第 2 步做 Karp 归约，第 3 步下结论 NP-hard），此处显然应为「Show 4-Partition $\in$ NP-Hard」。

我们通过给出 3DM 到 4-Partition 的一个 Karp-reduction 来证明这一点。

(a) 给定 3DM 的输入 $X, Y, Z$ 与三元组 $T$，我们以 $r$ 为基数（base）构造数字，其中 $r = 100 \cdot |X \cup Y \cup Z|$。注意 $N[x_i]$ 表示元素 $x_i$ 在 $T$ 的三元组中出现的次数。

然后我们构造 "actual"（真实）数字，作为 $n$ 个整数集合的一部分：

i. 对每个元素 $x_i \in X$：$10r^4 + ir^3 + 1$

ii. 对每个元素 $y_j \in Y$：$10r^4 + jr^2 + 2$

iii. 对每个元素 $z_k \in Z$：$10r^4 + kr + 4$

我们还在 $n$ 个整数的集合中加入以下 "dummy"（哑）数字：

i. 对每个元素 $x_i \in X$：$N[x_i] - 1$ 份数字 $11r^4 + ir^3 + 1$

ii. 对每个元素 $y_j \in Y$：$N[y_j] - 1$ 份数字 $11r^4 + jr^2 + 2$

iii. 对每个元素 $z_k \in Z$：$N[z_k] - 1$ 份数字 $8r^4 + kr + 4$

最后，为 $T$ 中的每个三元组添加一个 "triple"（三元组）数字：

i. 对每个三元组 $(x_i, y_j, z_k)$：$10r^4 - ir^3 - jr^2 - kr + 8$

我们设目标 sum（和）$t = 40r^4 + 15$，它可以通过一个 "triple element"（三元组元素）$10r^4 - ir^3 - jr^2 - kr + 8$ 加上三个 "actual elements"（真实元素）来实现，或者由一个 "triple element" 加上三个 "dummy elements"（哑元素）来实现。此外，我们把 $r$ 选得足够大，使得任何其他 "actual"、"dummy" 或 "triple" 元素的组合都无法达到目标和。

(b) 若 $S \subset T$ 是 3DM 的一个解，我们就可以构造出一个 4-Partition 的解。对每个三元组 $(x_i, y_j, z_k) = s \in S$，我们用对应的 "triple element" 以及与 $x_i, y_j, z_k$ 对应的三个 "actual elements" 组成一个 4 元素集合，其和恰为 $40r^4 + 15$。剩余的匹配则对应这样的划分：一个 "triple" 元素加上与 $x_i, y_j, z_k$ 对应的三个 "dummy" 元素，其和仍为 $40r^4 + 15$。于是我们就能组成若干 4 元素集合，得到一个 4-Partition。

> <span style="color:#1e8449;">**[note]**</span> 这一页展示了数值型归约的典型手法——用大基数 $r$ 的各个「数位」来打包多种离散信息：低位常数项（$1/2/4/8$）标记元素属于 $X/Y/Z$ 还是 triple 元素；$r, r^2, r^3$ 位编码元素在各自集合内的下标 $i/j/k$；$r^4$ 位区分 actual（$10$）、dummy（$11$ 或 $8$）与 triple（$10$）元素。$r = 100 \cdot |X \cup Y \cup Z|$ 取足够大，是为了防止任何进位（carry）污染高一位，这正是归约设计中 padding（填充/留足余量）思想的体现。把 $t = 40r^4 + 15$ 拆开看：$40r^4$ 由三个 actual（$10+10+10$）或三个 dummy（$11+11+8$）与 triple（$10$）共同凑成，而 $+15$ 则由 $8+4+2+1$（一个 triple 加三种类型各一个元素）实现，两个约束共同锁死了四元组的构成。最后值得注意：4-Partition 属于 strong NP-complete（强 NP 完全）问题，即使把数字改写成 unary（一元）编码它仍是 NP 完全的；与之对照，普通的 Partition 或 Subset Sum 属于 weak（弱）NP 完全问题，存在伪多项式时间算法（例如 $O(n \cdot \text{sum})$ 的动态规划）。这一区分在 Garey & Johnson 的经典著作《Computers and Intractability: A Guide to the Theory of NP-Completeness》（1979）中有系统讨论。

---

<!-- ===== PDF p4 ===== -->

**（续）**

(c) 假设给定一个 4-Partition 的解。考虑其中的任意一个 4 元素集合。通过考察元素大小之和模 $r, r^2, r^3, r^4$ 和 $r^5$ 的余数，我们可以证明：这个集合恰好包含与某个三元组的每个成员对应的元素，且这三个元素要么全是 "actual" 元素，要么全是 "dummy" 元素。如果该三元组只含 "actual elements"，它就是 3DM 解 $S \subset T$ 的一部分，否则不是。

设 $B$ 为这四个元素之和，我们知道 $B \bmod r = 15$。这仅在四个元素对应一个 triple element、且分别来自 $X, Y, Z$ 各一个元素时才可能。

和 $B \bmod r^2 = 0 \cdot r + 15$，这仅在 triple element 与 $z_k$ 对应的 dummy 或 actual 元素匹配时才可能，从而保证我们的集合使用一个 triple element 和与之对应的 $z_k$ 元素。

和 $B \bmod r^3 = 0 \cdot r^2 + 15$，这仅在 triple element 与 $y_j$ 对应的 dummy 或 actual 元素匹配时才可能，从而保证我们的集合使用一个 triple element 和与之对应的 $y_j$ 元素。

和 $B \bmod r^4 = 0 \cdot r^3 + 15$，这仅在 triple element 与 $x_i$ 对应的 dummy 或 actual 元素匹配时才可能，从而保证我们的集合使用一个 triple element 和一个 $x_i$ 元素。

和 $B \bmod r^5 = 40r^4 + 15$，这仅在使用一个 triple element、且只用了三个 dummy 元素或三个 actual 元素时才可能。

因此，我们可以保证：若给定一个 4-Partition，则划分中的每个集合要么对应 $S$ 中的一个 3DM matching（匹配），要么对应一个未被使用的 matching。

**3.** 这就证明了 3DM 在多项式时间内归约到 4-Partition，也就是说 4-Partition 至少和 3DM 一样难，因此 4-Partition 是 NP-hard 的。

> <span style="color:#1e8449;">**[note]**</span> 这一段的「逐位模数校验」是前面数值构造的逆过程：因为 $r$ 足够大，任何下标不匹配的组合都会在相应数位留下非零系数（例如 $z$ 元素若取下标 $l \ne k$，会在 $r$ 位上留下 $(l - k)r$ 项），从而无法满足 $B \bmod r^k$ 的期望值。检查 $B \bmod r, r^2, r^3, r^4, r^5$ 就是从最低位开始逐位「解码」四个元素的身份：模 $r$ 锁定「一个 triple + 三种类型各一」，模 $r^2/r^3/r^4$ 依次锁定下标 $k/j/i$ 匹配，模 $r^5$ 锁定类型齐一（全 actual 或全 dummy）。这种「数字位即信息位、证明时按位解锁」的模式可以提炼成可复用模板：设计编码时先明确每个数位的职责（类型、下标、来源集合），正确性证明再通过取模逐层验证——凡是在归约构造中出现大基数 $r$ 的地方，几乎都是这套思路。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**4　Reducing Clique to Independent Set（把团归约到独立集）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Clique（团）:**</span> 给定图 $G = (V, E)$ 和整数 $k$，是否存在一个顶点集合 $C \subseteq V$，满足 $|C| = k$ 且其中的顶点构成一个 complete graph（完全图）？

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Independent Set（独立集）:**</span> 给定图 $G = (V, E)$ 和整数 $k$，是否存在一个顶点集合 $I \subseteq V$，满足 $|I| = k$，使得对任意 $u, v \in I$ 都有 $(u, v) \notin E$？

已知 Clique 是 NP-Complete 的，我们据此证明 Independent Set 是 NP-complete 的。

**1. 证明 Independent Set $\in$ NP**

要证明这一点，我们需要证明存在一个验证器 $V(x, y)$。设 $x = (G, k)$ 是一个 "yes" 输入，设 $y$ 是满足条件的 $I$。

检查 $|I| = k$ 需要 $O(|I|)$ 时间。检查对每一对 $u, v \in I$ 都有 $(u, v) \notin E$ 需要 $O(|I|^2)$ 时间。这就在多项式时间内验证了证书 $y$ 证明 $x$ 是一个有效输入。因此，Independent Set 属于 NP。

**2. 证明 Independent Set $\in$ NP-hard**

我们通过给出 Clique 到 Independent Set 的一个 Karp-reduction 来证明这一点。

(a) 给定 Clique 的一个输入 $x = (G, k)$，构造输入 $G'$：它与 $G$ 有相同的顶点，但包含边 $(u, v)$ 当且仅当 $(u, v) \notin E$。这需要 $O(|E|)$ 时间，因此该归约是多项式时间的。

(b) 若 $I$ 是 $G'$ 的一个形成 $k$-Independent Set 的顶点集合，则 $C = I$ 是 $G$ 的一个 $k$-Clique，因为对 $u, v \in I$，Independent Set 保证 $(u, v) \notin E'$，而由构造方式，这蕴含对 Clique 问题有 $(u, v) \in E$。这表明 $C$ 中每对节点之间都有边。此外 $|C| = k$，因此 $C$ 是一个 $k$-clique。

(c) 若 $C$ 是 $G$ 中一个形成 $k$-Clique 的顶点集合，则 $I = C$ 是 $G'$ 的一个 $k$-Independent set。这是因为 $u, v \in C$ 蕴含对 Clique 有 $(u, v) \in E$，而这又蕴含对 Independent Set 有 $(u, v) \notin E'$。由于 $|I| = |C| = k$，这表明构造出的 $G'$ 中有 $k$ 个两两不相邻的元素。

**3.** 这就证明了 Clique 在多项式时间内归约到 Independent Set，也就是说 Independent Set 至少和 Clique 一样难，因此 $k$-Independent Set 是 NP-hard 的。

> <span style="color:#1e8449;">**[note]**</span> 这个归约是最简单的 Karp 归约之一，它利用的正是 complement graph（补图）关系：$G$ 中的 clique 恰好是补图 $\bar{G}$ 中的 independent set，反之亦然。由于补图只需补边、可在 $O(|E|)$ 时间内构造，整个归约几乎没有「计算成本」。更有意思的是，Clique、Independent Set 与 Vertex Cover 三者在补集/补图关系下两两相通（顶点覆盖正是独立集在 $V$ 上的补集），构成 NP 完全性教学里的「铁三角」，它们经常被放在一起证明；相关内容见 CLRS 第 34 章 34.5 节（团与顶点覆盖部分）。值得注意的是，这种「取补」式归约只能用于这类结构自对偶的问题；对 Hamiltonian Cycle 那种结构迥异的难题，就必须回到拆点、数值编码等更精巧的手段——这也正是本复习课把两类归约并列展示的原因：归约的「形状」应当反映两个问题之间的结构相似性。


<!-- ===== PDF p6 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**5　Reducing Vertex Cover to Set Cover（把顶点覆盖归约到集合覆盖）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Vertex Cover（顶点覆盖）:**</span> 给定图 $G = (V, E)$ 和整数 $k$，是否存在集合 $Y \subseteq V$，使得 $|Y| = k$，且对每条边 $(u, v) \in E$，要么 $u \in Y$ 要么 $v \in Y$（或两者都在）？

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Set Cover（集合覆盖）:**</span> 给定一个由 $n$ 个元素组成的集合 $S = \{1, 2, \ldots, n\}$ 以及 $m$ 个集合 $S_1, \ldots, S_m$（其中 $S_i \subseteq S$），是否存在 $k$ 个集合 $S_{i_1}, \ldots, S_{i_k}$，使得 $S_{i_1} \cup \cdots \cup S_{i_k} = S$？

已知 Vertex Cover 是 NP-complete 的，我们据此证明 Set Cover 也是 NP-Complete 的。

**1. 证明 Set Cover $\in$ NP**

要证明这一点，我们需要证明存在一个 verifier（验证器）$V(x, y)$。设 $x = S, S_1, \ldots, S_m$ 是一个 "yes"（是）输入，设 $y$ 是满足条件的 $S_{i_1}, \ldots, S_{i_k}$。在 $O(k)$ 时间内，我们可以判断是否恰好有 $k$ 个集合；在 $O(kn)$ 时间内，我们可以判断 $S$ 中的所有元素是否都出现在并集中。这就证明了存在一个 polynomial time verifier（多项式时间验证器），也就意味着 Set Cover 属于 NP。

**2. 证明 Set Cover $\in$ NP-Hard**

要证明这一点，我们把 Vertex Cover 归约到 Set Cover。

(a) 对 Vertex Cover 的一个输入 $x = (G, k)$，我们构造 $R(x) = S, S_1, \ldots, S_m$。设 $S$ 为所有边 $e_j \in E$ 组成的集合。对每个 $v_i \in V$，创建集合 $S_i$，它包含所有与 $v_i$ 相连（touch）的边 $e_j$。这个新输入是多项式大小的，因为 $|S| = |E|$、每个集合 $S_i$ 的大小至多为 $|E|$、且共有 $|V|$ 个集合。

(b) 若存在 $k$-Vertex Cover，则存在 $k$-Set Cover。若顶点 $v_i \in V'$ 属于顶点覆盖，则 $S_i$ 属于集合覆盖。由于每条边 $e_j \in E$ 都关联（incident）到某个顶点 $v_i \in V'$，这意味着每个元素 $e_j \in S$ 都被集合 $S_i$ 覆盖。

(c) 若存在 $k$-Set Cover，则存在 $k$-Vertex Cover。若 $S_i$ 在集合覆盖中，就选 $v_i$ 进入顶点覆盖。每个元素 $e_j$ 都包含在某个集合 $S_i$ 中。由构造方式，这意味着每条边 $e_j$ 都关联到被选中的顶点 $v_i$。由于有 $k$ 个集合，顶点覆盖中就会选出 $k$ 个顶点。

> <span style="color:#1e8449;">**[note]**</span> 这个归约几乎是「换皮」：顶点覆盖要求每条边都被覆盖、每个顶点恰好能覆盖与它关联的边；集合覆盖要求每个元素都被覆盖、每个集合恰好能覆盖它所包含的元素。于是「顶点 ↔ 集合、边 ↔ 元素」的一一对应让两个问题在结构上完全同构，归约只需把每个顶点 $v_i$ 换成「它关联的全部边所构成的集合 $S_i$」即可。这也解释了为什么正反两个方向都能逐项平移：正方向把顶点覆盖里的每个顶点换成对应集合；反方向因为每个集合 $S_i$ 明确对应唯一的顶点 $v_i$，选出的 $k$ 个集合自然给出 $k$ 个顶点，且每条边都有所关联。Set Cover 是 Karp 1972 年 21 个 NP 完全问题之一；有趣的是，从近似算法角度看这两个问题天差地别——minimum vertex cover 有常数 2-近似（基于 maximal matching（极大匹配）），而 minimum set cover 一般只能做到 $O(\ln n)$ 因子，且不存在 $(1-o(1))\ln n$ 以下的近似（除非 $P = NP$）——「结构相似、难度迥异」正是近似算法理论的核心教训之一，参见 CLRS 第 35 章。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**6　Prove Max2SAT is NP-Complete: Reducing from Clique（证明 Max2SAT 是 NP 完全的：从团归约）**</span>

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Clique(G, k)（团）:**</span> 给定图 $G = (V, E)$ 和整数 $k$，是否存在一个顶点集合 $U \subseteq V$，满足 $|U| \ge k$，且其中的顶点构成一个 complete graph（完全图）（即一个 $k$-clique（$k$-团））？

<span style="color:#2471a3;">**[definition]**</span> <span style="color:#00838f;">**Max2SAT(C, X, k)（最大 2-可满足性）:**</span> 给定一个 CNF（合取范式，conjunctive normal form）公式，由子句 $C = \{c_1, c_2, \ldots, c_n\}$ 和文字（literals）$X = \{x_1, x_2, \ldots, x_k\}$ 组成，其中每个子句恰好涉及两个文字，是否存在对这些文字的一个 TRUE/FALSE 赋值（assignment），使得至少 $k$ 个子句被满足？

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**6.1　Show Max2SAT $\in$ NP（证明 Max2SAT $\in$ NP）**</span>

要证明这一点，我们需要证明存在一个 polytime verifier（多项式时间验证器）。给定任意 "yes" 输入（CNF 公式），其 "witness/certificate"（见证/证书）就是满足至少 $k$ 个子句的、对文字的一个 TRUE/FALSE 赋值。验证器只需根据该赋值逐一求值 CNF 公式中的每个子句，并验证其中至少 $k$ 个被满足即可。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**6.2　Show Max2SAT $\in$ NP-Hard（证明 Max2SAT $\in$ NP-Hard）**</span>

**1.** 要证明这一点，我们把 Clique 归约到 Max2SAT。给定 Clique 的一个输入 $(G, k)$，我们将构造 Max2SAT 的一个输入 $(C, X, k')$，使得 Clique 的 "yes" 实例映射为 Max2SAT 的 "yes" 实例，Clique 的 "no"（否）实例映射为 Max2SAT 的 "no" 实例。

(a) 在设计 CNF 公式时，我们用文字 $x_1, x_2, \ldots, x_n$ 表示图中的 $n$ 个顶点。我们想设计一些充当约束的子句，强制「$x_1 = $ TRUE 对应于顶点 1 被选入团」。

> <span style="color:#1e8449;">**[mathtip]**</span> 原文此处以「$x_1 = $ TRUE 对应顶点 1 被选入团」举例书写；结合后文第 (c)(d) 步与下一页的论证可知，其一般含义是「$x_i = $ TRUE 当且仅当顶点 $i$ 被选入团」，其中 $i$ 遍历 $1, \ldots, n$。

(b) 回顾：一个顶点集合 $U$ 是团，当且仅当对所有 $i, j \in U$ 都有 $(i, j) \in E$。等价地（由逆否命题（contrapositive）），$U$ 是团当且仅当对所有满足 $(i, j) \notin E$ 的 $i, j \in V$，要么 $i \notin U$ 要么 $j \notin U$。我们将用这些约束来设计 CNF 公式中的子句。

(c) 因此，对每条 "non-edge"（非边）$(i, j) \notin E$，我们都有一个子句 $(\neg x_i \lor \neg x_j)$。这意味着对每条非边，两个端点至少有一个不在团中。

(d) 然而，把所有文字 $x_i$ 都设为 FALSE 也能满足这些子句。为了鼓励选出含更多顶点的团，对每个顶点 $i$，我们引入子句 $(x_i \lor z) \land (x_i \lor \neg z)$，其中 $z$ 是一个新的文字。要满足这两个子句，$x_i$ 必须为 TRUE。

> <span style="color:#1e8449;">**[mathtip]**</span> 原文此处（以及本页末尾的定义汇总、下一页中）均写作 $(x_i \land \neg z)$，但按下一页的计数公式 $2|U| + |V \setminus U|$ 校验，该子句在数学上必须是 $(x_i \lor \neg z)$：当 $x_i = $ TRUE、$z = $ TRUE 时，$(x_i \land \neg z)$ 为 FALSE，会破坏「每个 $i \in U$ 恰好贡献 2 个被满足子句」的计数；而 $(x_i \lor \neg z)$ 在 $x_i = $ TRUE 时恒为 TRUE，与下一页的计数完全吻合。这是原文的印刷笔误，下文一律按数学上正确的 $(x_i \lor \neg z)$ 理解。

(e) 取 $k' = $ 非边的数目 $+ |V| + k$。

因此，Max2SAT 的输入定义如下：

- 文字（literals）$X = V \cup \{z\}$，
- 子句（clauses）$C = \{(\neg x_i \lor \neg x_j) \mid \text{所有非边 } (i, j)\} \cup \{(x_i \lor z) \land (x_i \lor \neg z) \mid \text{所有顶点 } i\}$，
- 以及阈值 $k' = |\text{non-edges}| + |V| + k$。

> <span style="color:#1e8449;">**[note]**</span> 这个归约的精妙处在于用「计数」把关：非边子句 $(\neg x_i \lor \neg x_j)$ 只禁止「两个端点同时被选」，理论上把所有 $x_i$ 全赋 FALSE 就能让它们全部满足，因此必须加一个装置防止平凡赋值「白嫖」。$z$ 的引入正是为此——对每个顶点引入 $(x_i \lor z) \land (x_i \lor \neg z)$ 两个子句后，无论 $z$ 取什么值，$x_i$ 都必须为 TRUE 才能同时满足两者，这就把「选 $x_i$」强制成了唯一出路。把阈值定为 $k' = |\text{non-edges}| + |V| + k$ 使得多选一个顶点（多贡献 2 个满足子句）严格优于不选（只靠 $z$ 顶多贡献 1 个），于是「至少 $k'$ 个子句满足」被精确等价为「团大小 $\ge k$」。顺带一提：Max2SAT 与 2-SAT（要求全部子句满足）形成鲜明对照——2-SAT 可用蕴含图（implication graph）+ 强连通分量（strongly connected components）在线性时间内求解（属 P），而一旦放宽为「至少满足 $k$ 个」就跳升为 NP 完全。在近似算法视角下，Max2SAT 有 3/4 近似（随机赋值的期望论证，Johnson 1974）；Goemans–Williamson 的半正定规划（SDP）松弛能把近似比进一步提到约 0.878（对 MAX-CUT），见 Lec 17 与 CLRS 第 35 章。

---

<!-- ===== PDF p8 ===== -->

**（续）**

**2.** 证明：若 Clique 是 "yes"，则 Max2SAT 是 "yes"。给定图 $G$ 中满足 $|U| \ge k$ 的一个团 $U \subseteq V$，我们可以采用如下赋值：对所有 $i \in U$，$x_i$ 为 TRUE；对所有 $i \notin U$，$x_i$ 为 FALSE；$z$ 为 TRUE。这是一个满足我们 CNF 公式中至少 $k'$ 个子句的赋值。首先，对所有非边 $(i, j)$，子句 $(\neg x_i \lor \neg x_j)$ 都被满足，因为 $U$ 是一个团。对所有 $i \in U$，$(x_i \lor z)$ 和 $(x_i \lor \neg z)$ 都被满足。对所有 $i \notin U$，$(x_i \lor z)$ 被满足，而 $(x_i \lor \neg z)$ 不被满足。因此，被满足的子句数为

```math
|\text{non-edges}| + 2|U| + |V \setminus U| = |\text{non-edges}| + |V| + |U| \ge k'.
```

**3.** 证明：若 Max2SAT 是 "yes"，则 Clique 是 "yes"。给定对文字 $X$ 的一个赋值，使得至少 $k'$ 个子句被满足，我们将证明可以在原图中找到一个 $k$-clique。若对所有 $(i, j) \notin E$，$(\neg x_i \lor \neg x_j)$ 都被满足，那么被置为 TRUE 的文字在原图 $G$ 中构成一个大小 $\ge k$ 的团（由构造方式）。然而，若当前赋值并不对应于一个团，我们将证明可以修改该赋值，以找到一个大小至少为 $k$ 的团。对每个未被满足的子句 $(\neg x_i \lor \neg x_j)$，我们把 $x_i$ 改为 FALSE（改哪一个端点都无所谓），从而满足该子句。这会导致 $(x_i \lor z)$ 或 $(x_i \lor \neg z)$ 中的一个变得不被满足。此外，$x_i$ 还可能出现在其他 "non-edge" 子句中。因此，净效果是：经过这次修改，被满足的子句数只会增加或保持不变。我们继续选取未被满足的 "non-edge" 子句，按此方式修改赋值，直到所有 "non-edge" 子句都被满足。注意，未被满足的 "non-edge" 子句数量在每次修改中单调递减（monotonically decreasing）。因此，我们得到一个对应于团的赋值（因为所有 "non-edge" 子句都被满足）。此外，该团的大小至少为 $k$，因为被满足的子句数仍至少为 $k'$——每次修改都不会减少被满足的子句数。

> <span style="color:#1e8449;">**[note]**</span> 第 3 步本质是一个「修复」论证（repair / fixing argument）：先说明任意满足足够多子句的赋值若不构成团，就能通过不断把某个非边端点翻成 FALSE 来修复，且每次翻转都不会减少已满足子句数——这是归约正确性证明中很典型的 monotonicity（单调性）技巧。为什么翻转 $x_i$ 不会让满足数下降？因为对固定的 $z$，$(x_i \lor z)$ 与 $(x_i \lor \neg z)$ 中至多有一个会因翻转而变成不满足（两个子句恰好一个依赖 $x_i$），而这次翻转至少满足了原本不满足的那条非边子句，且 $x_i$ 在其余非边子句中只以 $\neg x_i$ 出现、翻转只会让它们从满足到不满足的「反方向」变好，三者相抵后净变化非负。论证还依赖另一个细节：当所有非边子句都被满足后，剩下的满足子句只能来自 $2|V|$ 个 gadget 子句，其数量等于 $|V| + |U|$（$x_i$ 为真的顶点贡献 2 个、为假的贡献 1 个），因此由满足子句总数 $\ge k' = |\text{non-edges}| + |V| + k$ 立即推出 $|U| \ge k$，正好闭合整个归约的两侧。这类「先构造、再修复、最后用计数钉死阈值」的证明套路，在 NP 完全性归约（尤其是涉及优化问题的归约）中几乎每次都会出现，值得单独记下来。

---

<!-- ===== PDF p9 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）　Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
