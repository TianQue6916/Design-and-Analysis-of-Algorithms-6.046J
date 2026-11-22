<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 8（Lecture 8）：哈希（Hashing）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

**课程概览（Course Overview）**：本讲涵盖以下模块：
1. 回顾：字典、链式哈希、简单均匀假设（Review: dictionaries, chaining, simple uniform）
2. 通用哈希（Universal hashing）
3. 完美哈希（Perfect hashing）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**回顾（Review）**</span>

**字典问题（Dictionary Problem）**：字典（dictionary）是一种维护一组条目（set of items）的**抽象数据类型（Abstract Data Type, ADT）**。每个条目有一个键（key）。字典支持以下操作：
- **insert(item)**：把条目加入集合
- **delete(item)**：把条目从集合中移除
- **search(key)**：若存在，返回具有该键的条目

我们假设条目具有互不相同的键（或者插入新条目时会覆盖（clobbers）旧条目）。

这个问题比前一讲（用 van Emde Boas 树、AVL/2-3 树或跳跃表解决的）的**前驱/后继问题**更容易。

**来自 6.006 的哈希（Hashing from 6.006）**

**目标（Goal）**：每次操作 $O(1)$ 时间，空间复杂度 $O(n)$。

**定义（Definitions）**：
- $u$ = 所有可能条目的键的数量
- $n$ = 当前在表中的键/条目的数量
- $m$ = 表中槽位（slots）的数量

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b;">**哈希（续）**</span>

**解决方案：链式哈希（Solution: hashing with chaining）**

假设**简单均匀哈希（simple uniform hashing）**：

```math
\Pr\{h(k_1) = h(k_2)\} = \frac{1}{m} \quad (k_1 \ne k_2)
```

我们实现每次操作 $\Theta(1 + \alpha)$ 时间，其中 $\alpha = \frac{n}{m}$ 称为**负载因子（load factor）**。该算法的缺点是：它需要**假设输入键是随机的**，且只对平均情形（average case）有效，就像基本快速排序一样。今天我们要**去掉这个不合理的简单均匀哈希假设**。

**词源（Etymology）**：英语 'hash'（1650 年代）意为「切成小块」（cut into small pieces），来自法语 'hacher'（意为「剁碎」chop up），后者来自古法语 'hache'（意为「斧头」axe，比较英语 'hatchet' 小斧头）。另一种说法是它们来自瓦肯语（Vulcan）'la'ash'，意为「斧头」。（R.I.P. Leonard Nimoy.）

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 简单均匀哈希假设（每个键等概率落入每个槽、且各键独立）是个「理想但通常不成立」的假设：实际输入键往往有结构（如连续整数、用户 ID），敌手（adversary）甚至可以**故意选择**让所有键都撞进同一个槽的输入，使哈希表退化为链表（最坏 $O(n)$）。这正是本讲要解决的痛点：**不再假设输入随机，而是让哈希函数本身随机化**——从一族函数里随机挑一个。词源小彩蛋：'hash' 来自「剁碎（chop up）」——把键「剁碎」分散到槽里，正是哈希的直观画面；讲义顺带致敬了斯波克（Leonard Nimoy，2015 年 2 月去世——本讲制作时正值其逝世不久），因为瓦肯语猜测是词源之一。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**通用哈希（Universal Hashing）**</span>

通用哈希的思想如下：
- 从 $\mathcal{H}$ 中**随机选择**一个哈希函数 $h$
- 要求 $\mathcal{H}$ 是一个**通用哈希族（universal hashing family）**，使得对任意 $k \ne k'$：

```math
\Pr_{h \in \mathcal{H}}\{h(k) = h(k')\} \le \frac{1}{m}
```

🎥 *Devadas 在视频中[区分两类哈希技术]*："Probably the most useful one is called universal hashing. We'll spend most of our time on that. But the theoretically cooler one is called perfect hashing. ... Universal hashing, we're going to guarantee there are very few conflicts in expectation."（翻译：大概最有用的叫通用哈希，我们会花大部分时间在上面。但理论上更酷的叫完美哈希。……通用哈希要保证的是：期望上的冲突非常少。）——**通用哈希管「平均好」，完美哈希管「最坏也好」**，这正是本讲后半段从「期望无碰撞」推进到「绝对无碰撞」的动机。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 通用哈希的定义是「**两两碰撞概率 $\le 1/m$**」：任意两个不同键，随机选的哈希函数使它们落入同一槽的概率至多 $1/m$（和均匀随机时一样）。注意这个定义**不要求**「每个键等概率落入每个槽」——它只约束「成对碰撞」，而「期望冲突数」恰恰只由成对碰撞决定（下一节的线性期望证明会用到）。这个「**把强假设（简单均匀）弱化为成对概率**」的松弛，是通用哈希能成立的数学根源：我们只对「会发生什么坏事的概率」做约束，而对「分布的具体形态」不做假设。Wiki 查证：通用哈希正是为了**对抗恶意输入**而设计——若输入由敌手选择，确定性哈希函数总能被「预计算原像」攻击（让所有键落入同一槽），而随机选函数让敌手无法预知哪个函数会被用到。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b;">**通用哈希（续）**</span>

- 现在**只假设 $h$ 是随机的**，对输入键不做任何假设（像随机化快速排序一样）。

<span style="color:#2471a3;">**[theorem]**</span> **定理（Theorem）**：对 $n$ 个任意互异的键和随机 $h \in \mathcal{H}$（$\mathcal{H}$ 是通用哈希族）：

```math
E[\text{一个槽中碰撞的键数}] \le 1 + \alpha \quad \text{其中 } \alpha = \frac{n}{m}
```

**证明（Proof）**：考虑键 $k_1, k_2, \ldots, k_n$。令

```math
I_{i,j} = \begin{cases} 1 & \text{若 } h(k_i) = h(k_j)\\ 0 & \text{否则} \end{cases}
```

那么我们有

```math
E[I_{i,j}] = \Pr\{I_{i,j} = 1\} = \Pr\{h(k_i) = h(k_j)\} \le \frac{1}{m} \quad \text{对任意 } j \ne i \quad (1)
```

```math
\begin{aligned}
E[\#\text{哈希到与 } k_i \text{ 相同槽的键数}] &= E\left[\sum_{j=1}^{n} I_{i,j}\right]\\
&= \sum_{j=1}^{n} E[I_{i,j}] \quad (\text{期望线性性 linearity of expectation})\\
&= \sum_{j \ne i} E[I_{i,j}] + E[I_{i,i}]\\
&\le \frac{n}{m} + 1 \quad (2)
\end{aligned}
```

> <span style="color:#7f8c8d;">[说明] 原文 (2) 式中 $\sum_{j\ne i} E[I_{i,j}] \le \frac{n}{m}$ 由 $n-1$ 个 $\le 1/m$ 的项相加得到（$n/m$ 是宽松上界），$E[I_{i,i}] = 1$ 来自「$k_i$ 与自己必在同一槽」。</span>

由上述定理可知，Insert、Delete、Search 都需要 $O(1 + \alpha)$ 期望时间。下面给出通用哈希函数的一些例子。

**所有哈希函数（All hash functions）**：$\mathcal{H} = \{$ 所有哈希函数 $h : \{0, 1, \ldots, u-1\} \to \{0, 1, \ldots, m-1\}\}$。显然 $\mathcal{H}$ 是通用的，但它**没用**（useless）。一方面，存储单个哈希函数 $h$ 需要 $\log(m^u) = u \log(m)$ 比特 $\gg n$ 比特；另一方面，我们需要预计算 $u$ 个值，需要 $\Omega(u)$ 时间。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这个证明是「期望线性性 + 指标变量（indicator variable）」双剑合璧的教科书示范：**把「计数」问题改写为「一堆 0/1 变量的和」**，再把期望「线性地」拆到每个变量上。$E[I_{i,j}] = \Pr\{h(k_i) = h(k_j)\} \le 1/m$ 直接用通用哈希定义，而「$k_i$ 与自己」的那一项 $I_{i,i} = 1$ 贡献了「$+1$」——这解释了为什么界是 $1 + \alpha$ 而不是 $\alpha$：**每个槽里至少有它自己**。期望线性性的妙处在于**无需独立性**：即使 $I_{i,j}$ 之间高度相关，和的期望仍等于期望的和。这个「指标变量 + 线性性」模板在后续的随机图、桶排序、生日悖论分析里会反复出现。而「全函数族」的例子说明：**通用性只是必要条件，可用性还要求函数族能高效存储与求值**——存储 $u\log m$ 比特的函数（要查 $u$ 个表项）显然不可行，这引出了下一页「点积族」这样既通用又可快速求值的精巧设计。

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**点积哈希族（Dot-product hash family）**</span>

**假设（Assumptions）**：
- $m$ 是**素数**（prime）
- $u = m^r$，其中 $r$ 是整数

在实际情形中，我们总是可以**向上取整** $m$ 和 $u$ 以满足上述假设。现在把键按 $m$ 进制（base $m$）看待：$k = \langle k_0, k_1, \ldots, k_{r-1} \rangle$。对键 $a = \langle a_0, a_1, a_2, \ldots, a_{r-1} \rangle$，定义

```math
h_a(k) = a \cdot k \bmod m \quad (\text{点积 dot product}) = \sum_{i=0}^{r-1} a_i k_i \bmod m \quad (3)
```

那么我们的哈希族是 $\mathcal{H} = \{h_a \mid a \in \{0, 1, \ldots, u-1\}\}$。

存储 $h_a \in \mathcal{H}$ 只需存储一个键 $a$。在 **word RAM 模型**（word RAM model）中，操作 $O(1)$ 个机器字（machine words）需要 $O(1)$ 时间，且「感兴趣的对象」（这里是键）恰好装进一个机器字。因此计算 $h_a(k)$ 需要 $O(1)$ 时间。

<span style="color:#2471a3;">**[theorem]**</span> **定理（Theorem）**：点积哈希族 $\mathcal{H}$ 是通用的。

**证明（Proof）**：取任意两个键 $k \ne k'$。它们必在**某一位上不同**。设 $k_d \ne k'_d$。定义 $\mathrm{not}\ d = \{0, 1, \ldots, r-1\} \setminus \{d\}$。现在

```math
\begin{aligned}
\Pr_a\{h_a(k) = h_a(k')\} &= \Pr_a\left\{\sum_{i=0}^{r-1} a_i k_i = \sum_{i=0}^{r-1} a_i k'_i \pmod m\right\}\\
&= \Pr_a\left\{\sum_{i \ne d} a_i k_i + a_d k_d = \sum_{i \ne d} a_i k'_i + a_d k'_d \pmod m\right\}\\
&= \Pr_a\left\{\sum_{i \ne d} a_i (k_i - k'_i) + a_d (k_d - k'_d) = 0 \pmod m\right\}\\
&= \Pr_a\left\{a_d = -(k_d - k'_d)^{-1} \sum_{i \ne d} a_i (k_i - k'_i) \pmod m\right\} \quad (4)
\end{aligned}
```

（$m$ 是素数 ⇒ $\mathbb{Z}_m$ 中有**乘法逆元** multiplicative inverses）

```math
= E_{a_{\mathrm{not}\ d}}\left[\Pr_{a_d}\{a_d = f(k, k', a_{\mathrm{not}\ d})\}\right]
```

```math
= \sum_{x} \Pr\{a_{\mathrm{not}\ d} = x\}\Pr\{a_d = f(k, k', x)\} = E_{a_{\mathrm{not}\ d}}\left[\frac{1}{m}\right] = \frac{1}{m} \quad \square
```

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 点积族是「**固定 d 位 + 随机其余**」的证明模板：两个不同键 $k \ne k'$ 至少在某一位 $d$ 上不同，于是 $h_a(k) = h_a(k')$ 的碰撞条件可以**解出 $a_d$**（第 4 行），而 $a_d$ 是随机均匀的，所以碰撞概率恰为「$a_d$ 恰好等于那个特定值」的概率 $= 1/m$。关键在于**把碰撞事件改写成「某个特定随机变量的取值命中」**——这是通用性证明的标准手法。为什么 $m$ 必须是素数？因为需要 $\mathbb{Z}_m$ 中每个非零元素有**乘法逆元**（$k_d - k'_d \ne 0$ 才能求逆解出 $a_d$）——这是你在 6.042J 数论部分学过的：**模素数的环是域，非零元可逆**；若 $m$ 是合数，$k_d - k'_d$ 可能不可逆，证明就卡住了。这也解释了为什么所有经典哈希族都要「找附近的素数」：素性不是锦上添花，而是可逆性（从而可解出随机变量）的前提。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**另一个通用哈希族（Another universal hash family）**</span>

**来自 CLRS 的另一个通用哈希族**：选择素数 $p \ge u$（只选一次）。定义

```math
h_{ab}(k) = [ (ak + b) \bmod p ] \bmod m
```

令 $\mathcal{H} = \{h_{ab} \mid a, b \in \{0, 1, \ldots, u-1\}\}$。

> <span style="color:#7f8c8d;">[译注] 原文未注明，但数学上需排除 $a = 0$：若 $a = 0$，则 $h_{0b}(k) = b \bmod m$ 恒为常数，任何两键都以概率 1 碰撞，族不通用。故通用族实际取 $a \ne 0$。</span>

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> CLRS 的 $h_{ab}(k) = ((ak + b) \bmod p) \bmod m$ 是「**线性函数 + 双模**」的通用族，它是点积族的「标量版」：点积族把键拆成 $r$ 位做内积，而 CLRS 族把整个键当作一个大整数套进线性函数 $ak + b$。第一个 $\bmod p$ 把结果压到素数 $p$ 的范围内（保证可逆性），第二个 $\bmod m$ 把结果映射到 $m$ 个槽。这个族在 CLRS §11.3.3 里有完整证明，是实际中最常用的通用哈希之一。注意它对应你在 6.042J 学过的**仿射函数（affine function）**：$k \mapsto ak + b$ 是 $k$ 的一次函数，只是额外取了模——「一次函数 + 随机斜率/截距」正是它的全部结构。

---

<span style="color:#c0392b">**完美哈希（Perfect Hashing）**</span>

**静态字典问题（Static dictionary problem）**：给定 $n$ 个键存入表，只需支持 **search(k)**。不会发生插入或删除。

**完美哈希（Perfect hashing）**：[Fredman, Komlós, Szemerédi 1984]
- 多项式时间的构建（polynomial build time），且以高概率（w.h.p.）
- **最坏情形**下 search $O(1)$ 时间
- **最坏情形**下 $O(n)$ 空间

**想法（Idea）：两级哈希（2-level hashing）**

该算法包含以下两个主要步骤：

**Step 1**：从通用哈希族中选 $h_1 : \{0, 1, \ldots, u-1\} \to \{0, 1, \ldots, m-1\}$，其中 $m = \Theta(n)$（例如取附近的素数）。用 $h_1$ 以**链式哈希（chaining）**散列所有条目。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 完美哈希要解决的核心矛盾：**既要 $O(1)$ 最坏情形查询（不能退化成链表的 $O(n)$），又不能让所有槽位都开满导致 $O(n^2)$ 空间**。两级方案的思路是「第一级容忍少量碰撞、第二级消灭所有碰撞」：第一级 $h_1$ 把 $n$ 个键散到 $m = \Theta(n)$ 个槽（期望每个槽 $O(1)$ 个键），第二级对每个槽 $j$ 单独用一个完美哈希 $h_{2,j}$ 把它的 $l_j$ 个键映射到 $l_j^2$ 个槽——而「$l_j$ 个键散到 $l_j^2$ 个槽」能以概率论（生日悖论）论证保证**几乎必然无碰撞**（期望碰撞数 $< 1/2$，配合 Step 2.5 重散列，详见下页分析）。FKS（Fredman-Komlós-Szemerédi，1984）是第一个给出「静态字典最坏情形 $O(1)$ 查询 + $O(n)$ 空间」的构造，是数据结构史上的里程碑。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b">**完美哈希（续）**</span>

**Step 2**：对每个槽 $j \in \{0, 1, \ldots, m-1\}$，设 $l_j$ 是槽 $j$ 中的条目数：$l_j = |\{i \mid h(k_i) = j\}|$。从通用哈希族中选 $h_{2,j} : \{0, 1, \ldots, u-1\} \to \{0, 1, \ldots, m_j\}$，其中 $l_j^2 \le m_j \le O(l_j^2)$（例如取附近的素数）。把槽 $j$ 的链替换为用 $h_{2,j}$ 的链式哈希。

空间复杂度为 $O\left(n + \sum_{j=0}^{m-1} l_j^2\right)$。为了把它降到 $O(n)$，我们需要再加两步：

**Step 1.5**：若 $\sum_{j=0}^{m-1} l_j^2 > cn$（$c$ 是选定的常数），则**重做 Step 1**。

**Step 2.5**：当存在 $i \ne i'$ 使 $h_{2,j}(k_i) = h_{2,j}(k_{i'})$ 时（对任何 $j$），**重新挑选 $h_{2,j}$** 并重散列（rehash）这 $l_j$ 个键。

上述两步保证了**第二级没有碰撞**，且空间复杂度为 $O(n)$。因此，search 时间是 $O(1)$。现在看一下算法的构建时间。Step 1 和 Step 2 都是 $O(n)$。Step 1.5 和 Step 2.5 呢？

**对 Step 2.5**：

```math
\Pr_{h_{2,j}}\{h_{2,j}(k_i) = h_{2,j}(k_{i'}) \text{ 对某对 } i \ne i'\} \le \sum_{i \ne i'} \Pr_{h_{2,j}}\{h_{2,j}(k_i) = h_{2,j}(k_{i'})\} \quad (\text{并集界 union bound})
```

```math
\le \binom{l_j}{2} \cdot \frac{1}{m_j} \le \binom{l_j}{2} \cdot \frac{1}{l_j^2} < \frac{1}{2}
```

因此，每次试验都像一次抛硬币。若结果是「反面」（tail），我们进入下一步。由讲次 7，我们有 $E[\#\text{trials}] \le 2$ 且 $\#\text{trials} = O(\log n)$ w.h.p.。由 **Chernoff 界**，$l_j = O(\log n)$ w.h.p.，所以每次试验需 $O(\log n)$ 时间。因为我们得对每个 $j$ 做这个，总时间复杂度为 $O(\log n) \cdot O(\log n) \cdot O(n) = O(n \log^2 n)$ w.h.p.。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Step 2.5 的「$l_j^2$ 槽消灭碰撞」是**生日悖论（birthday paradox）的反向应用**：$l_j$ 个键散到 $l_j^2$ 个槽时，期望碰撞对数 $= \binom{l_j}{2} \cdot \frac{1}{l_j^2} < \frac{1}{2}$——比 1/2 还小，所以「完全无碰撞」的概率至少 1/2（因为若碰撞对数期望 $< 1/2$，则「至少一对碰撞」的概率 $< 1/2$）。这正是生日悖论（$n$ 个人中两人生日相同的概率随 $n$ 平方增长）的镜像：**碰撞的期望数随槽数的平方反比下降**——槽数取 $l_j^2$ 恰好压到 $< 1/2$。而「每次试验 $< 1/2$ 失败概率」正是讲次 7 分析过的几何分布（期望 $\le 2$ 次重试）。注意两处「w.h.p.」的叠加：$l_j = O(\log n)$ 保证每次重试便宜（Chernoff），$\#\text{trials} = O(\log n)$ 保证重试次数可控（几何分布 + 并集界），两者相乘得 $O(n \log^2 n)$ 总构建时间。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#c0392b">**完美哈希构建时间（续）**</span>

**对 Step 1.5**：定义

```math
I_{i,i'} = \begin{cases} 1 & \text{若 } h(k_i) = h(k_{i'})\\ 0 & \text{否则} \end{cases}
```

那么我们有

```math
\begin{aligned}
E\left[\sum_{j=0}^{m-1} l_j^2\right] &= E\left[\sum_{i=1}^{n} \sum_{i'=1}^{n} I_{i,i'}\right]\\
&= \sum_{i=1}^{n} \sum_{i'=1}^{n} E[I_{i,i'}] \quad (\text{期望线性性})\\
&\le n + 2\binom{n}{2} \cdot \frac{1}{m} = O(n) \quad \text{因为 } m = \Theta(n)
\end{aligned}
```

由 **Markov 不等式**（Markov inequality），有

```math
\Pr_{h_1}\left\{\sum_{j=0}^{m-1} l_j^2 \ge cn\right\} \le \frac{E[\sum l_j^2]}{cn} \le \frac{1}{2}
```

对足够大的常数 $c$ 成立。由讲次 7，$E[\#\text{trials}] \le 2$ 且 $\#\text{trials} = O(\log n)$ w.h.p.。因此，Step 1 与 Step 1.5 合计需要 $O(n \log n)$ 时间 w.h.p.。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Step 1.5 用 **Markov 不等式**（$\Pr[X \ge a] \le E[X]/a$，你已在前几讲见过它的「多项式尾部」版本）控制「平方负载和过大」的概率：$E[\sum l_j^2] = O(n)$ 且取 $c$ 足够大，则 $\Pr[\sum l_j^2 \ge cn] \le 1/2$——**一半概率需要重试，重试期望次数 $\le 2$**。为什么用平方和 $\sum l_j^2$？因为第二级空间与每个槽负载的**平方**成正比（槽 $j$ 需要 $l_j^2$ 个二级槽），第一级必须保证「平方和」不过大，空间才可控。而 $E[l_j^2]$ 的计算又是「指标变量 + 线性性」：$l_j^2$ 展开成 $\sum_i \sum_{i'} I_{i,i'}$，对角项贡献 $n$（每个键与自己配对），非对角项 $\le 1/m$——这与你见证过无数次的「配对计数」完全同构。至此 FKS 完美哈希的完整论证闭合：**一级控「平方负载期望」（Markov 重试）+ 二级控「槽内无碰撞」（生日悖论重试）+ 空间 $O(n)$ + 查询 $O(1)$ 最坏情形**。

---

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
