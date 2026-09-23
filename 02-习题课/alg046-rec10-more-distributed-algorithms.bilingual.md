<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 10（复习课 10）：More Distributed Algorithms（更多分布式算法）**</span> <span style="color:#7f8c8d;">（2015 年 5 月 1 日，Massachusetts Institute of Technology，授课教授：Erik Demaine、Srini Devadas 与 Nancy Lynch）</span>

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Review（复习）**</span>

本复习课将回顾课堂（lecture）上可能令人困惑的内容。课堂上的一些关键思想（key ideas）包括：

1. **同步 vs. 异步网络模型（Synchronous vs. asynchronous network models）**（基于无向图（undirected graph））。在同步（synchronous）模型中，所有节点步调一致（in lockstep）地运转：它们全部先发送消息（send messages），再接收消息（receive messages），然后进行计算（do computation）。计算完成后，它们再次发送消息并重新开始这一循环。在异步（asynchronous）模型中，各轮之间可以流逝任意有限的时间（any finite amount of time）。这段有限时间对不同节点可以不同，因而产生大量可能的交错（interleavings）。（注意：对异步模型的时间分析通常会附加某种假设，即节点在某个时刻 $t$ 前完成一轮。然而这一假设并不用于正确性（correctness）论证——正确性不依赖任何时间上界；只有做时间分析时，我们才需要某种界（bound）。）

2. **衡量协议开销（cost）的方式有很多种**：可以统计消息数（number of messages）、比特数（number of bits）、轮数（number of rounds），也可以做异步时间分析（asynchronous time analysis）。

3. 课堂上讨论过许多证明方法（proof methods），例如不变量（invariants）、用于打破对称性（breaking symmetry）的概率方法（probabilistic methods）等。

4. **同步领导者选举（Synchronous Leader Election）**：一个经典的打破对称（symmetry-breaking）问题。在诸如团（clique）这样的对称图中，若各进程完全相同且是确定性的（deterministic），该问题无解；但若进程拥有 UID（唯一标识符，unique identifier）或可以使用随机性（randomness），则有解。

5. **同步极大独立集（Synchronous Maximal Independent Set）**：另一个有趣的打破对称问题。目标是让执行结束时处于开启状态的节点集合构成一个极大独立集（maximal independent set, MIS）。Luby 算法（Luby's algorithm）可以解决此问题：每一轮，节点从 $[1, n^5]$ 中随机挑选一个 ID，然后每个节点把自己的 ID 发送给所有邻居。某个节点若其 ID 比所有邻居的 ID 都大，就选择进入该集合；它告诉所有邻居自己已进入 MIS，这些邻居便不再属于 MIS。此后重复上述轮次。该算法将在 $O(\lg n)$ 时间内终止。

6. **同步广度优先生成树构造（Synchronous Breadth-First Spanning Tree construction）**：同步模式下，$v_0$ 向所有邻居发送一条消息，这些邻居再向外发送消息，如此进行下去。节点会保存从第一个给它发消息的节点那里收到的信息，即把该节点记为它的父节点（parent）。这是课堂上的简单算法，其消息复杂度（message complexity）为 $O(|E|)$。

<!-- ===== PDF p2 ===== -->

**（续）**

7. **利用汇聚式收集（convergecast）实现终止（termination）**——稍后详细讲解。
8. **同步最短路树构造（Synchronous Shortest-Paths Tree construction）**：算法（使用松弛（relaxation））、正确性（correctness）、分析、子节点指针（child pointers）、简单应用（simple applications）。
9. **利用汇聚式收集实现终止（带修正（with corrections））**。
10. **异步分布式算法（Asynchronous distributed algorithms）**：回顾模型假设（model assumptions）。进程自动机（process automata）、信道自动机（channel automata），以及如何将它们复合（compose）。求最大值计算的例子（Max computation example）。
11. **异步 BFS 树构造（Asynchronous BFS tree construction）**：如果直接异步地运行同步算法会出什么问题？一个正确算法（使用松弛）及其正确性、分析，以及用汇聚式收集实现终止。
12. **异步 SP 树构造（Asynchronous SP tree construction）**：算法（大量使用松弛）、分析，以及一个令人意外的反向最坏情况例子（surprising back worst-case example）。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　New Questions（新问题）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.1　Leader election in a ring with UIDs（带 UID 的环上的领导者选举）**</span>

假设各进程拥有 UID，且有一致的方向感（consistent sense of direction）（端口被标记为"左（left）"（顺时针（clockwise））或"右（right）"（逆时针（counterclockwise）））。

先考虑同步（synchronous）环境。

**简单算法（Simple algorithm）**：每个节点把自己的 UID 沿顺时针方向发送出去。当它收到自己的 UID 转回来时，就知道自己已经收到了所有 UID。于是，拥有最大 UID 的进程即可宣布自己为领导者。

时间（Time）：$n$；消息数（Messages）：$n^2$

> <span style="color:#1e8449;">**[exercise]**</span> **Q：What if we want to save messages?（如果我们想节省消息数怎么办？）**
>
> 想法（Idea）：把任何小于自己 UID、或小于自己见过的最大 UID 的到达消息丢弃掉。但这些做法在最坏情况下并不改变量级。请构造一个例子说明之。

> <span style="color:#1e8449;">**[mathtip]**</span> 构造例子时，可考虑一个方向性的环路：让环上的 UID 按递减次序排列，使得每个节点都倾向于丢弃"左邻"传来的比自己小的消息，于是消息仍需一路传播到最大 UID 所在节点才能被保留，最坏情况下消息数仍为 $\Theta(n^2)$。

**Q：Can we beat $O(n^2)$?（我们能击败 $O(n^2)$ 吗？）**

Hirshberg–Sinclair 算法（Hirshberg-Sinclair）是一个解决方案，其思路是对不断翻倍的距离进行搜索（searching to successively doubled distances）。具体而言，每个进程执行如下操作：

- 发送一条携带自己 UID、消息跳数 $h$（hop count）与方向 $d$ 的消息 $(UID, h, d)$。
- 若收到另一节点的消息 $(UID, h, d)$，则仅当跳数大于 0 且该 UID 大于等于之前见过的任何 UID 时，才沿方向 $d$ 转发 $(UID, h-1, d)$。每当消息被转发一次，其跳数减一。若该 UID 大于之前见过的任何 UID 且跳数为 0，则把它沿 $d$ 的相反方向转发。
- 若某节点没有收到自己发出的消息转回来，它就不再发送任何新消息。

<!-- ===== PDF p3 ===== -->

**（续）**

- 若某节点在右端口收到自己的 $(UID, h, \text{left})$ 消息、在左端口收到自己的 $(UID, h, \text{right})$ 消息，则说明这些消息绕了整整一圈，因而该 UID 是环中的最大值。这个节点便成为领导者。

最坏情况下该算法发送多少条消息？在跳长（hop length）为 $2^r$ 的那一轮结束时，仍会发送消息的节点至多为 $n/2^r$ 个。在第 $r$ 轮发送消息的每个节点，其跳数为 $2^r$，因而最多导致 $2^{r+1}$ 条消息被发送。于是消息总数可达

```math
\sum_{i=0}^{\lg n} 2^{i+2}\,\frac{n}{2^i} = 4n \lg n.
```

这是一个巨大的改进！$O(n \lg n)$ 取代了 $O(n^2)$。

**Q：What about an asynchronous ring?（异步环又如何呢？）** 上述算法在异步环境下依然有效。

> <span style="color:#1e8449;">**[note]**</span> Hirshberg–Sinclair 算法由 D. Hirshberg 与 J. Sinclair 于 1982 年提出，是环上领导者选举的重要结果：它把消息复杂度从简单算法的 $\Theta(n^2)$ 降到 $\Theta(n \log n)$。其核心思想是"翻倍搜索"——每个节点以 $2^0, 2^1, 2^2, \dots$ 逐轮递增的跳数向两个方向派出携带自身 UID 的消息，只有比途中所有已见 UID 都大的消息才继续前进；最终唯一能同时从左右两侧完整绕回的消息就来自最大 UID。值得说明的是，对于仅靠比较 UID 的环上选举算法，$\Omega(n \log n)$ 是已知的消息复杂度下界（Frederickson 与 Lynch，1987），因此 Hirshberg–Sinclair 在"基于比较"这一类算法中已达最优量级。该算法在异步环上运行依然正确，因为它不依赖同步轮次，只依赖消息在链路上的传播。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2　An asynchronous algorithm for counting the nodes（计算节点数的异步算法）**</span>

假设图至少包含两个节点。假设在图的顶点 $v_0$ 处有一个根进程 $i_0$。

> <span style="color:#1e8449;">**[exercise]**</span> **Q：Give a simple algorithm that allows process $i_0$ to compute the total number of nodes in the network.（给出一个简单算法，使进程 $i_0$ 能够计算出网络中的节点总数。）**

先建立一棵生成树（spanning tree），例如把简单的同步生成树算法异步地运行。在异步环境下，这会产生某棵生成树，但不保证是 BFS 生成树。不过对本处的目的（统计节点数）而言这已经足够了。（由于时间异常（timing anomaly），它的时间复杂度也不会是最优的。）最后，加入一个汇聚式收集（convergecast），在向根上行的过程中顺便累加总和。

与全班一起逐步开发这份代码，分三个阶段进行：1. 采用讲义幻灯片中的异步（非 BFS）生成树代码；2. 加入维护子节点指针（child pointers）的动作；3. 再加入对节点数做汇聚式收集的动作。

时间代价分析：$O(|E|)$。

所有进程都将采用如下策略：把消息放入面向各邻居的 FIFO 发送队列（FIFO send queue），然后由独立的输出动作（output action）从队头开始真正发送消息。因此，所有进程都有如下变迁（transition）定义（之后我们将略去这些细节）：

```text
output send(m)u,v，m 一条消息，v ∈ Γ(u)
Precondition: m = head(send(v))
Effect: remove head of send(v)
```

> <span style="color:#1e8449;">**[note]**</span> 这里采用的是 I/O 自动机（input/output automaton）式的形式化描述：每个进程是一个自动机，其状态变量（state variables）与变迁（transitions）按"输入动作/内部动作/输出动作"组织；信道同样建模为自动机，从而可对分布式系统整体取复合。`send(v)` 表示发往邻居 $v$ 的发送队列，`head` 为队头元素；输出动作的 Precondition 保证了同一时刻每个队列至多发出队头一条消息，Effect 则描述了发送后队头的移除。这种"FIFO 队列 + 独立输出动作"的约定是 Nancy Lynch 所著《Distributed Algorithms》一书中的标准建模方式，便于对异步系统的运行时间与正确性做形式化论证。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2.1　Part 1: Setting up a tree（第一部分：建立一棵树）**</span>

第一条收到的 search 消息的发送者即成为你的父节点。继续把 search 消息转发下去。

**Process $v_0$（进程 $v_0$）**

```text
State variables:
for each v ∈ Γ(v0), send(v), a queue, initially (search)
Transitions:
input receive(search)v,v0 , v ∈ Γ(v0)
  Effect: none
```

**Process $u$（进程 $u$），$u \neq v_0$**

```text
State variables:
parent ∈ Γ(u) ∪ {⊥}, initially ⊥
for each v ∈ Γ(u), send(v), a queue, initially empty
Transitions:
input receive(search)v,u , v ∈ Γ(u)
  Effect:
  if parent = ⊥ then
    parent := v
    for each v ∈ Γ(u), add search to send(v)
```

<!-- ===== PDF p4 ===== -->

**（续）**

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2.2　Part 2: Adding child pointers（第二部分：加入子节点指针）**</span>

现在发送 `parent(true)` 或 `parent(false)` 作为应答（response）。跟踪哪些邻居已应答（responded），以及其中哪些应答了 `parent(true)`——后者即为子节点（children）。

**Process $v_0$（进程 $v_0$）**

```text
State variables:
responded ⊆ Γ(v0), initially ∅
children ⊆ Γ(v0), initially ∅
for each v ∈ Γ(v0), send(v), a queue, initially (search)
Transitions:
input receive(search)v,v0 , v ∈ Γ(v0)
  Effect: add parent(false) to send(v)
input receive(parent(b))v,v0 , b a Boolean, v ∈ Γ(v0)
  Effect:
  if b then
    children := children ∪ {v}
    responded := responded ∪ {v}
```

<!-- ===== PDF p5 ===== -->

**（续）**

**Process $u$（进程 $u$），$u \neq v_0$**

```text
State variables:
parent ∈ Γ(u) ∪ {⊥}, initially ⊥
responded ⊆ Γ(u), initially ∅
children ⊆ Γ(u), initially ∅
for each v ∈ Γ(u), send(v), a queue, initially empty
Transitions:
input receive(search)v,u , v ∈ Γ(u)
  Effect:
  if parent = ⊥ then
    parent := v
    add parent(true) to send(v)
  else add parent(false) to send(v)
input receive(parent(b))v,u , b a Boolean, v ∈ Γ(u)
  Effect:
  if b then
    children := children ∪ {v}
    responded := responded ∪ {v}
```

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2.3　Part 3: Adding the convergecast for the count（第三部分：为计数加入汇聚式收集）**</span>

现在沿树向上汇聚式收集（convergecast）计数。

**Process $v_0$（进程 $v_0$）**

```text
State variables:
responded ⊆ Γ(v0), initially ∅
children ⊆ Γ(v0), initially ∅
done ⊆ Γ(v0), initially ∅
total, a nonnegative integer, initially 0
for each v ∈ Γ(v0), send(v), a queue, initially (search)
```

<!-- ===== PDF p6 ===== -->

**（续）**

**Process $v_0$（进程 $v_0$）——Transitions（变迁）**

```text
Transitions:
input receive(search)v,v0 , v ∈ Γ(v0)
  Effect: add parent(false) to send(v)
input receive(parent(b))v,v0 , b a Boolean, v ∈ Γ(v0)
  if b then
    children := children ∪ {v}
    responded := responded ∪ {v}
input receive(done(k))v,v0 , k a nonnegative integer, v ∈ Γ(v0)
  Effect:
  done := done ∪ {v}
  total := total + k
  if done = Γ(v0) then (the final output is the value in total)
```

**Process $u$（进程 $u$），$u \neq v_0$**

```text
State variables:
parent ∈ Γ(u) ∪ {⊥}, initially ⊥
responded ⊆ Γ(u), initially ∅
children ⊆ Γ(u), initially ∅
done ⊆ Γ(u), initially ∅
total, a nonnegative integer, initially 0
for each v ∈ Γ(u), send(v), a queue, initially empty
Transitions:
input receive(search)v,u , v ∈ Γ(u)
  Effect:
  if parent = ⊥ then
    parent := v
    add parent(true) to send(v)
  else add parent(false) to send(v)
input receive(parent(b))v,u , b a Boolean, v ∈ Γ(u)
  Effect:
  if b then
    children := children ∪ {v}
    responded := responded ∪ {v}
  if responded = Γ(u) and children = ∅ then
```

<!-- ===== PDF p7 ===== -->

**（续）**

```text
    add done(1) to send(parent)
input receive(done(k))v,u , k a nonnegative integer, v ∈ Γ(u)
  Effect:
  done := done ∪ {v}
  total := total + k
  if responded = Γ(u) and done = children then
    add done(total + 1) to send(parent)
```

> <span style="color:#1e8449;">**[note]**</span> 汇聚式收集（convergecast）是树形网络中"把信息自底向上汇总到根"的经典模式，常与生成树上的泛洪（flooding/broadcast）配合：先向下广播建立树，再向上汇聚收集结果。这里的计数协议分三阶段渐进构造，是讲解 I/O 自动机建模的典型例子：每棵子树只有在其所有子节点都完成汇总（done）之后，才把自己的子树节点数 $total+1$ 上报给父节点；叶子节点（children 为空）则直接上报 `done(1)`。该算法的时间复杂度为 $O(|E|)$，因为每条边上的 search 消息与 done 消息各被传递常数次。根进程 $v_0$ 在收到所有邻居的 done 消息后，`total` 即为全网节点总数。

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">MIT OpenCourseWare　http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析），Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
