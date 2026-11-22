<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 6（Lecture 6）：随机化算法（Randomized Algorithms）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- 校验矩阵乘法（Check matrix multiplication）
- 快速排序（Quicksort）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**随机化算法或概率算法（Randomized or Probabilistic Algorithms）**</span>

什么是随机化算法（randomized algorithm）？

- 生成一个随机数 $r \in \{1, \ldots, R\}$，并根据 $r$ 的值做出决策的算法。
- 对同样的输入、不同的执行（executions），随机化算法可能：
  - 运行不同的步数（Run a different number of steps）
  - 产生不同的输出（Produce a different output）

随机化算法可以大致分为两类——**Monte Carlo** 与 **Las Vegas**。

| Monte Carlo | Las Vegas |
|:---|:---|
| 总是多项式时间运行（runs in polynomial time always） | 期望多项式时间运行（runs in expected polynomial time） |
| 输出以高概率正确（output is correct with high probability） | 输出总是正确（output always correct） |

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Monte Carlo 与 Las Vegas 的区分是本讲最需要记牢的概念骨架，用一句话概括：**Monte Carlo 是「时间确定、答案可能错」；Las Vegas 是「答案必对、时间可能慢（但期望受控）」**。前者是「赌答案」（如 Freivalds 算法：错误概率 $\le 1/2$，可迭代压低），后者是「赌时间」（如随机化快排：答案总是对的，只是偶尔慢）。两者的互补关系很妙：若你有一个「验证器」能检查 Monte Carlo 的输出是否正确，那么反复运行直到验证通过，就得到一个 Las Vegas 算法——把「错答案的风险」转化成「重复运行的时间」。Wiki 查证：这两个术语中 **Las Vegas 由 László Babai 于 1979 年在图同构（graph isomorphism）问题的背景下提出**，作为 Monte Carlo 的对偶；而 **「Monte Carlo」一词由 Nicholas Metropolis 于 1947 年引入**（源自摩纳哥赌场名）。本讲两个主角正好各占一类：Freivalds 校验是 Monte Carlo（答案可能错），随机化快排是 Las Vegas（答案必对）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**矩阵乘积（Matrix Product）**</span>

```math
C = A \times B
```

- 简单算法（Simple algorithm）：$O(n^3)$ 次乘法。
- **Strassen**：用 7 次乘法乘两个 $2 \times 2$ 矩阵：$O(n^{\log_2 7}) = O(n^{2.81})$
- **Coppersmith-Winograd**：$O(n^{2.376})$

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这里的矩阵乘法复杂度谱系值得记住：朴素 $O(n^3)$ → Strassen $O(n^{2.81})$（1969，用 7 次而非 8 次乘法乘 $2\times 2$ 块）→ Coppersmith-Winograd $O(n^{2.376})$（1987/1990）→ 后续用张量方法（tensor methods）持续改进：$O(n^{2.3728596})$（2021 年 Alman–Williams）→ 约 $O(n^{2.371})$（2024–2026 年间的最新记录，仍在缓慢下降）。这条曲线你会在 18.065/Strang 里看到它的线性代数侧面（张量分解 tensor decomposition），但本讲的重点不是「更快地算矩阵乘法」，而是「**更省地验证**矩阵乘法是否算对」——这正是下一页 Freivalds 算法的用武之地：**验证可能比计算容易得多**。这个「验证容易、求解难」的落差，与讲次 1 里 NP 中「验证容易」的思想一脉相承。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b;">**矩阵乘积校验器（Matrix Product Checker）**</span>

给定 $n \times n$ 矩阵 $A, B, C$，目标是检查 $A \times B = C$ 是否成立。

**问题（Question）**：我们能做得比完整执行乘法更好吗？

我们将看到一个 $O(n^2)$ 的算法，它满足：
- 若 $A \times B = C$，则 $\Pr[\text{output} = \text{YES}] = 1$。
- 若 $A \times B \ne C$，则 $\Pr[\text{output} = \text{YES}] \le \frac{1}{2}$。

我们将假设矩阵中的元素属于 $\{0, 1\}$，且算术在 **mod 2** 下进行。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**Freivalds 算法（Freivalds' Algorithm）**</span>

选择一个随机二进制向量（random binary vector）$r[1 \ldots n]$，使得对 $i = 1, \ldots, n$ 独立地有 $\Pr[r_i = 1] = \frac{1}{2}$。若 $A(Br) = Cr$，算法输出 **'YES'**；否则输出 **'NO'**。

**观察（Observation）**：该算法需要 $O(n^2)$ 时间，因为有 3 次「$n \times n$ 矩阵 × $n \times 1$ 矩阵」的乘法：$Br$、$A(Br)$ 与 $Cr$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Freivalds 算法的核心是把「验证 $AB = C$」这个**矩阵级问题**投影（project）到**一个随机向量**上：先算 $Br$（$O(n^2)$），再算 $A(Br)$（$O(n^2)$），最后算 $Cr$（$O(n^2)$）——利用**结合律 $A(Br) = (AB)r$**，我们永远不用真正算出 $AB$。为什么是「投影」？因为 $AB = C$ 当且仅当对所有向量 $r$ 都有 $(AB)r = Cr$；随机选一个 $r$ 就是在高维空间里随机抽一个方向做检测。算法巧妙在**只算向量而不是矩阵**：本来 $O(n^3)$ 的矩阵乘法被替换成 3 次「矩阵×向量」（各 $O(n^2)$）。这个「**用随机投影替代全量计算**」的思想极其通用——你在之后的 Johnson-Lindenstrauss 引理、流式算法（streaming）、机器学习里的随机特征映射（random features）中都会反复遇到。Wiki 查证：这是拉脱维亚计算机科学家 Rūsiņš Mārtiņš Freivalds 提出的算法，是随机化算法课的经典第一课。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**正确性分析（Analysis of Correctness if $AB \ne C$）**</span>

<span style="color:#2471a3;">**[claim]**</span> 声明（Claim）：若 $AB \ne C$，则 $\Pr[ABr = Cr] \ge \frac{1}{2}$。

设 $D = AB - C$。我们的假设是 $D \ne 0$。显然，存在某个 $r$ 使得 $Dr \ne 0$。

我们的目标是证明**存在很多 $r$ 使得 $Dr \ne 0$**。具体地，对随机选取的 $r$，$\Pr[Dr \ne 0] \ge \frac{1}{2}$。

$D = AB - C \ne 0 \Rightarrow \exists\ i, j$ 使得 $d_{ij} \ne 0$。固定向量 $v$：除了 $v_j = 1$ 外其余坐标全为 0。$(Dv)_i = d_{ij} \ne 0$，意味着 $Dv \ne 0$。取算法可能选出的任意 $r$。我们考虑 $Dr = 0$ 的情形。令 $r' = r + v$。

由于 $v$ 除 $v_j$ 外处处为 0，$r'$ 与 $r$ 相同，除了 $r'_j = (r_j + v_j) \bmod 2$。于是 $Dr' = D(r + v) = Dr + Dv = 0 + Dv \ne 0$。我们看到 $r$ 与 $r'$ 之间存在**一一对应**（1 to 1 correspondence）：若 $r + v = r'' + v$，则 $r = r''$。这意味着：

```math
\text{使 } Dr' \ne 0 \text{ 的 } r' \text{ 的个数} \ge \text{使 } Dr = 0 \text{ 的 } r \text{ 的个数}
```

由此我们得出结论：$\Pr[Dr \ne 0] \ge \frac{1}{2}$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这个「配对论证（pairing argument）」是随机化证明的经典模板，值得拆解成三步记忆：**① 定位**——既然 $D \ne 0$，必有某个非零元 $d_{ij}$，用它构造一个「探测向量」$v$（只在 $j$ 位为 1）；**② 配对**——对任意 $r$，构造 $r' = r + v$，使得 $Dr$ 与 $Dr'$ 不可能**同时**为零（因为 $Dr' = Dr + Dv$，而 $Dv \ne 0$）；**③ 计数**——「坏向量 $r$（使 $Dr = 0$）」与「好向量 $r'$（使 $Dr' \ne 0$）」一一配对，所以好向量至少和坏向量一样多，故 $\Pr[Dr \ne 0] \ge 1/2$。整个论证只用了一次「$Dv \ne 0$」的线性性质，就推出「**至多一半的 $r$ 会让错误逃逸**」。注意「mod 2」假设在此处的关键作用：$r_j + v_j \bmod 2$ 把「翻转一位」变成「配对操作」，而 $Dv \ne 0$ 用的是「$v$ 只在 $j$ 位为 1 时 $(Dv)_i = d_{ij}$」——这是你在 6.042J 学过的线性代数（矩阵×标准基向量 = 取列）的又一次应用。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b;">**快速排序（Quicksort）**</span>

分治算法，但**主要工作在分解（divide）步骤**而不是合并（combine）步骤。

像插入排序（insertion sort）一样**原地（in place）排序**，而不同于归并排序（mergesort，它需要 $O(n)$ 辅助空间）。

不同的变体（variants）：
- **基本版（Basic）**：平均情形下表现良好
- **基于中位数的枢纽选取（Median-based pivoting）**：使用中位数查找
- **随机版（Random）**：对所有输入在期望意义下都良好（Las Vegas 算法）

**快速排序的步骤：**
- **Divide（分解）**：在 $A$ 中选一个枢纽元素（pivot element）$x$，把数组划分为子数组：$L$（所有 $< x$ 的元素）、$G$（所有 $> x$ 的元素）、$E$（所有 $= x$ 的元素）。
- **Conquer（征服）**：递归排序子数组 $L$ 和 $G$。
- **Combine（合并）**：平凡（trivial）。

**基本快速排序（Basic Quicksort）**

围绕 $x = A[1]$ 或 $A[n]$（第一个或最后一个元素）作为枢纽：
- 依次从 $A$ 中移除每个元素 $y$
- 根据与枢纽 $x$ 的比较，把 $y$ 插入 $L$、$E$ 或 $G$
- 每次插入与移除需要 $O(1)$ 时间
- 划分步骤需要 $O(n)$ 时间
- 原地实现的细节：见 CLRS p.171

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 快速排序与归并排序的「重心」对比是本讲的第一个洞察：归并排序的合并步骤是重点（$O(n)$ 每层），而快速排序的**划分步骤**才是重点——一旦枢轴把数组分成 $L$（小于）与 $G$（大于）两部分，剩下的「合并」根本无事可做（左半边全小于右半边，天然有序拼接）。这也是为什么快速排序能**原地**运行：划分通过交换就地完成，不需要归并那种额外的 $O(n)$ 辅助数组。你在 6.006 学过快速排序，但这里引入了一个此前没有的视角——**随机化如何驯服快速排序的坏情形**。CLRS p.171 提到的原地划分（Lomuto/Hoare partition）是你在 CSAPP 里写过/见过的数组交换的经典应用。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b;">**基本快速排序分析（Basic Quicksort Analysis）**</span>

若输入已排序或逆序排序，那么我们每次都在围绕最小或最大元素划分。这意味着 $L$ 或 $G$ 之一有 $n-1$ 个元素，另一个有 0 个。这给出：

```math
T(n) = T(0) + T(n-1) + \Theta(n) = \Theta(1) + T(n-1) + \Theta(n) = \Theta(n^2)
```

然而，该算法在**实际中的随机输入**上表现良好。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**用中位数查找选择枢纽（Pivot Selection Using Median Finding）**</span>

可以用**秩/中位数选择算法**（rank/median selection algorithm，$\Theta(n)$ 时间）保证 $L$ 与 $G$ 平衡。下面的第一个 $\Theta(n)$ 用于枢纽选择，第二个用于划分步骤：

```math
T(n) = 2T\!\left(\frac{n}{2}\right) + \Theta(n) + \Theta(n) = \Theta(n \log n)
```

这个算法在实践中很慢（slow in practice），**输给归并排序**。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 中位数枢纽把递归变成完美的平衡分治 $T(n) = 2T(n/2) + \Theta(n)$，渐近上是 $O(n \log n)$ 且是**最坏情形**保证——但代价是每层都要调用一次 $O(n)$ 的中位数查找（讲次 2 的 median of medians），常数因子很大，实际反而输给归并排序。这个教训值得记住：**渐近复杂度相同的算法，常数因子可以天差地别**——「理论最优」不等于「实践最优」。这也是为什么实际库几乎都用「三点取中 + 插入排序兜底」的混合策略（如 C++ std::sort 的 introsort、Java 基本类型数组排序的 DualPivotQuicksort），而不是调用昂贵的中位数算法。你在 CSAPP 里学过的「常数因子也是复杂度的一部分」在这里具象化。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**随机化快速排序（Randomized Quicksort）**</span>

$x$ 从数组 $A$ 中**随机**选择（每次递归时都做一次随机选择）。

对所有输入数组 $A$，**期望时间**为 $O(n \log n)$。该算法的分析见 CLRS p.181–184；我们将分析它的一种变体。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**「偏执」快速排序（"Paranoid" Quicksort）**</span>

```
Repeat（重复）
    选择枢纽为 A 的随机元素
    执行划分 Partition
Until（直到）
    结果划分满足 |L| ≤ (3/4)|A| 且 |G| ≤ (3/4)|A|
Recurse on L and G（递归处理 L 和 G）
```

🎥 *Devadas 在视频中[解释「偏执」名字的由来]*："This quicksort is paranoid in the sense that it's going to be afraid of getting unbalanced partitions, and it's going to keep trying to get balanced partitions."（翻译：这个快速排序之所以「偏执」，是因为它害怕得到不平衡的划分，于是会不断重试直到获得平衡划分。）——算法不再接受任何划分结果，而是**坚持要求**两边都不超过 $\frac{3}{4}n$，否则重掷枢轴。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b;">**「偏执」快速排序分析（"Paranoid" Quicksort Analysis）**</span>

让我们定义「好枢纽」（good pivot）与「坏枢纽」（bad pivot）：

- **好枢纽（Good pivot）**：$L$ 和 $G$ 的大小都 $\le \frac{3}{4}n$。
- **坏枢纽（Bad pivot）**：$L$ 或 $G$ 之一的大小 $> \frac{3}{4}n$（即有一边过大）。

![原页 p05 图](data:image/png;base64,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)

> <span style="color:#7f8c8d;">[原图说明] 原页附图：把数组按枢纽位置分为 bad / good / bad 三段——左边 $\frac{n}{4}$ 与右边 $\frac{n}{4}$ 是「坏枢纽」位置（会导致某边 > 3n/4），中间 $\frac{n}{2}$ 是「好枢纽」位置。</span>

我们看到，一个枢纽是好的概率 $> \frac{1}{2}$。

设 $T(n)$ 是对任意规模为 $n$ 的数组的**期望运行时间上界**。$T(n)$ 由以下部分组成：
- 排序左子数组所需时间
- 排序右子数组所需时间
- 获得一次好调用（good call）所需的迭代次数。把划分步骤的成本记为 $c \cdot n$。

**期望（Expectations）**

```math
T(n) \le \max_{\frac{n}{4} \le i \le \frac{3n}{4}} \left( T(i) + T(n - i) \right) + E(\#\text{iterations}) \cdot cn
```

现在，由于好枢纽的概率 $> \frac{1}{2}$，有 $E(\#\text{iterations}) \le 2$。

> <span style="color:#7f8c8d;">[原图说明] 原页附图：期望代价的树状示意——根层 $2cn$，好枢纽概率 $>1/2$ 使下一层出现 $2cn/4$ 与 $3(2cn)/4$ 等分支，逐层递推，最终 $O(1)$。图注列出各层分支的期望代价。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 「偏执」快速排序的分析核心是两个漂亮的概率事实：**① 好枢纽概率 > 1/2**——从数轴看，$\frac{n}{4}$ 到 $\frac{3n}{4}$ 之间的位置构成「好」区段，占比恰好 $\frac{1}{2}$，且因为 $\le \frac{3n}{4}$ 是「至多」，所以严格来说好概率 $\ge 1/2$（含端点），讲义写 $> 1/2$ 是保守估计；**② 几何重试**——每次尝试是好枢纽的概率 $> 1/2$，所以期望重试次数 $E[\#\text{iterations}] \le 2$（几何分布期望 $1/p < 2$）。这个「**用重试把坏情形过滤掉，期望代价是几何级数**」的模板，就是后面跳表（skip list，讲次 7）随机层数、以及各类「随机化再试」算法的共同骨架。注意它把「单次最坏」变成「期望受控」：任何一次枢纽选择仍可能极差（概率指数小），但期望上每次划分都会把问题缩小到 $\le \frac{3}{4}n$。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b;">**「偏执」快速排序分析（续）**</span>

```math
T(n) \le T\!\left(\frac{n}{4}\right) + T\!\left(\frac{3n}{4}\right) + 2cn
```

我们在图中看到：无论沿哪条分支走到底，树的高度至多为 $\log_{\frac{4}{3}}(2cn)$。在每一层，我们总共做 $2cn$ 的工作。因此，期望运行时间为 $T(n) = \Theta(n \log n)$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 收尾的递推 $T(n) \le T(n/4) + T(3n/4) + 2cn$ 是**非对称分治**的典型——两边规模之和是 $n$，但最深的「最长路径」每层乘以 $\frac{3}{4}$（不是 $\frac{1}{2}$），所以树高是 $\log_{4/3} n$ 而不是 $\log_2 n$。关键结论：**即使分治不平衡，只要两边有「固定分数」的削减（$\frac{3}{4}n$ 而非 $n-1$），总代价仍是 $O(n \log n)$**——只是对数底变了（$\log_{4/3} n$ vs $\log_2 n$），常数稍大但渐近相同。这与基本快速排序 $T(n) = T(n-1) + \Theta(n) = \Theta(n^2)$ 形成鲜明对照：**「减法式」退化（每层只减少 1）导致平方，而「乘法式」缩减（每层乘以分数）保持对数**。这个「减法 vs 乘法」的分水岭，是你在 6.006 的欧几里得算法（减法版 vs 取模版）、以及二分搜索（每次减半）里反复看到的母题。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
