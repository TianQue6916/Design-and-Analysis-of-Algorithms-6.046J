<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 10（Lecture 10）：动态规划（Dynamic Programming）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- 最长回文序列（Longest palindromic sequence）
- 最优二叉搜索树（Optimal binary search tree）
- 交替取硬币游戏（Alternating coin game）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**DP 的四个要点（DP notions）**</span>

🎥 *Devadas 在视频中[预告本讲难度]*："You'll probably see problems here, at least alternating coin game, that are beyond 006 in the sense that it wasn't covered. Those kinds of notions weren't covered in 006."（翻译：你在这里可能会看到一些超出 6.006 范围的问题，至少「交替取硬币游戏」在 6.006 没讲过——这类概念当时没覆盖。）——本讲的取硬币博弈是「对手也最优」的博弈型 DP，确实超出基础课程，值得重点关注。

1. **刻画最优解的结构**（Characterize the structure of an optimal solution）
2. **递归地定义**基于子问题最优解的最优解的值（Recursively define the value of an optimal solution based on optimal solutions of subproblems）
3. **自底向上**计算最优解的值（递归 + 记忆化 memoization）
4. 从计算出的信息**构造最优解**（Construct an optimal solution from the computed information）

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> DP 的四步框架是 CLRS 第 15 章开篇的动态规划原理（§15.3 进一步展开）的浓缩，也是你在 6.006 学过的「子问题 + 记忆化」方法论的正式化。这四步的次序值得反复咀嚼：**先想清楚结构（步骤 1），再写递推（步骤 2）**——很多人跳过步骤 1 直接写递推，结果在「子问题空间选得太大/太小」上栽跟头。步骤 3 的「自底向上」或「递归 + 记忆化」是**同一个递推的两种实现**（本讲 p2 会对比），而步骤 4 往往被忽略——「算出了最优值」不等于「能给出最优解」，通常需要额外记录选择（choice）回溯。本讲的三个例子分别展示了 DP 的三种典型子问题结构：区间型（最长回文）、根型（最优 BST）、博弈型（取硬币）。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**最长回文序列（Longest Palindromic Sequence）**</span>

**定义（Definition）**：**回文（palindrome）**是反转后不变的字符串。
**例子**：radar、civic、t、bb、redder

**给定**：字符串 $X[1 \cdots n]$，$n \ge 1$
**目标**：找出**作为子序列（subsequence）**的最长回文

**例子**：给定 "character"，输出 "carac"
**答案长度至少为 1**

**策略（Strategy）**：$L(i, j)$：$X[i \cdots j]$ 的最长回文子序列的长度（对 $i \le j$）。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 注意区分「子串（substring）」与「子序列（subsequence）」：子串必须连续，子序列只需保持相对顺序（可以跳字符）。"character" 里 "carac" 跳过了中间的字符——若要求连续子串，答案就是 "c" 或 "ara" 之类的短串。这个「**回文子序列 vs 回文子串**」的区别是本题第一道坎：子序列问题通常更容易用 DP 解（因为子问题的「边界」可以自由收缩），而子串问题往往需要不同的状态设计。子问题 $L(i, j)$ 是**区间型（interval）**的：它只依赖 $X[i \cdots j]$ 这个子串区间，这提示我们 DP 状态按「区间长度」组织。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**最长回文序列（续）**</span>

```
1  def L(i, j):
2      if i == j:  return 1
3      if X[i] == X[j]:
4          if i + 1 == j:  return 2
5          else:  return 2 + L(i + 1, j - 1)
6      else:
7          return max(L(i + 1, j), L(i, j - 1))
```

**练习（Exercise）**：计算实际的解（即具体回文序列）。

**分析（Analysis）**：照此实现，程序可能**指数时间**运行：假设所有符号 $X[i]$ 都互不相同。

```math
T(n) = \begin{cases} 1 & n = 1\\ 2T(n-1) & n > 1 \end{cases} = 2^{n-1}
```

**子问题（Subproblems）**：但只有 $\binom{n}{2} = \Theta(n^2)$ 个互不相同的子问题：每个是满足 $i < j$ 的 $(i, j)$ 对。通过每个子问题**只求解一次**，运行时间降到 $\Theta(n^2) \cdot \Theta(1) = \Theta(n^2)$，其中 $\Theta(n^2)$ 是子问题个数、$\Theta(1)$ 是在较小子问题已解时求解每个子问题的时间。

**记忆化（Memoize）**：记忆化 $L(i, j)$——把输入哈希成输出值，查哈希表看子问题是否已解，否则递归。

**记忆化 vs 迭代（Memoizing vs. Iterating）**
1. 记忆化用字典存 $L(i, j)$，以 $i, j$ 为键查 $L$ 的值。这里可以直接用 2-D 数组，其中 **null 项**表示问题尚未求解。
2. 可以**按 $j - i$ 递增的顺序**求解子问题，这样较小的子问题先被求解。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 指数到多项式的跃迁，是 DP 全部威力的浓缩：**同样一个递推，直接递归是指数、记忆化后是多项式**——差别只在「是否重复求解相同子问题」。$T(n) = 2T(n-1)$ 来自「每次递归调用规模只减 1，却产生 2 个分支」；而 $\binom{n}{2}$ 个子问题说明「真正不同的 (i,j) 组合只有 $O(n^2)$ 个」——指数时间全浪费在**反复重算同一个 (i,j)** 上。这里「记忆化 vs 迭代」的对比是经典考点：**记忆化**（递归 + 查表）天然只求解**真正需要**的子问题（惰性，lazy），适合「递推依赖方向复杂」的情形；**迭代**（按 $j-i$ 递增）严格保证「小问题先于大问题」，适合「能确定拓扑序」的情形。两者时空复杂度相同，选择取决于实现直觉——但**子问题个数 × 每子问题时间**这个公式是统一的复杂度来源。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**最优二叉搜索树（Optimal Binary Search Trees）：CLRS 15.5**</span>

**给定**：键 $K_1, K_2, \cdots, K_n$，$K_1 < K_2 < \cdots < K_n$（不失一般性设 $K_i = i$），权重 $W_1, W_2, \cdots, W_n$。

**目标**：找出最小化下式的 BST $T$：

```math
\sum_{i=1}^{n} W_i \cdot (\mathrm{depth}_T(K_i) + 1)
```

**例子**：$W_i = p_i$ = 搜索 $K_i$ 的概率。那么，我们在最小化**期望搜索代价（expected search cost）**。

（假设我们表示一个英→法词典，常用词应有更大权重。）

**枚举（Enumeration）**：树的数量**指数多**。

- $n = 2$：$W_1 + 2W_2$ 或 $2W_1 + W_2$（两种形状）
- $n = 3$：$3W_1 + 2W_2 + W_3$、$2W_1 + 3W_2 + W_3$、$2W_1 + W_2 + 2W_3$、$W_1 + 3W_2 + 2W_3$、$W_1 + 2W_2 + 3W_3$（五种形状）

**策略（Strategy）**：

```math
W(i, j) = W_i + W_{i+1} + \cdots + W_j
```

```math
e(i, j) = K_i, K_{i+1}, \cdots, K_j \text{ 上的最优 BST 的代价}
```

目标：$e(1, n)$。

**贪心解（Greedy solution）？**：用某种贪心方式选 $K_r$，例如 $W_r$ 最大。
**贪心不适用**（greedy doesn't work），见讲义末尾的例子。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 最优 BST 与「最优二叉搜索」的直觉：把**高频键放浅（深度小）**能降低期望代价，但键之间的**大小关系**又约束了树形（中序遍历必须有序）。这两个约束（权重偏好 vs 中序约束）的拉锯，使问题不能用贪心解决——「最大权重当根」可能牺牲太多子树的深度。枚举树形是卡特兰数（Catalan number，$O(4^n / n^{3/2})$），指数爆炸，所以必须 DP。这里的目标函数 $W_i \cdot (\mathrm{depth} + 1)$ 值得拆解：深度 +1 是因为「比较次数 = 深度 + 1」（根在第 0 层，比较 1 次）。Wiki 查证：最优 BST 问题由 Knuth 提出（静态最优性），Sleator-Tarjan 的**伸展树（splay tree）**被猜想对动态最优树有常数竞争比（尚未证明）——这是数据结构领域最重要的开放问题之一。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**最优 BST（续）**</span>

> <span style="color:#7f8c8d;">[原图说明] 原页附图：以 $K_r$ 为根的最优 BST 分解——左子树含 $K_i, \ldots, K_{r-1}$（代价 $e(i, r-1)$），右子树含 $K_{r+1}, \ldots, K_j$（代价 $e(r+1, j)$）。</span>

**DP 策略：猜测所有根（Guess all roots）**

```math
e(i, j) = \begin{cases} W_i & \text{若 } i = j\\ \min_{i \le r \le j} \left( e(i, r-1) + e(r+1, j) + W(i, j) \right) & \text{否则} \end{cases}
```

$+W(i, j)$ 计入根 $K_r$ 的 $W_r$，以及 $K_r$ 各子树中所有其他键**深度加 1** 带来的增量（DP 尝试所有局部选择方式，并利用重叠子问题）。

**复杂度（Complexity）**：$\Theta(n^2) \cdot \Theta(n) = \Theta(n^3)$，其中 $\Theta(n^2)$ 是子问题个数、$\Theta(n)$ 是每个子问题的时间。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 最优 BST 的递推是「**根型（root-guessing）**」DP 的模板：**枚举所有可能的根 $r$，把问题分成左右两个独立子问题**。$+W(i, j)$ 这一项是全递推最精妙的地方——为什么不是 $+W_r$ 而是 $+W(i, j)$（整个区间的权重和）？因为当选定根 $K_r$ 后，左右子树里**每一个**键的深度都比「它们单独成为最优子树」时深 1 级（它们现在挂在 $K_r$ 下面），所以每个键的代价都要 +$W_k$，总计恰好是 $W(i, j)$（含根自己 $W_r$）。这个「**把『全部深度 +1』的增量一次算清**」的技巧，让子问题的解 $e(i, r-1)$、$e(r+1, j)$ 无需知道绝对深度——**深度只以相对方式进入递推**，这是它能 DP 的关键。复杂度 $O(n^3)$ 来自「$n^2$ 个子问题 × 每个要试 $n$ 个根」；CLRS 15.5 进一步用 **Knuth 优化**（利用最优根的位置随区间单调的性质）把复杂度降到 $O(n^2)$，本讲不展开。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**交替取硬币游戏（Alternating Coin Game）**</span>

一排 $n$ 个硬币，价值 $V_1, \cdots, V_n$，$n$ 是偶数。每回合，一名玩家选择**这一排的第一个或最后一个**硬币，将其**永久移除**，并获得该硬币的价值。

**问题（Question）**：先手（first player）总是能赢吗？

**试（Try）**：$4\ \ 42\ \ 39\ \ 17\ \ 25\ \ 6$

**策略（Strategy）**
1. 比较 $V_1 + V_3 + \cdots + V_{n-1}$ 与 $V_2 + V_4 + \cdots + V_n$，选择较大者。
2. 游戏过程中**只从所选子集中取**（你总能做到！）

**如何最大化你赢得的钱数（假设你先手）？**

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 先手**必胜**的证明是个漂亮的「奇偶位置」论证：把硬币按位置分成奇数组与偶数组，先手第一步取最左或最右——假设取了 $V_1$（位置 1，奇数位），那么剩下的硬币首尾是 $V_2$ 与 $V_n$（n 为偶数，二者都是**偶数位**），**后手只能取偶数位的硬币**；后手取走一个后，剩下区间的首尾变为**一奇一偶**，于是**先手又能取到奇数位的硬币**。如此往复，**先手永远取奇数位、后手永远取偶数位**。因此先手可以提前算好「所有奇数位之和」与「所有偶数位之和」，**选择总和更大的一组**，并在游戏中始终只取那组——这就是策略 2「只从所选子集中取」的由来。这个必胜策略很优雅，但它的目标是「**赢**（拿到一半以上的钱）」；下面要解决的是更强的「**最大化赢得的钱**」——这需要 DP，因为「必胜」不保证「最多」。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b">**最优策略（Optimal Strategy）**</span>

$V(i, j)$：轮到我们且只剩 $V_i, \cdots, V_j$ 时，我们能**确定赢得**的最大价值。

- $V(i, i)$：直接取 $i$。
- $V(i, i+1)$：取两者中较大者。
- $V(i, i+2)$、$V(i, i+3)$、$\cdots$

```math
V(i, j) = \max\{ (\text{范围变为 } (i+1, j)) + V_i,\ (\text{范围变为 } (i, j-1)) + V_j \}
```

**求解（Solution）**：$V(i+1, j)$ 子问题——对手取完后……

我们保证得到 $\min\{ V(i+1, j-1),\ V(i+2, j) \}$

其中 $V(i+1, j-1)$ 对应「对手取 $V_j$」，$V(i+2, j)$ 对应「对手取 $V_{i+1}$」。

于是

```math
V(i, j) = \max\left\{ \min\{ V(i+1, j-1),\ V(i+2, j) \} + V_i,\ \min\{ V(i, j-2),\ V(i+1, j-1) \} + V_j \right\}
```

**复杂度（Complexity）？** $\Theta(n^2) \cdot \Theta(1) = \Theta(n^2)$

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 博弈型 DP 的核心是「**对手也最优**」：我取走一枚硬币后，**轮到对手**在他的立场上取最优——他会选择让我（作为后手）接下来的收益最小的那条路。所以递推是「**内层 $\min$ + 外层 $\max$**」的嵌套：外层 $\max$ 是「我在取左、取右之间选对自己更好的」，内层 $\min$ 是「对手取 $V_j$ 或取 $V_{i+1}$ 后，我面对两种子问题里较差的那个」（对手替我做最坏选择）。具体看「取左」这一支：我拿 $V_i$ 后范围变 $(i+1,j)$ 轮到对手，他若取 $V_{i+1}$，剩 $(i+2,j)$；他若取 $V_j$，剩 $(i+1,j-1)$——无论哪种，我能确定拿到的就是这两个子问题里**较小的** $V$ 值。这个递推在 CLRS 15-5（最优策略游戏 optimal strategy for a game）里是标准形式，也是后续**极大极小（minimax）**思想在零和博弈中的雏形：你决策时永远假设对手在「跟你作对」，于是把自己能保证的收益下界最大化。注意本讲递推与「奇偶必胜策略」的关系：必胜策略只保证「赢」（拿到 > 一半），而 $V(i,j)$ 给出「最多能拿多少」——更强的量，代价是需要 $\Theta(n^2)$ 的 DP。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#c0392b">**贪心在最优 BST 问题上的反例（Example of Greedy Failing for Optimal BST problem）**</span>

> <span style="color:#7f8c8d;">[说明] 感谢 Nick Davis！</span>

> <span style="color:#7f8c8d;">[图 1 说明] 原页 Figure 1：某 BST 布局，代价 $= 1 \times 2 + 10 \times 1 + 8 \times 2 + 9 \times 3 = 55$。</span>

> <span style="color:#7f8c8d;">[图 2 说明] 原页 Figure 2：另一种 BST 布局，代价 $= 1 \times 3 + 10 \times 2 + 8 \times 1 + 9 \times 2 = 49$。</span>

**贪心反例的解读**：四键权重 $W_1=1, W_2=10, W_3=8, W_4=9$（比较次数 = 深度 + 1）。Figure 1（代价 55）把最大权重 $K_2$（$W_2=10$）放根：$K_2$ 比较 1 次，$K_1, K_3$ 各比较 2 次，$K_4$ 比较 3 次。但最优布局 Figure 2（代价 49）把**第三大**权重 $K_3$（$W_3=8$）放根：$K_3$ 比较 1 次，$K_2, K_4$ 各比较 2 次，$K_1$ 比较 3 次。对比可见：Figure 2 让 $K_4$ 从比较 3 次降到 2 次（省 $9$）、$K_3$ 从 2 次降到 1 次（省 $8$），代价是 $K_2$ 从 1 次升到 2 次（多花 $10$）、$K_1$ 从 2 次升到 3 次（多花 $1$），净省 $9+8-10-1 = 6$——所以「让多个较大权重都浅一点」胜过「让单个最大权重当根」。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这个反例完美展示了「为什么贪心在最优 BST 上失效」：贪心只盯着**单个最大权重**（$W_2 = 10$），却无视「把谁放根」对**所有其他键深度**的全局影响。Figure 2 的代价 49 < Figure 1 的 55，说明「让多个较大权重都浅一点」（$K_3, K_4$ 各自少比较一次）胜过「让单个最大权重当根」（$K_2$ 少比较一次但 $K_4$ 深很多）。这正是 DP「枚举所有根 + 算清 $W(i,j)$ 深度增量」相对贪心「局部最优」的本质优势：**DP 是在全局权衡，贪心只做局部最优**。这个「反例教学」的模式在 CLRS 里也是标配（如活动选择的贪心 vs 加权版失效），提醒你：**贪心正确性必须证明，不能想当然**——哪怕反例看起来只是「差一点点」。

---

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
