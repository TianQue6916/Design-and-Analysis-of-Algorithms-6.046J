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

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这两条 target recurrence（目标递推式）不是两个并列的目标，而是同一件几何事实的两种写法：square root（开平方）与把 bit length（比特长度）减半是同一个动作。设 $u = 2^{b}$，于是 $\log u = b$、$\sqrt{u} = 2^{b/2}$ —— 开平方就是把指数除以二。所以 $T(u) = T(\sqrt{u}) + O(1)$ 与 $T(\log u) = T(\log u/2) + O(1)$ 在变量替换 $b = \log u$ 下是同一个递推式，只是一个写在 universe（值域）上、一个写在 bit length（比特长度）上。
>
> 层数为什么正好是 $\log\log u$：每层把 $b$ 除以 2，从 $b$ 走到 1 要 $\log_2 b$ 步，而 $b = \log_2 u$，两步复合得 $\log_2\log_2 u$。底数不影响复杂度阶，但影响常数，所以下面一律取底 2。
>
> 可算的例子。取 $u = 2^{32} = 4\,294\,967\,296$（IPv4 地址总数），$b = 32$，位数序列是 $32 \to 16 \to 8 \to 4 \to 2 \to 1$，正好 5 次减半，所以一次操作要穿 5 层。对应的 universe（值域）序列是 $2^{32} \to 2^{16} \to 2^{8} \to 2^{4} \to 2^{2} \to 2^{1}$，每一项都是前一项的平方：$(2^{16})^2 = 2^{32}$、$(2^{8})^2 = 2^{16}$、$(2^{4})^2 = 2^{8}$、$(2^{2})^2 = 2^{4}$、$(2^{1})^2 = 2^{2}$。这说明 $2^{32} = 2^{2^{5}}$ 是一条高度 5 的 power tower（幂塔），递归恰好 5 层触底，不需要任何 padding（补齐）。换成 $u = 1000$ 就没这么整齐：最近的塔值是 $2^{16} = 65536$，必须先补到 65536 才能保证每层开方都是整数，比例 $65.536$ 倍；而 $\log\log 1000 \approx 3.32$ 与 $\log\log 65536 = 4$ 只差 0.68 层。
>
> 对比 $T(u) = T(u/2) + O(1)$：那是 32 层，比 5 层多 6.4 倍。再拿集合大小 $n = 10^{6}$ 的平衡树比，$\log_2 10^{6} \approx 19.93$，约 20 层；$n = 10^{9}$ 时约 30 层。所以对 network routing table（网络路由表）这种 $u$ 钉死为 $2^{32}$、$n$ 可以长大的场景，层数是常量 5，与 $n$ 完全脱钩。
>
> 跨课程连接。6.006 里的 binary search（二分搜索）满足 $T(n) = T(n/2)+1$，它二分的是 rank（秩）：要求元素已排序且连续存放，一次 comparison（比较）砍掉一半下标区间。vEB 二分的是 key（键）的 bit（比特）表示：不要求排序，只要键是整数，读一个 digit（数位）就砍掉一半 value（取值）区间。这就是「$\log n$ 依赖 rank、$\log\log u$ 依赖 bit length」的分界，也是正文说它比 hashing（哈希）有更酷查询的原因：hashing 把键压成一个地址，丢掉了顺序信息，而这里恰恰把顺序保留了。6.042J 里「整数的二进制表示是 $[0,u)$ 与 $\{0,1\}^{b}$ 之间的 bijection（双射）」就是这条递归的坐标纸：每一层对应一个 bit，从最高位走到最低位。
>
> 正文那行 $n = n^{O(1)}$ 与 $n^{(\log n)^{O(1)}}$ 其实是在限定 $u$ 相对 $n$ 有多大。前者意味着 $u$ 是 $n$ 的多项式，此时 $\log u$ 与 $\log n$ 同阶，$\log\log u$ 与 $\log\log n$ 只差一个常数，把界写成 $\log\log n$ 才说得通；后者意味着 $u$ 比 $n$ 的任何多项式都大得多，两个量之间才出现真正的缝隙。判据一句话：只有 $\log u = O(\log n)$ 时，$\log\log u$ 才能被读成 $\log\log n$；否则老老实实写 $u$。
>
> 一个常见混淆要先排掉：$\log\log u$ 与 iterated logarithm（迭代对数）$\log^{*}u$ 不是一回事。判据在算子：$\log\log u$ 来自「把 $b$ 除以 2」这种固定比例的缩水，缩水一次是常数步，缩水一个数量级只需要常数次；$\log^{*}u$ 来自「对 $u$ 反复取对数」这种每次换算子的缩水，$u \to \log u \to \log\log u \to \cdots$，直到落到常数为止。vEB 的递归是开方，开方在 $b$ 上就是除以 2，所以拿到的是 $\log\log u$，不是 $\log^{*}u$。这条区分在别处会咬人：union-find（并查集）的路径压缩给出的是 $\log^{*}$，因为那里每层的索引尺度都换了一次；谁把两者混着用，就会在某一讲里写出一个自己都验不过去的递推式。
>
> 再给一组「解耦」的数字，把 $n$ 与 $u$ 的角色摆清楚。固定 $u = 2^{32}$，让 $n$ 从 $10^{3}$ 长到 $10^{9}$：vEB 的层数恒为 5，而平衡树的层数从 $\log_2 10^{3} \approx 9.97$ 长到 $\log_2 10^{9} \approx 29.9$，倍数从 $2$ 倍拉到 $6$ 倍。反过来固定 $n = 10^{6}$，让 $u$ 从 $2^{16}$ 长到 $2^{64}$：vEB 的层数从 $\log\log 2^{16} = 4$ 变成 $\log\log 2^{64} = 6$，只涨 2 层；平衡树一直是 20 层不动。所以 vEB 对 $u$ 极不敏感、平衡树对 $n$ 极敏感 —— 这正是「$u$ 是地址空间、$n$ 是数据量」的现实场景要的东西。
>
> 收束：这两条式子的用处不是给答案，而是给判据 —— 任何声称快过 $O(\log\log u)$ 的方案，要么偷偷换了 cost model（代价模型），要么没有真正把 $n$ 与 $u$ 解耦。

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

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> cluster（簇）要解决的问题很具体：bit vector（位向量）的 Insert / Delete 是 $O(1)$，但 Successor 最坏要从 $\mathrm{low}(x)$ 一直扫到 $u-1$，代价 $O(u)$。分簇把「一次长扫描」换成「三次短扫描」，把 $u$ 换成 $3\sqrt{u}$。
>
> 为什么分割点必须是 $\sqrt{u}$，而不是随便一个 $k$。设分成 $k$ 个簇、每簇 $u/k$ 个位置，Successor 要付三段账：第一段簇内从 $\mathrm{low}(x)$ 往后扫，最坏 $u/k$；第二段跨簇找下一个非空簇，最坏 $k$；第三段进入目标簇找最小元素，最坏 $u/k$。总代价 $2u/k + k$。对 $k$ 求导令其为零：$-2u/k^{2} + 1 = 0$ 得 $k = \sqrt{2u}$，代回得 $2\sqrt{2u} \approx 2.83\sqrt{u}$。取 $k = \sqrt{u}$ 得 $3\sqrt{u}$，比最优只差 $6\%$，却换来两个好处：$u$ 是 2 的偶次幂时 $\sqrt{u}$ 整除 $u$，下标运算没有取整；$\sqrt{u}$ 也是 2 的幂，$\mathrm{high}$ 与 $\mathrm{low}$ 退化成移位与掩码。
>
> 这个「差一点但要整洁」的取舍不是审美，是硬约束。$u = 2^{32}$ 时 $\mathrm{high}(x) = x \gg 16$、$\mathrm{low}(x) = x\ \&\ 0\text{xFFFF}$，各 1 个 cycle。若改用 $k = 1000$，同一件事要做整除与取模，64 位除法在 x86 上延迟是 30–90 个 cycle，比移位慢两个数量级。所以 cluster（簇）的形状由 hardware（硬件）决定，不是由纯数学决定。
>
> 可算的量级。$u = 2^{32}$、$\sqrt{u} = 65536$：纯 bit vector（位向量）最坏扫 $4\,294\,967\,296$ 个位；分簇最坏 $3 \times 65536 = 196\,608$ 个位。比值 $4\,294\,967\,296 / 196\,608 = 21\,845.3$，正好是 $\sqrt{u}/3$。也就是说这一步单独就把最坏情况改善了 2 万倍，代价只是把一维下标换成二维下标。
>
> 与 CSAPP 的对照。第三章讲数组寻址时给出 `A[i][j]` 的地址是 `base + 4*(i*4 + j)`，行列索引与一维地址之间正是同一个 $\mathrm{index}(i,j) = i\sqrt{u} + j$ 的 bijection（双射）。区别：那个 stride（步长）由编译器在编译期定死，这里的分割基数由算法在运行期选，并且随递归层层改变 —— 上一层 65536、下一层 256、再下一层 16。
>
> 与线性代数的区别要说清，否则会误导。这不是 tensor product（张量积）：张量积作用在向量或矩阵上，把两个空间的维数相乘、元素是乘积之和；这里只是 index set（下标集合）$[0,u)$ 与 $[0,\sqrt{u})\times[0,\sqrt{u})$ 之间的一个 bijection（双射），没有线性结构，也不产生乘积项。分簇不造新向量，只给下标换了套坐标，这正是递归能接上的原因：换坐标之后，三段扫描里每一段都是一个规模 $\sqrt{u}$ 的 Successor（后继）子问题，问题形状没变、只是变了大小。
>
> 这一步还有一个副产品值得先记，它是后面所有递归的物理前提：「哪些簇非空」这件事本身每个簇只占 1 个 bit（比特），簇一共 $\sqrt{u}$ 个，于是这 $\sqrt{u}$ 个 bit（比特）可以再存成一条长 $\sqrt{u}$ 的 bit vector（位向量）。$u = 2^{32}$ 时它长 $65536$ 位，合 $65536/8 = 8192$ 字节即 $8\ \mathrm{KB}$，一级数据 cache（L1 data cache，常见容量 32 KB）整个装得下，读它只要几次 cache line（缓存行）传输，而不是 65536 次访问。若没有这个规模上的巧合，递归到第二层就要开始换页，$O(\log\log u)$ 只会停在纸面推导上。
>
> 还要说清为什么是「三次扫描」而不是「一次位运算」。簇内部此时仍是 bit vector（位向量），按理一条 `ctz`（数尾部零）指令就能给出 $\mathrm{low}(x)$ 之后第一个 1，代价是 1 个 cycle，为什么正文还写 $O(\sqrt{u})$？因为这里在算的是「不用硬件技巧时的模型代价」，是为了给下一节的递归留出改进空间：一旦你承认位向量可以被一条指令扫完，就没必要递归了。真到工程实现里，恰恰是这条指令让底层直接用位向量、上层才递归 —— 这正是最后一节 base case（基本情况）的做法。
>
> 收束：分簇不是优化，是把一个 $u$ 规模的问题拆成三个 $\sqrt{u}$ 规模的同类问题；能不能拆出无穷多层，就看下一节敢不敢递归。

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

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 一旦递归，Insert 从一次调用变成两次，一次进簇、一次进 summary（汇总），递推式从 $T(u) = T(\sqrt{u}) + O(1)$ 涨到 $2T(\sqrt{u}) + O(1)$，解从 $\log\log u$ 退到 $\log u$。这一条要把「2 从哪来、怎么消掉」讲透。
>
> 先解。令 $b = \log u$，得 $S(b) = 2S(b/2) + c$，三种方法各走一遍。recursion tree（递归树）：第 $k$ 层有 $2^{k}$ 个结点、每结点代价 $c$，最深一层 $k = \log_2 b$，总数 $c(2^{\log_2 b + 1} - 1) = c(2b-1)$，即 $\Theta(b) = \Theta(\log u)$。master theorem（主定理）：子问题数 $a = 2$、缩小因子 2、非递归部分 $f(b) = O(1) = O(b^{0})$，$\log_2 2 = 1 > 0$，落在第一种情形，得 $\Theta(b^{1}) = \Theta(b)$。substitution（代入法）：猜 $S(b) \le c'b$，验证 $2c'(b/2) + c = c'b + c$，只要 $c' \ge c$ 就闭合。
>
> 边界在这里，必须点明：master theorem（主定理）不能直接套在 $T(u) = 2T(\sqrt{u}) + O(1)$ 上。它的标准形要求子问题规模是 $n/b$，也就是线性缩水；这里是 $\sqrt{u}$，属于指数缩水。必须先做 $b = \log u$ 的换元，把指数缩水翻译成线性缩水，才进得了主定理。三种方法的分工是：recursion tree（递归树）不怕非线性缩水，代价是常数项容易算错；substitution（代入法）要求先猜对答案，猜错就证不下去；master theorem（主定理）最省事，但只认标准形，换元这一步它不管。
>
> 可算的数字。$u = 2^{32}$、$b = 32$、深度 5。双递归的结点总数 $1+2+4+8+16+32 = 63$，一次操作付 63 份代价，而单递归版本付 5 份，比值 $12.6$。若再糟一步变成三递归，$T(u) = 3T(\sqrt{u}) + 1$ 解出 $S(b) = \Theta(b^{\log_2 3}) = \Theta((\log u)^{1.585})$，$b = 32$ 时 $32^{1.585} \approx 243.0$，是 5 的 $48.6$ 倍。递归次数每加一，指数就从 $1$ 往 $1.585$、$2$ 爬，而层数一直还是 5：决定复杂度的是每层的 fan-out（扇出），不是深度。
>
> 关键观察：两次调用里只有一次是深递归。summary（汇总）的更新只在「该簇原本为空」时才必要 —— 那是第一次往这个簇放元素，summary 里对应的位从 0 变 1，之后同簇的插入对它毫无影响。所以正确写法是先判空：簇为空时先 `Insert(summary, high(x))`，此时簇内调用退化成「设 min/max 就返回」的 $O(1)$；簇非空时跳过 summary，簇内调用成为唯一一条深路径。两个分支互斥且完备，每层恰好一次深调用，$T(u) = T(\sqrt{u}) + O(1)$ 成立。反过来说，「无条件双递归」的浪费不在于多写一行代码，而在于它把「簇非空」这个信息白扔了 —— 而这条信息恰好只值一次判空。
>
> 跨课程连接。CSAPP 第六章说，一次 cache miss（缓存缺失）是 100–300 个 cycle，一次寄存器操作是 1 个 cycle。$2^{k}$ 个结点的递归树意味着同一层有 $2^{k}$ 次独立的结构访问，而这些访问若落在不同 cache line（缓存行）上，每次都要付一次 miss。所以递推式的常数项 $c$ 在真实机器上不是一个 cycle 而是几百个 —— 压 fan-out（扇出）不只是渐近问题，还是把几百倍的常数砍掉一次的问题。
>
> 这里要区分 worst-case（最坏情况）界与 amortized（摊还）界。上面那条「只有一个深调用」是逐次操作的界，不是平摊出来的：无论插入序列怎么构造，每一次 Insert 都只走一条深路径，不存在「某次很贵、靠后面几次便宜找补」的结构。这比摊还界强一档，而路由表这类场景恰好需要这一档：转发路径要求每次更新都有确定的延迟上界，不能被某一次抖动打断。要拿到摊还界其实更容易（把 summary 更新记账到簇的第一次插入上就行），但要拿到最坏界就必须靠代码里那个判空分支 —— 保证的强度取决于你写没写那行 `if`，不取决于递推式长得像什么。
>
> 顺带把 $3T(\sqrt{u})$ 这个版本的来历说清，它不是为了难为你：Successor 在新版里读三次才凑得齐答案，簇内读一次、summary 读一次、目标簇取 min 再读一次；如果三次都写成递归而不是一次判空加一次递归，就得到三条递归路径。而正文的写法把前两次压成一次（判空决定走哪条），第三次压成一次 $O(1)$ 的字段读取 —— 换句话说，$3$ 变成 $1$ 不是靠数学，是靠「多存两个字段、多写一个分支」把信息提前备好。这也解释了为什么后面的改进全是「加字段」：在 vEB 里，字段就是被压缩掉的递归层。
>
> 顺带修一处原文笔误：伪代码第 2 行 `j = Successor(V.cluster[i], j)` 右端的 $j$ 应写作 $\mathrm{low}(x)$，否则引用了尚未定义的同名变量。第 3 行的 $\infty$ 是 sentinel（哨兵）：把「不存在」编码成一个可比较的普通值，省掉返回值检查的那次 branch（分支）。

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

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这一条要解决的是「递归数怎么从 2 降到 1」，但真正的机制比「多存两个字段」更精确：它把「该往哪一层走」这个决定，从「递归进去试一下才知道」变成「一次 comparison（比较）就知道」。
>
> 对比两版 Successor。老版是串行的：先在簇内递归找，返回 $\infty$ 才去 summary（汇总）递归找，最坏两次深调用。新版先用 `low(x) < V.cluster[i].max` 判一次：成立则簇内一定有比 $\mathrm{low}(x)$ 更大的元素，答案必在簇内，只递归一次；不成立则连簇内最大元素都不大于 $\mathrm{low}(x)$，必然要跨簇，直接去 summary 找下一个非空簇，也只递归一次。两条分支互斥且完备，所以每层恰好一次深调用，总代价等于深度 $\log\log u$。
>
> 为什么判据必须是 $\max$ 而不是 $\min$：Successor 问的是「大于」，判据要卡在区间右端点；Predecessor（前驱）对称地用 $\min$。若拿 $\min$ 判「簇非空」，只能知道「里面有元素」，不能知道「$\mathrm{low}(x)$ 之后还有元素」，信息量不够，两条分支会重叠，退回两次递归。
>
> 一个能逐位复核的追踪。取 $u = 2^{16} = 65536$、$\sqrt{u} = 256$、$S = \{3, 30000\}$。查 successor(200)：$\mathrm{high}(200) = \lfloor 200/256 \rfloor = 0$、$\mathrm{low}(200) = 200 \bmod 256 = 200$。$\mathrm{cluster}[0].\max = 3 < 200$，第一分支不成立，转 summary 求后继：summary 里的非空簇是 0 与 $\lfloor 30000/256 \rfloor = 117$，得 117；再取 $j = \mathrm{cluster}[117].\min = 30000 - 117\times256 = 48$，返回 $\mathrm{index}(117, 48) = 117\times256 + 48 = 30000$。全程 1 次深递归，簇内那一层被完整跳过。再查 successor(2)：$\mathrm{high} = 0$、$\mathrm{low} = 2$，$\mathrm{cluster}[0].\max = 3 > 2$，进簇内得 3，也是 1 次深递归。两个分支各演示一遍，判定都只花一次比较。
>
> 抽象成可复用的形式：在父节点存一个 subtree（子树）的极值当 pruning（剪枝）依据，本质是把「目标区间是否与该子树相交」这个 $O(1)$ 判据提前拿到手。这与 CSAPP 第六章的 locality（局部性）原则同构 —— 把判定所需的最少信息放进与索引同一行 cache line（缓存行），一次 load（载入）就够，不用先解引用子指针再读。区别在于 CSAPP 数的是 latency（延迟），这里数的是递归层数；但「把判定信息提到访问路径前面」是同一条原则。顺带排一个误认：branch prediction（分支预测）不是这里的机制，那是在猜「走哪边」，这里根本不用猜，判据是确定性的。
>
> 与 6.042J 的连接。$[0,u)$ 上有一个 total order（全序），它限制到每个 cluster（簇）上仍是一个全序，$\min$ 与 $\max$ 是这个受限全序仅有的两个端点。判据 $\mathrm{low}(x) < \max$ 问的是「从 $x$ 往后那段后缀与该簇是否相交」，属于区间相交判定，而不是集合成员判定。这个区分是剪枝成立的全部理由：member（成员查询）必须下到叶子才知道，区间相交在父节点就能回答。
>
> 再补一层账：这两个字段本身也要维护，成本是 $O(1)$。Insert 时 min 与 max 至多各改一次，都发生在顶层、不进递归；Delete 时修复 max 也只是一次 $O(1)$ 的 summary（汇总）查询。所以「多存两个字段」不是免费的抽象，而是每层多两次比较、换来每层少一次递归。一次递归的代价是整整一条长度为 $\log\log u$ 的后续路径，一次比较的代价是 1 个 cycle —— 这个交换在任何常数下都划算，而且随着 $u$ 变大越来越划算，因为路径更长、比较不变。
>
> 还要说清 $\max$ 与 summary（汇总）不冗余。summary 只回答「簇非空与否」，那是集合层面的信息；$\max$ 回答「簇里有没有越过 $\mathrm{low}(x)$ 的元素」，那是区间层面的信息。前者是后者的必要条件而非充分条件，拿前者去判分支，遇到「簇非空但元素全在 $x$ 左边」这种情形就会走错路，白递归一趟再带着 $\infty$ 回来。这条差别在实现里表现为一次偶然的慢路径，在证明里则直接导致递推式的次数从 1 变回 2。
>
> 收束：式 (1) 的 `if x < V.min: return V.min` 是同一思想的端点情形 —— min 单独平铺存放，使「比整个集合都小」这个边界直接一次比较返回，一层都不用下。

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

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> borrowing（借位）解决的是「min 不递归存放」带来的 invariant（不变量）冲突。vEB 的 min 是一个平铺字段，不出现在下面的 summary（汇总）与任何 cluster（簇）里；因此插入一个比 min 更小的元素时不能直接塞进簇内 —— 一旦塞进去，它就同时是簇的 min（存在簇自己的顶层字段里）和整体 min（存在上一层的字段里），同一个元素存了两份，删一次删不干净。
>
> 正确做法是 swap（交换）：若 $x < V.\mathrm{min}$，先交换两者，顶层 min 变成更小的那个，被换出来的旧 min 拿去递归插入。于是「被递归下放的元素永远不是当前结构的 min」，invariant（不变量）保住。需要维护的 invariant（不变量）要精确表述：对每个结构 $V$，$V.\mathrm{min}$ 这个值不出现在 $V.\mathrm{summary}$ 与任何 $V.\mathrm{cluster}[i]$ 中；所以结构里真正被下放的元素数是 $|V| - 1$，只有当 $|V| \le 1$ 时 summary 才为空。这条不变量的用处很直接：它让 $V.\mathrm{min}$ 的读写永远是 $O(1)$，也让「元素总数」与「下层元素数」之间有一个可归纳的固定差值。
>
> 那么为什么 min 干脆不递归存？因为把它递归存会立刻贵一倍。假设每层都同步维护 min，一次 Insert 就得在簇内和 summary 两处都更新它，退化成 $2T(\sqrt{u}) + O(1)$，也就是 $O(\log u)$。而 min 的全部用途只有三个：successor 的边界判断、delete 时新 min 的来源、min 查询本身 —— 三者都是「读一次」，没有一个是「遍历」。既然如此，一份放在顶层就够，代价是插入与删除时要手工搬动它，这正是 borrowing（借位）与「提升」要干的活。一句话概括：min 在这里是 cache（缓存）而不是冗余 —— 冗余必须时时同步，缓存只需要在失效时重建。
>
> 手算一遍。$u = 16$、$\sqrt{u} = 4$，依次插入 5 与 3。插 5：结构为空，走 INSERT 第 1–3 行，设 min 与 max 同为 5 后返回，$O(1)$，此时 summary 与各簇全空。插 3：$3 < \mathrm{min} = 5$，swap 后 min 变 3、待插值变 5；$i = \lfloor 5/4 \rfloor = 1$、$j = 5 \bmod 4 = 1$；$\mathrm{cluster}[1]$ 为空，先 `Insert(summary, 1)` 使其 min 与 max 都等于 1，再 `Insert(cluster[1], 1)` 走「空插入」直接返回。结果：顶层 min=3，簇 1 存着位 1（对应值 5），summary 存 1。检验不变量：3 不在任何簇里，成立。此时查 successor(2)：$2 < \mathrm{min} = 3$，一次比较直接返回 3，一层都不下 —— 这就是 min 平铺存放的直接收益。
>
> 再删 3（等于 min）：summary 非空，$i = \mathrm{summary}.\mathrm{min} = 1$，新 min 取 $\mathrm{index}(1, \mathrm{cluster}[1].\mathrm{min}) = 1\times4 + 1 = 5$；随后必须把 5 从 $\mathrm{cluster}[1]$ 删掉，簇空了就同步删 summary 里的 1。删完顶层 min=5、簇全空、summary 空，不变量仍成立，整条路径只走一条深递归。这一步是「提升到顶层」的全部代价：提升 = 复制 + 从原处删除，两者必须成对；漏掉删除就破坏不变量，之后删它会删不掉（顶层说在、下面查不到），或者被计数两次。
>
> 跨课程连接。6.006 里 binary heap（二叉堆）的 insert 是同一个模式：先把新键放到数组末尾，占住顶层槽位，再把被挤出来的值一路 sift down（下沉）。区别在终止条件：堆是「沉到位置合适为止」，最坏 $\log n$ 次交换；vEB 是「只换一次，换下来的那个才递归」，递归深度由 universe（值域）决定、与 $n$ 无关。CSAPP 第三章的 `xchg`（交换指令）让这个 swap 在真实机器上是一条指令，所以代价全部落在下一次递归调用上，而不在交换本身。
>
> 顺带说明原文残缺：伪代码第 12–13 行 `if V.summary.max == None` 的分支体原文未给出，按语义应为「summary 为空则 $V.\mathrm{max}$ 一并置空，否则取 summary 最大簇的最大元素」。这一支只在删掉当前 max 时才走到，不影响主路径；但它必须存在，否则「删掉最大元素」之后 $V.\mathrm{max}$ 会指向一个已经不存在的值，后续的 Successor 判据全部失效。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b;">**下界与空间改进（Lower Bound and Space Improvements）**</span>

<span style="color:#2471a3;">**[theorem]**</span> <span style="color:#c0392b;">**下界（Lower Bound）[Patrascu & Thorup 2007]**</span>

即使对**静态查询**（static queries，无 Insert/Delete）：

- 对 $u = n^{(\log n)^{O(1)}}$，每次查询需要 $\Omega(\log \log u)$ 时间
- 空间为 $O(n \cdot \mathrm{poly}(\log n))$

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这一条要讲的是 lower bound（下界）为什么能证、以及它到底证了什么 —— 不是「有个定理说不能再快」。
>
> 先把结论的限定词念全：在 cell-probe model（单元探测模型）里，word（机器字）长 $w$ 位、空间 $O(n\cdot\mathrm{poly}(\log n))$ 位、universe（值域）$u = n^{(\log n)^{O(1)}}$ 时，predecessor（前驱）查询需要 $\Omega(\log\log u)$ 次探测。模型的含义是：任意编码、任意预处理、任意计算全部免费，只对「读了多少个 cell（单元）」计费。这个模型比真实机器宽松 —— 真实机器连算术都要花 cycle，所以在宽松模型里成立的下界，在真实机器上必然也成立；反过来不成立，这就是下界的条件性，也是它唯一的软肋。
>
> 为什么非要用这个模型？因为在 comparison model（比较模型）里，下界是 $\Omega(\log n)$。拿那个界去套 vEB 会得出「vEB 不存在」的荒谬结论 —— 而它明明存在。差别在于 comparison model 只允许比较，不许你把键当整数拆开看；cell-probe model 与 word-RAM model（字随机存取模型）允许读整字、做位运算，于是「值」本身成了可用的信息源。所以一条下界定理必须先声明模型，模型不声明，界就没有意义。这一点与 6.042J 里的公理体系是同构的：定理的强度由公理集合决定，换公理就换定理。
>
> 证明怎么走。第一步把数据结构问题翻译成 communication game（通信游戏）：一方知道集合 $S$、一方知道查询 $x$，双方只能通过「传送 cell 内容」对话；数据结构的每一次 cell probe（单元探测）就是一轮对话，只读 $t$ 个 cell 的结构等于一个 $t$ 轮协议。于是「$t$ 能有多小」变成「这个游戏要几轮」。
>
> 第二步用 round elimination（轮消除）把轮数夹住。给定一个 $t$ 轮协议，可以构造一个 $t-1$ 轮协议来解一个「稍微小一点」的同类问题，代价是规模按某个因子缩水。反复消除，问题一路缩到平凡，就没得玩了。缩水的形状与这一讲的递推式同形：每一轮把 universe（值域）的 bit length（比特长度）削掉一个常数比例，从 $\log u$ 位削到 1 位需要 $\Omega(\log\log u)$ 轮。换句话说，下界证明自己内部跑的是一个 $\log\log$ 递归 —— 上界与下界共用同一个几何骨架，只是方向相反：上界每层把问题缩到 $\sqrt{u}$，下界每层把对手能传的信息缩一个常数因子。
>
> 具体数字。$u = 2^{32}$、$n = 10^{6}$、空间 $n\log^{2}n$ 位：$\log\log u = 5$，任何结构至少 5 次探测；vEB 用 5 层递归刚好压到这个数，所以它「恰好达到天花板」不是巧合，是把深度用满。反过来说，任何声称 4 次探测解决 $2^{32}$ universe 的方案，必然在某处偷了模型外的东西：或者把 word 内 $w$ 位并行运算当免费却没有计入，或者预处理用了非多项式时间，或者悄悄假设了某种分布。
>
> 为什么朴素的 counting（计数）不够，这一点很关键。深度 $t$、每次读 $w$ 位的判定树最多有 $2^{wt}$ 个叶子；可能答案有 $u$ 个，于是 $2^{wt} \ge u$ 给出 $t \ge \log u / w$。代入 $u = 2^{32}$、$w = 32$ 得 $t \ge 1$，什么也没说。失败的原因是：计数只知道「总信息量」，不知道「信息在哪一方、要传几轮才传得过去」。round elimination（轮消除）补的正是这一层，所以它能给出 5，而计数只能给出 1 —— 这也是复杂度理论里反复出现的模式：信息量论证给出的是下限的下限，真正的界要靠交互结构。
>
> 与 6.042J 的对应。counting（计数）相当于 pigeonhole（鸽巢原理）的升级版，round elimination（轮消除）相当于 adversary（对抗法）的升级版 —— 不再问「有没有两只鸽子进同一个巢」，而是限制对手每一轮最多能传多少信息。历史坐标只放一句：Beame 与 Fich 在 1999 年先按 $n$ 与 $w$ 分别给出了静态 predecessor（前驱）问题的界，即 $\Omega(\log w/\log\log w)$ 与 $\Omega(\sqrt{\log n/\log\log n})$，Pătrașcu 与 Thorup 在 2006 至 2007 年把整条 time-space trade-off（时空权衡）曲线解完。区别在前者是固定参数下的单点界，后者是任意空间下的完整曲线；本讲引用的是 2007 年发表在 SODA 上的那篇 Randomization does not help searching predecessors，它与 2006 年 STOC 的时空版本结论一致，只是表述层面不同。
>
> 收束：下界的作用不是终结讨论，而是划出「换模型才有出路」。fusion tree（融合树）能到 $O(\log_w n)$，正是因为它假设 word（机器字）上的乘法与位运算都是 $O(1)$，把 comparison model（比较模型）换成了 word-RAM model（字随机存取模型）。同一个问题，模型一换，天花板就换。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**空间改进（Space Improvements）**</span>

我们可以从 $\Theta(u)$ 改进到 $O(n \log \log u)$。

- 只创建非空簇（nonempty clusters）
  - 若 $V.\mathrm{min}$ 变为 None，则释放（deallocate）$V$
- 把 $V.\mathrm{cluster}$ 存为**非空簇的哈希表**（hashtable of nonempty clusters）
- 每次插入可能创建一个新结构 $\Theta(\log \log u)$ 次（每次「空插入」）
  - 这确实可能发生（Vladimir Čunát 的例子）
- 把「指向结构的指针（pointer）及其哈希表条目」记账（charge）到该结构上

这给出 $O(n \log \log u)$ 空间（但是**随机化** randomized 的）。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> bit vector（位向量）方案的空间是 $\Theta(u)$，理由是「要回答 Successor 就得知道每个位置有没有元素」。这个理由站不住：$n$ 个元素时绝大多数位置是空的，空位置携带的信息量为零，但 $u - n$ 个零被一个个存了下来。lazy allocation（惰性分配）做的事就是把「零」不存。
>
> 具体做法是给每个结构加一张 hash table（哈希表）：键是簇编号，值是簇的 pointer（指针）。只有簇从空变非空的那一刻才创建它并插进哈希表，簇从非空变回空时删除并 deallocate（释放）。查找 $\mathrm{cluster}[i]$ 从「数组下标」退化成「一次哈希查找」，期望 $O(1)$。
>
> 为什么这里必须是 hash table（哈希表）而不是数组？因为数组的下标能力正是我们要扔掉的东西 —— 要按簇编号随机寻址，就得为每个可能的编号留一个槽位，那又回到 $\Theta(u)$。哈希表只对「存在的键」付费，这恰好对上「只对存在的簇付费」。代价是失去了最坏情况的确定性：一次哈希查找是期望 $O(1)$，不是保证 $O(1)$。
>
> 空间账用 charging（记账）算，不靠直觉。每创建一个结构，就记到「触发这次创建的那个元素」头上。一个元素从根到叶最多经过 $\log\log u$ 层，每层最多创建 1 个结构，所以每个元素最多背 $\log\log u$ 个结构的账，总数不超过 $n\log\log u$。这个界是紧的而不是保守估计：Čunát 给出的例子说明单次插入确实能一口气创建 $\log\log u$ 个新结构 —— 插入一个与现有元素完全不相邻的值时，沿途每一层的簇都是空的，全都要新建。
>
> 可算的对比。$u = 2^{32}$、$n = 10^{6} \approx 2^{20}$，稠密度 $n/u = 10^{6} / 4.29\times10^{9} \approx 2.33\times10^{-4}$，即平均每 $4295$ 个槽位才落一个元素，$99.98\%$ 的槽位是空的 —— 这就是惰性分配赢的地方，赢的幅度正比于空的比例。满配若每槽 4 字节，是 $2^{32}\times4 = 17\,179\,869\,184$ 字节 $= 16\ \mathrm{GiB}$；惰性方案有 $n\log\log u = 10^{6}\times5 = 5\times10^{6}$ 个结构，每个结构的 header（头部）里存 min、max、summary 指针与哈希桶指针，按 16 字节算是 $80\ \mathrm{MB}$。比值 $16384/80 = 204.8$ 倍。
>
> 跨课程连接。CSAPP 第九章 virtual memory（虚拟内存）的 demand paging（按需分页）是同一件事在 OS（操作系统）层的实现：48 位虚拟地址、4 KB 页，页表项全额驻留要 $2^{48}/2^{12} = 2^{36}$ 项，每项 8 字节是 $512\ \mathrm{GiB}$；而一个实际用了 100 MB 的进程只有 $100\ \mathrm{MB}/4\ \mathrm{KB} = 25\,600$ 个页，页表约 $200\ \mathrm{KB}$，比值 $2.68\times10^{6}$ 倍。区别：CSAPP 那套的「不存在」由 MMU（内存管理单元）识别，代价是 page fault（缺页）中断，微秒级；这里的「不存在」由哈希表识别，代价是一次哈希探测，纳秒级。两者共同的形状是：表的项数由地址空间决定，实际项数由工作量决定，中间那道差额就是省下来的空间。
>
> 与 18.065 与 Strang 的 sparse matrix（稀疏矩阵）连接。一个 $10^{6}\times10^{6}$ 的矩阵若只有 $10^{6}$ 个非零元，稠密存储要 $10^{12}$ 个元素，稀疏存储只要 $10^{6}$ 个三元组（行、列、值），比例 $10^{6}$。这和 $\Theta(u)$ 变到 $O(n\log\log u)$ 是同一个稀疏化动作：不为零付账。区别：稀疏矩阵的「零」是数据结构层面的静态约定，索引可以用 CSR（按行压缩）这种纯数组；vEB 的「空」随插入删除动态变化，所以索引用哈希表而不是数组，这也是它必须付随机化代价的根源。
>
> 代价与收束：哈希表只给 expected（期望）$O(1)$，所以这一版的空间与时间保证是 randomized（随机化）的。若对手知道哈希函数就能构造退化。工程上的解法是加 random seed（随机种子），使对手无法预知 —— 这正是「随机化换空间」的完整含义：用确定性的界换掉一部分确定性，换来一个数量级的空间。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**间接化（Indirection）**</span>

我们可以进一步把空间降到 $O(n)$。

- 用 BST 甚至数组存储 $n = O(\log \log u)$ 的 vEB 结构 ⇒ 基础情形（base case）下 $O(\log \log n)$ 时间
- 我们使用 $O(n / \log \log u)$ 个这样的结构（互不相交 disjoint）
- ⇒ $O\left(\frac{n}{\log \log u} \cdot \log \log u\right) = O(n)$ 空间（对小结构 small）
- 更大的结构「存储指向它们的指针」（larger structures "store" pointers to them）
- ⇒ $O\left(\frac{n}{\log \log u} \cdot \log \log u\right) = O(n)$ 空间（对大结构 large）
- 细节：拆分/合并小结构（Split/Merge small structures）

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> indirection（间接化）要消掉的是 $O(n\log\log u)$ 里那个乘法因子。$\log\log u$ 看着是常数（$u = 2^{32}$ 时是 5），但它是乘在 $n$ 上的，而且每个结构还有几个字的头部开销；$n$ 一大，这 5 倍就是实打实的 5 倍。上一节的空间方案里，辅助结构比数据本身大一个数量级，这在工程上等于没解决。目标很明确：让每个元素的存储代价从 $\log\log u$ 个 word（机器字）降到 1 个。
>
> 机制分两层看。底层是「换表示」：当 universe（值域）小到能装进一个 word（机器字）时，不再递归，直接用 bit vector（位向量）。$u = 2^{32}$ 时 word 是 32 位，一个 32 位整数的每一位就是一个槽位，所以它的 universe（值域）是 $2^{32}$；域内 Successor 化成一条 `ctz`（数尾部零）指令，1 个 cycle，不递归、不分配。这里发生的事值得单独说：递归从 $2^{32}$ 一路降到 $2^{5} = 32$，降到这一层之后，剩下的整个值域就是一个寄存器，于是「递归」这个词失效了 —— 剩下的工作由硬件一条指令完成。递归的终止条件不是「规模为 1」，而是「规模装得进一个 word（机器字）」。
>
> 顶层是「拼接」：所有更大的结构不各自承担 $\Theta(\log\log u)$ 个 word（机器字）的头部，而是用 $O(n/\log\log u)$ 个互不相交的小结构拼起来。小结构负责装元素，大结构只存指向小结构的 pointer（指针），于是「深度」被换成了「宽度」：不再每层开一整套结构，而是把 $n$ 个元素先分成若干组、每组独立处理。
>
> charging（记账）是这一步的全部内容，必须逐项对上。小结构数量 $n/\log\log u$，每个占 $\Theta(\log\log u)$ 个 word（一个 word 存位向量，其余存元素指针与 min、max、summary 这类头字段），相乘得 $O(n)$ 个 word —— 这正是正文那两行乘法 $O(n/\log\log u \cdot \log\log u) = O(n)$ 的意思。大结构只存 pointer（指针），指针总数不超过小结构数，也就是 $n/\log\log u$，同样落进 $O(n)$。两笔账都靠「每个小结构恰好装 $\Theta(\log\log u)$ 个元素」这一句成立，所以阈值不能随便挑。
>
> 阈值为什么必须落在 $\Theta(\log\log u)$，这是唯一需要夹的地方。阈值取小（比如 2），小结构数变成 $n/2$，每块那点常数头部摊不掉，退回 $O(n\log\log u)$；阈值取大（比如 $\log u$），块内元素变多、只能用 BST（二叉搜索树）或数组，块内代价涨到 $\log(\log\log u)$ 量级，开始侵蚀主项的 $\log\log u$ 收益。两边一夹，平衡点就在 $\log\log u$。这与 CSAPP 第六章 blocking（分块）里选块大小的逻辑一致：块太小则循环开销摊不掉，块太大则超出 cache（缓存）容量，最优在中间，由 $M$ 的几何量给出。
>
> 可算的对比。$u = 2^{32}$、$n = 10^{6}$、$\log\log u = 5$：小结构数量 $10^{6}/5 = 2\times10^{5}$ 个，每个 5 个 word（机器字）即 40 字节（64 位机器），合计 $2\times10^{5}\times40 = 8\ \mathrm{MB}$；元素数据本身按每个 8 字节算也是 8 MB，两者同阶 —— 这就是 $O(n)$ 的实义：辅助结构不比数据本身大一个量级以上。与上一节惰性分配的 $80\ \mathrm{MB}$ 相比降了 10 倍，与满配的 $16\ \mathrm{GiB}$ 相比降了约 2000 倍。
>
> 跨课程连接，这条是 vEB 最实用的一处。把递归 $\sqrt{u}$ 分割出来的这棵树，按递归顺序而不是按层序铺进内存，得到 vEB layout（van Emde Boas 布局），由 Harald Prokop 在 1999 年的硕士论文中提出，后来成为 cache-oblivious（缓存无关）模型的标准例子，同一条线上的工作还有 Frigo、Leiserson、Prokop、Ramachandran 在 FOCS 1999 的那篇 Cache-Oblivious Algorithms。结论：不论块大小 $B$ 是多少，任何一次根到叶搜索只产生 $O(\log_B n)$ 次 block transfer（块传输），对每个 $B$ 同时最优，而算法完全不知道 $B$ 的值。数字：$n = 10^{6}$ 个结点的完全二叉树，$B = 16$ 时 $\log_{16}10^{6} = 19.93/4 \approx 5$ 次传输；pointer chasing（指针追逐）的普通树每层一次 cache miss（缓存缺失），要 20 次。$B = 64$ 时降到 $\log_{64}10^{6} = 19.93/6 \approx 3.3$，即上限 4 次。区别：CSAPP 第六章的 cache（缓存）由硬件替你管、参数 $B$ 与 $M$ 固定且可见，调优靠把循环按块改写；cache-oblivious（缓存无关）要求算法对参数无感知，这在跨机器部署时是真优势 —— 同一份代码在 L1、L3 和磁盘上都自动最优。
>
> 但「理论最优布局」不等于「工程最优布局」。vEB layout（van Emde Boas 布局）只对静态树成立，动态插入要做打包与重排，常数很大；2001 年 Brodal、Fagerberg、Jacob 的实测报告显示，动态场景下 inorder（中序）布局反而更快，作者归因于重排算法更简单。这与 CSAPP 第六章「按行访问与按列访问差 2 倍」是同一类现象：模型里的最优与真实 cache（缓存）层次里的最优，差在常数与实现复杂度上。
>
> 收束：indirection（间接化）把一个乘法因子变成加法常数，条件是「小块足够便宜」。这个「阈值加两种结构」的模式后面会反复出现 —— 分块矩阵乘法、B-tree 的阶数选择、深度学习里的 tiling（分块），都是同一个决定的变体：把规模切成两段，小段用笨办法，大段用巧办法，边界由常数决定。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
