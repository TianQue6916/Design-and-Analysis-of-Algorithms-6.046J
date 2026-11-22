<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 9（Lecture 9）：数据结构增强（Augmentation）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲涵盖数据结构的**增强（augmentation）**，包括：
- 简单树增强（easy tree augmentation）
- 序统计树（order-statistics trees）
- 指搜索树（finger search trees），以及
- 范围树（range trees）

**核心思想**：修改「现成的」（off-the-shelf）常见数据结构，以存储（并更新）附加信息（additional information）。

🎥 *Devadas 在视频中[引入增强概念]*："We'll start out with a very simple one, which I call easy tree augmentation, which will include subtree size as a special case."（翻译：我们先从一个非常简单的开始，我称之为「简单树增强」，它会包含「子树大小」作为特例。）——本讲从最简单、最实用的增强（子树大小）出发，逐步走向更复杂的结构。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> augmentation（增广）是数据结构设计里最省力的通用技巧：不从头造新结构，而是给现成的结构在每个节点上多存一个字段，这些结构就是 AVL tree（AVL 树）、2-3 tree（2-3 树）、B tree（B 树），让这个字段携带 subtree（子树）级的信息，去支撑一个新查询。本讲的四个例子正好是四个难度台阶：easy tree augmentation（简单树增广）只存 subtree size（子树大小）；order-statistics tree（序统计树）用它支撑 rank 与 select；finger search tree（指搜索树）用它支撑「从任意节点出发的搜索」；range tree（范围树）用它支撑多维 orthogonal range query（正交范围查询）。
>
> 判据只有一条：新增的字段能不能由「节点自己的 key」加「孩子们的同名字段」在 $O(1)$ 时间里拼出来。能，增广几乎免费；不能，就得为它付 $O(n)$，或者干脆放弃这个查询。
>
> 把判据拆成因果链。设每个节点 $x$ 存 $x.f$，并要求 invariant（不变量）$x.f = F(\text{subtree}(x))$ 对所有 $x$ 成立。一次 insert（插入）或 delete（删除）只改变从修改点到 root（根）这条路径上各节点所管辖的点集；路径之外的节点，subtree（子树）一个元素都没动，所以不变量只可能在这条路径上被破坏。于是维护协议固定成两步：先在叶子附近把字段算对，再沿 parent pointer（父指针）逐级上行，在每个祖先处用 $x.f = g(x.\text{key}, c_1.f, \dots, c_m.f)$ 重算。总代价等于路径长度乘单节点重算成本；balanced tree（平衡树）的路径长度是 height（树高）$O(\lg n)$，所以只要 $g$ 是 $O(1)$，总代价退回 $O(\lg n)$，和没有增广时的插入同一量级。
>
> 反过来看失败的样子。假设想支持的查询是 rank（序数，即严格小于 $x$ 的键有多少个）。这个量依赖整棵树，而不只依赖 $x$ 的 subtree（子树）：插入一个比所有键都小的新键，所有旧键的 rank 全部加一。不存在任何 $g$ 能从孩子算对，因为孩子的 rank 本身也错了。要修只能扫全树，$O(n)$。这不是实现没写好，而是字段的数学性质不合格 —— 它不是 subtree（子树）的函数。
>
> 代入数量级。$n = 10^6$，树高 $20$。一次插入只维护 subtree size（子树大小），写 20 个字段；若维护 rank，写 $10^6$ 个字段，比值 $5 \times 10^4$。这与你 CSAPP 第 6 章 cache（缓存）那条规则互为镜像：只让被触碰的数据失效，别做全局重算。区别在于 cache（缓存）的一致性由硬件协议自动维护，而这里的失效传播由你自己写的那条上行循环维护。
>
> 三个设计问题其实可以一次问完：字段是什么、它怎么被维护、新查询怎么读它。前两个问题决定代价，第三个决定收益。四层例子的差别只在第三个问题的读法上：subtree size（子树大小）被 rank 与 select 顺着路径读；最小与最大键被 top-down search（自顶向下搜索）在孩子之间比较着读；第二坐标的排序表被 secondary tree（次树）里的又一轮区间分解读。字段本身几乎没变过，变的是读它的协议 —— 这也是为什么增广能反复复用同一套正确性论证。
>
> 同一形状在数据库里天天出现。B+ tree（B+ 树）的内部节点存「这棵子树有多少个键」这类计数，正是为了让计数查询与分页定位不必真去数叶子；区别是 B+ tree（B+ 树）按 page（页）分块、fanout（扇出）几百，树高只有三到四层，上行维护几乎不要钱；二叉平衡树 fanout（扇出）为 2、树高 20，同样的增广要多写五倍的节点。CLRS 第 14 章把这套协议正式化成四步：选字段、证明可维护、写出新操作、给出界 —— 本讲后面三个例子都是在同一协议上换字段，不再换协议。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**简单树增强（Easy Tree Augmentation）**</span>

这里的目标是在每个节点 $x$ 存储 $x.f$，它是该节点的一个函数，即 $f($ 以 $x$ 为根的子树 $)$。假设 $x.f$ 可以由 $x$、孩子（children）以及 children.f **在 $O(1)$ 时间内**计算（更新）。那么，修改一个节点集合 $S$ 需要 $O(\#$ 的祖先数$)$ 时间来更新 $x.f$，因为我们需要沿树上行到根（walk up the tree to the root）。$O(\lg n)$ 更新的两个例子是：
- **AVL 树**：旋转（rotating）两个节点后，先更新新的底层节点，再更新新的顶层节点
- **2-3 树**：分裂（splitting）一个节点后，更新两个新节点
- 两种情况都要继续沿树上行更新

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 简单树增广的核心约束是「$O(1)$ 可组合」：$f(\text{subtree})$ 必须能由「节点自身的值」加「孩子各自的 $f$」在常数时间拼出来。为什么这能保证总代价 $O(\lg n)$？因为一次修改只影响从修改点到 root（根）的 $O(\lg n)$ 个祖先，每个祖先用孩子的 $f$ 重算只要 $O(1)$。这个「沿祖先链传播更新」的模式是树增广的通用骨架。而 rotate（旋转）与 split（分裂）的处理细节决定了骨架会不会塌：顺序必须自底向上。
>
> 把约束写成一个式子：存在常数元函数 $g$，使得 $x.f = g(x, c_1.f, \dots, c_m.f)$ 对每个节点成立。它保证「一次修改的代价 = 被影响节点数乘每节点重算代价 = $O(\lg n) \times O(1) = O(\lg n)$」。若 $g$ 需要读到孙子的字段，重算一次就是 $O(\lg n)$，总代价退化到 $O(\lg^2 n)$；若 $g$ 需要全树信息，退化到 $O(n)$。所以「$O(1)$ 可组合」不是风格偏好，是复杂度的分界线。
>
> 更细的一层是更新顺序。rotate（旋转）改写两个节点的孩子集合，而这两个节点在新树里往往互为祖孙，于是必须按新树的自底向上顺序重算。给个能手算的例子。取一棵 AVL tree（AVL 树）的局部：$y$ 有左孩子 $x$、右孩子 $C$；$x$ 有左孩子 $A$、右孩子 $B$。设三棵子树的 size（大小）为 $|A| = 1$、$|B| = 2$、$|C| = 3$。旋转前先确认字段是对的：$x$ 的 size 为 $1+1+2 = 4$，$y$ 的 size 为 $1+4+3 = 8$。做一次 right rotation（右旋）：$x$ 升上来当这个局部的根，$x$ 的左孩子仍是 $A$、右孩子变成 $y$，$y$ 的左孩子变成 $B$、右孩子仍是 $C$。
>
> 正确顺序是先 $y$ 后 $x$：$y$ 的 size 变为 $1 + 2 + 3 = 6$，再算 $x$ 的 size 为 $1 + 1 + 6 = 8$。总量守恒还是 8，因为旋转只重排结构、不增减元素。若顺序反过来先算 $x$：$x$ 读到的是旧的 $y$ 的 size 即 8，得到 $1 + 1 + 8 = 10$ —— 多算的那 2 正是 $B$ 被数了两遍。更糟的是错误会顺着祖先链上传：$x$ 之上每个祖先都用这个 10 去算自己的字段，整条路径被打脏，而且不会自行恢复。
>
> 所以规则不是「两个节点都更新就行」，而是「按新结构的拓扑序更新」。2-3 tree（2-3 树）的 split（分裂）同理：一个节点裂成两个兄弟，它们的 children（孩子）没变，所以两者谁先算都行，但两者都必须先于 parent（父节点）。这是一条可以复用的检验：拿到任何一种平衡方案，先画出「哪些节点的孩子集合变了」，再问这张有向图有没有环 —— 没有环，从汇点开始算就是对的。
>
> 跨课程接两条。CSAPP 第 6 章：这条上行循环是一串数据相关的 load（加载），每一步的地址依赖上一步取回来的指针，所以 out-of-order（乱序）窗口几乎没法把它们重叠起来。若 $n = 10^6$、树高 20、每次 miss 到 DRAM 约 100 ns，一次插入的 pointer chase（指针追踪）就是 $20 \times 100 = 2000$ ns 量级 —— 这正是「$O(\lg n)$ 很便宜」这句话在真机上要打折的地方，也是 B+ tree（B+ 树）把 fanout（扇出）做大、把树高压到三四层的直接动机。6.042J 的结构归纳：不变量「每个节点字段正确」按 height（树高）归纳，base（基例）是叶子，归纳步就是上面这条 $g$ 的重算顺序 —— 顺序错了，归纳步不成立，证明塌掉。
>
> 还有一处值得单独记：旋转改变了哪些节点的子孙集合，不等于改变了哪些节点的 size（大小）。以右旋为例，$A$ 的祖先全变了（$x$ 变成 $y$ 的父亲，$A$ 的祖先链从 $A,x,y,\dots$ 变成 $A,x,y,\dots$ 的顺序不同但集合仍是 $x$ 与 $y$），但 $A$ 内部的节点一个都没动，所以 $A$ 的 size（大小）字段不需要重算。真正需要重算的只有 $x$ 与 $y$ 两个 —— 这条「只有孩子集合变了的节点才需要重算」是维护协议里的省力原则，也是判断一次旋转要写几个字段的唯一依据。
>
> 一句话收束：增广的骨架是「一个 $O(1)$ 的 $g$」加「一次自底向上的传播」，两者缺一个，$O(\lg n)$ 就守不住。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**序统计树（Order-Statistics Trees）（来自 6.006）**</span>

序统计树的目标是设计一个**抽象数据类型（ADT）接口**，支持以下操作：
- insert($x$)、delete($x$)、successor($x$)，
- **rank($x$)**：找 $x$ 在排序顺序中的索引，即 $< x$ 的元素个数，
- **select($i$)**：找 rank 为 $i$ 的元素。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> rank（序数）与 select（选择）是互为逆运算的一对查询：rank 从元素到序数（它在排序顺序里排第几，即严格小于它的元素个数），select 从序数到元素（第 $i$ 小的是谁）。严格的说法是 select(rank(x)) = x 对每个元素成立，rank(select(i)) = i 对每个 $1 \le i \le n$ 成立。这层互逆关系不只是措辞上的对称，它决定了两个操作共享同一份字段。
>
> 先把 ADT（抽象数据类型）与实现分开。接口承诺五个操作：insert、delete、successor、rank、select；接口不承诺底层是 AVL tree（AVL 树）、2-3 tree（2-3 树）还是 red-black tree（红黑树），也不承诺时间界。实现一侧才承诺实质：在 balanced tree（平衡树）的每个节点存 subtree size（子树大小），五个操作全部 $O(\lg n)$。这正是 CLRS 第 14 章区分「增广现成结构」与「重新设计结构」的地方：接口一个字符不改，只加一个字段。
>
> 为什么一个字段就够？因为 search（搜索）的每一步本来就在剪枝：比较完之后，被排除的那一边有多少个元素，恰恰就是 rank 需要累加的量。没有 size（大小）字段时你只能排除、不能记账；有了它，剪枝与记账合并成一步。这是「局部可组合」在查询一侧的对偶 —— 更新时沿路径上行重算，查询时沿路径下行累加，同一条路径、同一套字段，两个方向。
>
> 手算一遍。把键 $10,20,30,40,50,60,70,80$ 放进一棵 BST（二叉搜索树）：root（根）是 $40$，左子树根 $20$（孩子 $10$ 与 $30$），右子树根 $60$（孩子 $50$ 与 $70$，$70$ 的右孩子是 $80$）。size 字段：$10$、$30$、$50$、$80$ 各为 1；$20$ 为 3；$70$ 为 2；$60$ 为 4；$40$ 为 8。求 rank(70)：$70$ 的左子树为空，初值 $0+1 = 1$；$70$ 是 $60$ 的右孩子，上移时累加 $60$ 的左子树大小加一即 2，rank 变 3；$60$ 是 $40$ 的右孩子，累加 4，rank 变 7。核对排序位置，$70$ 确实第七。再求 select(5)：从 $40$ 起，$40$ 的 rank 是 $3+1 = 4$，$5>4$ 说明目标在右，令 $i = 5-4 = 1$ 并下到 $60$；$60$ 的 rank 是 $1+1 = 2$，$1<2$ 说明在左，下到 $50$；$50$ 的 rank 是 $0+1 = 1$，等于 $i$，返回 $50$。核对排序，第五个键正是 $50$。
>
> 注意 select 里「$i$ 减去当前 rank」的含义：减掉的正是「当前节点及其左子树一共有多少个元素比目标小」，于是子问题形状不变，只是 $i$ 被折算过去。循环版只需要一次下降，深度等于 height（树高）$O(\lg n)$，没有递归开销。
>
> 与讲次 2 的 median finding 对照：那里是静态一次性，给一个长度 $n$ 的数组找第 $k$ 小，$O(n)$ 就完事，因为数组构造完就不再变；序统计树是动态，结构会变、查询会重复，代价从前台搬到了预处理与维护。算一下取舍：$n = 10^6$，要做 $m = 10^4$ 次 select，静态做法合计 $10^{10}$ 次比较，序统计树建树约 $n \lg n = 2 \times 10^7$ 加查询 $m \lg n = 2 \times 10^5$，合计约 $2 \times 10^7$，差 500 倍。判据是「查询次数是否远大于 $\lg n$」，只要 $m$ 上去，树就赢。
>
> 补一条容易记错的约定：讲义脚注说若下标从 0 开始，rank 计算里的加一可以省掉。这不是技巧而是定义问题 —— 从 0 开始计时，rank 就等于「严格小于它的元素个数」，从 1 开始计时则要加一。写代码时先钉死一边，别混着用，否则 select 与 rank 会互为逆运算这件事在边界上直接失效，测试会在 $i = 1$ 和 $i = n$ 两处炸掉。
>
> 工程对照：关系数据库的 `LIMIT k OFFSET i` 分页在多数引擎里并不维护 subtree count（子树计数），深分页的代价是老老实实跳过前面 $i$ 行，于是越翻越慢；要它变快，就得像序统计树这样在索引节点里存计数。6.006 讲的是「怎么在树上做 rank 与 select」，6.046 讲的是「为什么只加一个 size（大小）字段就够，以及这个判断的一般形式」。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**序统计树（续）**</span>

我们可以用 AVL 树（或 2-3 树）上的简单树增强来实现上述 ADT，存储**子树大小（subtree size）**：$f($ 子树 $) = $ 其中的节点数。于是

```math
x.\mathrm{size} = 1 + \sum_{c \in x.\mathrm{children}} c.\mathrm{size}
```

作为对比，我们**不能**为每个节点存储 rank。因为 insert($-\infty$) 会改变**所有**节点的 rank。

**rank($x$)** 可以如下计算：
- 初始化 rank $= x.\mathrm{left}.\mathrm{size} + 1$（若下标从 0 开始则省略 $+1$ 项）
- 从 $x$ 沿树上行到根，每当做一次**左移**（$x \to x'$，即 $x$ 是 $x'$ 的右孩子、上移到 $x$ 左侧的祖先），rank $+$ $x'.\mathrm{left}.\mathrm{size} + 1$

**select($i$)** 可以如下实现：
- $x = $ root
- rank $= x.\mathrm{left}.\mathrm{size} + 1$
- 若 $i = $ rank：返回 $x$
- 若 $i < $ rank：$x = x.\mathrm{left}$
- 若 $i > $ rank：$x = x.\mathrm{right}$，$i -= $ rank

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 为什么不能给每个节点存 rank（序数）？因为 rank 是全局属性：插入一个 $-\infty$（最小的键）会让所有其他节点的 rank 全部加一 —— 一次插入改 $O(n)$ 个字段，增广直接退化。而 subtree size（子树大小）是局部属性，只依赖孩子的子树大小，一次插入只影响 $O(\lg n)$ 个祖先。这条「选对增广字段：局部可组合 对 全局需传播」的判断是本讲最重要的设计判断。
>
> 把两者的差别说透一点：rank 是绝对坐标，size（大小）是局部尺寸；而查询真正需要的从来只是坐标之差，差可以由路径上的局部尺寸拼出来。把 rank 存进节点，等于把「前缀计数」这个全局量复制 $n$ 份，任何改变全序的 insert（插入）都得把 $n$ 份全部改对。$n = 10^6$ 时是改 $10^6$ 个字段 对 改树高 20 个字段。
>
> 有个容易被忽略的细节：rotate（旋转）其实不改变任何节点的 rank。旋转只重排父子关系，中序次序一模一样，所以中序位置不动。这说明病根不在平衡维护，而在 insert（插入）与 delete（删除）本身。分辨这一点很重要：如果病根在旋转，那么 AVL tree（AVL 树）与 red-black tree（红黑树）都必然失败；而事实是这两种树上的 size（大小）增广完美工作，因为病根只在「元素集合被改变」的那一刻。
>
> 累加技巧值得逐句拆开。从 $x$ 向上走，只在「上移是从右孩子跳到父亲」时记账：此时父亲 $x'$ 的左子树全部小于 $x'$，而 $x'$ 本身也小于 $x$，所以要加上 $x'$ 的左子树大小再加一。若上移是从左孩子跳到父亲，父亲比 $x$ 大，一个都不该算，直接跳过。
>
> 用同一棵 BST（二叉搜索树）验算（键 $10$ 到 $80$，root（根）$40$，左子树根 $20$，右子树根 $60$）。rank(30)：左子树为空，初值 1；$30$ 是 $20$ 的右孩子，累加 $20$ 的左子树大小加一即 2，rank 变 3；$20$ 是 $40$ 的左孩子，跳过；到根结束，rank 为 3，核对确实是第三小。rank(50)：左子树为空，初值 1；$50$ 是 $60$ 的左孩子，跳过；$60$ 是 $40$ 的右孩子，累加 $40$ 的左子树大小加一即 4，rank 变 5，核对确实是第五小。两条路径都是三步，正好等于 height（树高）。
>
> 再往第一性原理走一层：这两个例子里被累加的量，全都是「某个祖先的左子树有多大」。一个节点的 rank 等于「所有把它包在右孩子一侧的祖先的左子树大小之和」再加一，每一份都是那个祖先的局部量。所以 rank 从来不需要被存储，它由 $O(\lg n)$ 个局部量在线拼出来。这就是「用可重算的局部量替代不可维护的全局量」的完整论证，也是选择增广字段时可用的判据：凡能被路径上的局部量拼出来的，就别存。
>
> 跨课程接两条。CSAPP 第 6 章：一个字段若必须全局更新，就等于让每次写都 invalidate（失效）整个 working set（工作集），与 cache（缓存）的 locality（局部性）设计正好相反；size（大小）字段只在路径上失效，路径是 $O(\lg n)$ 个 cache line（缓存行），硬件友好。6.042J 的偏序：rank 建立在 total order（全序）上，插入元素改变的不是任何两个元素的位置关系，而是整条序列的偏移 —— size（大小）之所以稳，是因为它只依赖 subtree（子树）的偏序结构，不依赖外部还有多少元素。
>
> 顺带一条同构的工程方案：binary indexed tree（树状数组）把前缀计数拆成 $O(\lg n)$ 个块的和，点更新只碰 $O(\lg n)$ 个块，从来不存每个元素的排名。它与 subtree size（子树大小）是同一个设计判断的两种实现 —— 都存可组合的局部量，把全局量留到查询时现拼。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**指搜索树（Finger Search Trees）**</span>

**指搜索树** [Brown and Tarjan, 1980] 的目标是：若我们已经有了节点 $y$，希望在 $O(\lg |\mathrm{rank}(y) - \mathrm{rank}(x)|)$ 时间内**从 $y$ 出发**搜索 $x$。直觉上，我们希望：若已经有一个**靠近 $x$** 的节点 $y$，那么对 $x$ 的搜索应该很快。

一个想法是使用**层链接的 2-3 树（level-linked 2-3 trees）**，其中每个节点有指向**同层下一个节点**和**同层上一个节点**的指针。

> <span style="color:#7f8c8d;">[说明] 原文脚注 1、2：若下标从 0 开始，则省略 rank 计算中的 $+1$ 项。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> finger search tree（指搜索树）重新审视一个被默认忽略的假设：搜索从哪里开始。普通 balanced tree（平衡树）的 search（搜索）永远从 root（根）出发，代价 $O(\lg n)$ 与目标位置无关 —— 哪怕你刚访问过目标旁边那个键，下一次还是从顶上下来那 20 层。复杂度 $O(\lg |\mathrm{rank}(y) - \mathrm{rank}(x)|)$ 的精妙在于：把「距离」从「到 root（根）的层数」换成了 rank 差，即两个键之间夹着多少个元素。这正是 finger（手指）的比喻：手指放在 $y$ 上，它附近的搜索都快。
>
> 这个替换为什么是本质的？因为在树里 rank 差小并不意味着路径短。两个相邻的键可能分别挂在 root（根）的左右两侧，路径长达 $2\lg n$；反过来，相距很远的两点也可能是兄弟。所以你不能指望「免费拿到」这个界 —— 必须在结构里显式加东西，也就是同层之间的指针，才能把 rank 距离换成物理步数。这是指搜索树存在的全部理由。
>
> 现实里的访问模式很少随机。编辑器光标一次移一格、数据库 cursor（游标）顺序扫、时间序列流式到达、1D 区间查询从 $a$ 一路往右推，这些场景都满足「下一次访问的键离上一次很近」。给数量级：$n = 10^9$、树高约 $30$，从 root（根）出发永远 30 次比较；finger（手指）放在离目标 100 个键处，$\lg 100 \approx 6.6$，取整 7 次，约 4 倍加速；距离 2 时 1 次比较，距离 1 时直接命中。
>
> 这个性质还有一个不显眼但更重要的后果：顺序遍历全部 $n$ 个元素的总代价是 $O(n)$ 而不是 $O(n \lg n)$，因为每一步的 rank 差都是 1，$\lg 1 = 0$，只付常数。这正是 B+ tree（B+ 树）叶子层链表的全部意义 —— 叶子连成链表之后，范围扫描是「一次下降加 $k$ 步横移」，不是 $k$ 次独立搜索。换句话说，$O(\lg n + k)$ 里的那个 $+k$ 而不是 $\times k$，就是靠这一条撑起来的。
>
> 实现细节。level links（层链接）是给每个节点加「同层前驱」与「同层后继」两个指针。为什么必须每层都连、只连叶子不行？因为只有叶子链表时，横移到目标附近后你仍要回到某一层再下降，跨层的「跳跃」能力就丢了；每层都有链接，才让「上移一层就把跨度乘 2 到 3」这条几何论证成立（见下一条注）。代价是每节点多两个指针，且 split（分裂）与 merge（合并）必须成对维护。
>
> 从 $y$ 出发的过程可以写成四行循环，值得逐行看它的不变量。取包含 $y$ 的叶子为 $v$；若 $x$ 落在 $v$ 的 min（最小值）与 max（最大值）之间，说明目标就在这棵子树内，转成自顶向下搜索并返回；若 $x$ 比 min 小，横移到同层前驱；若 $x$ 比 max 大，横移到同层后继；最后上移到 parent（父节点）。不变量是：每一轮之后，$v$ 管辖的键区间与 $x$ 的距离不再变大，而 $v$ 的 height（高度）加一。
>
> 历史坐标要写准。Brown 与 Robert Tarjan 在 1980 年的 SIAM Journal on Computing 第 9 卷第 3 期 594 到 614 页给出这族结构的分析；层链接的 (2,4)-tree（2-4 树）由 Huddleston 与 Mehlhorn 在 1982 年 Acta Informatica 第 17 卷 157 到 184 页给出；Wikipedia 的 finger search tree（指搜索树）条目说这类结构由 Guibas 等人在 B-tree（B 树）之上引入，最早版本给出 $O(\log d)$ 的 finger search（指搜索）。三条线索指向同一件事：把局部性写进结构，而不是指望硬件 cache（缓存）替你碰运气。
>
> 与你已经会的 splay tree（伸展树）比一下。splay tree（伸展树）也能给出摊还意义上的 finger（手指）界，但它靠访问后自我调整，最坏单次代价仍是 $O(n)$；层链接结构的界是 $O(\lg k)$ 的最坏情形。取舍是：splay tree（伸展树）不需要额外指针、实现短、常数小，但要接受单次退化；而且它只在「访问序列本身有局部性」时才快，无法承诺给定两个键的距离界。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**指搜索树（续）**</span>

层链接（level links）可以在分裂（split）与合并（merge）时维护。

我们在**叶子**中存储所有键。非叶子节点不存储键；相反，它们通过简单树增强存储子树的**最小与最大键**。

那么，原本的**自顶向下 search($x$)**（不给 $y$）可以如下实现：
- 从根开始，看每个孩子 $c_i$ 的 min 与 max
- 若 $c_i.\mathrm{min} \le x \le c_i.\mathrm{max}$，下到 $c_i$
- 若 $c_i.\mathrm{max} \le x \le c_{i+1}.\mathrm{min}$，返回 $c_i.\mathrm{max}$（作为前驱 predecessor）或 $c_{i+1}.\mathrm{min}$（作为后继 successor）

**从 $y$ 出发的 search($x$)** 可以如下实现。把 $v$ 初始化为包含 $y$ 的叶子节点（给定），然后循环：
- 若 $v.\mathrm{min} \le x \le v.\mathrm{max}$（说明 $x$ 在以 $v$ 为根的子树内），对 $x$ 做自顶向下搜索并返回
- 否则若 $x < v.\mathrm{min}$：$v = v.\mathrm{prev}$（本层的前一个节点）
- 否则若 $x > v.\mathrm{max}$：$v = v.\mathrm{next}$（本层的后一个节点）
- $v = v.\mathrm{parent}$

**分析（Analysis）**：我们从叶子层出发，每次迭代上移 1 层。在第 $i$ 步，高度 $i$ 的层链接大约跳过 $c^i$ 个键（rank），其中 $c \in [2, 3]$。因此，若 $|\mathrm{rank}(y) - \mathrm{rank}(x)| = k$，我们将在 $O(\lg k)$ 步内到达包含 $x$ 的子树，随后的自顶向下搜索也是 $O(\lg k)$。

![原页 p03 图](data:image/png;base64,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)

> <span style="color:#7f8c8d;">[原图说明] 原页附图：层链接 2-3 树在 split（分裂出 new）、merge（合并）、delete（删除）时的层链接维护示意。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 从 $y$ 出发搜索的正确性依赖一个几何事实：从叶子向上爬时，每层跨度按因子 $c \in [2,3]$ 增长。第 $i$ 层的层链接大约跳过 $c^i$ 个键 —— 因为 2-3 tree（2-3 树）的每个内部节点有 2 个或 3 个孩子，向上走一层，覆盖的叶子范围就扩大 2 到 3 倍。若目标 $x$ 与 $y$ 相距 $k$ 个 rank，则需要向上爬 $\log_c k = O(\lg k)$ 层（因为 $c^{h} \ge k$ 时该层的范围已覆盖 $x$）。这个「每层跨度指数增长，层数对数于距离」的论证，与 binary search（二分查找）、skip list（跳跃表）的「对数于范围」如出一辙 —— 几何级数在这里又当了主角。
>
> 先把高度与跨度的对应关系钉死。2-3 tree（2-3 树）的每个内部节点有 2 个或 3 个孩子，所以 height（高度）为 $i$ 的节点下面至少有 $2^i$ 个叶子、至多有 $3^i$ 个。反过来从叶子往上数：每上一层，当前节点管辖的键数至少乘 2、至多乘 3。因此「第 $i$ 层的层链接跨过约 $c^i$ 个键」不是估计，是节点的度数下界直接给出的。
>
> 搜索过程每一步只做两件事：需要时横移到同层相邻节点，然后上移到 parent（父节点）。关键观察是每层只需要 $O(1)$ 次横移 —— 第 $i$ 层横移一次就跨过约 $c^i$ 个键，于是从第 $0$ 层到第 $h-1$ 层各横移一次，累计跨过的距离是几何级数和 $1 + c + c^2 + \cdots + c^{h-1} = (c^{h}-1)/(c-1)$。只要这个和达到 $k$，就说明 $h = \log_c k$ 层足够覆盖整段距离。总步数等于 $h$ 次上移加至多 $h$ 次横移，即 $2h = O(\log_c k) = O(\lg k)$。
>
> 代入两组数，把「几何」这两个字落到具体整数上。取 $k = 1000$。若 $c = 2$：$1+2+4+\cdots+2^{9} = 1023 \ge 1000$，十项够，$h = 10$，总步数约 20；而 $\log_2 1000 \approx 9.97$，两者一致。若 $c = 3$：$1+3+9+27+81+243+729 = 1093 \ge 1000$，只有 7 项，$h = 7$，总步数约 14；而 $\log_3 1000 \approx 6.29$，向上取整正是 7。
>
> 这 3 层的差意味着什么？单看一次 finger search（指搜索），省 6 次访问不算什么。但树高本身也随 fanout（扇出）塌缩：$n = 10^6$ 的二叉情形 $h = 20$，三叉情形 $3^{13} \approx 1.6 \times 10^6$，所以 $h = 13$。这就是 B-tree（B 树）把 fanout（扇出）做到几百时树高只有三四层的原因 —— 扇出进到指数的位置，换来的层数下降是叠加的，而每次溅出的 cache miss（缓存未命中）代价固定，层数少一半就是访问时间少一半。
>
> 一条防止理解错的补充：那个「每层 $O(1)$ 次横移」不是说算法在每层只尝试一次，而是说最优策略会先上移、只在跨度与剩余距离可比时才横移。若在同一层连续横移很多次，说明上移得太晚 —— 上移一次把跨度乘 $c$ 是免费的（只花 1 步），比横移划算。这条「先升维再比较」的直觉，与 skip list（跳跃表）里「先在高层的链表里跳、跳不动了再降一层」完全同构。
>
> 跨课程接三条。CLRS 的 binary search（二分查找）：每比较一次把候选区间减半，区间长度是 $n/2^{t}$，所以次数是 $\lg n$；这里每上一层把跨度乘 $c$，所以层数是 $\log_c k$ —— 同一个「几何衰减到对数」的模板，只是「除以 2」换成了「乘 $c$」。6.042J 的 geometric series（几何级数）求和：$\sum_{i<h} c^{i} = (c^{h}-1)/(c-1)$，平时用来算复利，这里用来算「爬几层能横跨多远」，推导一模一样。B+ tree（B+ 树）的范围扫描：叶子链表让「向右一格」是 $O(1)$，到达起点是 $O(\lg n)$，所以 $O(\lg n + k)$ —— 与本讲的 $c^{i}$ 论证是同一个几何事实在数据库里的落地。
>
> 收束：指搜索的快，本质是把「树高是叶子数的对数」这条性质用在了错误的坐标上（按到 root（根）的距离数层），而正确的坐标是 rank 差。换坐标之后，几何级数自动给出对数界，一行求和就够。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**正交范围搜索与范围树（Orthogonal Range Searching and Range Trees）**</span>

假设 $d$ 维空间中有 $n$ 个点。我们希望有一个数据结构支持这些点上的**范围查询（range query）**：找出给定**轴对齐盒子（axis-aligned box）**内的所有点。轴对齐盒子在 1D 是区间（interval）、在 2D 是矩形（rectangle）、在 3D 是立方体（cube）。

更精确地，每个点 $x_i$（$i$ 从 1 到 $n$）是 $d$ 维向量 $x_i = (x_{i1}, x_{i2}, \ldots, x_{id})$。**Range-query($a$, $b$)** 接受两个点 $a = (a_1, a_2, \ldots, a_d)$ 与 $b = (b_1, b_2, \ldots, b_d)$，应返回一个索引集合 $\{i \mid \forall j,\ a_j \le x_{ij} \le b_j\}$。

**1D 情形（1D case）**：我们从 1D 点的简单情形开始，即所有 $x_i$ 与 $a$、$b$ 都是标量。那么，我们可以直接用**排序数组**（sorted array）。要执行 Range-query($a$, $b$)，只需分别对 $a$ 和 $b$ 做**两次二分搜索**，然后返回中间的所有点（设有 $k$ 个）。复杂度为 $O(\lg n + k)$。

排序数组对插入与删除效率低。对于支持范围查询的**动态**数据结构，我们可以用上一节的**指搜索树**。指搜索树支持高效的插入与删除。要执行 Range-query($a$, $b$)，先搜索 $a$，然后**不断做「向右 1 位」的指搜索**，直到超过 $b$。每次「向右 1 位」的指搜索需 $O(1)$，所以总复杂度也是 $O(\lg n + k)$。

然而，上述两种方法**都不能推广到高维**。这就是我们现在引入范围树的原因。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 1D 情形看起来已被彻底解决：sorted array（排序数组）两次 binary search（二分查找）加顺序输出是 $O(\lg n + k)$；finger search tree（指搜索树）同样 $O(\lg n + k)$，还能动态更新。真正值得记的不是这两个方案，而是它们暴露的三条判据。
>
> 第一条：复杂度里的 $k$ 不可消除。任何算法都要把答案一个个写出去，写 $k$ 个至少 $k$ 步，所以 $\Omega(k)$ 是与算法无关的下界。这类复杂度叫 output-sensitive（输出敏感）—— 界里带输出规模，而不只带输入规模。工程含义很实在：$n = 10^6$、$k = 100$ 时，$O(\lg n + k)$ 是 $20 + 100 = 120$ 步，而「扫全数组再筛」是 $10^6$ 步，差约 8000 倍；但若 $k$ 本身接近 $n$（查询覆盖九成的点），两种做法都在 $10^6$ 量级，索引几乎白建。所以建不建结构，取决于你对 $k$ 的预期分布，而不是取决于 $n$ 有多大。
>
> 第二条：静态与动态的取舍是「预处理代价能不能摊销」。sorted array（排序数组）一次 $O(n \lg n)$ 排好，之后每次查询 $O(\lg n + k)$，但不能插入删除；finger search tree（指搜索树）每次更新 $O(\lg n)$。判据是更新次数与查询次数的比例：若只查一次，排序都不该做，直接扫 $O(n)$ 更快；若查 $10^4$ 次，排序的 $O(n \lg n)$ 摊到每次可以忽略。这条判据在数据库里就是「建索引还是不建索引」，而 DBA 的经验法则来自同一个式子。
>
> 第三条，也是让本讲转向 range tree（范围树）的那条：这两种方法都不可能推广到高维。原因不在工程实现，而在数学结构。1D 的排序之所以有效，是因为实数上的 $a \le x \le b$ 是一个 interval（区间），而 interval（区间）在任何 total order（全序）下都是连续的一段 —— 于是「二分定位加连续扫描」成立。2D 的点集没有这样的全序可用。
>
> 给一个能自己验证的构造。取三个点 $(1,1)$、$(2,3)$、$(3,1)$，查询盒子是 $1 \le x \le 3$ 且 $1 \le y \le 2$，答案应是 $(1,1)$ 与 $(3,1)$，不含 $(2,3)$。若按 $x$ 排序，序列是 $(1,1),(2,3),(3,1)$，答案落在第 1 个和第 3 个位置，中间夹着不该要的 $(2,3)$ —— 不连续。换任何别的排序键也一样：只要查询盒子可以任意选，就能造出「答案被打散」的例子。这就是高维与 1D 的本质差别 —— $d$ 维的「在盒子里」是 $d$ 个条件的 conjunction（合取），每个条件是某个坐标轴上的一段，但没有单一排序能让 $d$ 段同时连续。
>
> 用你已经学过的语言重说一遍：1D 的键集上有 total order（全序），所以「比 $a$ 小」「比 $b$ 大」「两者之间」三种谓词划出的都是前后缀；2D 的点集上只有 partial order（偏序，即逐坐标的积序），「在盒子里」是这个偏序上的一个区间，而偏序的区间不必是有序序列里的一段。这个观察直接决定下一步：既然没有单一坐标能一次搞定，就把查询按坐标拆成若干次 1D 查询的组合 —— 这就是 range tree（范围树）的全部动机。
>
> 顺带划清一个容易混的界：range query（范围查询）与 interval tree（区间树）解决的不是同一个问题。前者是「给定点集，找落在盒子里的点」；后者是「给定区间集合，找与给定点或给定区间相交的那些区间」。本讲处理的是前者。两者都靠增广字段剪枝（区间树在内部节点存子树内所有区间的最大右端点），但被剪的对象一个是点、一个是区间。
>
> 收束：$O(\lg n + k)$ 这个形状是高维结构的模板 —— 前面的 $\lg n$ 是定位代价，后面的 $k$ 是报告代价；后面所有改进只动前面那一项，$k$ 永远不动。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**1D 范围树（1D range trees）**</span>

1D 范围树是一棵**完全二叉搜索树**（complete binary search tree；动态情形用 AVL 树）。

**Range-query($a$, $b$)** 可以如下实现：
- search($a$)
- search($b$)
- 找到 $a$ 与 $b$ 的**最近公共祖先（least common ancestor, LCA）** $v_{\mathrm{split}}$
- 返回「中间」（in between）的节点与子树。有 $O(\lg n)$ 个节点与 $O(\lg n)$ 棵子树「在中间」。

**分析（Analysis）**：$O(\lg n)$ 隐式表示答案；$O(\lg n + k)$ 输出全部 $k$ 个答案；$O(\lg n)$ 通过子树大小增强报告 $k$。

> <span style="color:#7f8c8d;">[图 1 说明] 原页 Figure 1：1D 范围树。Range-query(a, b) 返回所有空心节点与阴影子树。图片来自 Wikipedia http://en.wikipedia.org/wiki/Range_tree。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 1D range tree（1D 范围树）的技巧是把一个区间表示成 $O(\lg n)$ 棵不相交的 subtree（子树）。几何图像是两条 root-to-leaf（根到叶）路径在某个节点分岔，分岔点之后一左一右，各自沿途贡献一批整棵子树。这个「用两族子树表示连续区间」的分解，是把连续对象换成 $O(\lg n)$ 个离散片段的经典手法 —— 它让「报告 $k$ 个元素」不必逐个扫描，而是先隐式定位几棵子树、再延迟展开。
>
> 机制逐句拆。先用 $a$ 做 search（搜索），再用 $b$ 做 search（搜索）。两条路径共享一段前缀，然后在 $v_{\mathrm{split}}$ 处第一次分开，这个点就是 least common ancestor（最近公共祖先）。分开之后性质变好：$v_{\mathrm{split}}$ 之下，$a$ 的路径每一步都往左（因为 $a$ 比路径上的键小），$b$ 的路径每一步都往右。
>
> 于是 $a$ 路径上每个节点 $u$ 的右子树整棵落在 $[a,b]$ 里 —— $u$ 的键与它左子树都小于 $a$，而它右子树里的键都大于 $u$ 且不超过 $b$（最后半句要求 $u$ 在 $v_{\mathrm{split}}$ 之下，此时 $u$ 不超过 $b$）。对称地，$b$ 路径上每个节点的左子树整棵落在区间里。把这两族子树，连同 $v_{\mathrm{split}}$ 与两个端点节点并起来，就得到全部答案，且这些集合两两不交。
>
> 数量界不需要更细的工具：两条路径各长 $O(\lg n)$，每层至多贡献一棵子树加一个节点，所以族的大小是 $O(\lg n)$。「每层至多两棵」就是「$O(\lg n)$ 个 canonical subtree（规范子树）」这条界的全部内容。这也解释了前面分析里那句「有 $O(\lg n)$ 个节点与 $O(\lg n)$ 棵子树在中间」。
>
> 手算一遍，用键 $1$ 到 $15$ 的完全 BST（二叉搜索树）：root（根）是 $8$；左边 $4$（孩子 $2$ 与 $6$，$2$ 的孩子 $1$ 与 $3$，$6$ 的孩子 $5$ 与 $7$）；右边 $12$（孩子 $10$ 与 $14$，$10$ 的孩子 $9$ 与 $11$，$14$ 的孩子 $13$ 与 $15$）。查询 $[4,11]$。搜 $4$ 的路径是 $8 \to 4$；搜 $11$ 的路径是 $8 \to 12 \to 10 \to 11$；第一处分岔在 $8$，所以 $v_{\mathrm{split}} = 8$。
>
> 收片段：$a$ 一侧，$4$ 的右子树是 $6$，含 $5,6,7$，整棵要；$b$ 一侧，$12$ 的左子树是 $10$，含 $9,10,11$，整棵要；再加上端点 $4$ 与 $8$。答案等于 $\{4\}$ 并 $\{5,6,7\}$ 并 $\{8\}$ 并 $\{9,10,11\}$，共 8 个点，与直接枚举 $4$ 到 $11$ 完全一致。分解只用了 4 个片段，而不是 8 个点 —— 片段数对数于 $n$，点数才能是 $k$。
>
> 这里 size（大小）增广的作用就看出来了：要报这四段各有多少元素，不必下去数，直接读四个根上的 size（大小）字段，$1+3+1+3 = 8$，$O(1)$ 就把 $k$ 报出来。同一条查询因此有三种答复：$O(\lg n)$ 只回一个数（区间里有多少点）、$O(\lg n + k)$ 回全部点、以及 $O(\lg n)$ 下到那几棵子树的根并「延迟展开」。第三种是范围树能嵌进高维的接口 —— 上层只关心「哪些子树」，不关心里面的点。
>
> 跨课程接三条。CLRS 的 BST（二叉搜索树）章节：这里的 $v_{\mathrm{split}}$ 就是 CLRS 12.2 论证 successor（后继）与 predecessor（前驱）时用的那个分岔点，同一个几何，只是那次只用它找相邻元素。6.042J 的树归纳：命题「任何区间分解成每层至多两棵子树」按 height（树高）归纳，归纳步就是「分岔点之上两路径共享、之下一左一右」，比写成循环不变量干净得多。数据库对照最能显出差别：B+ tree（B+ 树）的范围扫描是「下降到起点叶子，再沿叶子链表逐页走」，代价 $O(\lg n + k)$ 但常数是 $k$ 次 page（页）访问；范围树是「只访问 $O(\lg n)$ 个内部节点，整棵子树不进去」，常数小得多，而且只问计数时是 $O(\lg n)$ —— B+ tree（B+ 树）做不到，因为它的内部节点只存键、不存 size（大小）。
>
> 收束：这个技巧之所以是高维结构的地基，是因为它把连续区间换成了 $O(\lg n)$ 个离散片段，而离散片段可以逐维再分解，维数只是把这段逻辑套一次。

---

<span style="color:#c0392b">**2D 范围树（2D range trees）**</span>

2D 范围树由一棵**主 1D 范围树**与许多**次 1D 范围树**（secondary 1D range trees）组成。主范围树存储所有点，按**第一坐标**为键。主范围树中的每个节点 $v$ 把 $v$ 子树中的所有点存储在一棵**按第二坐标**为键的次范围树中。

**Range-query($a$, $b$)** 可以如下实现：
- 用主范围树找出所有「第一坐标在正确范围内」的点。只隐式表示答案，所以这需要 $O(\lg n)$。
- 对 $O(\lg n)$ 个**节点**，手动检查它们的第二坐标是否在正确范围内。
- 对 $O(\lg n)$ 棵**子树**，用它们的次范围树找出所有「第二坐标在正确范围内」的点。

**分析（Analysis）**：$O(\lg^2 n)$ 隐式表示答案，因为我们要在次范围树中找到 $O(\lg^2 n)$ 个节点与子树。$O(\lg^2 n + k)$ 输出全部 $k$ 个答案。$O(\lg^2 n)$ 通过子树大小增强报告 $k$。

**空间复杂度为 $O(n \lg n)$**：主子树是 $O(n)$。每个点在次子树中被复制至多 $O(\lg n)$ 次，每棵祖先一次。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 2D range tree（2D 范围树）是典型的时间空间交换：主树在 $x$ 维做区间分解（$O(\lg n)$ 棵子树），对每棵被选中的子树，再在 $y$ 维用它的 secondary tree（次树）做一次区间分解（又是 $O(\lg n)$）—— 每层分解乘一次 $\lg n$，所以 2D 查询是 $\lg^2 n$。空间 $O(n \lg n)$ 的来源同样直白：每个点被复制到它所有祖先的次树里，祖先链长 $O(\lg n)$，所以总复制 $O(n \lg n)$。
>
> 结构说清楚：主树是一棵按第一坐标排序的 1D range tree（1D 范围树）；主树的每个节点 $v$ 额外挂一棵 secondary tree（次树），内容是 $v$ 的 subtree（子树）里所有点按第二坐标排序后的序列（静态用 sorted array，排序数组；动态用 1D range tree，1D 范围树）。注意次树的内容是「$v$ 管辖的全部点」，不是只有 $v$ 一个点 —— 这个包含关系是空间代价的唯一来源。
>
> 查询分三步，对应三种不同处理，别混。第一步，在主树里定位 $x$ 区间，得到 $O(\lg n)$ 个片段，其中一部分是单个节点、一部分是整棵子树。第二步，对单个节点（只有 $O(\lg n)$ 个），直接比较它的第二坐标是否落在 $[a_2,b_2]$，$O(1)$ 一个。第三步，对每棵子树（也只有 $O(\lg n)$ 棵），在它的次树里做一次 1D 查询，$O(\lg n)$ 一次。总计 $O(\lg n) \times O(\lg n) = O(\lg^2 n)$。
>
> 把数字代进去看规模。$n = 10^6$、$\lg n = 20$：复制总量 $n \lg n = 2 \times 10^7$ 个点引用，若每个引用 4 字节，约 80 MB；主树本身按每节点 40 字节算是 40 MB，合计约 120 MB，三倍。再加一维到 3D：$n \lg^2 n = 4 \times 10^8$ 个引用，约 1.6 GB。这就是为什么 $d \ge 3$ 的 range tree（范围树）在工程里几乎没人用，也是数据库宁可用 k-d tree（k-d 树）或 R-tree（R 树）这类空间线性、界不那么漂亮的结构的原因。
>
> 收益也要算清楚。$n = 10^6$ 时 $\lg^2 n = 400$ 次基本操作，与 1D 的 20 次比慢 20 倍，但比「扫全部 $10^6$ 个点」快 2500 倍。所以范围树的价值不是常数好，而是把一个不可行的界变成可行：$O(\lg^2 n + k)$ 在 $k = 10^3$ 时是 $400 + 1000 = 1400$ 步，线性扫描是 $10^6$ 步。
>
> 维护代价常被忽略。插入一个点，要沿主树祖先链在 $O(\lg n)$ 棵次树里各插一次，每次 $O(\lg n)$，所以更新是 $O(\lg^2 n)$。这与注 2 的骨架完全一致：总代价等于被影响节点数乘单点重算代价，只是这里「被影响的节点」是 $O(\lg n)$ 棵次树，而每棵次树的更新本身又是一次 $O(\lg n)$ 的路径传播 —— 增广套增广，层数相乘。把它推广一步就得到 $d$ 维：更新 $O(\lg^d n)$，空间 $O(n \lg^{d-1} n)$。
>
> 还有一个常被低估的细节：主树的节点本身也要存 size（大小），而且这个 size（大小）是「subtree（子树）里有多少个点」，与次树里的 size（大小）是两套独立的字段。前者回答「这一片有多少点」，后者回答「这一片的第二坐标落在区间里的有多少点」。两者都用注 2 的同一套上行维护，但一个在主树路径上传播、一个在每棵次树内部传播 —— 分清这两层，是读懂 $O(\lg^2 n)$ 的前提。
>
> 「按维逐层处理」这个模式在 computational geometry（计算几何）里反复出现，但要留意它和分治的关系：主树在 $x$ 上分治、次树在 $y$ 上分治，两层分治叠起来正是那个 $S_d(n) \le 2S_d(n/2) + S_{d-1}(n)$ 的递推 —— 空间的多出来一个 $\lg$ 就是分治层数的代价，与你在线性代数里做矩阵分块时「每层分块多一次扫描」是同一件事。
>
> 跨课程接一条最实用的。CSAPP 第 6 章：$n \lg n$ 的复制不只是内存数字，还是 cache（缓存）行为。次树内部按 $y$ 排好，单棵次树上的二分是顺序访问；但查询要在 $O(\lg n)$ 棵不同次树之间跳，每跳一次都要新的 cache line（缓存行），20 次跳乘以每行 64 字节，约 1.25 KB 的流量，约等于 20 次潜在的 DRAM 访问。对比 1D 二分：也是 20 次，但都在一条连续数组上，硬件 prefetcher（预取器）能部分掩盖。所以真机上 $\lg^2 n$ 的常数惩罚比 400 比 20 这个比值还要大一些。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b">**$d$ 维范围树（d-D range trees）**</span>

直接**递归**：主 1D 范围树 → 次 1D 范围树 → 三次 1D 范围树 → $\cdots$

**Range-query 复杂度**：$O(\lg^d n + k)$。
**空间复杂度**：$O(n \lg^{d-1} n)$。

**Chazelle 的改进结果**（见 6.851）：Range-query 复杂度 $O(\lg^{d-1} n + k)$，空间复杂度 $O\left(n \left(\frac{\lg n}{\lg \lg n}\right)^{d-1}\right)$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> $d$ 维的推广是机械递归：主树按第一坐标，每个节点的次树按第二坐标，次树的每个节点再挂三次树，直到第 $d$ 坐标。两件事随之变化，而它们都可以用递推式算出来，不必背结论。
>
> 空间。设 $S_d(n)$ 是 $d$ 维范围树的空间。主树左右两半各含约 $n/2$ 个点，因此是 $2 S_d(n/2)$ 项；但每个主树节点还要挂一棵 $(d-1)$ 维的范围树，内容是该节点管辖的全部点，逐层摊下来是 $S_{d-1}(n)$ 项。于是 $S_d(n) \le 2 S_d(n/2) + S_{d-1}(n)$，边界 $S_1(n) = O(n)$。按主定理逐层解：每一层把 $S_{d-1}(n)$ 复制到 $2^{j}$ 个规模 $n/2^{j}$ 的子问题里，累加得 $O(n \lg^{d-1} n)$。$d = 2$ 时正是上一条注算过的 $n \lg n$。
>
> 时间。$d$ 维查询要在第一维定位出 $O(\lg n)$ 个片段，而每个片段（只要它是子树）都要在它的次树里做一次 $(d-1)$ 维查询，所以 $Q_d(n) = O(\lg n) \cdot Q_{d-1}(n)$，边界 $Q_1(n) = O(\lg n)$，解得 $Q_d(n) = O(\lg^{d} n + k)$。注意这里乘的不是「加」—— 这是范围树时间界看起来比空间界差一档的原因：空间里每个点只被复制，查询里每一步都要对整批片段逐个下去。
>
> 代数看规模。$n = 10^6$、$\lg n = 20$：$d = 2$ 是 $400$，$d = 3$ 是 $8000$，$d = 4$ 是 $160000$。而每次查询最差也要和「扫 $10^6$ 个点」比：$d = 4$ 时只快 6 倍，空间却要 $n \lg^{3} n = 8 \times 10^9$ 个引用，按每引用 4 字节是 32 GB。结论很清楚：$d$ 从 2 到 3 还在工程范围内，到 4 就不值了。这条曲线就是高维范围查询的实用天花板。
>
> 于是所有改进都在做同一件事：省掉一个 $\lg$ 因子。fractional cascading（分数级联）是最漂亮的一招，机制可以三句话说完。第一，主树节点 $v$ 的次树列表与它孩子 $u$ 的次树列表共享绝大多数元素 —— $u$ 的点集是 $v$ 点集的子集，按同一个第二坐标排序后，$u$ 的列表几乎是 $v$ 列表的一个 subsequence（子序列）。第二，既然共享，就不必在 $u$ 的列表里重新二分：在 $v$ 的列表里二分一次，然后给 $v$ 列表的每个元素存一个 pointer（指针），指向它在 $u$ 列表里的位置。第三，从 $v$ 里得到的位置出发，取相邻两项在 $u$ 里的指针，就 $O(1)$ 定位到 $u$ 里的正确位置。
>
> 代价与收益。一次真正的二分 $O(\lg n)$，之后每下降一层只花 $O(1)$，于是「$d$ 个维度上各自独立的 $d$ 次二分」被压成「一次二分加 $d-1$ 次指针跟随」，查询界从 $O(\lg^{d} n + k)$ 降到 $O(\lg^{d-1} n + k)$。代入数字：$n = 10^6$ 时每维省 20 倍 —— 2D 从 400 到 20，3D 从 8000 到 400，4D 从 160000 到 8000。空间代价是多存那批 pointer（指针），按层数乘一个常数，渐进界不变。
>
> 历史与归属要写准，这里讲义有一个常见的归并误差。range tree（范围树）由 Jon Louis Bentley 在 1979 年给出，Lueker 在 1978 年、Lee 与 Wong 在 1980 年、Willard 各自独立得到同类结构。fractional cascading（分数级联）出自 Chazelle 与 Guibas 在 1986 年的两篇论文。讲义把改进记作「Chazelle 的 1990 年结果」，而 1990 年 Chazelle 在 Journal of the ACM 第 37 卷上的两篇（第 2 期 200 到 212 页的 reporting case，第 3 期 439 到 463 页的 arithmetic model）是 lower bound（下界）一侧的工作。三件事必须分开：结构是谁发明的、加速技术是谁给的、下界是谁证的 —— 混成一句就会记错十三年。
>
> 顺带说清 space 界 $O(n(\lg n/\lg\lg n)^{d-1})$ 的来路：它属于 compressed range tree（压缩范围树）那条线，思路是「把次树之间的冗余重复真正压掉」，而不是像分数级联那样只加指针。它与下界的形状几乎吻合，所以被引用得多；但工程上实现复杂、常数大，课程里只当结论用。
>
> 跨课程接两条。6.851 是本课程的高级版本，它把「去掉一个 $\lg$ 或 $\lg\lg$ 因子」当作主线：从 range tree（范围树）到 fractional cascading（分数级联）、到 compressed range tree（压缩范围树）、再到与 lower bound（下界）匹配，正是一个「上界往下压、下界往上顶、两者相遇」的完整研究范式 —— 这条范式比任何一个具体结构都更值得记。CSAPP 第 6 章：pointer（指针）跳转把一次 $O(\lg n)$ 的二分换成 $O(1)$ 的顺序访问，本质上是拿「内存层级的局部性」换「算法步数」；而沿 pointer（指针）走的位置在父列表里是相邻的，所以顺带改善了 cache（缓存）命中率 —— 这是少数「理论界与工程常数同时变好」的例子。
>
> 收束：$d$ 维范围树是一条可算的曲线 —— 时间 $\lg^{d} n$、空间 $n \lg^{d-1} n$，分数级联把时间压到 $\lg^{d-1} n$。它的价值在于把高维查询从不可能变成可能，再把「多一个 $\lg$」变成一件值得专门研究的事。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
