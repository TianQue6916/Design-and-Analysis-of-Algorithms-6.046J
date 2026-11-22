<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 10 解答（Problem Set 10 Solutions）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

<span style="color:#7f8c8d;">麻省理工学院（Massachusetts Institute of Technology）｜授课教授：Prof. Erik Demaine、Prof. Srini Devadas、Prof. Nancy Lynch｜2015 年 5 月 8 日（May 8, 2015）</span>

本习题集截止时间（due）：**2015 年 5 月 8 日（周五）23:59**。

---

<span style="color:#2471a3;">**[exercise]**</span> <span style="color:#c0392b;">**Exercise 10-1.**</span>

阅读 L19 与 L20 两讲的课堂幻灯片（lecture slides）。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 10-1. Leader Election in a Synchronous Ring（同步环中的领导者选举）**</span> <span style="color:#7f8c8d;">[25 points]</span>

考虑由 $n$ 个相同进程（processes）组成的集合，它们排列在一个同步环网络（synchronous ring network）中。每个进程有两组端口（ports），各通向其一个直接邻居：通往其顺时针（clockwise）邻居的端口命名为 `left`，通往其逆时针（counterclockwise）邻居的端口命名为 `right`。因此，这些进程具有共同的定向感（sense of orientation）。

目标是让这些进程选出唯一的领导者（leader）：最终恰好有一个进程输出 `LEADER`。

**(a)** [5 points] 首先假设进程是确定性的（deterministic），并且它们知道 $n$（环的大小）。要么给出针对这种情况的正确领导者选举算法，要么证明不存在这样的算法。若给出算法，请分析其时间（time）与消息（message）复杂度。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 不可能（Impossible）。反设这样的算法存在。我们可以使用标准的归纳对称论证（inductive symmetry argument）：对轮数（rounds）$r$ 作归纳，证明经过 $r$ 轮之后，所有进程都处于完全相同的状态（identical states）。由于算法必须解决领导者选举问题，最终会有某个进程输出 `LEADER`；但这样一来，所有进程都会在同一轮做出完全相同的事情。

**(b)** [10 points] 现在假设进程是概率性的（probabilistic，即随机化的 randomized），并且它们知道 $n$。我们想要一个算法，它 (a) 绝不会选出多于一个领导者，并且 (b) 以概率 1 最终选出一个领导者。要么给出满足这些性质的算法，要么证明不存在这样的算法。若给出算法，请分析其时间与消息复杂度。你的分析应将复杂度与成功概率（success probability）联系起来。具体地，对任意 $\varepsilon$（$0 < \varepsilon < 1$），你应给出以至少 $1 - \varepsilon$ 的概率成立的时间与消息复杂度上界。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 这里存在一个简单算法。

<!-- ===== PDF p2 ===== -->

**（续）**

> **引理（Lemma，与课堂上讲过的一个引理类似）：** 若 $n$ 个进程独立地（independently）从 $\{1, \ldots, n^2\}$ 中均匀随机（uniformly at random）地选择 id，则以至少 $1/2$ 的概率，所选出的这些数全部互不相同（distinct）。
>
> **算法（Algorithm）：** 该算法分一系列阶段（phases）进行。在每个阶段，所有进程都从一个足够大的取值空间中选择随机 id，即如引理中所定义的 $\{1, \ldots, n^2\}$。然后它们把自己的 id 沿环发送一周（比如说按顺时针方向）。恰好经过 $n$ 步后，每个进程检查自己收到的 id 序列。此时分三种情况：
>
> 1. 若序列中的最大 id 不唯一，则该进程放弃（abandons）本阶段并进入下一阶段。
> 2. 若序列中的最大 id 唯一且正是该进程自己的 id，则它输出 `LEADER` 并停机（halts）。
> 3. 若序列中的最大 id 唯一但不是该进程自己的 id，则它直接停机。
>
> 应当清楚的是：在第一个所有进程都选出互不相同 id 的阶段，恰好有一个进程将自己选为领导者，随后算法停机。因此，算法绝不会选出多于一个领导者。
>
> 在每个阶段，以至少 $1/2$ 的概率，所有选出的 id 互不相同，算法终止。由于不同阶段中的选择相互独立，算法在 $h$ 个阶段内完成的概率至少为 $1 - (1/2)^h$。因此，它以概率 1 最终完成。
>
> 我们分析时间与消息复杂度。每个阶段由 $n$ 轮组成，并发送 $n^2$ 条（单跳 single-hop）消息。考虑任意 $\varepsilon$（$0 < \varepsilon < 1$）。选取 $h$ 为使得 $(1/2)^h \le \varepsilon$ 的最小整数，即 $h = \lceil \lg(1/\varepsilon) \rceil$。于是，以至少 $1 - (1/2)^h \ge 1 - \varepsilon$ 的概率，算法在 $h$ 个阶段内完成，共使用 $n \cdot h$ 轮和 $n^2 \cdot h$ 条消息。也就是说，以至少 $1 - \varepsilon$ 的概率，时间复杂度至多为 $n \lceil \lg(1/\varepsilon) \rceil$，消息复杂度至多为 $n^2 \lceil \lg(1/\varepsilon) \rceil$。

**(c)** [10 points] 最后假设进程是概率性的，且它们不知道 $n$。也就是说，无论进程被放置在多大的环中，同一个算法都必须能够正常工作。我们同样希望得到一个算法，它 (a) 绝不会选出多于一个领导者，并且 (b) 以概率 1 最终选出一个领导者。要么给出满足这些性质的算法，要么证明不存在这样的算法。若给出算法，请按 (b) 部分所述分析其时间与消息复杂度。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 这是不可能的。反设这样的算法存在。考虑该算法在大小为 $n$ 的环 $S$ 中运行，$n$ 取任意特定值。按各进程在环中的位置（沿顺时针方向计数）将 $S$ 的进程编号为 $1, \ldots, n$。由于算法在环 $S$ 中以概率 1 最终选出领导者，$S$ 中必然存在至少一条执行（execution）$\alpha$，使得某个进程 $j$ 输出 `LEADER`。也就是说，存在某个从进程到随机选择序列（sequences of random choices）的特定映射，使得在做出这些选择的执行 $\alpha$ 中，某个进程 $j$ 被选为领导者。

<!-- ===== PDF p3 ===== -->

**（续）**

> 现在考虑大小为 $2n$ 的环 $R$，它由两个半环（half-rings）$R_1$ 与 $R_2$ 组成，每个大小为 $n$。将 $R$ 的进程编号为 $1, \ldots, 2n$。于是存在 $R$ 的一条执行 $\alpha'$，其中 $R$ 中的进程 $i$ 与 $n + i$ 恰好做出与 $S$ 中进程 $i$ 相同的随机选择。在执行 $\alpha'$ 中，进程 $i$ 与 $n + i$ 的行为与 $S$ 的执行 $\alpha$ 中进程 $i$ 的行为完全相同。由于进程 $j$ 在执行 $\alpha$ 中输出 `LEADER`，因此进程 $j$ 与 $n + j$ 都会在执行 $\alpha'$ 中输出 `LEADER`。这与题目要求 (a) 相矛盾。
>
> 注意，这一证明表明：我们无法达到任何正的选举概率 $\varepsilon$，而不仅仅是在概率 1 的意义上无法做到。

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 10-2. Breadth-First Search in an Asynchronous Network（异步网络中的广度优先搜索）**</span> <span style="color:#7f8c8d;">[25 points]</span>

课堂上讲授的异步广度优先搜索（asynchronous Breadth-First Search, BFS）算法涉及一些修正（corrections），这些修正可能触发大量消息的发送，导致在最坏情况下消息复杂度为 $O(n \cdot E)$、时间复杂度为 $O(\text{diam} \cdot n \cdot d)$，直到所有节点的 parent（父节点）变量在广度优先生成树（breadth-first spanning tree）中稳定（stabilize）为正确的父节点为止。（这里我们不关心各进程单独的 parent 输出，也不考虑全局终止（global termination）；同时忽略本地处理时间。）

本题要求设计一个更好的异步广度优先搜索算法——一个不需要做任何修正的算法。这样一来，一旦某个进程设置好其 parent 变量，就可以输出该值，因为那就是它的最终决定。假设网络图是连通的（connected）且至少包含两个节点。

**(a)** [18 points] 用文字仔细描述这样一个算法：根节点（root node）$v_0$ 逐层（level by level）协调树的构建。你的算法应在所有 parent 变量设置完成之前耗时 $O(\text{diam}^2 \cdot d)$。

> （提示：根节点可以进行广播-汇播波（broadcast-convergecast waves）来构建树中相继的各层。四种类型的消息应该就足够了，例如：用于探测新节点的 `search` 消息；用于父/非父应答的 `parent(b)`（$b$ 为布尔值 Boolean）；以及用于在树上广播与汇播信号的 `ready` 与 `done` 消息。）

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 节点 $v_0$ 发起算法的相继各阶段。在每个阶段 $d$，恰好是与 $v_0$ 相距 $d$ 的那些节点被纳入 BFS 树。
>
> **阶段 1：** 节点 $v_0$ 向所有邻居发送 `search` 消息。邻居将 $v_0$ 记录为自己的 parent，记录自己是新节点（new nodes），并向 $v_0$ 发回 `parent` 应答。当 $v_0$ 收到所有邻居的 `parent` 应答后，它就准备好开始阶段 2。
>
> **阶段 $d$（$d \ge 2$）：** 节点 $v_0$ 将 `ready` 消息沿目前已建成的树的各分支广播下去，直到它们到达新节点。每个新节点向自己的所有邻居发送 `search` 消息。当一个节点收到 `search` 消息时，若它还没有 parent，就将自己的 parent 变量设置为发送者的 id，记录自己是新的，并发送 `parent` 应答；若它已有 parent，则发送 `nonparent` 应答。

<!-- ===== PDF p4 ===== -->

**（续）**

> 当一个新节点收到了对其所有 `search` 消息的应答后，它向自己的 parent 发送 `done(b)` 消息，其中：若该节点至少收到一条 `parent` 应答，则 $b = \text{TRUE}$；否则 $b = \text{FALSE}$。`done` 消息沿树向上汇播（convergecast）；每个节点将其发送给 parent 的消息中的位（bit）$b$ 设置为自己从孩子处收到的各位的「或」（or）。
>
> 当节点 $v_0$ 收到所有孩子的 `done` 消息后，若其中任何一条消息的值为 1，则开始阶段 $d + 1$；否则停机。

**(b)** [7 points] 分析你的算法的时间与通信（communication）复杂度，并将它们与课堂上讲授的异步 BFS 算法的代价进行比较。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 时间复杂度为 $O(\text{diam}^2 \cdot d)$。每个阶段耗时 $O(\text{diam} \cdot d)$，共有 $O(\text{diam})$ 个阶段。
>
> 消息复杂度为 $O(E + \text{diam} \cdot n)$。每条边被 `search` 与 `parent` 消息沿每个方向各遍历一次。`ready` 与 `done` 消息只遍历树边，因此每阶段这类消息有 $O(n)$ 条。

> <span style="color:#2471a3;">**[solution]**</span> **解答（Solution）：** 如果这有启发作用的话，下面给出 (a) 部分的一些代码，我们认为它可能有助于更深入地理解本题。
>
> **进程 `v0`（Process v0）：**

```text
Process v0

State variables:
  for each v ∈ Γ(v0), send(v), a queue, initially (search)
  responded ⊆ Γ(v0), initially ∅
  newinfo, a Boolean, initially false

Transitions:
  input receive(search)v,v0 , v ∈ Γ(v0)
  Effect: add parent(false) to send(v)

  input receive(parent(true))v,v0 , v ∈ Γ(v0)
  Effect:
    responded := responded ∪{v}
    if responded = Γ(v0) then
      for each w ∈ Γ(v0), add ready to send(w)
      responded := ∅
```

<!-- ===== PDF p5 ===== -->

**（续）**

**进程 `v0`（续）：**

```text
      newinfo := false

  input receive(done(b))v,v0 , b a Boolean, v ∈ Γ(v0)
  Effect:
    responded := responded ∪{v}
    newinfo := newinfo ∨ b
    if responded = Γ(v0) and newinfo then
      for each w ∈ Γ(v0), add ready to send(w)
      responded := ∅
      newinfo := false

  output send(m)v,v0 , m a message, v ∈ Γ(v0)
  Precondition: m = head(send(v))
  Effect: remove head of send(v)
```

**进程 `u`（Process u，$u \neq v_0$）：**

```text
Process u, u ≠ v0

State variables:
  parent ∈ Γ(u) ∪ {⊥}, initially ⊥
  children ⊆ Γ(u), initially ∅
  newnode a Boolean, initially false
  for each v ∈ Γ(u), send(v), a queue, initially empty
  responded ⊆ Γ(u), initially ∅
  newinfo, a Boolean, initially false

Transitions:
  input receive(search)v,u, v ∈ Γ(u)
  Effect:
    if parent = ⊥ then
      parent := v
      newnode := true
      add parent(true) to send(v)
    else add parent(false) to send(v)

  input receive(parent(b))v,u, b a Boolean, v ∈ Γ(u)
  Effect:
    if b then
      children := children ∪{v}
      newinfo := true
    responded := responded ∪{v}
    if responded = Γ(u) then add done(newinfo) to send(parent)
```

<!-- ===== PDF p6 ===== -->

**（续）**

**进程 `u`（续）：**

```text
  input receive(ready)v,u, v ∈ Γ(u)
  Effect:
    if newnode then for each w ∈ Γ(u), add search to send(w)
    else for each w ∈ children, add ready to send(w)
    responded := ∅
    newinfo := false

  input receive(done(b))v,u, b a Boolean, v ∈ Γ(u)
  Effect:
    responded := responded ∪{v}
    newinfo := newinfo ∨ b
    if responded = children then add done(newinfo) to send(parent)

  output send(m)u,v, m a message, v ∈ Γ(u)
  Precondition: m = head(send(v))
  Effect: remove head of send(v)
```

> <span style="color:#1e8449;">**[note]**</span> 代码语义说明：`search` 消息用于探测新节点；`parent(b)` 为父/非父应答；`ready` 沿树向下广播以驱动新一轮探测；`done(b)` 沿树向上汇播，其中的位 $b$（即变量 `newinfo`）表示该节点所辖子树内是否出现了新节点。普通节点收到孩子的 `done` 后，会把孩子们传来的位逐层「或」起来，再转发给其 parent。根节点 `v0` 收到全部孩子的 `done` 消息后，若其中任一 $b$ 为真，则进入下一阶段；否则所有层都已构建完毕，算法停机。

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare（麻省理工学院开放课程）**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析）· Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms</span>
