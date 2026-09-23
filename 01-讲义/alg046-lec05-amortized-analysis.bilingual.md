<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 5（Lecture 5）：摊还分析（Amortization）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

摊还分析（amortized analysis）是数据结构分析的一门强大技术，它关心的是**一个操作序列的总运行时间**（the total runtime of a sequence of operations）——而这往往才是我们真正在意的。本讲涵盖：

- 摊还分析的不同技术（Different techniques of amortized analysis）
  - 聚合方法（aggregate method）
  - 记账方法（accounting method）
  - 收费方法（charging method）
  - 势能方法（potential method）
- 摊还分析的例子（Examples of amortized analysis）
  - 表倍增（table doubling）
  - 二进制计数器（binary counter）
  - 2-3 树与 2-5 树（2-3 tree and 2-5 tree）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**表倍增（Table doubling）**</span>

（从 6.006 回忆起）我们希望在一个大小为 $m = \Theta(n)$ 的表中存储 $n$ 个元素。一个想法是：当 $n$ 因插入而变得大于 $m$ 时，把 $m$ 加倍。把大小为 $m$ 的表翻倍的成本显然是 $\Theta(m) = \Theta(n)$，这也是单次插入的最坏情形成本（worst case cost）。

但 **$n$ 次插入的总成本**是多少？它至多为

```math
2^0 + 2^1 + 2^2 + \cdots + 2^{\lfloor \lg n \rfloor} = \Theta(n)
```

在这种情况下，我们说每次插入的**摊还成本（amortized cost）**为 $\Theta(n)/n = \Theta(1)$。

🎥 *Devadas 在视频中[强调几何级数收敛]*："This is a geometric series, and so this is order n."（翻译：这是个几何级数，所以总和是 $O(n)$。）——表倍增分析的全部要点浓缩在这一句：几何级数 $2^0 + 2^1 + \cdots + 2^{\lg n}$ 的和不超过最大项的常数倍，因此「$n$ 次插入总成本 $\Theta(n)$、每次摊还 $\Theta(1)$」成立。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 表倍增的直觉是「昂贵操作（翻倍，$\Theta(n)$）是稀有操作，廉价操作（普通插入，$O(1)$）是多数」——而摊还分析把这两类统一到「每次操作的平均成本」。关键观察：$n$ 次插入里，真正的翻倍只有 $\lg n$ 次，且每次翻倍成本恰好是**上一次翻倍以来插入的元素数**的两倍。总和 $2^0 + 2^1 + \cdots + 2^{\lfloor \lg n \rfloor} < 2n$，即**几何级数（geometric series）收敛**——这是摊还分析里最核心的数学直觉：几何级数的部分和不超过最大项的常数倍，所以「指数式昂贵的稀有操作」被「线性次数」摊平后仍是常数摊还。注意这里区分两个概念：**最坏情形成本（worst-case cost，单次操作 $O(n)$）**与**摊还成本（amortized cost，平均到每次 $O(1)$）**——摊还分析并不承诺「每次都不慢」，而是承诺「总时间受控」。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**聚合方法（Aggregate Method）**</span>

我们在上述分析中使用的方法就是**聚合方法**：把**所有操作的成本加总**，再除以操作数。

```math
\text{每次操作的摊还成本 amortized cost per operation} = \frac{\text{k 次操作的总成本 total cost of } k \text{ operations}}{k}
```

聚合方法是最简单的方法。正因为简单，它可能无法分析更复杂的算法。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 聚合方法的哲学是「先算总账，再求平均」——它**不区分**不同操作的类型，把整个序列当作一个整体。它的局限也源于此：当不同操作的成本差异巨大、且序列结构复杂时（比如插入、删除、合并交错的复杂数据结构），「总成本除以 $k$」就难以给出有意义的每个操作的分项摊还成本。这也是为什么需要后面三种方法：**记账法**给每个操作单独分配一个「预付成本」，**收费法**允许把当前操作的昂贵部分「追溯」到过去的操作，**势能法**则用一个全局的「势能函数」把成本在序列上平滑化。四种方法视角不同但本质等价（都会在后面看到），选择取决于哪个在你的具体问题上更直观——正如本讲视频里 Devadas 所说，它们「都是计算同一类总和的不同方式」。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b;">**摊还界定义（Amortized Bound Definition）**</span>

摊还成本可以是、但不一定是平均成本（average cost）。我们可以给每个操作**任意分配**摊还成本，只要它们「**保持总成本**」（preserve the total cost），即对任何操作序列：

```math
\sum \text{摊还成本 amortized cost} \ge \sum \text{实际成本 actual cost}
```

其中求和遍历所有操作。

例如，我们可以说一棵 2-3 树实现了：每次创建（create）$O(1)$ 摊还成本、每次插入（insert）$O(\lg n^{*})$ 摊还成本、每次删除（delete）$0$ 摊还成本，其中 $n^{*}$ 是整条操作序列期间 2-3 树达到的最大规模（maximum size）。我们敢这样声称的原因是：对任何操作序列，设共有 $c$ 次创建、$i$ 次插入、$d \le i$ 次删除（不能从空树删除），则**总摊还成本**与**总实际成本**渐近相同：

```math
O(c + i \lg n^{*} + 0d) = O(c + i \lg n^{*} + d \lg n^{*})
```

稍后我们会把每次插入的摊还成本收紧到 $O(\lg n)$，其中 $n$ 是当前规模（current size）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**记账方法（Accounting Method）**</span>

该方法允许一个操作在「其分配的摊还成本 > 实际成本」时，把多余的部分作为**信用（credit）**存入**银行（bank）**以备将来使用；也允许一个操作在「其分配的摊还成本 < 实际成本」时，用已有的信用支付其超额的实际成本。

**表倍增（Table doubling）**：例如在表倍增中：
- 若一次插入**未触发**表倍增，则存入一枚代表 $c = O(1)$ 工作的硬币（coin）以备将来使用。
- 若一次插入**触发**了表倍增，那么在**上一次倍增之后**必有 $n/2$ 个元素被插入，它们的硬币尚未被消耗。用这 $n/2$ 枚硬币来支付 $O(n)$ 的表倍增。见下图。
- 表倍增的摊还成本：$O(n) - c \cdot n/2 = 0$（对足够大的 $c$）。
- 每次插入的摊还成本：$1 + c = O(1)$。

> <span style="color:#7f8c8d;">[原图说明] 原页附图：表格中的「元素」格与「未用硬币」格示意，以及下一次插入触发表倍增的位置。硬币直观代表「已为未来工作预付的成本」。</span>

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b;">**记账方法（续）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2-3 树（2-3 trees）**</span>

现在让我们尝试在 2-3 树上应用记账方法。我们的目标是证明：insert 有 $O(\lg n)$ 摊还成本，delete 有 $0$ 摊还成本。让我们尝试一个自然的做法：为插入一个元素存一枚 $O(\lg n)$ 的硬币，稍后删除该元素时用这枚硬币。然而，我们会遇到一个问题：到删除该元素时，树的大小可能已经变大 $n' > n$，我们存的硬币不足以支付那次 delete 操作的 $\lg n'$ 实际成本！这个问题可以用下一节的**收费方法（charging method）**来解决。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**收费方法（Charging Method）**</span>

收费方法允许操作**追溯性地（retroactively）**把成本记到过去的操作上。

```math
\text{一个操作的摊还成本 amortized cost of an operation} = \text{该操作的实际成本 actual cost of this operation} - \text{记给过去操作的总成本 total cost charged to past operations} + \text{未来操作记给它的总成本 total cost charged by future operations}
```

**表倍增与表减半（Table doubling and halving）**：例如在表倍增中，当表从 $m$ 翻倍到 $2m$ 时，我们可以把 $\Theta(m)$ 的成本记到自上次倍增以来的 $m/2$ 次插入操作上。每次插入被记 $\Theta(1)$，且不会被再次记账。所以每次插入的摊还成本是 $\Theta(1)$。

现在把上面的例子扩展为**表减半（table halving）**。动机是在有删除时节省空间。如果表只剩 $1/4$ 满，即 $n = m/4$，我们以 $\Theta(m)$ 的成本把表从 $m$ 缩小到 $m/2$。这样，任何一次调整大小（倍增或缩半）之后表都是半满的。现在每次表倍增仍有 $\ge m/2$ 次插入操作可记账，每次表减半有 $\ge m/4$ 次删除操作可记账。所以每次插入或删除的摊还成本仍是 $\Theta(1)$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 记账方法与收费方法是「银行视角」的一体两面：记账法由**操作主动存钱**（存信用），收费法由**昂贵操作主动收钱**（向过去的操作索债）。两者最终都落到同一个守恒等式：`总摊还成本 = 总实际成本 + 净存/净收`。表减半引入的关键技巧是「$1/4$ 阈值」（quarter threshold）：只有在表降到 $1/4$ 满时才缩半，而不是 $1/2$——因为若在 $1/2$ 时缩半，紧接着的插入会立刻触发倍增，形成「倍增-缩半-倍增」的抖动（thrashing），摊还分析就崩了。用 $1/4$ 阈值保证「缩半之后还有 $m/4$ 的余量」才可能再触发倍增——**阈值设计是摊还结构里防止坏情形循环的关键参数**。这个「留出安全余量」的思想在后续的哈希表负载因子、内存池增长策略里反复出现。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b;">**2-3 树中的免费删除（Free deletion in 2-3 trees）**</span>

再举一个例子，考虑 2-3 树中的插入与删除。我们的目标仍是证明：insert 有 $O(\lg n)$ 摊还成本（$n$ 是该次插入发生时树的大小），delete 有 $0$ 摊还成本。

Insert **不需要**记任何账（does not need to charge anything）。

Delete 将**向某个 insert 操作收费**。但我们**不会**向「被删除元素自己的那次 insert」收费，因为我们会遇到与记账方法相同的问题。相反，**每次 delete 操作会向「使树达到当前规模 $n$ 的那次 insert 操作」收费**。每次 insert 仍至多被收费一次，因为要让树规模再次达到 $n$，必须有另一次 insert 发生。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这个「向使树达到当前规模的那次 insert 收费」是本讲最精巧的论证之一。为什么不能向「被删元素自己的 insert」收费？因为树在删除时可能已变得更大（$n' > n$），当年存的 $\lg n$ 不够付现在的 $\lg n'$——即「信用随时间缩水」。解法是把收费对象**从『某个元素』改成『某个规模里程碑』**：每次 delete 使规模从 $n$ 降到 $n-1$，它的成本由「把规模推高到 $n$ 的那次 insert」来支付。关键保证是：**要让规模再次达到 $n$，必须有另一次 insert 发生（成为新的里程碑）**——因此每个 insert 至多被收费一次，总收费与总 insert 次数匹配。这体现了收费法区别于记账法的核心价值：**让「债主」是可验证的、不重复的**——你在后续的并查集（union-find）、伸展树（splay tree）分析里会再次看到这个「把成本锚定到不重复事件」的思维。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**势能方法（Potential Method）**</span>

该方法定义一个**势能函数（potential function）** $\Phi$，把数据结构的配置（configuration）映射为一个数值。函数 $\Phi$ 等价于所有过去操作存储的**未使用信用总量**（即银行账户余额）。现在

```math
\text{一个操作的摊还成本 amortized cost of an operation} = \text{该操作的实际成本 actual cost of this operation} + \Delta\Phi
```

且

```math
\sum \text{摊还成本 amortized cost} = \sum \text{实际成本 actual cost} + \Phi(\text{最终 DS final DS}) - \Phi(\text{初始 DS initial DS})
```

为了让摊还上界成立，$\Phi$ 在任意时刻都不应降到 $\Phi(\text{initial DS})$ 以下。若 $\Phi(\text{initial DS}) = 0$（通常是这种情况），那么 $\Phi$ 不应变成负数（直觉上：我们不能「欠银行」owe the bank）。

**与记账方法的关系**：在记账方法中，我们指定 $\Delta\Phi$；在势能方法中，我们指定 $\Phi$。两者互相决定，因此这两种方法是**等价**的。但有时一种比另一种更直观。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 势能方法是记账法的「全局化」：记账法把信用挂在**具体元素/操作**上（硬币贴在某处），势能法则把信用抽象成**整个数据结构状态的一个函数** $\Phi(S)$——它不关心「谁付了钱」，只关心「当前状态的『势』有多高」。摊还成本 $= \text{实际成本} + \Delta\Phi$ 的物理解读：若操作把势能**提高**（$\Delta\Phi > 0$），说明它「预支」了未来要用的信用，摊还成本高于实际成本；若操作把势能**降低**（$\Delta\Phi < 0$），说明它在「消耗」过去积累的信用，摊还成本低于实际成本。而「$\Phi$ 永不跌回初值」的约束，等价于「银行永不透支」——这保证了「总摊还 ≥ 总实际」的守恒。选择 $\Phi$ 的艺术在于：**让『昂贵操作』恰好把势能打回低位（ΔΦ 大负），让『廉价操作』恰好把势能补回高位（ΔΦ 小正）**，两者相消，摊还成本就均匀了。这套「选一个量，让它在大事件时暴跌、在小事件时缓升」的设计，在后面的字典树、斜堆（skew heap）、并查集里会反复出现。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**二进制计数器（Binary counter）**</span>

我们势能方法的第一个例子是**递增一个二进制计数器（incrementing a binary counter）**。例如：

```math
0011010111 \xrightarrow{\text{increment 递增}} 0011011000
```

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b;">**二进制计数器（续）**</span>

递增的成本是 $\Theta(1 + \#1)$，其中 $\#1$ 表示**末尾连续 1 的个数**（number of trailing 1 bits）。所以直觉是：**1 位是坏的（1 bits are bad）**。

定义 $\Phi = c \cdot \#1$。那么对足够大的 $c$：

```math
\begin{aligned}
\text{摊还成本 amortized cost} &= \text{实际成本 actual cost} + \Delta\Phi\\
&= \Theta(1 + \#1) + c(-\#1 + 1)\\
&= \Theta(1)
\end{aligned}
```

$\Phi(\text{initial DS}) = 0$ 当计数器从 $000\cdots 0$ 开始。这对上述摊还分析是**必要**的。否则，$\Phi$ 可能变得比 $\Phi(\text{initial DS})$ 更小。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 二进制计数器是势能法的**最小可运行示例**，值得彻底吃透。每次递增：末尾的 $k$ 个 1 全部翻成 0（成本 $k$），然后第一个 0 翻成 1（成本 1），所以实际成本 $\Theta(1 + \#1)$。势能 $\Phi = c \cdot \#1$ 的选取动机是「1 位越多，未来翻 0 时越费钱」——把**未来成本预存在当前的 1 位里**。一次递增让 $\#1$ 的变化是 $-k + 1$（$k$ 个 1 变 0，1 个 0 变 1），于是 $\Delta\Phi = c(-k+1)$，摊还 $= \Theta(1+k) + c(-k+1) = \Theta(1)$（取 $c$ 足够大吸收常数）。直觉闭环：**每把一位翻成 1，就存一枚币；每把一位翻成 0，就花一枚币**——1 的总数守恒，摊还就均匀。这个「用计数总量做势能」的模板，是你在 CSAPP 里二进制运算的自然延伸，也是后面分析二项堆（binomial heap）计数位的雏形。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2-3 树中的插入（Insert in 2-3 trees）**</span>

Insert 在最坏情况下可能引起 $O(\lg n)$ 次**分裂（splits）**，但我们可以证明它只引起 $O(1)$ 次摊还分裂。首先考虑什么引起分裂：**插入到一个 3-节点（a node with 3 children）**。在这种情况下，这个 3-节点需要分裂成两个 2-节点。

所以 **3-节点是坏的（3-nodes are bad）**。我们定义 $\Phi = $ 3-节点的个数（the number of 3-nodes）。那么 $\Delta\Phi \le 1 - $ 分裂次数（the number of splits）。摊还分裂数 $= $ 实际分裂数 $+ \Delta\Phi = 1$。$\Phi(\text{initial DS}) = 0$ 当树初始为空。

**上述分析对任意 $(a,b)$-树（(a,b)-tree）都成立**，只要我们把 $\Phi$ 定义为 $b$-节点的个数。

如果我们同时考虑 2-3 树中的插入与删除，能否声称 insert 有 $O(1)$ 次摊还分裂、delete 有 $O(1)$ 次摊还合并（merges）？**答案是否定的**：因为一次分裂产生两个 2-节点，而 2-节点对合并来说是「坏的」。在最坏情况下，它们可能在下次 delete 时被合并，然后又在下一次 insert 时被分裂，如此反复。

**我们如何解决这个问题？** 我们需要阻止分裂与合并产生「坏」节点。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2-5 树中的插入与删除（Insert and delete in 2-5 trees）**</span>

我们可以声称在 2-5 树中，insert 有 $O(1)$ 次摊还分裂，delete 有 $O(1)$ 次摊还合并。

在 2-5 树中，插入到一个 **5-节点（a node with 5 children）**会导致它分裂成两个 3-节点。

> <span style="color:#7f8c8d;">[原图说明] 原页附图：5-节点（含 5 个键、6 个子节点）分裂为两个 3-节点（各含 3 个键、3 个子节点），中间一个键提升（promote）到父节点。</span>

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b;">**2-5 树（续）**</span>

从一个 **2-节点（a node with 2 children）**删除会导致它与另一个 2-节点**合并**成一个 3-节点。

> <span style="color:#7f8c8d;">[原图说明] 原页附图：2-节点（含 1 个键、2 个子节点）删除后与兄弟 2-节点合并为 3-节点（含 2 个键、3 个子节点），父节点的一个键降级（demote）下来。图中「key demoted」标注了从父节点降下的键。</span>

**5-节点与 2-节点都是坏的**。我们定义 $\Phi = $ 5-节点的个数 $+$ 2-节点的个数。

摊还分裂数与摊还合并数 $= 1$。$\Phi(\text{initial DS}) = 0$ 当树初始为空。

**上述分析对任意满足 $b > 2a$ 的 $(a,b)$-树都成立**，因为分裂与合并**不会产生**坏节点。我们定义 $\Phi$ 为 $b$-节点的个数加上 $a$-节点的个数。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 从 2-3 树到 2-5 树的关键升级是「**势能函数必须对『当前会发生的坏事件』免疫**」。2-3 树单独分析插入时 3-节点是坏的（要分裂）；但若同时考虑删除，2-节点对合并也是坏的——分裂产生两个 2-节点，恰是下次合并的「坏种子」，于是插入-删除-插入会无限抖动。2-5 树的妙处在于：5-节点分裂产生两个 3-节点（**3-节点既不是 2-节点也不是 5-节点，即不是任何一方的坏节点**），2-节点合并产生一个 3-节点（同理）。分裂的产物不落进合并的「坏集合」，合并的产物也不落进分裂的「坏集合」——**用 $b > 2a$ 保证分裂/合并的产物都在 $(a+1, b-1)$ 这个「安全中间地带」**。这本质上是把势能法从「单操作分析」升级到「多操作联合分析」：势能函数必须同时计价两种操作的「坏」根源。你可以把 $b > 2a$ 理解为「2 与 5 之间的空隙足够大，让分裂产物（3）不触及合并红线（2）」——一个关于整数间隙的最小设计约束，却决定了整个摊还界的成立。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 注意：势能法的这些例子也都可以用记账方法实现——把硬币放在 1 位（二进制计数器）或 2/5-节点（2-5 树）上。这再次印证「记账法与势能法等价」：记账法把势能「显式地」贴到具体对象上，势能法则用函数「隐式地」描述总量。实践中选择哪个，取决于「硬币放哪更自然」：对象少而清晰时记账法直观（如二进制计数器的 1 位），状态复杂时势能法更省心（如 2-5 树同时追踪两类坏节点）。另外补充一个 Wiki 查证的事实：摊还分析的前驱研究出现于 1970 年代，其框架由 Robert Tarjan 于 1985 年的论文《Amortized Computational Complexity》（SIAM J. Alg. Disc. Meth. 6(2)）正式确立——论文中的经典例子是**列表 move-to-front、平衡搜索树（2-3 树/B-树）与并查集（set union）**。也就是说，你今天学的方法论，正是 Tarjan 在分析 1970 年代并查集（1975 年 set-union 论文）与平衡搜索树等数据结构时打磨出来的工具；它后来又被用于伸展树（splay tree，Sleator & Tarjan 1985）等 1980 年代数据结构的分析。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
