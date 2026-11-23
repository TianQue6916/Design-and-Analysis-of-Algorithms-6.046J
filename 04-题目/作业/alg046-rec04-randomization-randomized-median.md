<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Recitation 4（复习课 4）：Randomized Select and Randomized Quicksort（随机化选择与随机化快速排序）**</span> <span style="color:#7f8c8d;">（2015 年 2 月 27 日，Massachusetts Institute of Technology（麻省理工学院），Profs. Erik Demaine, Srini Devadas and Nancy Lynch）</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**1　Randomized Select（随机化选择）**</span>

RANDOMIZED-SELECT（随机化选择）算法能从任意数组（arbitrary array）中选出第 $k$ 个顺序统计量（order statistics）。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**1.1　Algorithm（算法）**</span>

RANDOMIZED-SELECT 算法的工作方式是：先按 RANDOMIZED-PARTITION（随机化划分）对数组 $A$ 进行划分（partition），然后在划分得到的某个子数组上递归（recurse）。

```text
RANDOMIZED-SELECT(A, p, r, i)
1  if p = r
2      then return A[p]
3  q ← RANDOMIZED-PARTITION(A, p, r)
4  k ← q − p + 1
5  if i ≤ k
6      then return RANDOMIZED-SELECT(A, p, q, i)
7      else return RANDOMIZED-SELECT(A, q + 1, r, i − k)

RANDOMIZED-PARTITION(A, p, r)
1  i ← RANDOM(p, r)
2  exchange A[p] ↔ A[i]
3  return PARTITION(A, p, r)
```

上述两个算法均与 CLRS 中的一致。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**1.2　Analysis of Running Time（运行时间分析）**</span>

设 $T(n)$ 为 Randomized Select 的期望运行时间（expected running time）。我们希望为它写出一个递推式（recursion）。

记 $E_i$ 为「随机划分把数组分成大小分别为 $i$ 和 $n - i$ 的两个数组」这一事件（event）。于是我们看到

```math
T(n) \le n + \sum_{i=0}^{n-1} \Pr(E_i) \left( \max\left(T(i),\, T(n-i)\right) \right) \tag{1}
```

> <span style="color:#1e8449;">**[note]** </span> 这里对期望运行时间取上界时引入了 $\max\left(T(i), T(n-i)\right)$：即便随机划分恰好把数组切得很不均衡，我们仍假设递归发生在**较大**的那一块子数组上，因此所求的上界涵盖了所有可能情形，故式中为 $\le$。RANDOMIZED-SELECT 出自 CLRS 第 9.2 节，其期望运行时间为 $O(n)$；与之相对，CLRS 第 9.3 节借助「中位数的中位数」（median-of-medians）构造的确定性 SELECT 算法能在最坏情形下同样达到线性时间。注意（1）式中「按较大块递归」的假设把问题归约到最坏分支上，这正是下面代入法（substitution）证明能够闭合的关键。

<!-- ===== PDF p2 ===== -->

**（续）**

式中取 $\max$ 的含义是：我们假设递归发生在较大的那个子数组（subarray）上（正因如此才会出现小于等于号）。

为简单起见，我们假设 $n$ 为偶数。注意到 $\max(T(i), T(n-i))$ 与 $\max(T(n-i), T(i))$ 总是相同的，这让我们可以把不等式链（chain of inequalities）进一步延展为

```math
T(n) \le n + 2\sum_{i=0}^{n/2-1} \Pr(E_i) \left( \max\left(T(i),\, T(n-i)\right) \right) \tag{2}
```

另外，由于划分元素（partition element）是随机选取的，数组被划分成大小为 $0, 1, \cdots, n-1$ 的各种情形等可能，因此对所有 $i$ 都有 $\Pr(E_i) = 1/n$。于是我们得到

```math
T(n) \le n + \frac{2}{n} \sum_{i=0}^{n/2-1} \left( \max\left(T(i),\, T(n-i)\right) \right) \tag{3}
```

我们不会通过代入法来证明 $T(n) = O(n)$。

> <span style="color:#1e8449;">**[note]** </span> 原文此处写作 "will not show"（「将**不**会证明」），但紧随其后的正是 Theorem 1 及其完整的 substitution（代入法）证明，二者互相矛盾，显然这是原文的印刷笔误，应为 "will now show"（「我们现在就来证明」）。这里照实翻译，特此说明。

<span style="color:#2471a3;">**[theorem]**</span> <span style="color:#00838f;">**Theorem 1（定理 1）**</span> 设 $T(n)$ 表示 randomized select 的期望运行时间，则 $T(n) = O(n)$。

<span style="color:#2471a3;">**[proof]**</span> **Proof（证明）.** 我们用代入法（method of substitution）来证明。假设 $T(n) \le cn$，并检验它是否成立。

我们首先必须检验基本情况（base case）。这一点是显然的：因为对某个很小的常数 $n'$，$T(n')$ 是一个常数。

现在来检验归纳情况（inductive case）。假设对所有 $k < n$ 都有 $T(k) \le ck$，接下来要证明 $T(n) \le cn$：

```math
T(n) \le n + \frac{2}{n}\sum_{i=0}^{n/2-1} \left( \max\left(T(i),\, T(n-i)\right) \right) \le n + \frac{2}{n}\sum_{i=0}^{n/2-1} \left( \max\left(ci,\, c(n-i)\right) \right) \tag{4}
```

我们注意到它等同于

```math
n + \frac{2}{n}\sum_{i=n/2}^{n-1} ci \tag{5}
```

项 $\frac{2}{n}\sum_{i=n/2}^{n-1}(ci)$ 与 $\frac{2c}{n}\sum_{i=n/2}^{n-1} i$ 相同。于是我们得到

```math
T(n) \le n + c\left( \frac{2}{n}\sum_{i=n/2}^{n-1} i \right) \le n + c(3n/4) = n\left(1 + \frac{3c}{4}\right) \tag{6}
```

因此，若取 $c = 4$（这也适用于 $T(1) \le 4$ 的情形），我们得到

```math
T(n) \le n\left(1 + \frac{3\cdot 4}{4}\right) = n(1+3) = 4n \tag{7}
```

正如我们所期望的。

> <span style="color:#1e8449;">**[note]** </span> 第 (5) 式的由来：对 $i \in [0, n/2-1]$ 有 $\max(i, n-i) = n-i$，即 $\max(ci, c(n-i)) = c(n-i)$，所以 (4) 式的求和对象就是「较大的半边」那些下标；严格地说 $\{n-i : i \in [0, n/2-1]\} = \{n/2+1, \ldots, n\}$。源 PDF 此处写作 $\sum_{i=n/2}^{n-1}$，相当于把求和端点各平移一位（漏掉 $n$、补进 $n/2$），属于无害的 off-by-one——两端只差一个常数项，渐近结论不受影响。第 (6) 式 $\frac{2}{n}\sum_{i=n/2}^{n-1} i \approx \frac{2}{n}\cdot \frac{3n^2}{8} = \frac{3n}{4}$（区间 $[n/2, n-1]$ 共 $n/2$ 项、平均约为 $3n/4$），取 $c = 4$ 时 $T(n) \le n(1 + 3c/4) = 4n$，归纳「恰好」闭合到线性界，也顺带覆盖了 $T(1) \le 4$ 的基本情况。

<!-- ===== PDF p3 ===== -->

<span style="color:#2471a3;">**[section]**</span> <span style="color:#c0392b;">**2　Randomized Quicksort（随机化快速排序）**</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.1　Algorithm（算法）**</span>

RANDOMIZED-QUICKSORT 算法的工作方式是：先对数组 $A$ 进行划分，然后递归地对两个划分部分分别排序。

```text
RANDOMIZED-QUICKSORT(A, p, r)
1  if p < r
2      then q ← RANDOMIZED-PARTITION(A, p, r)
3           RANDOMIZED-QUICKSORT(A, p, q − 1)
4           RANDOMIZED-QUICKSORT(A, q + 1, r)
```

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**2.2　Analysis of Running Time（运行时间分析）**</span>

设 $T(n)$ 为 Randomized Quicksort 的期望运行时间。记 $E_i$ 为「数组被划分成大小分别为 $i$ 和 $n - i - 1$ 的两个数组」这一事件。枢轴值（pivot value）不属于任何一个划分部分。于是我们有

```math
T(n) \le \sum_{i=0}^{n-1} \Pr(E_i)\left( T(i) + T(n-i-1) + \Theta(n) \right) \tag{8}
```

由于对所有 $i$ 都有 $\Pr(E_i) = 1/n$，我们有

```math
T(n) \le \frac{1}{n}\sum_{i=0}^{n-1}\left( T(i) + T(n-i-1) + \Theta(n) \right) \tag{9}
```

```math
T(n) \le \frac{2}{n}\sum_{i=0}^{n-1} T(i) + \Theta(n) \tag{10}
```

> <span style="color:#1e8449;">**[mathtip]** </span> 从 (9) 到 (10) 用到了对称性：令 $j = n - i - 1$，则 $\sum_{i=0}^{n-1} T(n-i-1) = \sum_{j=0}^{n-1} T(j)$，两个求和恰好相等，合并后得到 $2\sum_{i=0}^{n-1} T(i)$；同时 $\Theta(n)$ 这一项被求和 $n$ 次得到 $\Theta(n^2)$，再除以 $n$ 仍是 $\Theta(n)$。

与 Randomized Select 相同，我们用归纳法（induction）证明 $T(n) = \Theta(n \log n)$。假设对某个常数 $c > 0$ 有 $T(n) \le cn\log n$。注意到如下事实：

```math
\sum_{i=0}^{n-1} i\log i \le \frac{1}{2}n^2\log n - \frac{1}{8}n^2 \tag{11}
```

于是对于归纳步骤（inductive step），我们有

```math
T(n) \le \frac{2c}{n}\sum_{i=0}^{n-1} i\log i + \Theta(n) \tag{12}
```

```math
T(n) \le \frac{2c}{n}\left( \frac{1}{2}n^2\log n - \frac{1}{8}n^2 \right) + \Theta(n) \tag{13}
```

```math
T(n) \le cn\log n - \left( \frac{cn}{4} - \Theta(n) \right) \tag{14}
```

当 $c$ 取得足够大时，$T(n) \le cn\log n$。

> <span style="color:#1e8449;">**[note]** </span> 不等式 (11) $\sum_{i=0}^{n-1} i\log i \le \frac{1}{2}n^2\log n - \frac{1}{8}n^2$ 是随机化快速排序代入法证明中的一条标准求和界（可用积分近似验证：$\sum_{i=0}^{n-1} i\log i \approx \int_0^n x\log x\, dx = \frac{1}{2}n^2\log n - O(n^2)$，末端的 $- \frac{1}{8}n^2$ 正是离散求和相对积分的修正项）。由于无论以 2 为底还是取自然对数都不影响 $\Theta$ 阶，这里统一写作 $\log$。在式 (14) 中，只要把常数 $c$ 选得足够大，使得 $\frac{cn}{4} - \Theta(n) \ge 0$，归纳即可闭合，得出期望运行时间 $\Theta(n \log n)$。这也说明：即便输入已经有序（最坏情形），随机化快速排序的**期望**时间依然是 $O(n \log n)$——这正是随机化（randomization）带来的保证。

<!-- ===== PDF p4 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**　http://ocw.mit.edu　6.046J / 18.410J Design and Analysis of Algorithms · Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">如需了解如何引用这些材料，或查阅我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
