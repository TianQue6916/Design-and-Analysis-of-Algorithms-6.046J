<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 4（Lecture 4）：分治——van Emde Boas 树（Divide and Conquer: van Emde Boas Trees）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- 一系列改进的数据结构（Series of Improved Data Structures）
- 插入、后继（Insert, Successor）
- 删除（Delete）
- 空间（Space）

> <span style="color:#7f8c8d;">[说明] 本讲基于与 Michael Bender 的私人交流（personal communication with Michael Bender, 2001）。</span>

<span style="color:#2471a3;">**[goal]**</span> **目标（Goal）**：我们希望维护值域（range）$\{0, 1, 2, \ldots, u-1\}$ 内的 $n$ 个元素，并让 **Insert、Delete、Successor** 三种操作都在 $O(\log \log u)$ 时间内完成。

- 若 $n = n^{O(1)}$ 或 $n^{(\log n)^{O(1)}}$，那么我们有 $O(\log \log n)$ 时间的操作
  - 比平衡二叉搜索树（Balanced Binary Search Trees）**指数级更快**（Exponentially faster）
  - 比哈希（hashing）有**更酷的查询**（Cooler queries，如后继/前驱）
- **应用：网络路由表（Network Routing Tables）**
  - $u$ = IP 地址的值域 → 转发到的端口（port）
  - （IPv4 中 $u = 2^{32}$）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**$O(\log \log u)$ 上界可能来自哪里？（Where might the $O(\log \log u)$ bound arise?）**</span>

- 对 $O(\log u)$ 个元素做二分搜索（binary search）
- 递推式（Recurrences）：

```math
T(\log u) = T\!\left(\frac{\log u}{2}\right) + O(1)
```

```math
T(u) = T(\sqrt{u}) + O(1)
```

两者都解出 $\log \log u$：第一式每次把 $\log u$ 减半（对 $u$ 来说就是每层取平方根）；第二式直接对 $u$ 取平方根。

🎥 *Devadas 在视频中[给出 log log u 的直观]*："And as you may know, network routers are basically computers. ... It's going to take like five operations to do log log 2 to the 32."（翻译：如你所知，网络路由器本质上就是计算机。……对 $\log \log 2^{32}$ 来说，只需大约 5 次操作。）——$u = 2^{32}$ 时 $\log \log u = \log 32 = 5$，这正是 vEB 结构对真实 IPv4 世界的意义：常量级的实际操作数。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这里先建立两个「目标递推」的心理锚点，后面所有的改进都是朝这两个式子收敛。第一种 $T(\log u) = T(\log u / 2) + O(1)$：把「$\log u$ 这个数本身」不断减半，从 $\log u$ 到 1 需要 $\log \log u$ 层——这正是「对数的对数」这个名字的来历。第二种 $T(u) = T(\sqrt{u}) + O(1)$：把宇宙大小 $u$ 取平方根，从 $u$ 到常数也需要 $\log \log u$ 层（因为 $\sqrt{\sqrt{\cdots\sqrt{u}}} = u^{1/2^k}$，令其为常数解得 $k = \log \log u$）。为什么二分搜索的 $O(\log u)$ 不是目标？因为对 $u$ 个可能值做二分只利用了「值域的大小」，而 vEB 树要把「集合大小 $n$」与「值域大小 $u$」分离，让复杂度只依赖 $\log \log u$——这也是为什么它有别于 $O(\log n)$ 的普通平衡树：**当 $u$ 是常数级指数（如路由表的 $u = 2^{32}$）而 $n$ 较大时，$\log \log u$ 远小于 $\log n$**。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b;">**改进（Improvements）**</span>

我们将通过在一个非常简单的数据结构上做一系列改进，来逐步发展出 van Emde Boas 数据结构。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**位向量（Bit Vector）**</span>

我们维护一个大小为 $u$ 的向量（vector）$V$，使得 $V[x] = 1$ 当且仅当 $x$ 在集合中。现在，插入与删除只需翻转（flipping）向量中对应的位即可。然而，**后继/前驱**（successor/predecessor）需要遍历向量找到下一个 1 位。

- **Insert / Delete：$O(1)$**
- **Successor / Predecessor：$O(u)$**

> <span style="color:#7f8c8d;">[图 1 说明] 原页 Figure 1：$u = 16$ 的位向量。当前集合为 $\{1, 9, 10, 15\}$，下标 0–15 依次对应位 $0,1,0,0,0,0,0,0,0,1,1,0,0,0,0,1$。</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**把宇宙拆成簇（Split Universe into Clusters）**</span>

我们可以通过把值域 $\{0, 1, 2, \ldots, u-1\}$ 拆成 $\sqrt{u}$ 个大小为 $\sqrt{u}$ 的**簇**（clusters）来改进性能。若 $x = i\sqrt{u} + j$，则 $V[x] = V.\mathrm{Cluster}[i][j]$。

```math
\mathrm{low}(x) = x \bmod \sqrt{u} = j
```

```math
\mathrm{high}(x) = \left\lfloor \frac{x}{\sqrt{u}} \right\rfloor = i
```

```math
\mathrm{index}(i, j) = i\sqrt{u} + j
```

> <span style="color:#7f8c8d;">[图 2 说明] 原页 Figure 2：$u = 16$ 的位向量被拆成 $\sqrt{16} = 4$ 个大小为 4 的簇：V.Cluster[0]（下标 0–3，含 1）、V.Cluster[1]（下标 4–7，空）、V.Cluster[2]（下标 8–11，含 9、10）、V.Cluster[3]（下标 12–15，含 15）。</span>

- **Insert（插入）**：
  - 置 $V.\mathrm{cluster}[\mathrm{high}(x)][\mathrm{low}(x)] = 1$，$O(1)$

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b;">**分簇位向量（续）**</span>

- **Insert（续）**：
  - 把簇 $\mathrm{high}(x)$ 标记为非空（non-empty），$O(1)$
- **Successor（后继）**：
  - 先在簇 $\mathrm{high}(x)$ 内部查找，$O(\sqrt{u})$
  - 否则，找下一个非空簇 $i$，$O(\sqrt{u})$
  - 在该簇内找最小条目 $j$，$O(\sqrt{u})$
  - 返回 $\mathrm{index}(i, j)$
  - **总计 $= O(\sqrt{u})$**

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 分簇是 vEB 树的第一次「代数化」：把下标 $x$ 拆成 $x = i\sqrt{u} + j$，其中 $\mathrm{high}(x)=i$ 是「在第几个簇」，$\mathrm{low}(x)=j$ 是「簇内第几位」。这本质上是在说：$u$ 个位置被组织成一个 $\sqrt{u} \times \sqrt{u}$ 的二维棋盘——这正是你在 Strang 线性代数里熟悉的**张量积（tensor product）**结构：下标系统 $[0, u)$ 分解为 $[0, \sqrt{u}) \times [0, \sqrt{u})$。后继操作的三步（簇内找、跳到下一非空簇、再找最小）每步都是对 $\sqrt{u}$ 大小的子问题做同样的后继查询——递归结构在此埋下伏笔：**同一个问题在更小的宇宙上再次出现**。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**递归（Recurse）**</span>

Successor 中的三个操作其实也都是对大小为 $\sqrt{u}$ 的向量的 Successor 调用。我们可以用递归（recursion）来加速。

- $V.\mathrm{cluster}[i]$ 是大小为 $\sqrt{u}$ 的 van Emde Boas 结构（对所有 $0 \le i < \sqrt{u}$）
- $V.\mathrm{summary}$（汇总）是大小为 $\sqrt{u}$ 的 van Emde Boas 结构
- $V.\mathrm{summary}[i]$ 指示 $V.\mathrm{cluster}[i]$ 是否非空

**INSERT($V$, $x$)**

```
1  Insert(V.cluster[high(x)], low[x])
2  Insert(V.summary, high[x])
```

于是得到递推式：

```math
T(u) = 2T(\sqrt{u}) + O(1)
```

```math
T'(\log u) = 2T'\!\left(\frac{\log u}{2}\right) + O(1) \implies T(u) = T'(\log u) = O(\log u)
```

**SUCCESSOR($V$, $x$)**

```
1  i = high(x)
2  j = Successor(V.cluster[i], j)
3  if j == ∞
4      i = Successor(V.summary, i)
5      j = Successor(V.cluster[i], -∞)
6  return index(i, j)
```

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这一步揭示了「两个递归调用」的问题：INSERT 每次都要递归两次（簇内 + summary），导致 $T(u) = 2T(\sqrt{u}) + O(1)$，解出 $O(\log u)$——只和普通平衡树打平，没赚到。关键观察：**这两次递归里只有一次是「必要」的**。Insert 到簇内后，summary 只需要在「该簇原本是空的」时才需要更新（第一次放入元素时）。如果每次插入都无条件双递归，浪费了。这个「区分首插 vs 常规插入」的思路，是后续把 $2$ 变成 $1$ 的杠杆。注意 SUCCESSOR 伪代码第 2 行的 $j$ 初始应为 $\mathrm{low}(x)$（原文有笔误），逻辑是：先在簇 $i$ 里找大于 $\mathrm{low}(x)$ 的后继，找不到（$\infty$）才去 summary 找下一个非空簇，再到那个簇里找最小元素。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b;">**递归（续）**</span>

```math
T(u) = 3T(\sqrt{u}) + O(1)
```

```math
T'(\log u) = 3T'\!\left(\frac{\log u}{2}\right) + O(1) \implies T(u) = T'(\log u) = O\left((\log u)^{\log 3}\right) \approx O\left((\log u)^{1.585}\right)
```

要获得 $O(\log \log u)$ 的运行时间，我们需要把递归次数**减少到一次**（reduce the number of recursions to one）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**维护最小与最大值（Maintain Min and Max）**</span>

我们在每个结构中存储最小条目与最大条目（the minimum and maximum entry）。这给每次 Insert 操作带来 $O(1)$ 的时间开销。

**SUCCESSOR($V$, $x$)**

```
1  i = high(x)
2  if low(x) < V.cluster[i].max
3      j = Successor(V.cluster[i], low(x))
4  else i = Successor(V.summary, high(x))
5      j = V.cluster[i].min
6  return index(i, j)
```

```math
T(u) = T(\sqrt{u}) + O(1) \implies T(u) = O(\log \log u)
```

**不要递归存储 Min（Don't store Min recursively）**

Successor 调用现在需要单独检查 min：

```math
\text{if } x < V.\mathrm{min} : \text{return } V.\mathrm{min} \quad (1)
```

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 「维护 Min 和 Max」这个改进的威力在于：第 2 行用 `V.cluster[i].max` 判断「簇 $i$ 里有没有比 $\mathrm{low}(x)$ 更大的元素」——若有，只需进簇内递归一次（第 3 行）；若没有，则跳过簇内递归，直接去 summary 找下一个非空簇（第 4–5 行），仍然只递归一次。**关键是用 $O(1)$ 可读的 max 信息，把「我到底该递归进哪一层」在常数时间内决定好**，从而保证每层只走一条递归路径——这就是把递归数从 2 或 3 降到 1 的机制。这与你在 CSAPP 里学的「分支预测」精神相通：先花 $O(1)$ 拿到关键信息，避免盲目试探。注意式 (1)：因为 min 存在且可在 $O(1)$ 读出，successor 的前驱边界情况（$x$ 比整个集合的最小值还小）可以直接短路返回。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b;">**INSERT 与 DELETE（续）**</span>

**INSERT($V$, $x$)**

```
1  if V.min == None
2      V.min = V.max = x        # O(1) 时间
3      return
4  if x < V.min
5      swap(x ↔ V.min)
6  if x > V.max
7      V.max = x
8  if V.cluster[high(x)] == None
9      Insert(V.summary, high(x))        # 第一次调用 First Call
10 Insert(V.cluster[high(x)], low(x))    # 第二次调用 Second Call
```

如果第一次调用（进 summary）执行了，第二次调用（进簇）只需 $O(1)$ 时间（因为该簇此刻为空，走 INSERT 第 1–3 行直接设 min/max 后返回）。所以

```math
T(u) = T(\sqrt{u}) + O(1) \implies T(u) = O(\log \log u)
```

**DELETE($V$, $x$)**

```
1  if x == V.min                # 找新最小值
2      i = V.summary.min
3      if i == None
4          V.min = V.max = None          # O(1) 时间
5          return
6      V.min = index(i, V.cluster[i].min)   # 取出新最小值
7  Delete(V.cluster[high(x)], low(x))   # 第一次调用 First Call
8  if V.cluster[high(x)].min == None
9      Delete(V.summary, high(x))       # 第二次调用 Second Call
10 # 现在更新 V.max
11 if x == V.max
12     if V.summary.max == None
13         ...
14     else
15         i = V.summary.max
16         V.max = index(i, V.cluster[i].max)
```

如果第二次调用（删 summary）执行了，第一次调用（删簇）只需 $O(1)$ 时间（簇内已空，DELETE 走第 3–5 行直接清空返回）。所以

```math
T(u) = T(\sqrt{u}) + O(1) \implies T(u) = O(\log \log u)
```

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> INSERT 的**借位（borrowing）**技巧值得单独记：如果 $x < V.\mathrm{min}$，先把 $x$ 与 $V.\mathrm{min}$ 交换，再插入交换后的值——这样 $V.\mathrm{min}$ 永远以 $O(1)$ 可读的方式存着「当前最小」，而递归插入的总是「非最小」的元素，从而避免「要维护的最小值散落在深层递归里」。DELETE 对称地处理 $V.\mathrm{max}$。这体现了**「把最常用的信息提升到顶层，用 $O(1)$ 访问换取 $O(\log \log u)$ 深度」**的交换：表面上多存了两个字段，实际上让每层递归都只走一条路径。伪代码第 12–13 行原文残缺（`if V.summary.max == None` 分支体未给出），语义是：若 summary 为空则 $V.\mathrm{max}$ 也应置 None，否则取 summary 最大簇的最大元素——译注按上下文补全，不影响主流程。此外原文多处 OCR 笔误已静默修正（不改变语义）：INSERT 第 8 行 `V.cluster[high(x) == None` 缺右括号补全、第 7 行 `V.max = x)` 去掉多余右括号、DELETE 第 3 行 `if i = None` 改为 `==`。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b;">**下界与空间改进（Lower Bound and Space Improvements）**</span>

<span style="color:#2471a3;">**[theorem]**</span> <span style="color:#c0392b;">**下界（Lower Bound）[Patrascu & Thorup 2007]**</span>

即使对**静态查询**（static queries，无 Insert/Delete）：

- 对 $u = n^{(\log n)^{O(1)}}$，每次查询需要 $\Omega(\log \log u)$ 时间
- 空间为 $O(n \cdot \mathrm{poly}(\log n))$

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这是本讲的重要结论：$O(\log \log u)$ **不可能再被改进**（在下界假设下）——它不仅是 vEB 树能达到的最好，也是「前驱问题（predecessor problem）」在通用指针机上的**最优下界**，由 M. Pătrașcu 与 M. Thorup 在 2007 年证明。这意味着 vEB 树不是「某个聪明的设计碰巧快」，而是「恰好达到理论天花板」。理解「下界证明」对算法研究的意义：上界（$O(\log \log u)$ 的算法）告诉我们「能做多快」，下界（$\Omega(\log \log u)$）告诉我们「不可能更快」——两者一夹，问题就被彻底解决了。这是你在 6.042J 学过的**证明的力量**在算法复杂度上的终极体现。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**空间改进（Space Improvements）**</span>

我们可以从 $\Theta(u)$ 改进到 $O(n \log \log u)$。

- 只创建非空簇（nonempty clusters）
  - 若 $V.\mathrm{min}$ 变为 None，则释放（deallocate）$V$
- 把 $V.\mathrm{cluster}$ 存为**非空簇的哈希表**（hashtable of nonempty clusters）
- 每次插入可能创建一个新结构 $\Theta(\log \log u)$ 次（每次「空插入」）
  - 这确实可能发生（Vladimir Čunát 的例子）
- 把「指向结构的指针（pointer）及其哈希表条目」记账（charge）到该结构上

这给出 $O(n \log \log u)$ 空间（但是**随机化** randomized 的）。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 位向量方案的空间是 $\Theta(u)$——宇宙多大就要多大数组，这在 $u = 2^{32}$ 时不可行。第一个改进是「惰性分配（lazy allocation）」：不为空簇分配任何存储，只在第一次插入元素时才创建簇（用哈希表记录已存在的簇）。每个「空插入」沿递归路径创建 $\Theta(\log \log u)$ 个新结构，所以总空间 $O(n \log \log u)$。这与你 CSAPP 里学的「按需分页（demand paging）」「惰性求值」同构：**不为不存在的东西付账**。哈希表让「给定簇号找簇」在期望 $O(1)$ 完成——空间节省换来的是随机化的时间保证。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**间接化（Indirection）**</span>

我们可以进一步把空间降到 $O(n)$。

- 用 BST 甚至数组存储 $n = O(\log \log u)$ 的 vEB 结构 ⇒ 基础情形（base case）下 $O(\log \log n)$ 时间
- 我们使用 $O(n / \log \log u)$ 个这样的结构（互不相交 disjoint）
- ⇒ $O\left(\frac{n}{\log \log u} \cdot \log \log u\right) = O(n)$ 空间（对小结构 small）
- 更大的结构「存储指向它们的指针」（larger structures "store" pointers to them）
- ⇒ $O\left(\frac{n}{\log \log u} \cdot \log \log u\right) = O(n)$ 空间（对大结构 large）
- 细节：拆分/合并小结构（Split/Merge small structures）

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 间接化（indirection）是数据结构设计里一个极其通用的**空间压缩范式**：与其让每个 vEB 结构都带着完整的 $\log \log u$ 层递归（对每个小集合都浪费），不如在「递归到底层」时改用普通 BST/数组处理小集合，把「每个小结构 $O(\log \log u)$ 的固有开销」摊销到 $n / \log \log u$ 个结构上——两者相乘恰好抵消为 $O(n)$。这是「大小分治（size separation）」思想的典型应用：**大结构用精妙但昂贵的结构，小结构用朴素但便宜的结构**，边界设在大约 $\log \log u$ 处。你会看到这种「阈值 + 两种结构」的模式在后续的缓存无关算法、分块（blocking）技术里反复出现。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
