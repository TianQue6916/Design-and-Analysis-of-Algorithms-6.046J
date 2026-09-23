<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 3 解答（Problem Set 3 Solutions）**</span> <span style="color:#7f8c8d;">（2015 年 2 月 26 日，Massachusetts Institute of Technology；Profs. Erik Demaine, Srini Devadas, and Nancy Lynch）</span>

<span style="color:#7f8c8d;">本习题集于 2015 年 2 月 26 日（星期四）晚上 11:59 截止（This problem set is due at 11:59pm on Thursday, February 26, 2015）。</span>

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 3-1. 阅读 CLRS 第 20.3 节（Read CLRS, Section 20.3）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 3-2. 习题 20-3.1（Exercise 20-3.1）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 3-3. 习题 20-3.2（Exercise 20-3.2）。**

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 3-1. van Emde Boas 的变体（Variants on van Emde Boas）**</span> <span style="color:#7f8c8d;">[25 points]</span>

对于下列 van Emde Boas data structure（数据结构）的每种变体（在 Lecture 4（第 4 讲）与 CLRS 第 20.3 节中介绍），仔细描述需要对 pseudocode（伪代码）（来自讲义或教材）做哪些改动，并分析 vEB 操作 `INSERT`（插入）、`DELETE`（删除）与 `SUCCESSOR`（后继）的 cost（代价），与原始 vEB 结构上相同操作的代价进行比较。

> <span style="color:#2471a3;">**[problem]**</span> **(a)** [7 points] 不把结构分成 $u^{1/2}$ 组、每组 $u^{1/2}$ 个数，而是使用 $u^{1/3}$ 组、每组 $u^{2/3}$ 个数。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 伪代码不需要改动，除了对 clusters（簇）的不同划分。MIN 与 MAX 操作需要常数时间（constant time）。
>
> 对于 MEMBER，我们有递推式（recurrence）
>
> ```math
> T(u) = T(u^{2/3}) + c.
> ```
>
> 之前我们有递推式 $T(u) = T(u^{1/2}) + c$，它解得（见 CLRS）
>
> ```math
> T(u) = O(c \lg \lg u) = O(c \log_2 \log_2 u).
> ```
>
> 对于新的递推式，沿用相同的求解方法，我们得到
>
> ```math
> O(c \log_{3/2} \log_2 u),
> ```
>
> 这属于同一个数量级（order of magnitude），只是常数略大一些。
>
> 对于 SUCCESSOR、PREDECESSOR、INSERT 与 DELETE，我们得到递推式
>
> ```math
> T(u) = \max\{T(u^{1/3}), T(u^{2/3})\} + c = T(u^{2/3}) + c.
> ```
>
> 因此分析与 MEMBER 相同，结果为
>
> ```math
> O(c \log_{3/2} \log_2 u) = O(\lg \lg u).
> ```

---

<!-- ===== PDF p2 ===== -->

> <span style="color:#2471a3;">**[problem]**</span> **(b)** [18 points] 除了把 minimum（最小）元素从低层 vEB 结构中排除之外，也把 maximum（最大）元素从低层 vEB 结构中排除（并将其存储于已经存在的 max 属性中）。（这里使用原始的划分：$u^{1/2}$ 组、每组 $u^{1/2}$ 个数。）
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 我们重写 CLRS 第 20.3 节中涉及 min 与 max 的部分代码。复杂度的数量级（order of magnitude）不变。
>
> 空数据结构的初始化与之前相同。对于 MIN 与 MAX 查询，代码不变。因为我们对称地对待 MIN 与 MAX，SUCCESSOR（后继）与 PREDECESSOR（前驱）现在彼此对称。
>
> **SUCCESSOR:** 从第 551 页的代码开始。第 1–11 行与第 14–15 行不变。不过，第 12–13 行必须修改，以考虑 successor（后继）可能根本不在任何 cluster（簇）中的情形；这与第 552 页旧的 predecessor（前驱）代码的第 13–14 行类似。因此，我们以如下代码替换当前的第 12–13 行：
>
> ```
> VEB-TREE-SUCCESSOR(V, x)
> 10  [ This replaces lines 12-13 in the original code.
> 11  if succ-cluster = NIL
> 12      if V.max = NIL and x < V.max
> 13          return V.max
> 14      else return NIL
> ```
>
> <span style="color:#1e8449;">**[note]** </span> 原 PDF 第 12 行写作 `if V.max = NIL and x < V.max`，其中的「$V.\max = \mathrm{NIL}$」应为「$V.\max \ne \mathrm{NIL}$」，属排印笔误：若 $V.\max$ 为 NIL，则条件 $x < V.\max$ 无从成立，而修改后的逻辑正是要处理「后继可能不存在于任何簇」时后继落在 $V.\max$ 上的情形，故条件应为 $V.\max \ne \mathrm{NIL}$ 且 $x < V.\max$。
>
> **PREDECESSOR:** 现在前驱与后继对称。事实上，前驱代码保持为第 552 页的代码不变。
>
> **INSERT:** 修改后的代码如下：
>
> ```
> ONE-ELEMENT-TREE-INSERT(V, x)
> 1  [ This should be called when V.min = V.max
> 2  if x > V.min
> 3      V.max = x
> 4  else V.min = x
> ```

<!-- ===== PDF p3 ===== -->

**（续）**

> ```
> VEB-TREE-INSERT(V, x)
> 1  if V.min == NIL
> 2      VEB-EMPTY-TREE-INSERT(V, x)
> 3  elseif V.min == V.max
> 4      ONE-ELEMENT-TREE-INSERT(V, x)
> 5  else
> 6      if x < V.min
> 7          exchange x with V.min
> 8      elseif x > V.max
> 9          exchange x with V.max
> 10     if VEB-TREE-MINIMUM(V.cluster[high(x)]) == NIL
> 11         VEB-TREE-INSERT(V.summary, high(x))
> 12         VEB-EMPTY-TREE-INSERT(V.cluster[high(x)], low(x))
> 13     else
> 14         VEB-TREE-INSERT(V.cluster[high(x)], low(x))
> ```
>
> **DELETE:** 修改后的代码如下。
>
> ```
> VEB-TREE-DELETE(V, x)
> 1  if V.min == V.max
> 2      V.min = NIL
> 3      V.max = NIL
> 4  elseif VEB-TREE-MINIMUM(V.summary) == NIL
> 5      if x = V.min
> 6          V.min = V.max
> 7      elseif x = V.max
> 8          V.max = V.min
> 9  else
> 10     if x == V.min
> 11         first-cluster = VEB-TREE-MINIMUM(V.summary)
> 12         x = index(first-cluster,
> 13             VEB-TREE-MINIMUM(V.cluster[first-cluster]))
> 14         V.min = x
> 15     elseif x == V.max
> 16         last-cluster = VEB-TREE-MAXIMUM(V.summary)
> 17         x = index(last-cluster,
> 18             VEB-TREE-MAXIMUM(V.cluster[last-cluster]))
> 19         V.max = x
> 20     VEB-TREE-DELETE(V.cluster[high(x)], low(x))
> 21     if VEB-TREE-MINIMUM(V.cluster[high(x)], low(x)) == NIL
> 22         VEB-TREE-DELETE(V.summary, high(x))
> ```
>
> <span style="color:#1e8449;">**[note]** </span> 第 21 行的 `VEB-TREE-MINIMUM(V.cluster[high(x)], low(x))` 中多出的参数 `low(x)` 应为笔误，正确写法应与第 10 行一致，即 `VEB-TREE-MINIMUM(V.cluster[high(x)])`；此判断用于检查该簇是否已空，从而决定是否从 summary 中删除该簇。
>
> 我们说明对 CLRS 第 554 页代码的修改。第 1–3 行保持不变，因为它们只是在测试 $V$ 的单元素（1-element）特殊情况。

<!-- ===== PDF p4 ===== -->

**（续）**

> 现在我们在线 3 之后添加一个新的双元素（2-element）特殊情况。注意 summary 为空，因为结构中只包含 max 与 min，且二者都不出现在簇（clusters）中。
>
> ```
> VEB-TREE-DELETE(V, x)
> [ Lines 1-3 are as in the book
> 1  if V.min == V.max
> 2      V.min = NIL
> 3      V.max = NIL
> 4  elseif VEB-TREE-MINIMUM(V.summary) == NIL
> [ This deals with the case where the summary is empty
> 5      if x = V.min
> 6          V.min = V.max
> 7      elseif x = V.max
> 8          V.max = V.min
> ```
>
> 在执行的这一时刻，我们知道结构至少包含 3 个元素。因此，我们不需要 base case（基准情况）第 4–8 行，因为每个基准结构最多只能包含两个元素。从第 9 行开始有很多变化，因为我们对称地处理 min 与 max。所以我们可以写：
>
> ```
> VEB-TREE-DELETE(V, x)
> 8  [ Lines 4-8 from the original precede this.
> 9  else
> 10     if x == V.min
> 11         [ The logic from lines 10-12 in the original goes here
> 12     elseif x == V.max
> 13         [ The logic from lines 10-12 in the original goes here
> 14         [ but with VEB-TREE-MAXIMUM(V.summary), last-cluster,
> 15         [ VEB-TREE-MAXIMUM(V.cluster[last-cluster]),
> 16         [ and setting V.max = x in the final line
> ```
>
> 对于这段代码，注意 $V$ 的簇不可能全为空，因为 $V$ 至少包含 3 个元素。所以上述对 `V.summary` 的操作实际上都会返回值。这些行的净效果是重置 `x`，使其现在指向 $V$ 内某个簇中要删除的元素。新的 `x` 如果合适，可以被放入 min 或 max 中。在这段代码之后，保留原始 Delete 代码中的第 13 行（它把元素从其簇中删除），以及第 14–15 行（它在必要时把该簇从 summary 中删除）。我们省略第 16–23 行，因为它们处理的是删除 $V$ 的最大元素的情形，而我们已经处理过了。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">如需了解如何引用这些材料或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
