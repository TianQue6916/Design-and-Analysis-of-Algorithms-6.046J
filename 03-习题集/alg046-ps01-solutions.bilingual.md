<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 1 解答（Problem Set 1 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">2015 年 2 月 15 日（February 15, 2015）· 麻省理工学院（Massachusetts Institute of Technology）· 授课教授（Profs.）：Erik Demaine、Srini Devadas 与 Nancy Lynch</span>

<span style="color:#7f8c8d;">本习题集截止时间为 2015 年 2 月 12 日（周四）晚上 11:59（This problem set is due at 11:59pm on Thursday, February 12, 2015）。</span>

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 1-1. Asymptotic Growth（渐近增长）**</span>

按下述 asymptotic (big-O) growth（渐近（大 O）增长）的递增顺序（increasing order）对下面所有函数排序。若某些函数具有相同的渐近增长（same asymptotic growth），务必指明。照旧，$\lg$ 表示以 2 为底的对数（base 2）。

1. $5^n$
2. $4^{\lg n}$
3. $4^{\lg\lg n}$
4. $n^4$
5. $n^{1/2}\lg^4 n$
6. $(\lg n)^{5\lg n}$
7. $n^{\lg n}$
8. $5^n$
9. $4^{n^4}$
10. $4^{4^n}$
11. $5^{5^n}$
12. $5^{5n}$
13. $n^{n^{1/5}}$
14. $n^{n/4}$
15. $(n/4)^{n/4}$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**
>
> ```math
> 4^{\lg\lg n} < 4^{\lg n} < n^{1/2}\lg^4 n < 5^n < n^4 < (\lg n)^{5\lg n} < n^{\lg n} < n^{n^{1/5}} < 5^n < 5^{5n} < (n/4)^{n/4} < n^{n/4} < 4^{n^4} < 4^{4^n} < 5^{5^n}
> ```

> <span style="color:#1e8449;">**[note]** </span> 官方解答给出的这条排序链有两处继承自原 PDF 的排印错误，此处照原文保留并指出：① 由 $4^{\lg n} = (2^2)^{\lg n} = 2^{2\lg n} = n^2$，应有 $n^{1/2}\lg^4 n < 4^{\lg n}$，而链中写反了（$4^{\lg n}$ 与 $n^{1/2}\lg^4 n$ 的位置应互换）；② 指数函数支配多项式，应有 $n^4 < 5^n$，链中亦写反。修正后链的起始部分应为
>
> ```math
> 4^{\lg\lg n} < n^{1/2}\lg^4 n < 4^{\lg n} = n^2 < n^4 < 5^n < \cdots
> ```
>
> 其余各项顺序均正确；这两处错误不影响「排序」方法本身，只影响开头几项的次序。

> <span style="color:#1e8449;">**[mathtip]** </span> 比较这类指数-对数混合函数时，常用技巧是把每个函数写成 $2^{f(n)}$ 的形式再比较指数 $f(n)$。例如 $4^{\lg n} = n^2$，$(\lg n)^{5\lg n} = 2^{5\lg n \cdot \lg\lg n}$，$n^{\lg n} = 2^{(\lg n)^2}$，$n^{n^{1/5}} = 2^{n^{1/5}\lg n}$。另外注意 $5^n$ 在题目中出现了两次（第 1 项与第 8 项），而 $4^{4^n}$ 与 $5^{5^n}$ 是双重指数（double exponential）函数，增长极快。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 1-2. Solving Recurrences（求解递推）**</span>

对下面每个递推式（recurrence）中的 $T(n)$ 给出渐近上界（upper bound）与下界（lower bound）。假设对 $n \le 2$，$T(n)$ 为常数（constant）。使你的界尽可能紧（tight），并论证你的答案（justify your answers）。

<!-- ===== PDF p2 ===== -->

**（续）**

**(a)** $T(n) = 4T(n/4) + 5n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n\lg n)$，主定理（Master Theorem）情形 2（Case 2）。

**(b)** $T(n) = 4T(n/5) + 5n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n)$，主定理（Master Theorem）情形 3（Case 3）。

**(c)** $T(n) = 5T(n/4) + 4n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n^{\log_4 5}) = \Theta(n^{\lg\sqrt{5}})$，主定理（Master Theorem）情形 1（Case 1）。

**(d)** $T(n) = 25T(n/5) + n^2$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n^2\lg n)$，主定理（Master Theorem）情形 2（Case 2）。

**(e)** $T(n) = 4T(n/5) + \lg n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n^{\log_5 4})$，主定理（Master Theorem）情形 1（Case 1）。

**(f)** $T(n) = 4T(n/5) + \lg^5 n\sqrt{n}$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $T(n) = \Theta(n^{\log_5 4})$，主定理（Master Theorem）情形 1（Case 1）。
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 中该题排印为 $T(n) = 4T(n/5) + \lg^5 n\sqrt{n}$（末端的 $\sqrt{n}$ 疑为排印残留，很可能源自下一题 (g) 的 $\sqrt{n}$）。无论 $f(n)$ 是 $\lg^5 n$ 还是 $\sqrt{n}\lg^5 n = n^{1/2}\lg^5 n$，由于 $\log_5 4 \approx 0.861 > 1/2$，都有 $f(n) = O(n^{\log_5 4 - \epsilon})$，情形 1 均适用，故答案 $\Theta(n^{\log_5 4})$ 不受影响。

**(g)** $T(n) = 4T(\sqrt{n}) + \lg^5 n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 变换变量（change variables）。假设 $n$ 是 2 的幂，令 $n = 2^m$，我们得到
>
> ```math
> T(2^m) = 4T(2^{m/2}) + m^5
> ```
>
> 若定义 $S(m) = T(2^m)$，则得到递推式 $S(m) = S(m/2) + m^5$。根据主定理（Master Theorem）情形 3（Case 3），$S(m) = \Theta(m^5)$，即 $T(2^m) = \Theta(m^5)$。
>
> 换回原变量（$m = \lg n$），得 $T(n) = \Theta(\lg^5 n)$。
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 中此处写为 $S(m) = S(m/2) + m^5$（漏写了因子 4）。由 $T(2^m) = 4T(2^{m/2}) + m^5$ 应得到 $S(m) = 4S(m/2) + m^5$。对 $a = 4$、$b = 2$、$f = m^5$，因 $m^5 = \Omega(m^{2 + \epsilon})$，情形 3 仍给出 $S(m) = \Theta(m^5)$，故结论 $T(n) = \Theta(\lg^5 n)$ 正确。

**(h)** $T(n) = 4T(\sqrt{n}) + \lg^2 n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 与上一题类似。令 $n = 2^m$，得到 $T(2^m) = 4T(2^{m/2}) + m^2$。令 $S(m) = T(2^m)$，得 $S(m) = 4S(m/2) + m^2$。根据主定理（Master Theorem）情形 2（Case 2），$S(m) = \Theta(m^2\lg m)$，即 $T(2^m) = \Theta(m^2\lg m)$。
>
> 换回原变量（$m = \lg n$），得 $T(n) = \Theta(\lg^2 n \cdot \lg\lg n)$。

<!-- ===== PDF p3 ===== -->

**（续）**

**(i)** $T(n) = T(\sqrt{n}) + 5$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 仍然类似。令 $n = 2^m$，得到 $T(2^m) = T(2^{m/2}) + 5$。令 $S(m) = T(2^m)$，得 $S(m) = S(m/2) + 5$，其解为 $S(m) = \Theta(\lg m)$，所以 $T(2^m) = \Theta(\lg m)$。换回原变量，得 $T(n) = \Theta(\lg\lg n)$。

**(j)** $T(n) = T(n/2) + 2T(n/5) + T(n/10) + 4n$

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** $\Theta(n\lg n)$，使用显式递归树（explicit trees）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 1-1. Restaurant Location（餐厅选址）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

Drunken Donuts（醉甜甜圈），一家新的 wine-and-donuts（酒与甜甜圈）餐厅连锁，希望在许多街角（street corners）开设餐厅，目标是最大化总利润（total profit）。

街道网络（street network）描述为一个无向图（undirected graph）$G = (V, E)$，其中潜在的餐厅选址（restaurant sites）是图的顶点（vertices）。每个顶点 $u$ 有一个非负整数值（nonnegative integer value）$p_u$，描述选址 $u$ 的潜在利润（potential profit）。两个餐厅不能建在相邻顶点（adjacent vertices）上（以避免自我竞争，self-competition）。你需要设计一个算法，输出所选择的选址集合 $U \subseteq V$，使总利润

```math
\sum_{u \in U} p_u
```

最大化。

首先，对于 (a)–(c) 小题，假设街道网络 $G$ 是无环的（acyclic），即一棵树（tree）。

**(a)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

考虑如下「贪心」（greedy）餐厅选址算法：在树中选取利润最高的顶点 $u_0$（按某种顶点名称顺序打破平局，breaking ties according to some order on vertex names），将其放入 $U$。把 $u_0$ 连同其在 $G$ 中的所有邻点（neighbors）从进一步考虑中移除。重复，直到没有剩余顶点。

给出一个反例（counterexample），说明该算法并不总能给出利润最大的餐厅选址。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 例如，这棵树可以是一条直线（line）（9, 10, 9）。

**(b)** <span style="color:#7f8c8d;">[9 points]（9 分）</span>

给出一个确定最大利润选址的高效算法（efficient algorithm）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**
>
> **[Algorithm]（算法）**
>
> 我们可以用动态规划（dynamic programming）在 $O(n)$ 时间内解决该问题。首先任选一个节点 $u_0$ 作为树的根（root），并按照深度优先搜索（depth-first search）对所有节点排序。

<!-- ===== PDF p4 ===== -->

**（续）**

> （DFS，接上页。）将排好序的节点存入数组 $N$。由 DFS 的定义，父节点（parent node）在 $N$ 中总是出现在其所有子节点（children）之前。
>
> 对每个节点 $v$，定义 $A(v)$ 为当 $v$ 被包含（included）时、以 $v$ 为根的子树（subtree）中选址的最优代价（best cost）；定义 $B(v)$ 为当 $v$ 未被包含时、以 $v$ 为根的子树中选址的最优代价。可以建立 $A()$ 与 $B()$ 的如下递推方程（recursion equations）：
>
> 若 $v$ 是叶子（leaf），则 $A(v) = p_v$，$B(v) = 0$。
>
> 若 $v$ 不是叶子，则
>
> ```math
> A(v) = p_v + \sum_{u \in v.\text{children}} B(u)
> ```
>
> ```math
> B(v) = \sum_{u \in v.\text{children}} \max(A(u), B(u))
> ```
>
> 按逆序（reverse order）对 $N$ 中每个节点 $v$ 计算 $A(v)$ 与 $B(v)$。最终 $\max(A(u_0), B(u_0))$ 即为最大利润。
>
> 达到该最大利润的选址方案可以从根开始递归比较 $A()$ 与 $B()$ 得出：若 $A(u_0) > B(u_0)$，则根 $u_0$ 应被包含，否则排除。若 $u_0$ 被排除，则转到 $u_0$ 的所有子节点并重复该步骤；若 $u_0$ 被包含，则转到 $u_0$ 的所有孙节点（grandchildren）并重复该步骤。该算法遍历树一次即输出一个最优选址（optimal placement）。
>
> **[Correctness]（正确性）**
>
> 在 $v$ 为叶子节点的基本情形（base case）中，算法输出最优选址，即包含该节点。
>
> 在一个最优选址中，节点 $v$ 要么被包含（这会排除其所有子节点），要么不被包含（这不会增加任何约束）。由归纳法（induction），若 $v$ 的所有子节点都有正确的 $A()$ 与 $B()$ 值，则 $A(v)$ 与 $B(v)$ 也将正确，从而得到 $v$ 处的最大利润。由于数组 $N$ 是按 DFS 排序并逆序处理的，子节点保证先于其父节点被处理。
>
> **[Timing Analysis]（时间分析）**
>
> 用 DFS 对所有节点排序需要 $O(n)$ 时间。在已知子节点值的条件下，计算 $A(v)$ 与 $B(v)$ 的时间与 $\deg(v)$（degree，度数）成正比。因此总时间是所有节点度数之和，即 $O(|E|)$，其中 $|E|$ 是总边数。对于树结构，$|E| = n - 1$，所以计算所有 $A()$ 与 $B()$ 的时间为 $O(n)$。最后，利用所求得的 $A()$ 与 $B()$ 找出最优选址只需访问每个节点一次，因此也是 $O(n)$。
>
> 总体而言，该算法复杂度为 $O(n)$。

**(c)** <span style="color:#7f8c8d;">[6 points]（6 分）</span>

假设由于缺乏良好的市场调研（market research），DD 认为所有选址都同样好，因此目标只是设计一个拥有最多位置数（largest number of locations）的餐厅选址方案。针对这一情形给出一个简单的贪心算法（greedy algorithm），并证明其正确性。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**

<!-- ===== PDF p5 ===== -->

**（续）**

> **[Algorithm]（算法）**
>
> 与 (b) 小题类似，任选一个节点 $u_0$ 作为树的根，并按照深度优先搜索（depth-first search，DFS）对所有节点排序，将排好序的节点存入数组 $N$。按逆序（reverse order）处理 $N$ 中每个有效节点（valid node）：将其包含进方案，并把它的父节点从 $N$ 中移除。
>
> **[Correctness]（正确性）**
>
> **Claim（声明）：** 该贪心算法给出一个最优选址。
>
> **Lemma 1（引理 1）：** 对任意节点权值都相等的树，存在一个包含所有叶子（leaves）的最优选址。
>
> 若存在某个不含叶子 $v$ 的最优选址 $O$，只需把 $v$ 加入 $O$，并在必要时移除其父节点。结果不会比选址 $O$ 更差。对所有叶子重复该操作，直到得到一个包含所有叶子的最优选址。
>
> 主声明即可用引理 1 证明。由于 DFS 排序的性质，$N$ 中逆序的第一个有效节点必为叶子节点。根据引理 1，它可以被包含进最优选址，而其父节点被排除。当 $N$ 中的节点按逆序被处理时，每个被处理的节点都是当前 $N$ 中最后一个有效节点，因此都是叶子。而包含该叶子正是对应子树的一个最优解的一部分。总体而言，我们为原树得出了一个最优选址。
>
> **[Timing Analysis]（时间分析）**
>
> 用 DFS 对节点排序需要 $O(n)$ 时间。贪心算法也只需 $O(n)$ 时间，因为它只处理每个节点一次。总体而言该算法复杂度为 $O(n)$。

**(d)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

现在假设图是任意的，不一定是无环的。给出你能想到的、解决该问题的最快正确算法（fastest correct algorithm）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 一个简单的算法是尝试 $U$ 的所有可能顶点子集（共 $2^{|V|}$ 个子集），测试每个子集是否满足所需的独立性质（independence property，即每条边至多有一个端点被选入，共 $|E|$ 条边），对每个合法解计算总利润（需 $O(|V|)$ 时间），并取最优者。该算法运行时间为 $O(2^{|V|}|E|)$。这是预期解答（intended solution）。
>
> 事实上，该问题正是最大独立集（Maximum Independent Set）问题，已知其为 NP 完全（NP-complete）。因此，除非 $P = NP$，否则它没有多项式时间算法。（事实上，若假设一个更强的条件——指数时间假说（Exponential Time Hypothesis），则不存在 $2^{o(|V|)}$ 时间的算法。）

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 1-2. Radio Frequency Assignment（无线电频率分配）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

美国联邦通信委员会（Federal Communications Commission，FCC）的 Wheeler 教授有一大堆来自美国本土（Continental U.S.）各广播电台的请求，希望能在无线电频率（radio frequency）88.1 FM 上发射。FCC 很乐意

<!-- ===== PDF p6 ===== -->

**（续）**

批准所有这些请求，前提是任意两个请求地点之间的欧几里得距离（Euclidean distance）不超过 1（距离 1 或许意味着，比如说，20 英里）。然而，只要有任何两个地点距离在 1 之内，Wheeler 教授就会恼火并拒绝整组请求。

假设每个申请 88.1 FM 频率的请求都由一些标识信息（identifying information）外加电台位置的 $(x, y)$ 坐标组成。假设任意两个请求的 $x$ 坐标互不相同，同样地，任意两个请求的 $y$ 坐标也互不相同。输入包含两个有序列表（sorted lists）：$L_x$（按 $x$ 坐标排序的请求）与 $L_y$（按 $y$ 坐标排序的请求）。

**(a)** <span style="color:#7f8c8d;">[3 points]（3 分）</span>

假设地图被划分成方格网格（square grid），其中每个方格（square）的尺寸为 $\frac{1}{2} \times \frac{1}{2}$。若两个请求位于同一个方格之内或边界上（in, or on the boundary of, the same square），为什么 FCC 必须拒绝这组请求？

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 因为它们之间的距离在 1 之内（$\sqrt{2}/2 < 1$）。

**(b)** <span style="color:#7f8c8d;">[14 points]（14 分）</span>

为 FCC 设计一个高效算法，判断这一堆请求中是否含有两个欧几里得距离在 1 之内的请求；若有，算法还应返回一个示例对（example pair）。要得满分，你的算法应运行在 $O(n\lg n)$ 时间内，其中 $n$ 是请求数。

提示（Hint）：使用分治（divide-and-conquer），并利用 (a) 小题。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**
>
> **[Algorithm]（算法）**
>
> 预期的分治算法（divide-and-conquer algorithm）如下。
>
> **Divide（划分）：** 利用列表 $L_x$ 找一条竖直线，把请求分成大小约为 $n/2$ 的两个子集，并且该直线不穿过任何请求。两个子集各自的 $L_x$ 与 $L_y$ 都应被计算出来。
>
> **Conquer（征服）：** 若某个子集包含不超过某个常数 $C$（例如 $C = 2$）个请求，则检查其中任意一对是否距离在 1 之内，若有则返回该对。否则，若子集包含多于 $C$ 个请求，则把该子集进一步划分成更小的子集。
>
> **Merge（合并）：** 我们只需检查位于一条宽为 2 的「条带」（stripe）$S$ 内的请求对，该条带以两半之间的分界线为中心。我们把 $S$ 内的所有请求合并成一个（可能缩减后的）列表 $L$，并仍按 $y$ 坐标保持有序。对请求 $r \in L$，检查 $r$ 与 $L$ 中紧随其后的 7 个请求（它们具有更大的 $y$ 坐标）之间的距离。若任何一对的距离在 1 之内，返回该对。
>
> **[Correctness]（正确性）**
>
> 在分治阶段之后，若两个子问题都没有发现违规（violation），那么违规唯一还可能发生的地方就是条带 $S$ 内，而这正是我们在合并阶段检查的。在合并阶段，只需检查紧随 $r$ 之后的 7 个请求，理由如下。

<!-- ===== PDF p7 ===== -->

**（续）**

> 若我们把条带 $S$ 划分成尺寸为 $1/2$ 的方格，则每个方格至多含有一个请求，否则同一方格内的违规对应当已被子问题检测到。对请求 $r \in L$，一个紧随其后的、距离在 1 之内的请求必须位于一个横跨分界线的 $2 \times 1$ 方格（2-by-1 square）内。这样的方格只有 8 个。因此，除去 $r$ 自身，只需检查紧随其后的 7 个请求。（$y$ 坐标较小的请求已经与 $r$ 检查过了。）
>
> **[Timing Analysis]（时间分析）**
>
> 划分请求需要 $O(n)$ 时间。合并阶段遍历条带中的每个请求，对每个请求只需常数时间。因此合并阶段需要 $O(n)$ 时间。于是我们得到如下关于时间复杂度的递推式：
>
> ```math
> T(n) \le 2T(n/2) + cn
> ```
>
> 根据主定理（master theorem），其解为 $O(n\lg n)$。
>
> **[Other Solutions]（其他解法）**
>
> 还有其他不使用分治的解法。最相近的一种是：直接把 $L_x$ 划分成若干最大团簇（maximal clusters），使每个团簇中点的 $x$ 方向跨度（x extent）至多为 1，然后用与上述类似的算法比较相邻团簇之间的点。另一种关联较弱的做法是：若允许计算某个坐标的向下取整（floor），我们可以把点分配到各自的 $\frac{1}{2} \times \frac{1}{2}$ 方格中，再用哈希表（hash table）检查方格内或与八个相邻方格之间的请求冲突。但哈希表的插入在最坏情况下需要 $O(n)$ 时间，尽管期望时间（expected time）为 $O(1)$。这使得总的最坏情况运行时间为 $O(n^2)$。我们也可以改用一棵平衡二叉搜索树（balanced BST），把最坏情况运行时间降到 $O(n\lg n)$。

**(c)** <span style="color:#7f8c8d;">[8 points]（8 分）</span>

描述如何修改 (b) 小题的解法，以判断是否存在三个请求，它们彼此间的距离都在 1 之内（three requests, all within distance 1 of each other）。要得满分，你的算法应运行在 $O(n\lg n)$ 时间内，其中 $n$ 是请求数。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 解法与 (b) 小题类似。对于基本情形（base case），若某个子集内的请求数不超过 $C$（例如 $C = 3$），则计算该子集中每个 3 元组（3-tuple）的距离。
>
> 若没有找到，那么剩下的唯一可能是一个三角形（triangle），其中两个点在线的一侧、一个点在另一侧。按 (b) 小题的做法继续进行：找到条带 $S$，把它划分成方格。
>
> 现在，对缩减列表中的每个请求，检查它与上述相同常数个（constant number of）紧随其后的请求之间的距离，但这次要找的是两个这样的请求。对每一对，还要检查该对中两个请求彼此之间的距离。
>
> 额外的工作量仍然是 $O(n)$，所以我们得到同样的 $O(n\lg n)$ 界。

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare · http://ocw.mit.edu · 6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）· Spring 2015（2015 春季）</span>

<span style="color:#7f8c8d;">关于引用这些材料的说明或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
