<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**6.046J / 18.410J　算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#c0392b;">**Problem Set 2 解答（Problem Set 2 Solutions）**</span> <span style="color:#7f8c8d;">（2015 年 2 月 19 日，Massachusetts Institute of Technology；Profs. Erik Demaine, Srini Devadas, and Nancy Lynch）</span>

<span style="color:#7f8c8d;">本习题集于 2015 年 2 月 19 日（星期四）晚上 11:59 截止（This problem set is due at 11:59pm on Thursday, February 19, 2015）。</span>

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-1. 阅读 CLRS 第 30.1 与 30.2 节（Read CLRS, Sections 30.1 and 30.2）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-2. 习题 30-2.3（Exercise 30-2.3）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-3. 习题 30-2.4（Exercise 30-2.4）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-4. 阅读 CLRS 第 18 章（Read CLRS, Chapter 18）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-5. 习题 18.2-5（Exercise 18.2-5）。**

---

<span style="color:#2471a3;">**[exercise]**</span> **Exercise 2-6. 习题 18.3-2（Exercise 18.3-2）。**

---

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 2-1. 模式匹配（Pattern Matching）**</span> <span style="color:#7f8c8d;">[25 points]</span>

假设给定一个长度为 n 的 source string（源字符串）$S[0 \ldots n-1]$，由符号 a 与 b 组成。再假设给定一个长度为 $m \ll n$ 的 pattern string（模式字符串）$P[0 \ldots m-1]$，由符号 a、b 与 ∗ 组成，表示要在字符串 S 中查找的一个 pattern（模式）。符号 ∗ 是一个 "wild card"（通配符）符号，它匹配单个符号，即 a 或 b 之一。其余符号必须精确匹配。

问题是要输出一个有序的合法 "match positions"（匹配位置）列表 M，其中匹配位置是指 S 中使得模式 P 匹配子串 $S[j \ldots j + |P| - 1]$ 的位置 j。例如，若 $S = a\,b\,a\,b\,b\,a\,b$ 且 $P = a\,b\,*$，则输出应为 $M = [0, 2]$。

> <span style="color:#2471a3;">**[problem]**</span> **(a)** [4 points] 描述一个直接的、naïve（朴素的）算法来解决该问题。你的算法运行时间应为 $O(nm)$。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 可以显式地检查每一个可能的起始位置 $s \in \{0, 1, \ldots, n - m\}$，逐项检查 P 中的每一项从 s 到 $s + m - 1$ 是否匹配。
>
> ```
> NAIVE-ALGORITHM(S, P)
> 1  M = []
> 2  for s = 0 to n − m
> 3      valid = TRUE
> 4      for j = 0 to m − 1
> 5          if P[j] ≠ ∗ and P[j] ≠ S[s + j]
> 6              valid = FALSE
> 7      if valid
> 8          M.APPEND(s)
> 9  return M
> ```

---

<!-- ===== PDF p2 ===== -->

> <span style="color:#2471a3;">**[problem]**</span> **(b)** [12 points] 给出一个算法，通过把问题归约为 polynomial multiplication（多项式乘法）来解决它。具体而言，描述如何把字符串 S 与 P 转换为多项式，使得多项式的乘积能让你确定答案 M。请基于上面给出的 S 与 P 字符串示例，举例说明你对输入的多项式表示，以及你从乘积中确定输出的方式。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 我们用 1 表示 a，用 $-1$ 表示 b，用 0 表示 ∗。在下面的解答中，我们将使用这些表示来代替原来的符号。
>
> 注意，P 从位置 j（$0 \le j \le n - m$）起匹配 S，当且仅当对每一个 i（$0 \le i \le m - 1$），要么 $P[i] = 0$，要么 $S[j + i]P[i] = 1$。而这当且仅当
>
> ```math
> \sum_{i=0}^{m-1} S[j + i]\,P[i] = k,
> ```
>
> 其中 k 是 P 中非 ∗ 符号的个数。
>
> 我们希望把这些求和表示为某两个多项式乘积的系数。设 x 为一个变量。将 S 表示为
>
> ```math
> f_S(x) = S[0] + S[1]x + \cdots + S[n - 1]x^{n-1}.
> ```
>
> 将 P 表示为
>
> ```math
> f_P(x) = Q[0] + Q[1]x + \cdots + Q[m - 1]x^{m-1},
> ```
>
> 其中每个 $Q[i] = P[m - 1 - i]$。因此，我们在最后这个表示中反转了系数的顺序。
>
> 设 C 是 S 多项式与 P 多项式的乘积。那么 C 中 $x^{m-1+j}$ 的系数为
>
> ```math
> \sum_{i=0}^{m-1} S[m - 1 + j - i]\,Q[i],
> ```
>
> 它等于
>
> ```math
> \sum_{i=0}^{m-1} S[i + j]\,P[i].
> ```
>
> 这与上面的求和相同。为了得到输出 M，我们只需检查 C 的所有系数：当且仅当 $x^{m-1+j}$ 的系数等于 k（即 P 中非 ∗ 符号的总数）时，输出位置编号 j（$0 \le j \le n - m$）。我们按 j 递增的顺序输出这些位置，正如所要求的那样。
>
> 在上面的示例中，S 表示为
>
> ```math
> f_S(x) = 1 - x + x^2 - x^3 - x^4 + x^5 - x^6
> ```

---

<!-- ===== PDF p3 ===== -->

> 而 P 表示为
>
> ```math
> f_P(x) = -x + x^2.
> ```
>
> 乘积 C 为
>
> ```math
> -x + 2x^2 - 2x^3 + 2x^4 - 2x^6 + 2x^7 - x^8.
> ```
>
> 数 k 等于 2，因此我们关心的项是 $2x^2$、$2x^4$ 与 $2x^7$。这些项将给出 j = 0、2、5，但 5 被排除在外，因为我们只考虑 $j \le n - m = 7 - 3 = 4$。
>
> <span style="color:#2471a3;">**[problem]**</span> **(c)** [3 points] 假设你把第 (b) 部分的解答与 Lecture 3 中给出的、用于多项式乘法的 FFT algorithm（快速傅里叶变换算法）结合起来。所得字符串匹配问题解的时间复杂度是多少？
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 它是 $O(n \lg n)$。执行所需的 DFT 与 inverse DFT（逆 DFT）算法需要 $O(n \lg n)$ 时间，为 DFT 算法产生输入以及从输出中提取 M 需要 $O(n)$ 时间。
>
> <span style="color:#2471a3;">**[problem]**</span> **(d)** [6 points] 现在考虑同样的问题，但符号字母表（symbol alphabet）更大。具体而言，假设给定一条 DNA 链（DNA strand）的表示，即一个长度为 n、由符号 A、C、G 与 T 组成的字符串 $D[0 \ldots n-1]$；并且给定一个长度为 $m \ll n$、由符号 A、C、G、T 与 ∗ 组成的模式字符串 $P[0 \ldots m-1]$。
>
> 问题依然是输出一个有序的合法 "match positions"（匹配位置）列表 M，其中匹配位置是指 D 中使得模式 P 匹配子串 $D[j \ldots j + |P| - 1]$ 的位置 j。例如，若 $D = A\,C\,G\,A\,C\,C\,A\,T$ 且 $P = A\,C\,*\,A$，则输出应为 $M = [0, 3]$。
>
> 基于你对第 (b) 与 (c) 部分的解答，给出针对这一设定的高效算法。请在上面的示例上演示你的算法。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 使用一个归约（reduction）。将 A 编码为 a a，C 编码为 a b，G 编码为 b a，T 编码为 b b，∗ 编码为 ∗∗。使用我们之前的解答在所得字符串上求解该问题，得到一个位置列表 $M'$。
>
> 最终的输出列表 M 将只包含列表 $M'$ 中的偶数，且全部除以 2。
>
> 这将花费转换、随后在长度分别为 2n 与 2m 的数组上求解原问题所需的时间：
>
> ```math
> O(2n + 2n \lg(2n)) = O(n \lg n).
> ```

---

<!-- ===== PDF p4 ===== -->

<span style="color:#2471a3;">**[problem]**</span> <span style="color:#c0392b;">**Problem 2-2. 合并 B 树（Combining B-trees）**</span> <span style="color:#7f8c8d;">[25 points]</span>

考虑一个新的 B 树操作 COMBINE（合并）$(T_1, T_2, k)$。该操作以两棵具有相同 minimum degree（最小度数）参数 t 的 B 树 $T_1$ 与 $T_2$ 为输入，外加一个新键 k，k 不出现在 $T_1$ 或 $T_2$ 中。我们假设 $T_1$ 中的所有键严格小于 k，$T_2$ 中的所有键严格大于 k。COMBINE 操作产生一棵新的、具有相同最小度数 t 的 B 树 T，其键为 $T_1$ 中的键、$T_2$ 中的键以及 k。在此过程中，它会销毁原来的树 $T_1$ 与 $T_2$。

在本问题中，你将设计一个实现 COMBINE 操作的算法。你的算法运行时间应为 $O(|h_1 - h_2| + 1)$，其中 $h_1$ 与 $h_2$ 分别是树 $T_1$ 与 $T_2$ 的高度。在分析代价时，你应将 t 视为常数。

> <span style="color:#2471a3;">**[problem]**</span> **(a)** [5 points] 首先考虑该问题的特殊情形：假设 $h_1$ 等于 $h_2$。给出一个以常数时间（constant time）合并这两棵树的算法。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 为 T 构造一个新的根结点，它由 $T_1$ 与 $T_2$ 的根结点组成，$T_1$ 的根在左，k 插入在两个原根结点的键之间。然后，若所得根结点中键的个数至少为 $2t - 1$，则围绕其中位数（median）分裂该根结点，形成一个含有一个键的新根结点，以及两个各含至少 $t - 1$ 个键的子结点。

---

<!-- ===== PDF p5 ===== -->

> 相应算法如下：
>
> ```
> COMBINE(T1, T2, k)
> 1   T = T1
> 2   R = T.root
> 3   R2 = T2.root
> 4   i = R.n + 1
> 5   // 将两个根合并为一个（Merging the two roots into one）
> 6   T.k_i = k
> 7   for j = 1 to R2.n
> 8       i = i + 1
> 9       T.k_i = R2.k_j
> 10      T.c_i = R2.c_j
> 11  T.c_{i+1} = R2.c_{R2.n+1}
> 12  R.n = i + 1
> 13  if R.n ≥ 2t − 1
> 14      // 分裂结点（Splitting node）
> 15      mid = ⌈R.n/2⌉
> 16      dummy = ALLOCATE-NODE()
> 17      dummy.n = 1
> 18      dummy.k_1 = R.k_mid
> 19      // 分配子结点（Allocating Children）
> 20      C1 = ALLOCATE-NODE()
> 21      C1.n = mid − 1
> 22      C2 = ALLOCATE-NODE()
> 23      C2.n = R.n − mid
> 24      // 创建第一个子结点（Creating first child）
> 25      C1.c_1 = T.c_1
> 26      for j = 1 to C1.n
> 27          C1.k_j = T.k_j
> 28          C1.c_{j+1} = T.c_{j+1}
> 29      // 创建第二个子结点（Creating second child）
> 30      C2.c_1 = T.c_{mid+1}
> 31      for j = 1 to C2.n
> 32          C2.k_j = T.k_{j+mid}
> 33          C2.c_{j+1} = T.c_{j+mid+1}
> 34      dummy.c_1 = C1
> 35      dummy.c_2 = C2
> 36      T.root = dummy
> 37  return T
> ```
>
> <span style="color:#7f8c8d;">[说明] 原 PDF 第 27–28 行「创建第一个子结点」循环之后的第 32–33 行误写为 `C1.k_j = T.k_{j+mid}`、`C1.c_{j+1} = T.c_{j+mid+1}`（应写入第二个子结点 C2，否则 C2 的键/孩子不会被填充）。此处已按逻辑更正为 `C2`。</span>
>
> <span style="color:#2471a3;">**[problem]**</span> **(b)** [5 points] 考虑另一种特殊情形：假设 $h_1$ 恰好等于 $h_2 + 1$。给出一个常数时间算法来合并这两棵树。

---

<!-- ===== PDF p6 ===== -->

> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 将 k 附加（append）到 $T_1$ 的右子结点（right child node）的右端，再把 $T_2$ 的根附加到其后。显然这保持了有序次序。现在右子结点可能有从 $t + 1$ 到 $4t - 1$ 个键。若它有 $2t - 1$ 个或更多键，则围绕其中位数键分裂它。若这导致根结点有 $2t - 1$ 个键，则围绕其中位数键分裂之，从而为树增加一层。
>
> ```
> COMBINE(T1, T2, k)
> 1   x = T1.root
> 2   x = x.c_{x.n+1}
> 3   r = T2.root
> 4   n = x.n + 1 + r.n
> 5   // 将 k 附加到最右子结点（Append k to the rightmost child）
> 6   x.k_{x.n+1} = k
> 7   // 将 T2 的根附加到该结点（Append the root of T2 to the node）
> 8   x.c_{x.n+2} = r.c_1
> 9   for j = 1 to r.n
> 10      x.k_{x.n+j+2} = r.k_j
> 11      x.c_{x.n+j+3} = r.c_{j+1}
> 12  // 若结点过大则分裂（Split node if too big）
> 13  if n ≥ 2t − 1
> 14      p = x.parent
> 15      n = p.n
> 16      B-TREE-SPLIT-CHILD(p, n)
> ```
>
> <span style="color:#2471a3;">**[problem]**</span> **(c)** [5 points] 现在考虑更一般的情形，其中 $h_1$ 与 $h_2$ 是任意的。由于算法必须在如此之小的时间内工作，并且必须对任意高度都有效，第一步是开发一种新型的 augmented B-tree（增强 B 树）数据结构，其中每个结点 x 总是携带关于 x 之下子树高度（height）的信息。描述如何增强常用的 B 树 insertion（插入）与 deletion（删除）操作来维护这些信息，同时仍保持所有操作的渐近时间复杂度。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 通过为每个结点增加一个 height（高度）属性来增强该树。叶结点的高度为 0。对于内部结点，$\text{HEIGHT}(x) = \text{HEIGHT}(x.c_1) + 1$。
>
> 插入（Insertion）: 新结点在分裂（splitting）过程中加入。分裂中新分配的结点与原结点具有相同的高度。唯一另一个加入结点的时刻是根被分裂时。这是通过使根成为一个 dummy（哑）结点的子结点然后分裂它来完成的。新根的高度设为比旧根的高度大 1。
>
> 删除（Deletion）: 在删除中，除根之外没有结点被删除。由于高度值从叶开始索引，删除不影响结点的高度。

---

<!-- ===== PDF p7 ===== -->

> **（续）** 有了这些补充，插入与删除的渐近运行时间与之前相同，为 $O(\lg n)$。
>
> <span style="color:#2471a3;">**[problem]**</span> **(d)** [10 points] 现在给出一个在一般情形下合并两棵 B 树 $T_1$ 与 $T_2$ 的算法，其中 $h_1$ 与 $h_2$ 是任意的。你的算法运行时间应为 $O(|h_1 - h_2| + 1)$。
>
> <span style="color:#2471a3;">**[solution]**</span> **Solution（解答）:** 若 $|h_1 - h_2| < 2$，使用第 (a) 或 (b) 部分。否则，假设 $h_1 > h_2 + 1$（$h_2 > h_1 + 1$ 的情形对称处理）。设 x 为 $T_1$ 中处于高度 $h_2$ 的最右结点。在 x 的右端添加 k，并把 $T_2$ 的根附加到其后。现在结点 x 可能有从 $t + 1$ 到 $4t - 1$ 个键。若它有 $2t - 1$ 个或更多键，则围绕其中位数键分裂它。该分裂可能向上传播，甚至可能一直传到根。因此，时间复杂度线性依赖于高度差 $O(|h_1 - h_2|)$。
>
> ```
> COMBINE(T1, T2, k)
> 1   T = T1
> 2   h1 = T1.height
> 3   h2 = T2.height
> 4   x = T.root
> 5   // 移动到高度 h2 处的最右结点（Move to the rightmost node at level h2）
> 6   for j = 1 to h1 − h2
> 7       n = x.n
> 8       x = x.c_n
> 9   r = T2.root
> 10  n = x.n + 1 + r.n
> 11  // 将 k 附加到该结点（Append k to the node）
> 12  x.k_{x.n+1} = k
> 13  // 将 T2 的根附加到该结点（Append the root of T2 to the node）
> 14  x.c_{x.n+2} = r.c_1
> 15  for j = 1 to r.n
> 16      x.k_{x.n+j+2} = r.k_j
> 17      x.c_{x.n+j+3} = r.c_{j+1}
> 18  // 若结点过大则分裂（Split node if too big）
> 19  if n ≥ 2t − 1
> 20      p = x.parent
> 21      n = p.n
> 22      B-TREE-SPLIT-CHILD(p, n)
> 23  return T
> ```

---

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">6.046J / 18.410J Design and Analysis of Algorithms（算法设计与分析），Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">关于引用这些材料或我们的使用条款（Terms of Use）的信息，请访问：http://ocw.mit.edu/terms。</span>
