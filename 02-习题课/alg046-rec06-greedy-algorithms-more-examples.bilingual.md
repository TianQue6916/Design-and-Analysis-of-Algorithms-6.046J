<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 6（复习课 6）：Greedy Algorithms: More Examples（贪心算法：更多例子）**</span> <span style="color:#7f8c8d;">（2015 年 3 月 20 日 · Massachusetts Institute of Technology（麻省理工学院），Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**Process Scheduling（进程调度）**</span>

你有一台 computer（计算机）和 $n$ 个进程，它们的处理时间（processing time）分别为 $t_1, \dots, t_n$。你必须决定运行这些进程的顺序。设 $p_i$ 表示你运行的第 $i$ 个进程。于是，进程 $p_i$ 的完成时间（completion time）$C_{p_i}$ 定义为

```math
C_{p_i} = \sum_{j=1}^{i} t_{p_j}
```

也就是到该进程结束为止所有进程的时间之和。你需要最小化平均完成时间（average completion time），即

```math
\frac{1}{n}\sum_{i=1}^{n} C_{p_i}
```

<span style="color:#00838f;">**Greedy solution（贪心解法）**</span>

这个问题有一个众所周知的贪心解法，称为 Shortest Processing Time First（SPTF，最短处理时间优先）规则。我们按照处理时间从低到高的顺序执行进程。下面来证明这一点。

假设 $t_{p_i} \ge t_{p_j}$ 且 $i < j$。如果我们将 $p_i$ 与 $p_j$ 对调（interchange），那么从进程 $i$ 到进程 $j$ 之间所有内容的完成时间都会减少 $t_{p_i} - t_{p_j}$，而该量是非负的（non-negative）；并且从进程 $j$ 及之后所有进程的完成时间保持不变。因此，对调后的进程顺序（的总完成时间）小于或等于原来的顺序。

通过这种方式，我们可以逐个执行 two-swaps（两两对调）来对进程时间排序，并且只会降低我们的平均完成时间。这可以在 $O(n \log n)$ 时间内完成。

<span style="color:#00838f;">**Online version（在线版本）**</span>

当进程可以被动态添加时，这个问题具有同样的解法。如果加入了一个处理时间比当前进程的剩余处理时间（remaining processing time）更短的进程，我们就切换到该进程并首先完成它。证明是类似的。

> <span style="color:#1e8449;">**[note]** </span> 这里的证明方法是典型的 exchange argument（交换论证）：只要找到相邻的一对「逆序」（即排在前面却处理时间更长的两个进程），对调它们总不会使目标变差，反复消除逆序即可得到最优排序。SPTF 对应的调度问题就是经典的「单机最小化总完成时间 $\sum C_i$」问题，平均完成时间最小化与总完成时间最小化完全等价（只差一个常数因子 $\frac{1}{n}$）。注意：由于平均完成时间只由各进程的完成时刻决定，而每个进程完成时刻又等于其自身及之前所有进程时间之和，因此按处理时间升序排列能同时压低每一个前缀和，这正是 SPTF 最优的直观原因。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**Event Overlap problem（事件重叠问题）**</span>

你的日历上有 $n$ 个事件，定义为具有开始时间（start time）$s_i$ 和结束时间（finish time）$f_i$ 的区间（interval）。这些事件可能重叠（overlap），而你希望参加所有事件，因此你将创造 $k$ 个自己的分身（clone）来实现这一点。你想要最小化所需分身的数量 $k$。一个分身可以参加某个互不重叠的事件子集。

<span style="color:#00838f;">**Greedy solution（贪心解法）**</span>

我们按开始时间对区间排序。我们从 0 个分身开始，并动态地将每个区间分配给其中一个分身。我们维护每个分身所分配到的最后一个区间的结束时间。

当我们遍历已排序的列表时，对每个区间而言，如果它的开始时间晚于某个分身的最后完成事件，我们就可以把这个区间分配给该分身而不产生重叠。于是我们把这个事件分配给它，并更新它的结束时间。如果它与任何分身都不兼容，我们就创建一个新分身，并把该事件分配给它。

<span style="color:#00838f;">**Correctness（正确性）**</span>

让我们考虑与添加第 $m$ 个分身相对应的事件。假设这发生在考虑区间 $(s_i, f_i)$ 时。这意味着此前考虑过的 $m - 1$ 个区间与该区间重叠。但是，由于这些区间都在 $s_i$ 之前开始（因为我们按开始时间排序），这意味着在时刻 $s_i$ 至少有 $m$ 个并发（concurrent）区间。这意味着最优解（optimal solution）至少使用 $\ge m$ 个分身。

<span style="color:#00838f;">**Implementation（实现）**</span>

下面是一个 $O(n \log n)$ 的实现。我们维护一个 min-heap（最小堆），其中存放每个分身最后一个区间的结束时间。当加入一个区间 $(s_i, f_i)$ 时，如果堆上的最小结束时间 $\ge s_i$，那么所有分身都不兼容，因此我们通过把 $f_i$ 加入堆来创建一个新分身。如果堆上的最小结束时间 $< s_i$，我们就可以把该区间加给它，于是从堆中弹出（pop）该最小值并加入 $f_i$。

**Note（注）：** 这个问题实际上可以推广为：由 Dilworth’s theorem（迪尔沃斯定理）将任意偏序（partial ordering）分解成链（chain）。

> <span style="color:#1e8449;">**[note]** </span> 本问题正是区间图上的 minimum clique cover / interval coloring（最小团覆盖 / 区间染色）问题：把「分身」看成不同的颜色，互不重叠的区间可同色，所求最小分身数就等于任意时刻重叠区间的最大数量。而把「区间可被同一分身接管」理解为一种偏序（$i$ 先于 $j$ 当且仅当 $f_i \le s_j$），那么将全部区间划分成最少的「链」恰好就是 Dilworth’s theorem（迪尔沃斯定理）的结论：最小链覆盖数等于最大反链（antichain）的大小；对区间偏序而言，最大反链即某个时间点上两两重叠的区间集合。实现中用最小堆取当前最早可用分身的结束时间，与经典的「按开始时间排序 + 最小堆贪心」求最少会议室的算法完全一致。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**Fractional Make-Change（分数版找零问题）**</span>

让我们考虑上一次复习课中的 make-change problem（找零问题），并做几处修改。不再是硬币（coin），我们有 $m$ 种金属（metal）。给定一个数值 $N$，我们要为 $N$ 分钱（cents）找零。金属类型 $i$ 每公斤的价值为 $S_i$，我们拥有其中 $n_i$ 公斤。由于这些金属处于熔融（molten）状态，而且我们有一台无限精度（infinite precision）的秤，因此我们也可以给出非整数（non-integral）的重量。

这意味着如果我们选择使用 $k_i$ 公斤类型 $i$ 的金属，其中 $0 \le k_i \le n_i$，那么给出的价值将是 $S_i k_i$。现在的目标是最小化所用金属的总重量 $\sum k_i$。

<span style="color:#00838f;">**Greedy solution（贪心解法）**</span>

我们针对原始找零问题的贪心直觉现在依然成立。我们取最有价值的金属，并尝试用它尽可能多地满足剩余价值。

换句话说，我们按每公斤价值递减的顺序对金属排序，并设定剩余价值（remaining value）$r = N$。当我们从 $i = 1$ 到 $m$ 遍历已排序列表时，如果 $S_i n_i < r$，就令 $r = r - S_i n_i$，并把 $n_i$ 公斤金属 $i$ 加入我们的集合。否则，如果 $S_i n_i \ge r$，就向收集集合中加入 $\frac{r}{S_i}$ 公斤金属 $i$，并令 $r = r - S_i \cdot \frac{r}{S_i} = 0$。此时我们中断（break），因为需求已经满足。

<span style="color:#00838f;">**Correctness（正确性）**</span>

证明遵循 cut-and-paste（剪切-粘贴）方法。假设我们有 $w$ 公斤未使用的金属 $i$，而在最优解中我们使用了 $w$ 公斤金属 $j$，使得 $S_i > S_j$。那么，我们可以用 $w \cdot \frac{S_j}{S_i}$ 更多的金属 $i$ 来替换那 $w$ 公斤金属 $j$。由于 $S_i > S_j$，有 $w \cdot \frac{S_j}{S_i} < w$，因此根据假设我们拥有这么多金属 $i$。此外，我们的总重量严格减少。这与「我们的解是最优的」这一假设相矛盾。

我们得出结论：我们总是会在使用价值较低的金属之前先用尽更有价值的金属，因此我们的贪心算法是正确的。

> <span style="color:#1e8449;">**[note]** </span> 本问题实质上就是 fractional knapsack（分数背包）问题：每公斤价值 $S_i$ 相当于「单位价值密度」，$\frac{r}{S_i}$ 公斤金属 $i$ 产生的价值恰好等于剩余需求 $r$。与普通分数背包不同的是，这里限定了每种金属的库存 $n_i$，并且需求是「恰好凑满 $N$」而不是「在容量内最大化价值」，但只要按单位价值降序逐种取用，结论同样成立。贪心之所以正确，关键在于金属是可分割（divisible）的——这也是它区别于整数背包（integral version）之处：整数版贪心可能失败（例如硬币面值不满足 canonical 条件时）。cut-and-paste 论证的思想：一旦最优解中出现了「用低价值金属而没有用光高价值金属」的反常情形，就可以把低价值部分替换为更轻的高价值金属，总重量严格下降，从而与最优性矛盾。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）　Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
