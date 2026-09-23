<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b">**讲次 24（Lecture 24）：缓存无关算法 II（Cache-oblivious algorithms II）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

- **搜索（Search）**
  - 二分（binary）
  - $B$-叉（B-ary）
  - 缓存无关（cache-oblivious）
- **排序（Sorting）**
  - 归并排序（mergesorts）
  - 缓存无关（cache-oblivious）

<span style="color:#c0392b">**为什么用 LRU 块替换策略？（Why LRU block replacement strategy?）**</span>

```math
\text{LRU}_M \le 2 \cdot \text{OPT}_{M/2} \quad \text{[Sleator and Tarjan 1985]}
```

**证明（Proof）**：
- 把块访问序列划分为**极大阶段（phases）**，每阶段含 $M/B$ 个不同块。
- LRU 每阶段花费 $\le M/B$ 次内存传输。
- OPT 每阶段必须花费 $\ge M/2B$ 次内存传输：最好情况下，它以**装满所需物品的 $M/2$ 缓存**开始该阶段。但阶段内有 $M/B$ 个块。所以**至多一半免费**。

> <span style="color:#1e8449;">**[note] Note（译者注，LRU 的最优性常数因子）:**</span> 这一页回答一个根本问题：**为什么缓存替换策略用 LRU 就够了？**答案（Sleator 与 Tarjan，1985）是一句名言：**任何 LRU 缓存（大小 $M$）的性能，至多是最优离线算法（optimal offline, 大小 $M/2$）的 2 倍**——注意 OPT 用了**双倍缓存的一半**（$M/2$）仍被 LRU 的 $M$ 压制在常数因子内。证明用**阶段划分**：把访问序列切成「$M/B$ 个不同块」的阶段，LRU 每阶段**至多**传 $M/B$ 块（阶段内不重复缺块，因为 LRU 的 $M$ 缓存装得下 $M/B$ 块；每阶段第一块可能缺、之后都命中）；而 OPT 用 $M/2$ 缓存，每阶段开始时缓存里**最多**有 $M/(2B)$ 个「对了的」块，阶段里共 $M/B$ 个块，所以**至少 $M/(2B)$ 次必缺**。于是 LRU/OPT $\le (M/B)/(M/2B) = 2$。**推论**：缓存无关分析可以用 LRU 作为「理想替换策略」的替身——只要算法对任意 LRU 缓存达到某传输界，就对「理想最优缓存」也成立（常数 2 内）。这让你在 CSAPP 学的「缓存替换策略」第一次有了**理论保证**。

<span style="color:#c0392b">**搜索（Search）**</span>

在比较模型中**预处理 $n$ 个元素**，以支持对 $x$ 的**前驱搜索（predecessor search）**。

<span style="color:#c0392b">**$B$-树（B-trees）**</span>

它们支持**前驱**（以及插入、删除），代价是 $O(\log_{B+1} N)$ 次内存传输。
- 每个节点占据 $\Theta(1)$ 个块。
- 高度 $= \Theta(\log_B N)$。
- **需要知道 $B$**。

> <span style="color:#1e8449;">**[note] Note（译者注，$B$-树 vs 缓存无关）:**</span> **$B$-树**是外部内存模型下的标准搜索结构：每个节点 $\Theta(1)$ 块、高度 $\Theta(\log_B N)$，一次前驱搜索 $\Theta(\log_B N)$ 次传输（每层一块）。它的缺点是**必须知道 $B$**（节点大小按 $B$ 设计），换台机器、$B$ 变了就得重建——这正是缓存无关模型要消除的。你在 6.006 的 Recitation 2（B-trees）和数据库系统里见过 B-树的实践价值；而本讲的目标是：**能不能不需要知道 $B$，也达到 $O(\log_B N)$？**答案是 vEB 布局（下一页）。「$B+1$」里 $+1$ 是为了处理 $B$ 很小的退化情形（如 $B=1$）。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**搜索（续）**</span>

**二分搜索（Binary search）**

大致上，每轮迭代访问**一个不同的块**，直到我们进入 $x$ 所在的块。因此，

```math
MT(N) = \Theta(\log N - \log B) = \Theta(\log(N/B)) \quad \text{SLOW（慢）}
```

<span style="color:#c0392b">**van Emde Boas 布局（van Emde Boas layout）**</span>

[Prokop 1999]

- 把 $N$ 个元素存在**完全二叉搜索树**中。
- 在**中间层（middle level）的边**处**切开** BST。
- **递归布局**各片段并**拼接**。
- 像分块矩阵乘法一样，**片段的顺序无关紧要**；只需每片段**连续存储**。

**vEB 布局中 BST 搜索的分析**：
- 考虑**细化级别**，此时结构 $\le B$ 个节点。
- vEB 树的高度介于 $\frac{1}{2}\lg B$ 与 $\lg B$ 之间 $\Rightarrow$ 大小介于 $\sqrt{B}$ 与 $B$ 之间。
- $\Rightarrow$ 任何根到节点的路径（搜索路径）访问 $\le \frac{\lg N}{\frac{1}{2}\lg B} = 2 \log_B N$ 棵大小 $\le B$ 的树。
- 每棵大小 $\le B$ 的树占据 $\le 2$ 个内存块。
- $\Rightarrow \le 4 \log_B N = O(\log_B N)$ 次内存传输。

> <span style="color:#1e8449;">**[note] Note（译者注，为什么朴素二分搜索慢）:**</span> 朴素二分搜索在缓存无关意义下**慢**：每轮迭代跳到数组的**不同区域**，几乎每次都缺块——前 $\Theta(\log N)$ 轮每轮一块，只有进入 $x$ 所在块后的最后 $\log B$ 轮才命中缓存，所以 $\Theta(\log(N/B))$ 次传输。对比 $B$-树的 $\Theta(\log_B N) = \Theta(\log N / \log B)$，二分搜索多了一个 $\log B$ 因子——**信息论上每块 $B$ 个字本该带来 $\log B$ 倍的搜索效率，二分搜索完全没用上**。这就像你在 CSAPP 学的「**遍历要按缓存块组织**」：二分跳转破坏了空间局部性。**vEB 布局**（Prokop 1999 硕士论文，正是缓存无关算法开山之作的一部分）把 BST 按**递归切中间层**重新排布，让「搜索路径上连续访问的节点」落在同一块里，从而无需知道 $B$ 就达到 $O(\log_B N)$。分析要点：切到「大小 $\le B$」的层级后，每个子结构占 $\le 2$ 块，搜索路径穿过 $\le 2\log_B N$ 个这样的子结构，每子结构 $\le 2$ 块，总 $\le 4\log_B N$ 次传输。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**搜索（续）**</span>

- 这可以推广到**非 2 的幂的高度**、**常数分支因子的 $B$-树**、以及**动态 $B$-树**：$O(\log_B N)$ 次传输的插入/删除。[Bender, Demaine, Farach-Colton 2000]

<span style="color:#c0392b">**排序（Sorting）**</span>

**$B$-树（B-trees）**

向（缓存无关）$B$-树插入 $N$ 次 $\Rightarrow MT(N) = \Theta(N \log_B N)$ **不是最优的（NOT OPTIMAL）**。相比之下，BST 排序是最优的 $O(N \lg N)$。

**二叉归并排序（Binary mergesort）**
- 二叉归并排序是缓存无关的。
- 归并是 3 个并行扫描。
- $\Rightarrow MT(N) = 2MT(N/2) + O(N/B + 1)$，$MT(M) = O(M/B)$。
- 递归树有 $\lg(N/M)$ 层，每层贡献 $O(N/B)$。
- $\Rightarrow MT(N) = \frac{N}{B} \lg \frac{N}{M}$。← 比之前讨论的 $B$-树版本**快 $B$ 倍**！

**$M/B$-路归并排序（$M/B$-way mergesort）**
- 把数组分成 $M/B$ 个相等子数组。
- 递归排序每个。
- 用 $M/B$ 个并行扫描归并（每个列表保持一个「当前」块）。
- $\Rightarrow MT(N) = \frac{M}{B} MT\left(\frac{N}{M/B}\right) + O(N/B + 1)$，$MT(M) = O(M/B)$。
- $\Rightarrow$ 高度变为 $\log_{M/B} \frac{N}{M} + 1 = \log_{M/B} \frac{N}{B} - \log_{M/B} \frac{M}{B} + 1 = \log_{M/B} \frac{N}{B}$。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**排序（续）**</span>

```math
\Rightarrow MT(N) = O\left(\frac{N}{B} \log_{M/B} \frac{N}{B}\right)
```

这在比较模型下是**渐近最优的（asymptotically optimal）**。

<span style="color:#c0392b">**缓存无关排序（Cache-oblivious Sorting）**</span>

这需要**高缓存假设（tall-cache assumption）**：对某个固定的 $\varepsilon > 0$，$M = \Omega(B^{1+\varepsilon})$，例如 $M = \Omega(B^2)$ 或 $M/B = \Omega(B)$。

那么，带递归（「**漏斗 funnel**」）归并的 $\approx N^\varepsilon$-路归并排序即可工作。

<span style="color:#c0392b">**优先队列（Priority Queues）**</span>

- 每次插入或 delete-min 的代价是 $O\left(\frac{1}{B} \log_{M/B} \frac{N}{B}\right)$（摊还）。
- 推广了排序。
- 同时适用于外部内存与缓存无关。
- 参见 6.851。

> <span style="color:#1e8449;">**[note] Note（译者注，排序的三个版本与最优界）:**</span> 排序的演进是缓存无关分析的集大成：**(1) 用 B-树排序**（插 $N$ 次）$= \Theta(N \log_B N)$ **不是最优**——它「买」了动态结构却付出 $\log_B N$ 每元素，比最优差 $O(\lg B)$；**(2) 二叉归并排序** $MT(N) = \frac{N}{B}\lg\frac{N}{M}$——递归树 $\lg(N/M)$ 层、每层 $O(N/B)$ 次传输，**比 B-树版快约 $B$ 倍**（因为归并的扫描利用了块局部性、每元素只摊 $\frac{1}{B}$ 块）；**(3) $M/B$-路归并排序** $MT(N) = O(\frac{N}{B}\log_{M/B}\frac{N}{B})$——分 $M/B$ 路使**每层传输都是 $N/B$**、层数压缩到 $\log_{M/B}(N/B)$，这正是**外部内存排序的下界**（Aggarwal-Vitter 1988），所以**渐近最优**。这个「**路数 = 缓存块数**」的直觉：归并时让 $M/B$ 个「当前块」同时驻留缓存，每个列表只需一个块来回滚。**高缓存假设 $M = \Omega(B^{1+\varepsilon})$** 是缓存无关排序（funnelsort）能工作的前提——它保证「缓存能装下很多块」而不是「一个巨块」。

> <span style="color:#1e8449;">**[note] Note（译者注，本讲在算法版图的位置）:**</span> 至此六讲「计算复杂性/系统」主题（Lec 16-24）收束：**缓存无关算法**让同一份代码在任意缓存大小的机器上都达到渐近最优的内存传输。核心武器是一以贯之的**递归分治**：分到「适合缓存」的子问题即可，而**不需要知道 $B$ 与 $M$**（递归的「自我缩放」自动适配）。本讲引用的经典结果：**funnelsort**（Frigo-Leiserson-Prokop-Ramachandran，1999）达到最优排序界；**动态缓存无关 B-树**（Bender-Demaine-Farach-Colton，2000）实现 $O(\log_B N)$ 的动态搜索；**优先队列**把排序推广成支持插入/删除最小（摊还 $O(\frac{1}{B}\log_{M/B}\frac{N}{B})$），并指向 6.851（高级数据结构）——你方舟计划里 6.851 是值得关注的后续课程。这条「**内存层级 → LRU 最优性 → vEB 布局 → 最优归并**」的主线，与你 CSAPP 的内存层级、以及本课早先的分治/树结构知识完全咬合。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
