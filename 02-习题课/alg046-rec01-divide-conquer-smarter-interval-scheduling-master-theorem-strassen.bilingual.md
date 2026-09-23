<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 1（复习课 1）：Matrix Multiplication and the Master Theorem（矩阵乘法与主定理）**</span> <span style="color:#7f8c8d;">（2015 年 2 月 6 日 · 麻省理工学院（Massachusetts Institute of Technology）· 6.046J/18.410J）</span>

<span style="color:#7f8c8d;">授课教授（Profs.）：Erik Demaine、Srini Devadas 与 Nancy Lynch</span>

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Weighted Interval Scheduling（带权区间调度）**</span>

考虑请求（requests）$1, \ldots, n$。对请求 $i$，$s(i)$ 是开始时间（start time），$f(i)$ 是结束时间（finish time），且满足 $s(i) < f(i)$。

若两个请求 $i$ 与 $j$ 互不重叠（don't overlap），即 $f(i) \le s(j)$ 或 $f(j) \le s(i)$，则称它们是兼容的（compatible）。每个请求 $i$ 都有一个权重（weight）$w(i)$。目标（goal）：调度（schedule）一个兼容请求的子集，使其总权重最大（maximum weight）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**1.1　The $O(n \log n)$ Dynamic Programming Solution（$O(n \log n)$ 动态规划解法）**</span>

按最早结束时间（earliest finish time）顺序对请求排序：

```math
f(1) \le f(2) \le \cdots \le f(n)
```

**定义（Definition）：** 对区间 $j$，$p(j)$ 是最大的下标（largest index）$i < j$，使得请求 $i$ 与 $j$ 兼容。

数组 $M[0 \ldots n]$ 保存最优解（optimal solution）的值。若只考虑请求 $1$ 到 $k$，则 $M[k]$ 是最大权重。

```text
M[0] = 0
for j = 1 to n
    M[j] = max(w(j) + M[p(j)], M[j - 1])
```

一旦得到 $M$，就可以通过回溯（tracing it back）在 $O(n)$ 时间内推导出最优解。按最早结束时间排序需要 $O(n \log n)$ 时间，因此整个算法共需 $O(n \log n)$ 时间。

> <span style="color:#1e8449;">**[note]** </span> 当所有请求权重相同（或未加权）时，区间调度可以用贪心算法（greedy algorithm）按最早结束时间排序，在 $O(n \log n)$ 内求解；但请求一旦带有不同权重，简单的贪心选择就不再保证最优，必须改用动态规划（dynamic programming）。递推式 $M[j] = \max(w(j) + M[p(j)],\ M[j-1])$ 刻画了「选或不选」区间 $j$ 的二分决策：若选 $j$，收益为 $w(j)$ 加上所有与它兼容的较早区间的最优值 $M[p(j)]$；若不选，则等于 $M[j-1]$。由于 $p(j)$ 是「最后一个与 $j$ 兼容的区间」，可在按结束时间排序后用二分查找（binary search）在 $O(\log n)$ 时间内逐个求出，从而整个算法的复杂度为 $O(n \log n)$。另外，原文第 11 行把 request 误拼为 requet，属无关紧要的笔误。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　Strassen（Strassen 算法）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.1　Matrix Multiplication（矩阵乘法）**</span>

取矩阵 $A$、$B$，把 $A$ 的第 $i$ 行与 $B$ 的第 $j$ 列相乘，填入结果矩阵 $C$ 的 $(i, j)$ 项（entry）。对方阵（square matrices）而言，运行时间是 $\Theta(n^3)$，其中 $n$ 是每个矩阵的维数（dimension）。

<!-- ===== PDF p2 ===== -->

**（续）**

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2　The Strassen Algorithm（Strassen 算法）**</span>

- 分治法（Divide and Conquer）的一个强大的早期应用（powerful early application）
- 并非最快的矩阵乘法（尽管在发现之时它是最快的）
  - Don Coppersmith、Shmuel Winograd、Andrew Stothers 与 Vassilevska Williams 对当前最快的矩阵乘法方法做出了贡献。详见 http://en.wikipedia.org/wiki/Coppersmith-Winograd_algorithm。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2.1　Steps（步骤）**</span>

- 把 $A$、$B$ 各自补成 $2^k \times 2^k$ 的矩阵，方法是用 $0$ 填充剩余的行/列：
  - 为什么可以这样做（Why can you do this）？
    - 每个维数增长的倍数都小于 2（each dimension increases by less than a factor of 2）
    - 即使采用传统的 $\Theta(n^3)$ 矩阵乘法，这也使运行时间增加的倍数始终小于 8，与 $N$ 的大小无关；而且讨论复杂度时，常数因子（constant factors）总是被忽略。
- 把 $A$、$B$、$C$（$C$ 的元素未知，但维数相同）各自划分成 4 个维数为 $2^{k-1}$ 的子矩阵（submatrices）
- 可以看到，$C$ 的 4 个子矩阵可以通过「把子矩阵当作元素」、对 $A$ 与 $B$ 做标准矩阵乘法求出：

```math
A = \begin{pmatrix} A_{1,1} & A_{1,2} \\ A_{2,1} & A_{2,2} \end{pmatrix},\qquad
B = \begin{pmatrix} B_{1,1} & B_{1,2} \\ B_{2,1} & B_{2,2} \end{pmatrix},\qquad
C = \begin{pmatrix} C_{1,1} & C_{1,2} \\ C_{2,1} & C_{2,2} \end{pmatrix}
```

```math
\begin{aligned}
C_{1,1} &= A_{1,1}B_{1,1} + A_{1,2}B_{2,1}\\
C_{1,2} &= A_{1,1}B_{1,2} + A_{1,2}B_{2,2}\\
C_{2,1} &= A_{2,1}B_{1,1} + A_{2,2}B_{2,1}\\
C_{2,2} &= A_{2,1}B_{1,2} + A_{2,2}B_{2,2}
\end{aligned}
```

- 优化的来源是这样一个事实：矩阵加法远比矩阵乘法简单（矩阵加法为 $\Theta(n^2)$，矩阵乘法为 $\Theta(n^3)$）

<!-- ===== PDF p3 ===== -->

**（续）**

定义（Define）

```math
\begin{aligned}
M_1 &= (A_{1,1} + A_{2,2})(B_{1,1} + B_{2,2})\\
M_2 &= (A_{2,1} + A_{2,2})B_{1,1}\\
M_3 &= A_{1,1}(B_{1,2} - B_{2,2})\\
M_4 &= A_{2,2}(B_{2,1} - B_{1,1})\\
M_5 &= (A_{1,1} + A_{1,2})B_{2,2}\\
M_6 &= (A_{2,1} - A_{1,1})(B_{1,1} + B_{1,2})\\
M_7 &= (A_{1,2} - A_{2,2})(B_{2,1} + B_{2,2})
\end{aligned}
```

于是

```math
\begin{aligned}
C_{1,1} &= M_1 + M_4 - M_5 + M_7 = A_{1,1}B_{1,1} + A_{1,2}B_{2,1}\\
C_{1,2} &= M_3 + M_5 = A_{1,1}B_{1,2} + A_{1,2}B_{2,2}\\
C_{2,1} &= M_2 + M_4 = A_{2,1}B_{1,1} + A_{2,2}B_{2,1}\\
C_{2,2} &= M_1 - M_2 + M_3 + M_6 = A_{2,1}B_{1,2} + A_{2,2}B_{2,2}
\end{aligned}
```

正确性证明（proof of correctness）直接由算术运算（arithmetic）得出。

我们可以用 $A_{1,1}$ 等的等尺寸（equally-sized）子矩阵递归地计算上面每一个子矩阵，这正是我们需要维数为 $2^n$（而不仅是偶数维数）的原因。

得到 $C$ 之后，剥去（strip out）与 $A$、$B$ 中补零部分对应的全 $0$ 行/列。

- 每一层递归步骤需要 7 次乘法和 18 次加法，而非 8 次乘法
- 可以看出，对小矩阵而言，这比 8 次乘法更低效。对一个被拆成 4 个 $1 \times 1$ 元素矩阵的 $2 \times 2$ 元素矩阵而言，工作量超过了三倍！

运行时间（Running time）：

```math
T(n) = \Theta\left(n^{\log_2(7)}\right) \approx \Theta\left(n^{2.8074}\right)
```

这个值是怎么得来的？（接下来讲）

> <span style="color:#1e8449;">**[note]** </span> Strassen 算法发表于 1969 年，是第一个把矩阵乘法运行时间降到 $\Theta(n^3)$ 以下的分治算法，其关键思想是「以加换乘」：用 7 次乘法（而非朴素的 8 次）配合 18 次加法组合出四个子矩阵的结果。由于矩阵加法只需 $\Theta(n^2)$ 时间，而乘法子问题递归下去后运行时间按指数 $\log_2 7 \approx 2.807$ 增长，当 $n$ 足够大时「减少乘法次数」的收益将压倒「增加加法开销」的代价。把矩阵补零扩展到 $2^k \times 2^k$ 是为了保证每次划分得到的子矩阵维数恰好减半；补零后每个维数至多翻倍，对 Strassen 而言运行时间至多乘以 $2^{\log_2 7} \approx 7$ 倍，仍只是一个常数因子，不改变渐近复杂度。注意讲义中「2-element matrix」指 $2 \times 2$ 的分块矩阵；对极小规模的实际矩阵，Strassen 往往因递归加法过多而比朴素算法更慢，工程实现通常在矩阵较小时回退到朴素乘法。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**3　Master Theorem（主定理）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**3.1　General Use（一般用法）**</span>

递推式（recurrence）的一般形式：

```math
T(n) = aT(n/b) + f(n)
```

- 若 $f(n)$ 多项式地小于（polynomially less than）$n^{\log_b(a)}$：$T(n) = \Theta\left(n^{\log_b(a)}\right)$
- 若 $f(n) = \Theta\left(n^{\log_b(a)} \log^k(n)\right)$，其中 $k \ge 0$：$T(n) = \Theta(f(n)\log(n)) = \Theta\left(n^{\log_b(a)} \log^{k+1}(n)\right)$
- 若 $n^{\log_b(a)}$ 多项式地小于 $f(n)$，且存在某个常数 $c < 1$ 使 $af(n/b) \le cf(n)$ 对所有足够大的 $n$ 成立：$T(n) = \Theta(f(n))$

若 $n^{\log_b(a)}$ 比 $f(n)$ 大，但并非多项式地大（not polynomially greater），则不能使用主定理（Master Theorem）来确定精确界（precise bound）。

（例如 $T(n) = 2T(n/2) + \Theta(n/\log n)$）

> <span style="color:#1e8449;">**[note]** </span> 主定理适用于形如 $T(n) = aT(n/b) + f(n)$ 的递推式，其中 $a \ge 1$、$b > 1$ 为常数。这里「多项式地小/大（polynomially less/greater）」指的是存在某个 $\varepsilon > 0$ 使得 $f(n) = O\left(n^{\log_b a - \varepsilon}\right)$（情形 1）或 $f(n) = \Omega\left(n^{\log_b a + \varepsilon}\right)$（情形 3），而不只是渐近地小/大；正因如此，$T(n) = 2T(n/2) + \Theta(n/\log n)$ 这类递推式（$f(n)$ 与 $n^{\log_b a}$ 仅差一个 log 因子）落在情形 1 与情形 2 之间的缝隙中，主定理无法给出精确界，需要改用递归树（recursion tree）等方法。情形 2 中 $f(n) = \Theta\left(n^{\log_b a} \log^k n\right)$，递归树每一层的工作量大致相同，树高为 $\log_b n$，故总工作量比 $f(n)$ 多出一个 log 因子。情形 3 的正则条件（regularity condition）$af(n/b) \le cf(n)$（$c < 1$）保证除顶层外各层工作量按几何级数衰减，从而使 $T(n) = \Theta(f(n))$。

<!-- ===== PDF p4 ===== -->

**（续）**

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**3.2　Strassen Runtime（Strassen 运行时间）**</span>

现在考虑 Strassen 算法。它每轮迭代执行 7 次乘法和 18 次加法/减法（additions/subtractions）。加法直接执行；乘法则递归地使用 Strassen 算法完成。

在每一层递归中，我们把矩阵分成 4 部分；但请记住，我们是以矩阵的维数（dimension）来衡量运行时间的，而不是元素的总个数。

于是递推式变为

```math
T(n) = 7T(n/2) + 18\Theta(n^2) = 7T(n/2) + \Theta(n^2)
```

然后我们可以考察主定理：

$n^{\log_2(7)}$ 多项式地大于 $n^2$。

因此 $\Theta\left(n^{\log_2(7)}\right)$ 就是该递推式的解。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**3.3　Median Finding（中位数查找）**</span>

证明递推式

```math
T(n) = T\left(\frac{n}{5}\right) + T\left(\frac{7n}{10} + 6\right) + \Theta(n)
```

的解为 $T(n) = \Theta(n)$。

**证明（Proof）：** 我们使用代入法（substitution method）（细节可参见 CLRS 教材）来解该递推式。我们首先猜测答案的形式为 $O(n)$，并试图证明对某个值 $d$ 有 $T(n) \le dn$。我们先假设该界对所有满足 $m < n$ 的正整数 $m$ 成立，因此当 $n$ 足够大时，它对 $T(n/5)$ 与 $T(7n/10 + 6)$ 也成立。代入递推式得到

```math
\begin{aligned}
T(n) &= T\left(\frac{n}{5}\right) + T\left(\frac{7n}{10} + 6\right) + cn \tag{1}\\
&\le d\left(\frac{n}{5} + 1\right) + d\left(\frac{7n}{10} + 6\right) + cn \tag{2}\\
&= \frac{9}{10}dn + 7d + cn \tag{3}\\
&\le dn \tag{4}
\end{aligned}
```

最后一个不等式在 $d > 10c$ 且 $n$ 足够大时成立。

> <span style="color:#1e8449;">**[note]** </span> 该递推式来自确定性线性时间选择算法（SELECT，见 CLRS 第 9 章）：把 $n$ 个元素每 5 个一组分组并取每组的中位数，再递归求出这些中位数的中位数作为划分基准（pivot）。这个基准能保证划分后每一侧都至少剔除约 $3n/10$ 个元素，于是两个子问题规模分别为 $n/5$（递归求基准）与 $7n/10 + 6$（划分子数组），配合线性时间的划分过程即得到上述递推式。代入证明的关键在于递归项系数之和 $1/5 + 7/10 = 9/10 < 1$：只要小于 1，递归树各层的工作量就按几何级数递减，总工作量由顶层的 $\Theta(n)$ 决定，从而 $T(n) = \Theta(n)$。常数项 $7d$ 只是稍大一些的启动开销，随着 $n$ 增大会被 $\left(\frac{1}{10}d - c\right)n$ 的正增长所吸收，这正对应条件「$d > 10c$ 且 $n$ 足够大」。讲义为行文简洁省略了取整记号，严格形式应为 $T(\lceil n/5 \rceil)$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**3.4　Extra Details（补充细节）**</span>

用递推式 $T(n) = 4T(n/2) + \Theta(n^2)$ 画一棵递归树（recursion tree），将会说明为什么当 $f(n)$ 并非多项式地大于 $n^{\log_b(a)}$ 时要使用 log 因子。想一想必须完成的总工作量（total amount of work）。

> **Feel free to examine** $T(n) = 4T(n/2) + \Theta(n^2 \log(n))$ **to see why the solution must be** $\Theta(n^2 \log^2(n))$ **instead of just** $\Theta(n^2 \log(n))$。

> <span style="color:#1e8449;">**[mathtip]** </span> 上面一句是讲义留给学生自行推导的练习（可自由考察……）：对 $T(n) = 4T(n/2) + \Theta(n^2)$，递归树每一层的工作量都是 $\Theta(n^2)$，树高为 $\log_2 n$，故总工作量为 $\Theta(n^2 \log n)$，即主定理情形 2 中 $k = 0$ 的特例。而对 $T(n) = 4T(n/2) + \Theta(n^2 \log n)$，第 $i$ 层的工作量约为 $\Theta\left(n^2(\log n - i)\right)$，从根到叶逐层累加得到一个等差数列求和，结果为 $\Theta(n^2 \log^2 n)$——这正解释了为什么情形 2 中当 $k \ge 0$ 时会额外多出一个 log 因子。由此可总结递归树方法的普遍规律：各层工作量大致相同时累积 log 因子；逐层递减时结果由根层决定；逐层递增时结果由叶子层决定。

<!-- ===== PDF p5 ===== -->

<span style="color:#7f8c8d;">**MIT 开放课程（MIT OpenCourseWare）**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）· 2015 春季学期（Spring 2015）</span>

<span style="color:#7f8c8d;">如需了解如何引用这些材料或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
