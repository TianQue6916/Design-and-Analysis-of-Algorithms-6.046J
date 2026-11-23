<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 3（复习课 3）：Union-Find and Amortization（并查集与摊还分析）**</span> <span style="color:#7f8c8d;">（2015 年 2 月 20 日 · Massachusetts Institute of Technology · Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Introduction（引言）**</span>

Union-find（并查集）数据结构，又称 disjoint-set data structure（不相交集合数据结构），是一种能够维护一族两两不相交（pairwise disjoint）的集合 $S = \{S_1, S_2, \ldots, S_r\}$ 的数据结构，其中总共包含 $n$ 个元素。每个集合 $S_i$ 都有一个任意选定的唯一元素，可作为整个集合的 representative（代表元素），记作 rep[$S_i$]。

具体来说，我们希望支持以下操作（operations）：

- **MAKE-SET($x$)**：在 $S$ 中加入一个新集合 $\{x\}$，且 rep[$\{x\}$] = $x$。
- **FIND-SET($x$)**：确定包含 $x$ 的集合 $S_x \in S$ 究竟是哪一个，并返回 rep[$S_x$]。
- **UNION($x$, $y$)**：对任意位于不同集合 $S_x$、$S_y$ 中的 $x$、$y$，在 $S$ 中用 $S_x \cup S_y$ 取代 $S_x$ 与 $S_y$。

此外，我们希望让这些操作尽可能高效。我们将一步步走过构造一种数据结构的过程，让它在 amortized（摊还）意义下表现惊人。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**1.1　Motivation（动机）**</span>

Union-find 数据结构被用在许多不同的算法中。一个自然的用例是在节点和边都可以动态添加的无向图中跟踪 connected components（连通分量）。我们以初始的连通分量作为各个 $S_i$。当我们加入一个节点 $v$ 及其边 $E = \{(v, v_j)\}$ 时，我们要么

1. 若 $E = \emptyset$，调用 **MAKE-SET** 生成一个新分量；
2. 调用 **FIND-SET($v_j$)** 找出节点 $v$ 将要连接到的那个分量，并调用 **UNION** 把所有通过 $v$ 相连的分量连接起来。

该数据结构的另一个用例是 Kruskal's algorithm（Kruskal 算法），你将在今后的课程中见到。

> <span style="color:#1e8449;">**[note]** </span> 本讲将沿一条清晰的路线推进：先从朴素的链表表示出发，再引入第一项改进 union by size（按大小合并，小集并入大集），随后转向树的森林表示并加入第二项改进 path compression（路径压缩），最后把两者结合起来。分析过程会依次用到摊还分析（amortized analysis）的三种经典方法——aggregate method（聚合分析）、accounting method（记账法）与 potential method（势能法）——它们给出相同的 $O(n \lg n)$ 界，而两项改进叠加后摊还代价进一步降到近乎常数的 $O(m\alpha(n))$。理解这三种方法的等价性，是掌握本章摊还分析的关键。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#7f8c8d;">Figure 1（图 1）：一个简单的双向链表（a simple doubly linked list）。</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　Starting Out（从最朴素的做法开始）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.1　Linked List Solution（链表解法）**</span>

解决该问题的一个 naïve（朴素）做法是像上图那样，用双向链表表示每个集合。我们还可以维护一个数据结构（例如一张把元素映射到指针的哈希表），从而能够在常数时间内访问每个链表节点。我们定义 rep[$S_i$] 为表示 $S_i$ 的那条链表表头处的元素。

以下是每个操作的算法：

- **MAKE-SET($x$)**：把 $x$ 初始化成一个孤立的节点。最坏情况下耗时 $\Theta(1)$。
- **FIND-SET($x$)**：在包含 $x$ 的链表中向左走，直到到达链表前端。最坏情况下耗时 $\Theta(n)$。
- **UNION($x$, $y$)**：走到 $S_x$ 的链表尾和 $S_y$ 的链表头，把两条链表拼接起来。最坏情况下耗时 $\Theta(n)$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2　Augmenting the Linked List（增强链表）**</span>

我们可以通过 augmenting（增强）链表来改进链表解法的行为：让每个节点都有一个指向其代表元素的指针，同时随时记录链表的尾部以及链表中的元素个数，我们把后者称为该链表的 weight（权值/大小）。

<span style="color:#7f8c8d;">Figure 2（图 2）：一个增强后的链表（an augmented linked list）。</span>

现在，**FIND-SET($x$)** 可以在 $\Theta(1)$ 时间内完成。

---

<!-- ===== PDF p3 ===== -->

我们还改变 **UNION($x$, $y$)** 的行为：把包含 $x$ 与 $y$ 的两条链表拼接起来，并更新 $y$ 中所有元素的 rep 指针。

然而，最坏情况下这仍可能需要 $\Theta(n)$ 时间！设想我们对 $1$ 到 $n$ 之间的每个整数都调用一次 **MAKE-SET**，然后依次调用 **UNION($n - 1$, $n$)**、**UNION($n - 2$, $n - 1$)**、…、**UNION($1$, $2$)**，其中第 $i$ 次 union 会修改一条长度为 $i$ 的链表。那么所有 UNION 调用的总代价为 $1 + 2 + \ldots + (n - 1) = \Theta(n^2)$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**3　First Improvement: Smaller into Larger（第一项改进：小并入大）**</span>

<span style="color:#7f8c8d;">（本部分已在 Quiz 1 复习课中更新并讲解。）</span>

不过，我们可以强制 UNION 把较小的链表并入较大的链表，从而解决这个问题。若这样做，上述场景每次都只会修改一条长度为 1 的链表，于是这 $n - 1$ 次 UNION 的总运行时间为 $\Theta(n)$。

> <span style="color:#2471a3;">**[claim]**</span> **Claim（声明）.** 有了第一项改进之后，先进行 $n$ 次 MAKE-SET、再进行 $n$ 次 UNION，这一序列调用的摊还运行时间为 $O(n \lg n)$。

> <span style="color:#2471a3;">**[proof]**</span> **Proof（证明，聚合分析视角）.** 我们首先使用 aggregate method（聚合分析）。假设移动一次 rep 指针的代价为 1。监视某个元素 $x$ 以及包含它的集合 $S_x$。在 MAKE-SET($x$) 之后，有 weight[$S_x$] = 1。当我们调用 UNION($x$, $y$) 时，会出现下面两种情况之一：
>
> - 若 weight[$S_x$] > weight[$S_y$]，则 rep[$x$] 保持不变，我们无需替 $x$ 付出任何代价，而且 weight[$S_x$] 只会增大。
> - 若 weight[$S_x$] $\le$ weight[$S_y$]，我们付出 1 来更新 rep[$x$]，同时 weight[$S_x$] 至少翻倍。
>
> $S_x$ 最多能翻倍 $\lg n$ 次，因此 rep[$x$] 最多被更新 $\lg n$ 次。于是对全部 $n$ 个元素求和，我们得到摊还运行时间为 $O(n \lg n)$。$\blacksquare$

> <span style="color:#2471a3;">**[proof]**</span> **Proof（证明，记账法/摊派法视角）.** 使用 accounting method（记账法）或 charging method（摊派法）时，证明非常类似。当我们调用 UNION($x$, $y$) 时，不失一般性假设 $|S_x| < |S_y|$。我们会向 $S_x$ 中的每个元素 $i$ 收费，因为它们的指针 rep[$i$] 都被更新了。同理，每个元素 $i$ 最多能被收费 $\lg n$ 次，因为 $|S_i|$ 至少翻倍。若采用记账法，每次 MAKE-SET($x$) 都向银行（bank）存入 $\lg n$ 枚硬币（coins），供将来的 UNION 使用。$\blacksquare$

> <span style="color:#2471a3;">**[proof]**</span> **Proof（证明，势能法视角）.** 现在我们想用 potential method（势能法）。势能法通常就是记账法的“逆过程”，potential function（势能函数）可以看作银行账户中的余额。在本例中，我们定义势能函数为
>
> ```math
> \Phi = \sum_{i}\left(\lg n - \lg |S_i|\right)
> ```
>
> 其中求和遍历结构中每个元素 $i$（$n$ 是元素总数的上界）。

---

<!-- ===== PDF p4 ===== -->

当我们调用 MAKE-SET($x$) 时，$|S_x| = 1$，因此摊还代价为 $O(1) + \Delta\Phi = O(\lg n)$。这意味着每次 MAKE-SET($x$) 都会向银行存入 $\lg n$ 枚硬币。

当我们调用 UNION($x$, $y$) 时，再次假设 $|S_x| < |S_y|$。实际代价（actual cost）为 $|S_x|$。由于 $S_x$ 中的每个元素 $i$ 现在的 $|S_i|$ 都翻倍（又因 $|S_y| > |S_x|$，新集合大小会超出原来的两倍），故有 $\Delta\Phi < -|S_x|$。这意味着我们从银行取出 $|S_x|$ 枚硬币来支付这次 UNION 操作。于是 UNION 的摊还代价为 $|S_x| + \Delta\Phi < 0$。$\blacksquare$

> <span style="color:#1e8449;">**[note]** </span> 这里对同一个 $O(n \lg n)$ 界给出了三种等价证明：聚合分析直接统计每个元素的 rep 指针最多被更新 $\lg n$ 次；记账法把将来更新所需的 $\lg n$ 枚硬币提前存入“银行”，MAKE-SET 存、UNION 花；势能法则把“银行余额”形式化为势能函数 $\Phi = \sum_i(\lg n - \lg|S_i|)$。三者的共同核心是同一个观察：小集并入大集后，元素所在集合的大小至少翻倍，故单个元素最多经历 $\lg n$ 次“升级”。这也正是“按大小合并”能保证良好界的本质原因。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**4　Forest of Trees Representation（树的森林表示）**</span>

一个有趣的观察是：我们其实并不关心链表中节点之间的链接，我们只关心 rep 指针。本质上，一条链表可以表示为一棵树的森林（forest of trees），其中 rep[$x$] 是包含 $x$ 的那棵树的根：

<span style="color:#7f8c8d;">Figure 3（图 3）：不相交集合数据结构的树形森林表示（a forest of trees representation of a disjoint set data structure）。</span>

此时的算法如下：

- **MAKE-SET($x$)**：把 $x$ 初始化成一个孤立的节点。最坏情况下耗时 $\Theta(1)$。
- **FIND-SET($x$)**：从包含 $x$ 的树向上攀爬到根。耗时 $\Theta(\text{height})$（$\Theta(树高)$）。
- **UNION($x$, $y$)**：攀爬到包含 $x$ 和 $y$ 的两棵树的根，合并集合，把 rep[$y$] 的父节点设为 rep[$x$]。耗时 $\Theta(\text{height})$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**4.1　Adapting the First Improvement（将第一项改进适配到树表示）**</span>

我们的第一个技巧可以稍加改造以适配树的森林表示：把高度较小的树合并到高度较大的树上。由此可以证明，树的高度仍为 $O(\lg n)$。

> <span style="color:#1e8449;">**[note]** </span> 这里的“把较矮的树并入较高的树”在文献中通常称为 union by height（按高度合并）；若用秩（rank，即高度的一个上界）来代替实际高度，则称为 union by rank（按秩合并）。其证明思路与第 3 节的“小并入大”完全平行：只有当两棵树高度相等时，合并后的树高才会增加 1；更精确地，可以证明一棵高度为 $h$ 的树至少包含 $2^h$ 个节点，从而任意树高为 $O(\lg n)$，故 FIND-SET 与 UNION 的代价都是 $O(\lg n)$。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#7f8c8d;">Figure 4（图 4）：调用 UNION($x_1$, $y_1$) 之后的数据结构。</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**5　Second Improvement: Path Compression（第二项改进：路径压缩）**</span>

现在我们改进 FIND-SET。当我们向上攀爬树时，会得知沿途经过的每一个中间节点的代表元素。因此我们应该重定向这些 rep 指针，使将来的 FIND-SET 调用不必重复做同样的计算。

<span style="color:#7f8c8d;">Figure 5（图 5）：调用 FIND-SET($x_8$) 之后的数据结构。</span>

> <span style="color:#2471a3;">**[claim]**</span> **Claim（声明）.** 设 $n$ 为我们所跟踪的元素总数。仅采用第二项改进时，$m$ 次操作（包括 FIND-SET）的摊还运行时间为 $O(m \lg n)$。

> <span style="color:#2471a3;">**[proof]**</span> **Proof（证明）.** 用势能函数做摊还分析。定义 weight[$x_i$] 为以 $x_i$ 为根的子树中的元素个数。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#7f8c8d;">Figure 6（图 6）：与图 5 相同的树，但重新绘制。注意其中的 $x_i$ 比图 4 紧凑得多。</span>

令 $\Phi(x_1, \ldots, x_n) = \sum_i \lg \text{weight}[x_i]$。若 UNION($x_i$, $x_j$) 把 $x_j$ 子树的根作为孩子挂接到 $x_i$ 子树的根上，那么它只会增大 $S_{x_i}$ 的根的权值，增大量至多为 $\lg n$，因为元素总数至多为 $n$。其他所有元素的权值都保持不变。

现在考虑 FIND-SET($x_i$) 中由子节点 $c$ 走到祖先 $p$ 的每一步：这一步会把 $c$ 的子树移出 $p$ 的子树。若在某一步有 weight[$c$] $\ge \frac{1}{2}$ weight[$p$]，则势能至少减少 1，正好支付这一步的开销。此外，满足 weight[$c$] $< \frac{1}{2}$ weight[$p$] 的步数至多为 $\lg n$，因为每走这样一步，我们当前所考察的树的大小都会缩小一半以上。$\blacksquare$

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**6　Why Don't We Do Both?（为什么不两项改进一起上？）**</span>

如果我们对树形表示同时应用这两项改进，就能得到惊人的表现：每个操作的摊还代价几乎为常数！

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**6.1　CLRS-Ackermann Function（CLRS-阿克曼函数）**</span>

我们定义一个函数 $A_k(j)$，其结构与著名的 Ackermann function（阿克曼函数）类似，定义如下：

```math
A_k(j) = 
\begin{cases}
j + 1 & \text{if } k = 0 \\
A_{k-1}^{j+1}(j) & \text{if } k \geq 1
\end{cases}
```

其中 $A_{k-1}^{j+1}$ 表示把 $A_{k-1}$ 反复迭代 $j + 1$ 次。由于当 $k \geq 1$ 时要反复迭代 $A_{k-1}$ 很多次，这个函数增长得极其迅猛：

---

<!-- ===== PDF p7 ===== -->

```math
A_0(1) = 2, \qquad A_1(1) = 3, \qquad A_2(1) = 7, \qquad A_3(1) = 2047
```

```math
A_4(1) > \underbrace{2^{2^{2^{\cdot^{\cdot^{\cdot^{2}}}}}}}_{\text{2048 个 } 2}
```

（即 $A_4(1)$ 超过一座由 2048 个 2 组成的指数塔。）

现在考虑它的“逆函数”如下：

```math
\alpha(n) = \min\{k : A_k(1) \geq n\}
```

虽然从严格意义上讲它并非常数，但对于非常大的 $n$，这个值也已经相当接近常数了。在实际应用中，你完全可以认为 $\alpha(n) \le 4$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**6.2　How Spectacular?（有多惊人？）**</span>

> <span style="color:#2471a3;">**[theorem]**</span> **Theorem（定理）.** 同时采用两项改进后，$m$ 次操作的摊还运行时间为 $O(m\alpha(n))$。

这个证明非常长且非常棘手。如果你感兴趣，请查阅 CLRS 第 21.4 节。

> <span style="color:#1e8449;">**[note]** </span> 反阿克曼函数 $\alpha(n)$ 之所以“几乎为常数”，是因为 $A_4(1)$ 已经是一棵含 2048 个 2 的指数塔，远超任何实际数据规模，因此对一切实际出现的 $n$ 都有 $\alpha(n) \le 4$。CLRS（《算法导论》）第 21.4 节证明了 $m$ 次操作的摊还界为 $O(m\alpha(n))$，并指出在合理的计算模型下这一界是最优的。需要说明的是，原文此处写作 “With both improvements improvement”，其中第二个 “improvement” 疑为笔误，按“同时采用两项改进”理解即可；另外，讲义中使用的 $A_k(j)$ 是 CLRS 版本、与经典 Ackermann 函数略有差异的“双层索引”变体，正是为了便于构造 $\alpha(n)$。

---

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">*MIT OpenCourseWare*　http://ocw.mit.edu
6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）
Spring 2015（2015 春季学期）
关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
