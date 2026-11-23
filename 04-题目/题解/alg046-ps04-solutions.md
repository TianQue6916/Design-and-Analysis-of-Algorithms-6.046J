<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 4 解答（Problem Set 4 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">2015 年 3 月 5 日（March 5, 2015）· 麻省理工学院（Massachusetts Institute of Technology）· 授课教授（Profs.）：Erik Demaine、Srini Devadas 与 Nancy Lynch</span>

<span style="color:#7f8c8d;">本习题集截止时间为 2015 年 3 月 5 日（周四）晚上 11:59（This problem set is due at 11:59pm on Thursday, March 5, 2015）。</span>

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-1.**</span> 阅读 CLRS 第 17 章（Read CLRS, Chapter 17）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-2.**</span> 完成习题 17.1-3（Exercise 17.1-3）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-3.**</span> 完成习题 17.2-2（Exercise 17.2-2）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-4.**</span> 完成习题 17.3-2（Exercise 17.3-2）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-5.**</span> 阅读 CLRS 第 7 章（Read CLRS, Chapter 7）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-6.**</span> 完成习题 7.1-3（Exercise 7.1-3）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-7.**</span> 完成习题 7.2-5（Exercise 7.2-5）。

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Exercise 4-8.**</span> 完成习题 7.4-4（Exercise 7.4-4）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 4-1. Extreme FIFO Queues（极端 FIFO 队列）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

设计一个维护整数 FIFO 队列（FIFO queue）的数据结构，支持 ENQUEUE（入队）、DEQUEUE（出队）与 FIND-MIN（求最小值）操作，且每种操作都在 $O(1)$ 的摊还时间（amortized time）内完成。换言之，任意 $m$ 次操作构成的序列都应在 $O(m)$ 时间内完成。你可以假定：在任何一次执行（execution）中，所有被入队的元素（items）都是互不相同的（distinct）。

> <span style="color:#1e8449;">**[note]** </span> 原文在题目中把 DEQUEUE 排印为 "DEUEUE"，系笔误；下文按 DEQUEUE（出队）处理。

**(a)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

描述你的数据结构。请包含清晰的 invariants（不变量），用以刻画其关键性质。提示（Hint）：用一个真正的队列（actual queue）加上一个或多个辅助数据结构（auxiliary data structure）来做簿记（bookkeeping）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 例如，我们可以使用一个 FIFO 队列 Main（主队列）和一个辅助的链表（linked list）Min（最小值链表），并满足以下不变量：
>
> 1. 元素 $x$ 出现在 Min 中，当且仅当（if and only if）$x$ 是 Main 的某个尾段（tail-segment）的最小元素。
> 2. Min 按递增顺序（increasing order）从前到后排序。

**(b)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

用文字或伪代码（pseudo-code）仔细描述你的 ENQUEUE、DEQUEUE 与 FIND-MIN 过程。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**

<!-- ===== PDF p2 ===== -->

**（续）**

> ```text
> ENQUEUE(x)
> 1  把 x 添加到 Main 的末尾（end of Main）。
> 2  从列表末尾开始，检查 Min 中的元素，移除那些大于 x 的元素；当遇到小于 x 的元素时停止检查。
> 3  把 x 添加到 Min 的末尾。
>
> DEQUEUE()
> 1  移除并返回 Main 的第一个元素 x。
> 2  若 x 是 Min 中的第一个元素，则把它移除。
>
> FIND-MIN()
> 1  返回 Min 的第一个元素。
> ```

**(c)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

证明你的操作给出正确的结果。提示（Hint）：你可能想证明其正确性可由你的数据结构不变量推出。在这种情况下，你还应简要论证（sketch arguments for）这些不变量为何成立。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：**
>
> 本解答针对上述给定的数据结构与过程选择；你自己的方案可能有所不同。
>
> 只有两个操作会返回结果，即 DEQUEUE 与 FIND-MIN。DEQUEUE 返回 Main 的第一个元素，这是正确的，因为 Main 维护着真正的队列（actual queue）。FIND-MIN 返回 Min 的第一个元素。它是 Min 中最小的元素，因为 Min 按递增顺序排序（由上文的 Invariant 2，不变量 2 保证）。Main 的最小元素就是由整个 Main 构成的尾段的最小值，也就是 Main 所有尾段最小值（tail-mins）中最小的那个。由不变量 1，这正是 Min 中最小的元素。因此，FIND-MIN 返回 Main 的最小元素，正如所需。
>
> 不变量的证明：不变量在初始状态（initial state）中是空洞地成立的（vacuously true）。我们论证 ENQUEUE 与 DEQUEUE 保持这些不变量；FIND-MIN 不影响它们。
>
> 容易看出两种操作都保持不变量 2：由于 DEQUEUE 操作只能从 Min 中移除元素，其余元素的顺序得以保持。对于 ENQUEUE(x)，我们从 Min 的末尾移除元素，直到找到一个小于 $x$ 的元素，然后把 $x$ 添加到 Min 的末尾。因为在 ENQUEUE(x) 之前 Min 已处于有序状态，当我们停止移除元素时，可知 Min 中剩余的所有元素都小于 $x$。由于我们没有改变 Min 中任何原有元素的顺序，所有元素仍然保持有序。
>
> 所以剩下的任务是证明不变量 1。它有两个方向：
>
> - **新的 Min 列表包含所有的尾段最小值（tail-mins）。**
>
>   ENQUEUE(x)：$x$ 是 Main 的单元素尾段（singleton tail-segment）的最小元素，它被加入 Min。此外，由于现在每个尾段都含有值 $x$，所有值大于 $x$ 的元素都不再可能是尾段最小值。所以，在它们被移除之后，Min 仍然包含所有的尾段最小值。

<!-- ===== PDF p3 ===== -->

**（续）**

> **DEQUEUE 元素 $x$：** 可能从 Min 中被移除的唯一元素是 $x$。移除 $x$ 是合理的，因为它已不在 Main 中，所以它不可能再是尾段最小值。其余所有尾段最小值仍保留在 Min 中。
>
> - **新的 Min 的所有元素都是尾段最小值。**
>
>   ENQUEUE(x)：$x$ 是唯一被加入 Min 的值，它是单元素尾段的最小值。Min 列表中保留下来的每个其他元素 $y$，在 ENQUEUE 之前都是尾段最小值，且小于 $x$。因此，$y$ 在 ENQUEUE 之后仍是尾段最小值。
>
>   DEQUEUE 元素 $x$：那么我们声称，若 $x$ 在操作前位于 Min 中，则它是 Min 的第一个元素，因此也会被从 Min 中移除。现在，若 $x$ 在 Min 中，它必是 Main 的某个尾段（tail）的最小元素。由于 $x$ 是 Main 的第一个元素，这个尾段必然包含整个队列。所以 $x$ 必是 Min 中最小的元素，这意味着它是 Min 的第一个元素。Min 中的每个其他元素 $y$ 在 DEQUEUE 之前是尾段最小值，在 DEQUEUE 之后仍是尾段最小值。

**(d)** <span style="color:#7f8c8d;">[10 points]（10 分）</span>

分析时间复杂度：每种操作的最坏情形代价（worst-case cost），以及任意 $m$ 次操作序列的摊还代价（amortized cost）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** DEQUEUE 与 FIND-MIN 在最坏情形下都是 $O(1)$ 操作。ENQUEUE 在最坏情形下是 $O(m)$。为说明代价可以达到这么大，设想依次对元素 $2, 3, 4, \ldots, m - 1, m$ 执行 ENQUEUE 操作。在此之后，Min 包含 $\{2, 3, 4, \ldots, m - 1, m\}$。然后执行 ENQUEUE(1)。这一步需要 $\Omega(m)$ 时间，因为 Min 中所有其他条目会被一个接一个地移除。
>
> 然而，任意 $m$ 次操作序列的摊还代价是 $O(m)$。为证明这一点，我们使用势能论证（potential argument）。首先如下定义各操作的实际代价（actual cost）：任何 FIND-MIN 操作的代价为 $1$。任何 DEQUEUE 操作的代价为 $2$（从 Main 中移除，以及可能从 Min 中移除）。ENQUEUE 操作的代价为 $2 + s$，其中 $s$ 是从 Min 中移除的元素个数。定义势能函数（potential function）$\Phi = |\mathrm{Min}|$。
>
> 现在考虑一个操作序列 $o_1, o_2, \ldots, o_m$，令 $c_i$ 表示操作 $o_i$ 的实际代价。令 $\Phi_i$ 表示恰好执行了 $i$ 次操作之后势能函数的值；令 $\Phi_0$ 表示 $\Phi$ 的初始值，此处为 $0$。把操作实例 $o_i$ 的摊还代价（amortized cost）$\hat{c}_i$ 定义为 $c_i + \Phi_i - \Phi_{i-1}$。
>
> 我们声称对每个 $i$ 都有 $\hat{c}_i \le 2$。若能证明这一点，则可知整个操作序列的实际代价满足：
>
> ```math
> \sum_{i=1}^{m} c_i = \sum_{i=1}^{m} \hat{c}_i + \Phi_0 - \Phi_m \le \sum_{i=1}^{m} \hat{c}_i \le 2m
> ```
>
> 这就给出了所需的 $O(m)$ 摊还界（amortized bound）。

<!-- ===== PDF p4 ===== -->

**（续）**

> 为证明对每个 $i$ 都有 $\hat{c}_i \le 2$，我们考虑三类操作。若 $o_i$ 是 FIND-MIN 操作，则
>
> ```math
> \hat{c}_i = 1 + \Phi_i - \Phi_{i-1} = 1 < 2
> ```
>
> 若 $o_i$ 是 DEQUEUE，则由于列表的长度不可能增加，我们有：
>
> ```math
> \hat{c}_i = c_i + \Phi_i - \Phi_{i-1} \le 2 + 0 \le 2
> ```
>
> 若 $o_i$ 是 ENQUEUE，则
>
> ```math
> \hat{c}_i = c_i + \Phi_i - \Phi_{i-1} \le 2 + s - s = 2
> ```
>
> 其中 $s$ 是从 Min 中移除的元素个数。因此，在每种情形下都有 $\hat{c}_i \le 2$，正如所声称的。
>
> <span style="color:#1e8449;">**[note]** </span> 上述势能论证的 ENQUEUE 情形有一处来自原 PDF 的小瑕疵：ENQUEUE 的实际代价是 $c_i = 2 + s$（入队 + 从 Min 中移除 $s$ 个元素），但入队还会**新增** 1 个元素到 Min（新元素 $x$ 本身就是新的尾段最小值），所以 $\Delta\Phi = (1 - s)$ 而非 $-s$。由此正确摊还代价应为 $\hat{c}_i = (2 + s) + (1 - s) = 3$，而不是 $\le 2$。总界相应为 $\hat{c}_i \le 3$，即任意 $m$ 次操作序列的摊还代价为 $O(3m) = O(m)$，结论不变——下面的记账法正是给 ENQUEUE 记 $3$（与其真实摊还代价一致）。
>
> 或者，我们也可以使用记账法（accounting method）。沿用上述相同的实际代价。给每个 ENQUEUE 分配摊还代价 $3$，每个 DEQUEUE 分配摊还代价 $2$，每个 FIND-MIN 分配摊还代价 $1$。那么我们必须论证：对任意操作序列及上述代价，
>
> ```math
> \sum_{i=1}^{m} \hat{c}_i \ge \sum_{i=1}^{m} c_i
> ```
>
> 之所以如此，是因为每个 ENQUEUE(x) 贡献了 $3$ 的摊还代价，它足以覆盖其自身的实际代价 $2$，再加上日后可能把 $x$ 从 Min 中移除的代价。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 4-2. Quicksort Analysis（快速排序分析）**</span> <span style="color:#7f8c8d;">[25 points]（25 分）</span>

在本问题中，我们将从错误概率（error probabilities）的角度而非期望（expectation）的角度来分析 QUICKSORT 的时间复杂度。假设待排序数组为 $A[1 \ldots n]$，并用 $x_i$ 表示在调用 QUICKSORT 之前初始位于数组位置 $A[i]$ 的元素。假设所有 $x_i$ 的值互不相同。

在求解本问题时，回顾课堂上的一个声明（claim）会很有用。这里略作改写：

> <span style="color:#2471a3;">**[claim]**</span> **Claim（声明）：** 设 $c > 1$ 为实常数，$\alpha$ 为正整数。那么，掷一枚公平硬币（fair coin）$3(\alpha + c)\lg n$ 次，至少出现 $c \lg n$ 次正面（heads）的概率至少为 $1 - \frac{1}{n^{\alpha}}$。

<span style="color:#7f8c8d;">注意（Note）：高概率界（High probability bounds）以及上述 Claim 将在周二的课堂上讲授。</span>

**(a)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

考虑一个特定元素 $x_i$。考虑一次 QUICKSORT 对包含元素 $x_i$、大小为 $m \ge 2$ 的子数组 $A[p \ldots p+m-1]$ 的递归调用。证明：以至少 $\frac{1}{2}$ 的概率，要么这次 QUICKSORT 调用把 $x_i$ 选为主元（pivot element），要么下一次包含 $x_i$ 的 QUICKSORT 递归调用所涉及的子数组大小至多为 $\frac{3}{4}m$。

<!-- ===== PDF p5 ===== -->

**（续）**

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 假设主元值（pivot value）为 $x$。若 $\lfloor \frac{m}{4} \rfloor + 1 \le x \le m - \lfloor \frac{m}{4} \rfloor$，那么划分（partition）产生的两个子数组大小都至多为 $\frac{3}{4}m$。此外，位于该范围内的 $x$ 值的个数至少为 $\frac{m}{2}$，所以选中这样一个值的概率至少为 $\frac{1}{2}$。于是要么 $x_i$ 是主元值，要么它位于两个段（segments）之一中。

**(b)** <span style="color:#7f8c8d;">[9 points]（9 分）</span>

考虑一个特定元素 $x_i$。证明：以至少 $1 - \frac{1}{n^2}$ 的概率，算法将 $x_i$ 与主元进行比较的总次数至多为 $d \lg n$，其中 $d$ 为某个特定常数。请明确给出 $d$ 的值。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 我们使用 (a) 小问与 Claim。由 (a) 小问，每次对包含 $x_i$ 的子数组调用 QUICKSORT 时，以至少 $\frac{1}{2}$ 的概率，要么 $x_i$ 被选为主元值，要么包含 $x_i$ 的子数组大小缩减为调用前的至多 $\frac{3}{4}$。我们说一次调用是 "successful（成功的）"，如果上述两种情况之一发生。也就是说，以至少 $\frac{1}{2}$ 的概率，该调用是成功的。
>
> 现在，在一次执行过程中，对包含 $x_i$ 的子数组最多只能发生 $\log_{4/3} n$ 次成功调用，因为经过这么多次成功调用之后，包含 $x_i$ 的子数组大小将被缩减为 $1$。利用对数的换底公式（change of base formula），
>
> ```math
> \log_{4/3} n = c \lg n, \quad \text{其中 } c = \log_{4/3} 2
> ```
>
> 现在我们可以把对包含 $x_i$ 的子数组的 QUICKSORT 调用序列建模为一枚公平硬币的投掷序列（sequence of tosses of a fair coin），其中正面（heads）对应成功调用。由 Claim，取 $c = \log_{4/3} 2$ 与 $\alpha = 2$，我们得出结论：以至少 $1 - \frac{1}{n^2}$ 的概率，在总计 $d \lg n$ 次调用内至少有 $c \lg n$ 次成功调用，其中 $d = 3(2 + c)$。$x_i$ 与主元的每次比较都发生在这些调用之一中，因此以至少 $1 - \frac{1}{n^2}$ 的概率，算法将 $x_i$ 与主元比较的总次数至多为 $d \lg n = 3(2 + c)\lg n = 3(2 + \log_{4/3} 2)\lg n$。所需的 $d$ 值为 $3(2 + \log_{4/3} 2) \le 14$。

**(c)** <span style="color:#7f8c8d;">[6 points]（6 分）</span>

现在考虑所有元素 $x_1, x_2, \ldots, x_n$。应用 (b) 小问的结果，证明：以至少 $1 - \frac{1}{n}$ 的概率，QUICKSORT 在给定数组输入（array input）上所做的比较总次数至多为 $d'n \lg n$，其中 $d'$ 为某个特定常数。请明确给出 $d'$ 的值。提示（Hint）：联合界（Union Bound）可能对你的分析有用。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 对原始数组 $A$ 的全部 $n$ 个元素使用联合界（union bound），我们得到：以至少 $1 - n \cdot \frac{1}{n^2} = 1 - \frac{1}{n}$ 的概率，数组中的每个值被与主元比较的次数至多为 $d \lg n$ 次，其中 $d$ 与 (b) 小问相同。因此，以至少 $1 - \frac{1}{n}$ 的概率，此类比较的总次数至多为 $dn \lg n$。取 $d' = d$ 即可。
>
> 由于 QUICKSORT 执行过程中进行的所有比较都涉及某个元素与主元的比较，我们对比较总次数得到相同的概率界（probabilistic bound）。

<!-- ===== PDF p6 ===== -->

**（续）**

**(d)** <span style="color:#7f8c8d;">[5 points]（5 分）</span>

将你上面的结果加以推广，得到 QUICKSORT 所做比较次数的一个界，该界以 $1 - \frac{1}{n^{\alpha}}$ 的概率成立，其中 $\alpha$ 为任意正整数，而不仅仅是概率 $1 - \frac{1}{n}$（即 $\alpha = 1$ 的情形）。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 修改很简单。Claim 与 (a) 小问保持不变。对于 (b) 小问，我们现在证明：以至少 $1 - \frac{1}{n^{\alpha+1}}$ 的概率，算法将 $x_i$ 与主元比较的总次数至多为 $d \lg n$，其中 $d = 3(\alpha + c)$。论证过程与之前相同，只是我们使用取值为 $\alpha$（而非 $2$）的 Claim。然后对于 (c) 小问，我们证明：以至少 $1 - \frac{1}{n^{\alpha}}$ 的概率，算法将任意值与主元比较的总次数至多为 $dn \lg n$，其中 $d = 3(\alpha + c)$。
>
> <span style="color:#1e8449;">**[note]** </span> 这里的常数有一处源 PDF 继承的 off-by-one：Claim 说的是「抛 $3(\alpha + c)\lg n$ 次硬币，至少 $c\lg n$ 次正面」以至少 $1 - 1/n^\alpha$ 的概率成立。因此 (b) 若要得到概率 $1 - 1/n^{\alpha+1}$，应把 Claim 的参数取为 $\alpha + 1$，即硬币数应为 $3(\alpha + 1 + c)\lg n$，故 $d = 3(\alpha + 1 + c)$；再用 union bound（$n$ 个元素，每个超界的概率 $\le 1/n^{\alpha+1}$）才能推出 (c) 的 $1 - 1/n^\alpha$。原文写作 $d = 3(\alpha + c)$ 时，(b) 实际得到的是概率 $1 - 1/n^\alpha$，直接取联合界只能给出 (c) 的 $1 - 1/n^{\alpha-1}$。当 $\alpha = 1$ 时 $3(\alpha + 1 + c) = 3(2 + c)$，与原文的 $d = 3(2 + c)$ 恰好一致，所以此前 (b)/(c) 的推导不受影响；一般 $\alpha$ 下按上述修正理解即可。

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare · http://ocw.mit.edu · 6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）· Spring 2015（2015 春季）</span>

<span style="color:#7f8c8d;">关于引用这些材料的说明或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
