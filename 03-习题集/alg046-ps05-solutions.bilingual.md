<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 5 解答（Problem Set 5 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">2015 年 3 月 20 日（March 20, 2015）· 麻省理工学院（Massachusetts Institute of Technology）· 授课教授（Profs.）：Erik Demaine、Srini Devadas 与 Nancy Lynch</span>

<span style="color:#7f8c8d;">本习题集截止时间为 2015 年 3 月 20 日（周五）晚上 11:59（This problem set is due at 11:59pm on Friday, March 20, 2015）。</span>

---

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-1**</span>：阅读 CLRS（《算法导论》）第 11 章（Read CLRS, Chapter 11）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-2**</span>：完成习题（Exercise）11.3-5。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-3**</span>：阅读 CLRS 第 14 章（Read CLRS, Chapter 14）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-4**</span>：完成习题（Exercise）14.3-3。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-5**</span>：阅读 CLRS 第 15 章（Read CLRS, Chapter 15）。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-6**</span>：完成习题（Exercise）15.1-4。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-7**</span>：完成习题（Exercise）15.2-4。

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 5-8**</span>：完成习题（Exercise）15.4-5。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 5-1. New Operations for Skip Lists（跳表的新操作）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

本问题将证明跳表（skip lists）及其若干增强（augmentations）能够高效地回答关于「邻近」（nearby）元素的某些查询。在动态集数据结构（dynamic-set data structure）中，查询 FINGER-SEARCH(x, k) 给定数据结构中的一个节点 $x$ 与一个键 $k$，它必须返回数据结构中包含键 $k$ 的一个节点 $y$。（你可以假设这样的节点 $y$ 确实出现在数据结构中。）目标是：当节点 $x$ 与 $y$ 在数据结构中彼此邻近时，FINGER-SEARCH 运行得更快。

**(a)** <span style="color:#7f8c8d;">[12 points]（12 分）</span> 为跳表编写 FINGER-SEARCH(x, k) 的伪代码（pseudocode）。假设跳表中的所有键互不相同（distinct）。假设给定的节点 $x$ 在第 0 层链表（level-0 list）中，且该操作应返回一个在第 0 层链表中存储 $k$ 的节点 $y$。

你的算法应以高概率（with high probability）运行 $O(\lg m)$ 步，其中 $m = 1 + |\text{rank}(x.\text{key}) - \text{rank}(k)|$。这里，$\text{rank}(k)$ 指键 $k$ 在动态集排序顺序（sorted order）中的秩（rank，即索引 index，当过程被调用时）。此处的「高概率」是相对于 $m$ 而言的；更精确地说，对任意正整数 $\alpha$，你的算法应以至少 $1 - \frac{1}{m^\alpha}$ 的概率在 $O(\lg m)$ 时间内运行。（此 $O$ 中隐含的常数可能依赖于 $\alpha$。）仔细分析你的算法。

在编写代码时，你可以假设跳表的实现在每个层级的前端（front）都存储一个 $-\infty$，并且每个层级的最后一个元素是 $+\infty$。

<span style="color:#7f8c8d;">6.046J/18.410J</span>

<!-- ===== PDF p2 ===== -->

**（续）**

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 对每个节点 $x$，我们使用如下字段（fields）：
>
> - $key[x]$：存储在节点 $x$ 中的键（the key stored in node $x$）
> - $level[x]$：包含 $x$ 的链表的层级（the level of the linked list containing $x$）
> - $next[x]$：包含 $x$ 的链表中的下一个元素（the next element in the list containing $x$）
> - $prev[x]$：包含 $x$ 的链表中的前一个元素（the previous element in the list containing $x$）
> - $up[x]$：第 $level[x] + 1$ 层链表中包含 $key[x]$ 的元素（the element in the level $level[x] + 1$ list containing $key[x]$）
> - $down[x]$：第 $level[x] - 1$ 层链表中包含 $key[x]$ 的元素（the element in the level $level[x] - 1$ list containing $key[x]$）
>
> 过程调用为 FINGER-SEARCH(x, k)，其中 $x$ 是跳表中的一个第 0 层节点，$k \neq key[x]$ 是一个我们假设出现在跳表中的键。该过程返回一个第 0 层节点 $y$，使得 $k = key[y]$。
>
> 我们给出 $k > key[x]$ 情形的代码；$k < key[x]$ 的情形是对称的（symmetric）。算法分两个阶段进行。在第一个阶段，我们尽可能快地向上跨越层级（ascend levels），并向右移动（move to the right），在维持不变量（invariant）$key[next[z]] \le k$ 的前提下尽可能走得远。
>
> ```math
> \begin{aligned}
> &\textbf{FINGER-SEARCH}(x, k)\\
> &1\quad z = x\\
> &2\quad \textbf{Do forever:}\\
> &3\qquad \textbf{if } up[z] \ne \text{NIL} \textbf{ and } key[next[up[z]]] \le k\\
> &4\qquad\quad z = up[z]\\
> &5\qquad \textbf{else if } key[next[next[z]]] \le k\\
> &6\qquad\quad z = next[z]\\
> &7\qquad \textbf{else break}
> \end{aligned}
> ```
>
> 在第二个阶段，我们尽可能快地向右移动并下降（descend），直到到达包含键 $k$ 的第 0 层节点。这维持了不变量 $key[z] \le k$。
>
> ```math
> \begin{aligned}
> &1\quad \textbf{Do forever:}\\
> &2\qquad \textbf{if } key[next[z]] \le k\\
> &3\qquad\quad z = next[z]\\
> &4\qquad \textbf{else if } level[z] \ne 0\\
> &5\qquad\quad z = down[z]\\
> &6\qquad \textbf{else return } z
> \end{aligned}
> ```
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 中这两处比较符号写作 $\ne$（PDF 内部以「等号字形 + 叠加斜线」渲染，文本提取层只显示等号）。因此正确逻辑为：第一阶段第 3 行是「$up[z] \ne \mathrm{NIL}$ 且 $key[next[up[z]]] \le k$」——只有 $up[z]$ 非空时才能继续上升，否则访问 $next[up[z]]$ 会越界；第二阶段第 4–6 行是「若 $level[z] \ne 0$ 则 $z = down[z]$，否则返回 $z$」——已到最底层（$level[z] = 0$）时无处可降，直接返回当前节点。
>
> 为了理解为何这以高概率花费 $O(\lg m)$ 时间，我们首先证明在 finger search（指尖搜索）过程中到达的最高层（highest level）的一个高概率界。
>
> **Lemma 1（引理 1）**：设 $\beta$ 为任意正整数。以至少 $1 - \frac{1}{m^\beta}$ 的概率，FINGER-SEARCH 所到达的最高层至多为 $(\beta + 1)\lg m)$。
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 中该公式多写了一个右括号，即 $(\beta + 1)\lg m)$；正确形式应为 $(\beta + 1)\lg m$。此处照原样保留并注明。
>
> **Proof（证明）：** 搜索所访问的节点处出现的唯一键（keys）都在从 $key[x]$ 到 $k$ 的范围内（含两端，inclusive）。该范围内共有 $m$ 个键。

<!-- ===== PDF p3 ===== -->

**（续）**

> 对任意 $c$，此范围内任一特定键出现在层级 $> c\lg m$ 的链表节点上的概率至多为 $\frac{1}{2^{c\lg m}} = \frac{1}{m^c}$。因此，由 union bound（联合界），这 $m$ 个键中任意一个出现在层级 $> c\lg m$ 处的概率至多为 $\frac{m}{m^c} \le \frac{1}{m^{c-1}}$。取 $c = \beta + 1$ 即得结论。
>
> 现在固定 $c = \beta + 1$。为分析 FINGER-SEARCH(x, k) 的时间，我们使用如下引理（来自课堂讲授，以及 Problem Set 4（习题集 4））：
>
> **Lemma 2（引理 2）**：设 $\beta$ 为任意正整数。以至少 $1 - \frac{1}{m^\beta}$ 的概率，为得到 $c\lg m$ 次正面（heads）所需的抛硬币次数（coin tosses）为 $O(\lg m)$。（$O()$ 中的常数依赖于 $c$ 和 $\beta$。）
>
> 现在考虑算法的行为。在执行的第一个阶段，把「正面」（heads）与上升一层联系起来，把「反面」（tails）与向右移动联系起来。在第二个阶段，把「正面」与下降一层联系起来，把「反面」与向右移动联系起来。该引理蕴含：以至少 $1 - \frac{1}{m^\beta}$ 的概率，下面两件事都会发生：
>
> 1. 搜索向上部分的头 $c\lg m$ 层（若搜索在 $c\lg m$ 层之内结束，则为整个向上部分）花费 $O(\lg m)$ 时间。（其行为类似于从右到左进行、且被截断为只考虑最底部 $c\lg m$ 层的跳表搜索操作（skip list search operation）的逆过程。）
> 2. 搜索向下部分的最后 $c\lg m$ 层花费 $O(\lg m)$ 时间。（其行为类似于普通跳表搜索操作的最后几步。）
>
> 为完成「算法以高概率花费 $O(\lg m)$ 时间」的证明，固定 $\beta = \alpha + 1$。union bound（联合界）告诉我们：以至少 $1 - \frac{2}{m^\beta}$ 的概率，下面两点都成立：搜索访问的层数至多为 $c\lg m$，且搜索的最底部 $c\lg m$ 层在 $O(\lg m)$ 时间内完成。换言之，以至少 $1 - \frac{2}{m^\beta}$ 的概率，整个搜索在 $O(\lg m)$ 时间内完成。由于 $\frac{2}{m^\beta} \le \frac{1}{m^\alpha}$，这蕴含以至少 $1 - \frac{1}{m^\alpha}$ 的概率，搜索在 $O(\lg m)$ 时间内完成。
>
> 动态集上的另一个查询是 RANK-SEARCH(x, r)：给定数据结构中的一个节点 $x$ 与一个正整数 $r$，返回数据结构中包含秩为 $\text{rank}(x) + r$ 的键的一个节点 $y$。你可以假设这样的节点出现在数据结构中。Rank search（秩搜索）可以在跳表中高效实现，但这需要用新的信息对跳表的节点进行增强（augmenting）。

**(b)** <span style="color:#7f8c8d;">[6 points]（6 分）</span> 定义一种增强跳表的方式，使之支持高效的 rank search（秩搜索），并证明你的增强不会提高 SEARCH、INSERT 和 DELETE 操作通常的高概率数量级时间界（high-probability order-of-magnitude time bounds）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**（接下页）

<!-- ===== PDF p4 ===== -->

**（续）**

> 向跳表中的每个节点 $x$ 添加另一个字段：
>
> - $count[x]$：跳表第 0 层中满足 $key[x] < k \le key[next[x]]$ 的键 $k$ 所对应节点的个数（the number of nodes in level 0 of the skip list that have keys $k$ with $key[x] < k \le key[next[x]]$）。
>
> 这一添加不影响 SEARCH 操作。
>
> 对 INSERT(k)，当在第 0 层链表中为 $k$ 寻找正确位置时，我们对每个向下移动（move down）所经过的节点的 count 加 1。当把新键插入第 0 层链表的节点 $x$ 中时，我们置 $count[x] = 1$。然后，当把键 $k$ 插入较高层链表的节点 $y$ 中时，我们通过把下一较低层链表中包含 $[k, \ldots, key[next[y]])$ 范围内键的所有节点的 count 相加来计算 $count[y]$。我们还将 $count[prev[y]]$ 减去 $count[y]$ 的新值。INSERT 的额外代价为 $O(\lg n)$（相对于 $n$ 而言以高概率成立），这可以通过再次应用 Lemma ??（引理 ??）看出。
>
> 对 DELETE(k)，对每个被移除的节点 $y$（无论在哪个层级），我们令 $count[prev[y]]$ 增加 $count[y] - 1$。
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 中此处引理编号缺失，显示为「Lemma ??」；结合上下文，应指前文 Lemma 2 一类关于抛硬币次数为 $O(\lg m)$ 的引理。

**(c)** <span style="color:#7f8c8d;">[7 points]（7 分）</span> 现在为跳表编写 RANK-SEARCH(x, r) 的伪代码。同样假设跳表中的所有键互不相同。假设给定的节点 $x$ 在第 0 层链表中，且该操作应返回第 0 层链表中的一个节点 $y$。

你的算法应以高概率运行 $O(\lg m)$ 步，这里的高概率相对于 $m = r + 1$ 而言。更精确地说，对任意正整数 $\alpha$，你的算法应以至少 $1 - \frac{1}{m^\alpha}$ 的概率在 $O(\lg m)$ 时间内运行。仔细分析你的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 这与 (a) 小题类似。我们同样使用两阶段算法：第一阶段尽可能快地上升，第二阶段尽可能快地向右移动。
>
> 第一阶段维持不变量（invariant）：$count[z] \le rem$。
>
> ```math
> \begin{aligned}
> &\textbf{RANK-SEARCH}(x, r)\\
> &1\quad z = x\\
> &2\quad rem = r\\
> &3\quad \textbf{Do forever:}\\
> &4\qquad \textbf{if } up[z] \ne \text{NIL} \textbf{ and } count[up[z]] \le rem\\
> &5\qquad\quad z = up[z]\\
> &6\qquad \textbf{else if } count[z] + count[next[z]] \le rem\\
> &7\qquad\quad rem = rem - count[z]\\
> &8\qquad\quad z = next[z]\\
> &9\qquad \textbf{else break}
> \end{aligned}
> ```
>
> 在第一阶段结束时，我们知道：$count[z] \le rem < count[z] + count[next[z]]$。
>
> 现在第二阶段尽可能快地向右移动，然后向下。

<!-- ===== PDF p5 ===== -->

**（续）**

> ```math
> \begin{aligned}
> &1\quad \textbf{Do forever:}\\
> &2\qquad \textbf{if } count[z] \le rem\\
> &3\qquad\quad rem = rem - count[z]\\
> &4\qquad\quad z = next[z]\\
> &5\qquad \textbf{else if } level[z] \ne 0\\
> &6\qquad\quad z = down[z]\\
> &7\qquad \textbf{else return } z
> \end{aligned}
> ```
>
> <span style="color:#1e8449;">**[note]** </span> 同 (a) 小题：这里的 $\ne$ 在 PDF 文本提取层被误显示为等号。正确逻辑为：第二阶段第 5–7 行是「若 $level[z] \ne 0$ 则 $z = down[z]$，否则返回 $z$」——到达最底层（$level[z] = 0$）时返回当前节点，否则继续下降一层。
>
> 分析与 (a) 小题的分析非常类似。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 5-2. Choosing Prizes（选择奖品）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

在本问题中，你面对一组 $n$ 个奖品（prizes），从中你被允许选择至多 $m$ 个奖品，其中 $m < n$。每个奖品 $p$ 有一个非负整数值（nonnegative integer value），记为 $p.\text{value}$。你的目标是最大化所选奖品的总价值（total value）。

该问题有几种变体，在下面的 (a)–(d) 小题中描述。在每种情况下，你都应给出一个求解该问题的高效算法（efficient algorithm），并分析你的算法的时间与空间需求。

在 (a)–(c) 小题中，奖品以序列（sequence）$P = (p_1, p_2, \ldots, p_n)$ 的形式呈现给你，且你的算法必须输出 $P$ 的一个子序列（subsequence）$S$。换言之，被选中的奖品 $S$（$|S| = m$）必须按照它们在 $P$ 中的相同顺序列出。

**(a)** <span style="color:#7f8c8d;">[4 points]（4 分）</span> 给出一个算法，返回 $P$ 的长度至多为 $m$ 的子序列 $S = (s_1, s_2, \ldots)$，使 $\sum_{j} s_j.\text{value}$ 最大。就 $n$ 和 $m$ 分析你的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 这简单地就是输出价值最大的 $m$ 个奖品。我们使用课堂讲授中的 SELECT 算法来寻找输入列表的第 $k = (n - m - 1)$ 个顺序统计量（order statistic）。然后围绕第 $k$ 个秩（rank）的奖品对输入列表进行划分（partition），并输出其右侧的所有奖品——恰好应有 $m$ 个。
>
> SELECT 与划分各花费 $O(n)$ 时间，总运行时间为 $O(n)$。就地（in-place）实现的 SELECT 只使用额外的 $O(1)$ 空间。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** （另一种方法（Alternate，原 PDF 误拼为 Aternate），次优方案（Sub-Optimal））这也可在 $O(n\lg m)$ 时间、$O(m)$ 空间内完成，方法是扫描一个表示 $P$ 的列表，用一个 min-heap（最小堆）来跟踪迄今见过的价值最高的 $m$ 个奖品。
>
> 更详细地说：对奖品列表的前 $m$ 个元素，我们直接把元素插入 min-heap。对每个后续元素，我们把新奖品的价值与堆中最小（堆顶，top）元素的价值比较。若新元素更大，则用新元素替换旧元素并调整其位置。这需要 $O(\lg m)$ 时间，处理整个奖品列表 $P$ 总共需要 $O(n\lg m)$ 时间。


<!-- ===== PDF p6 ===== -->

**（续）**

> 为了高效地输出最终的列表 $S$，一种有用的做法是为该构造补充 $P$ 的各元素与对应堆节点（heap nodes）之间的双向指针（two-way pointers）。这样当我们得到最终堆后，只需再次按顺序扫描一遍 $P$，输出那些出现在堆节点中的值即可。这一步耗时 $O(n)$。
>
> 总时间复杂度为 $O(n \lg m)$。由于这需要一个规模为 $m$ 的堆，所需空间为 $O(m)$。

**(b)** <span style="color:#7f8c8d;">[7 points]（7 分）</span> 现在假设有两类奖品：类型 A（type A）和类型 B（type B）。每个奖品的类型由属性 $p.\text{type}$ 给出。给出一个算法，返回 $P$ 的长度至多为 $m$ 的子序列 $S = (s_1, s_2, \ldots)$，使 $\sum_j s_j.\text{value}$ 最大，并受新的约束：在 $S$ 中，所有类型 A 的奖品都必须排在所有类型 B 的奖品之前。就 $n$ 和 $m$ 分析你的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 使用基于奖品列表前缀（prefixes）的 Dynamic Programming（动态规划）。我们定义两个函数 $C_A(i, k)$ 与 $C_B(i, k)$，其中 $0 \le i \le n$，$0 \le k \le m$：
>
> - $C_A(i, k)$：由 $(p_1, \ldots, p_i)$ 的子序列所能达到的最大总价值，该子序列至多包含 $k$ 个类型 A 的奖品。
> - $C_B(i, k)$：由 $(p_1, \ldots, p_i)$ 的子序列所能达到的最大总价值，该子序列由任意数量（可能为 0）的类型 A 奖品后接任意数量（可能为 0）的类型 B 奖品组成，且奖品总数至多为 $k$。
>
> 为计算这些函数的值，我们按 $i$ 依次增大的方式迭代进行。对于基础情形（base case），对每个 $k$ 有 $C_A(0, k) = C_B(0, k) = 0$；也就是说，在没有奖品可供选择时，我们无法取得正值。归纳地，对每个 $i$（$0 \le i \le n - 1$）和每个 $k$，我们用 $C_A(i, *)$ 与 $C_B(i, *)$ 的值来计算 $C_A(i + 1, k)$ 与 $C_B(i + 1, k)$。
>
> 对 $C_A$，我们定义 $C_A(i + 1, 0) = 0$；也就是说，若不允许选择任何奖品，则无法取得正值。对 $k \ge 1$，我们分两种情况定义 $C_A(i + 1, k)$：
>
> 1. 若 $p_{i+1}.\text{type} = A$，则
>
> ```math
> C_A(i + 1, k) = \max\left(p_{i+1}.\text{value} + C_A(i, k - 1),\; C_A(i, k)\right)
> ```
>
> max 中的第一个表达式对应选择新奖品，第二个表达式对应不选择它。
>
> 2. 若 $p_{i+1}.\text{type} = B$，则
>
> ```math
> C_A(i + 1, k) = C_A(i, k)
> ```
>
> 这里没有选择余地——我们不能选择新奖品，因为 $C_A$ 只考虑由类型 A 奖品组成的序列。
>
> 对 $C_B$，我们定义 $C_B(i + 1, 0) = 0$，并对 $k \ge 1$ 分两种情况定义 $C_B(i + 1, k)$：

<!-- ===== PDF p7 ===== -->

**（续）**

> 1. 若 $p_{i+1}.\text{type} = A$，则
>
> ```math
> C_B(i + 1, k) = \max\left(p_{i+1}.\text{value} + C_A(i, k - 1),\; C_B(i, k)\right)
> ```
>
> max 中的第一个表达式对应选择新奖品；若选择它，那么在位置 $i$ 之前就只能考虑由类型 A 奖品组成的序列。第二个表达式对应不选择新奖品。
>
> 2. 若 $p_{i+1}.\text{type} = B$，则
>
> ```math
> C_B(i + 1, k) = \max\left(p_{i+1}.\text{value} + C_B(i, k - 1),\; C_B(i, k)\right)
> ```
>
> 整个奖品序列的最佳总奖品价值为 $C_B(n, m)$。
>
> 共有 $(n + 1) \cdot (m + 1)$ 个子问题，每个子问题求解耗时 $O(1)$。因此，DP 的总运行时间为 $O(m \cdot n)$。在这种迭代构造中，计算 $i + 1$ 的值时我们只需存储索引 $i$ 的函数值，因此空间需求为 $O(m)$。
>
> 到目前为止，这只返回了可达到的最大奖品价值。为使 DP 返回实际选出的奖品序列，我们让每个子问题条目包含一个指向链表的指针，该链表保存着一个导致该子问题所记录价值（prize value）的奖品序列。时间复杂度仍为 $O(m \cdot n)$，因为构造每条新链表只需（可能）向旧链表追加一个元素。由于每个新子问题只向链表集合添加常数个节点，总空间为 $O(m \cdot n)$。

**(c)** <span style="color:#7f8c8d;">[7 points]（7 分）</span> 与 (a) 小题一样，这里只有一种类型的奖品。给出一个算法，返回 $P$ 的长度至多为 $m$ 的子序列 $S = (s_1, s_2, \ldots)$，使 $\sum_j s_j.\text{value}$ 最大，并受新的约束：在 $S$ 中，各奖品的值必须构成一个非递减序列（non-decreasing sequence）。就 $n$ 和 $m$ 分析你的算法。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 使用 Dynamic Programming（动态规划）。与 (b) 小题一样，我们基于奖品列表的前缀构造。这次我们定义一个函数 $P(i, k, z)$，其中 $0 \le i \le n$，$0 \le k \le m$，而 $z$ 是集合 $\{p_i.\text{value} \mid 1 \le i \le n\}$ 中的任意整数：
>
> - $P(i, k, z)$：由 $(p_1, \ldots, p_i)$ 的子序列所能达到的最大总价值，该子序列至多包含 $k$ 个奖品，其中所选奖品的值依次不递减，且所有所选奖品的值都 $\le z$。
>
> 为计算 $P$ 的值，我们按 $i$ 依次增大的方式迭代进行。对于基础情形，对每个 $k$ 和 $z$ 都有 $P(0, k, z) = 0$。我们按如下方式用 $P(i, *, *)$ 的值计算 $P(i + 1, k, z)$ 的值。
>
> 对每个 $z$ 定义 $P(i + 1, 0, z) = 0$。对 $k \ge 1$，我们分两种情况定义 $P(i + 1, k, z)$：
>
> 1. 若 $p_{i+1}.\text{value} > z$，则
>
> ```math
> P(i + 1, k, z) = P(i, k, z)
> ```
>
> 此时新值不能使用，因为所有被选值都必须 $\le z$。

<!-- ===== PDF p8 ===== -->

**（续）**

> 2. 若 $p_{i+1}.\text{value} \le z$，则
>
> ```math
> P(i + 1, k, z) = \max\left(p_{i+1}.\text{value} + P(i, k - 1, p_{i+1}.\text{value}),\; P(i, k, z)\right)
> ```
>
> 这里，第一个表达式对应选择新奖品；若选择它，那么之前所有奖品的值都必须 $\le p_{i+1}.\text{value}$。第二项代表不选择新奖品这一选项。
>
> 整个奖品序列的最佳总奖品价值为 $P(n, k, \text{maxZ})$，其中 $\text{maxZ}$ 是奖品价值的最大值，$\text{maxZ} = \max_{1 \le i \le n} p_i.\text{value}$。
>
> 共有 $(n + 1) \cdot (m + 1) \cdot n$ 个子问题，每个子问题耗时 $O(1)$。因此，DP 的总运行时间为 $O(m \cdot n^2)$。在这种迭代构造中，计算 $i + 1$ 的值时我们只需存储索引 $i$ 的函数值，因此空间需求为 $O(m \cdot n)$。
>
> 与 (b) 小题一样，为使 DP 返回实际的奖品选择序列，我们让它维护指向包含奖品序列的链表的指针。新的时间复杂度仍为 $O(m \cdot n^2)$。由于每个新子问题只向链表集合添加常数个节点，总空间为 $O(m \cdot n^2)$。

在 (d) 小题中，奖品由一棵有根二叉树（rooted binary tree）$T$ 表示，根顶点为 $r$，其中每个顶点 $u$ 关联一个奖品 $u.\text{prize}$。设 $P$ 为树中的奖品集合。与之前一样，每个奖品 $p$ 有一个非负整数值属性 $p.\text{value}$。

**(d)** <span style="color:#7f8c8d;">[7 points]（7 分）</span> 给出一个算法，返回至多 $m$ 个奖品的集合 $S$，使 $\sum_{s \in S} s.\text{value}$ 最大，并受新的约束：对任意与 $T$ 的非根节点 $u$ 相关联的 $s \in S$，节点 $u.\text{parent}$ 处的奖品也必须在 $S$ 中。（这蕴含所选奖品必须关联到构成一棵以 $r$ 为根的连通（connected）的 $T$ 的子树（subtree）的节点。）

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 使用基于有根子树（rooted subtrees）的 Dynamic Programming（动态规划）。对任意顶点 $u$，设 $T_u$ 为以 $u$ 为根的子树。对任意顶点 $u$ 和任意整数 $k$（$0 \le k \le m$），定义 $C(u, k)$：
>
> - $C(u, k)$：从子树 $T_u$ 中选取的至多 $k$ 个奖品组成的集合 $S_u$ 所能达到的最大总价值，并受约束：对位于 $T_u$ 中某个顶点 $v \neq u$ 处的任意 $s \in S_u$，节点 $v.\text{parent}$ 处的奖品也必须在 $S_u$ 中。
>
> 我们可以递归地定义 $C$。作为基础情形，对每个 $u$ 定义 $C(u, 0) = 0$。我们还扩展 $C$，对每个 $j$ 定义 $C(\text{NIL}, j) = 0$：不存在的节点无法贡献任何价值。对顶点 $u$ 与 $0 < k \le m$，定义：
>
> ```math
> C(u, k) = \max_{0 \le j \le k - 1}\left(u.\text{prize.value} + C(u.\text{left}, j) + C(u.\text{right}, k - 1 - j)\right)
> ```

<!-- ===== PDF p9 ===== -->

**（续）**

> 也就是说，我们考虑通过选择根节点 $u$ 处的奖品，并把剩余 $k - 1$ 个奖品的选择分摊到两棵子树之间，所能得到的最佳选择。整个输入二叉树 $T$ 的最佳总价值为 $C(r, m)$。
>
> 为计算 $C(u, k)$ 的值，算法可以对树执行 Depth-First Search（深度优先搜索），并按逆 DFS 顺序（reverse DFS order）计算 $C()$ 的值。共有 $n \cdot (m + 1)$ 个子问题，每个子问题耗时 $O(m)$（因为 $k \le m$），总时间复杂度为 $O(n \cdot m^2)$。对每个子问题 $C(u, k)$，我们只需记录所有 $j$（$0 \le j \le k - 1$）对应的 $C(u.\text{left}, j)$ 与 $C(u.\text{right}, j)$；因此，最坏情况下每个子问题需要存储 $O(m)$ 个值，总空间需求为 $O(m \cdot n)$。
>
> 为了返回实际的奖品集合而不是只返回最大的总价值，我们对每个子问题额外跟踪一些信息：用于得到使价值最大的 $k$ 之分割（split）的实际 $j$ 值。（若有两个不同的 $j$ 值产生最大值，我们任选其一。）一旦有了这些值，我们就可以自上而下遍历树 $T$，比如按广度优先顺序（breadth-first order），为每个节点 $u$ 分配一个计数，表示最优选择（optimal selection）中将从 $T_u$ 选出的资源数量。在此过程中，我们对每个被分配非零计数的节点 $u$ 输出 $u.\text{prize}$。

<!-- ===== PDF p10 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**　http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**　Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">如需了解如何引用这些材料或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
