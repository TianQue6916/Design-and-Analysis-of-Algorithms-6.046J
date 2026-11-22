<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b;">**讲次 3（Lecture 3）：分治——快速傅里叶变换（Divide and Conquer: Fast Fourier Transform）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- 多项式操作 vs 表示（Polynomial Operations vs. Representations）
- 分治算法（Divide and Conquer Algorithm）
- 塌缩采样集 / 单位根（Collapsing Samples / Roots of Unity）
- FFT、IFFT 与多项式乘法（FFT, IFFT, and Polynomial Multiplication）

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**多项式操作与表示（Polynomial operations and representation）**</span>

多项式（polynomial）$A(x)$ 可以写成以下形式：

```math
A(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_{n-1} x^{n-1} = \sum_{k=0}^{n-1} a_k x^k = (a_0, a_1, a_2, \ldots, a_{n-1})
```

其中最后的 $(a_0, a_1, \ldots, a_{n-1})$ 是**系数向量**（coefficient vector）。$A$ 的次数（degree）为 $n-1$。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**多项式上的操作（Operations on polynomials）**</span>

多项式有三种主要操作。

1. **求值（Evaluation）**：给定多项式 $A(x)$ 和一个数 $x_0$，计算 $A(x_0)$。这可以用 $O(n)$ 次算术操作在 $O(n)$ 时间内完成，方法是 **Horner 法则（Horner's rule）**。
   - **Horner 法则**：$A(x) = a_0 + x\big(a_1 + x\big(a_2 + \cdots x(a_{n-1}) \cdots \big)\big)$。每一步先做一次求和，再乘以 $x$，然后进入下一步。因此需要 $O(n)$ 次乘法与 $O(n)$ 次加法。
2. **加法（Addition）**：给定两个多项式 $A(x)$ 与 $B(x)$，计算 $C(x) = A(x) + B(x)$（对所有 $x$）。这用基本算术即可在 $O(n)$ 时间内完成，因为 $c_k = a_k + b_k$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Horner 法则（也称 Horner 方法 / 嵌套乘法 nested multiplication）把求值从「先算各次幂再求和」的 $O(n)$ 次乘法 + $O(n)$ 次加法，降到恰好 $n-1$ 次乘法 + $n-1$ 次加法——它本质上是用**因子分解消去重复幂**：$x^k$ 的幂不是每次重新算，而是每步乘以 $x$ 复用。这个「把重复计算折叠进循环」的思路是你在 CSAPP 第五章循环优化（loop optimization）里反复见到的强度削减（strength reduction）雏形：硬件、编译器与算法都在做同一件事——识别并消除重复计算。更深一层，Horner 法则与 100B 实分析里的**多项式除法/综合除法（synthetic division）**同构：每一步 $a_k + x(\cdots)$ 恰好等价于「除以 $(x - x_0)$ 求商与余数」，余数正是 $A(x_0)$，这是多项式余数定理（remainder theorem）的算法化。

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b;">**多项式操作（续）**</span>

3. **乘法（Multiplication）**：给定两个多项式 $A(x)$ 与 $B(x)$，计算 $C(x) = A(x) \cdot B(x)$（对所有 $x$）。则

```math
c_k = \sum_{j=0}^{k} a_j b_{k-j} \quad \text{对} \quad 0 \le k \le 2(n-1)
```

因为结果多项式的次数是 $A$ 或 $B$ 次数的两倍。这个乘法等价于向量 $A$ 与 $\mathrm{reverse}(B)$ 的**卷积（convolution）**。卷积是「所有相对位移的内积」——这个操作在数字信号处理（digital signal processing, DSP）里同样用于平滑（smoothing）等场景。

- 朴素多项式乘法（naive polynomial multiplication）需要 $O(n^2)$。
- 通过 Strassen 式（Strassen-like）的分治技巧可以达到 $O(n^{\lg 3})$，甚至 $O(n^{1+\varepsilon})$（对任意 $\varepsilon > 0$）。
- 今天，我们将通过**快速傅里叶变换（Fast Fourier Transform）**在 $O(n \lg n)$ 时间内计算乘积！

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 多项式乘法与卷积的等价是本节最核心的洞察：$c_k = \sum_{j=0}^{k} a_j b_{k-j}$ 中下标 $j$ 与 $k-j$ 的「此消彼长」正是卷积的定义。你在 6.042J 学过的卷积、在信号处理里的平滑/滤波、在概率论里即将学到的**两个独立随机变量之和的分布**（$P_{X+Y}(z) = \sum_x P_X(x) P_Y(z-x)$，即概率质量函数的卷积）——它们全都是同一个代数结构。傅里叶变换之所以无处不在，正因为**卷积在频域里退化成逐点乘法**（卷积定理 convolution theorem），而把「滑动求和」变成「逐点相乘」正是复杂度从 $O(n^2)$ 降到 $O(n \lg n)$ 的物理根源。这个「换一个域，把难操作变简单」的思想，后面会在 LEC 15 线性规划、密码学（离散对数）里反复出现。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**多项式的表示（Representations of polynomials）**</span>

首先考虑多项式的不同表示（representation），以及每种表示下完成各操作所需的时间。

主要有 3 种表示需要考虑。

1. **带单项式基（monomial basis）的系数向量**（coefficient vector）
2. **根 + 缩放项（Roots and a scale term）**
   - $A(x) = (x - r_0) \cdot (x - r_1) \cdots (x - r_{n-1}) \cdot c$
   - 然而，只用基本算术运算与 $k$ 次根运算，不可能找到精确的根（exact roots）。此外，在这种表示下加法极其困难，甚至不可能。乘法只需把根拼接（concatenate）起来，求值则可在 $O(n)$ 内完成。
3. **采样（Samples）**：$(x_0, y_0), (x_1, y_1), \ldots, (x_{n-1}, y_{n-1})$，满足 $A(x_i) = y_i$（对所有 $i$），且每个 $x_i$ 互不相同。根据 **Lagrange 插值**与**代数基本定理（Fundamental Theorem of Algebra）**，这些采样点唯一确定一个 $n-1$ 次多项式 $A$。加法与乘法可以通过对 $y_i$ 逐项相加/相乘完成（假设 $x_i$ 匹配）；但求值需要插值（interpolation）。

下表给出各表示与各操作的运行时间（算法 vs 表示）：

> <span style="color:#7f8c8d;">[表格说明] 下表为讲义原文中的「Algorithms vs. Representations」矩阵（行 = 操作，列 = 表示）。</span>

| 操作 \ 表示 | 系数（Coefficients） | 根（Roots） | 采样（Samples） |
|:---:|:---:|:---:|:---:|
| 求值（Evaluation） | $O(n)$ | $O(n)$ | $O(n^2)$ |
| 加法（Addition） | $O(n)$ | $\infty$ | $O(n)$ |
| 乘法（Multiplication） | $O(n^2)$ | $O(n)$ | $O(n)$ |

我们通过在 $O(n \lg n)$ 时间内**在系数与采样之间互相转换**，来综合每种表示的优势。

**怎么做？** 考虑矩阵形式的多项式。

令 $V$ 为 **Vandermonde 矩阵**（Vandermonde matrix），其元素 $v_{jk} = x_j^k$。则

```math
V \cdot A = Y
```

即系数向量 $A$ 乘 Vandermonde 矩阵 $V$ 得到采样值向量 $Y$。于是我们可用矩阵向量积 $V \cdot A$ 在系数与采样之间转换，这等价于求值，耗时 $O(n^2)$。

类似地，可以从采样转回系数：求解 $V \backslash Y$（MATLAB 记法）。这需要 $O(n^3)$（高斯消元 Gaussian elimination），或若 $V^{-1}$ 预先算好，则用 $O(n^2)$ 计算 $A = V^{-1} \cdot Y$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> Vandermonde 矩阵正是你在 Strang 线性代数里熟悉的「把函数值编码成矩阵」的典型例子：列 $k$ 是 $x_j^k$（对固定 $x_j$ 而言）。表中三列的取舍逻辑值得品味——**系数**表示求值容易但乘法难；**根**表示乘法（拼接根）容易但加法不可能；**采样**表示加法乘法都容易但求值/转换难。没有任何一种表示全能，所以策略是「按需切换」：把乘法难的系数表示转成乘法容易的采样表示做完乘法，再转回去。这本质上是在说：**同一个数学对象的不同坐标（坐标基）下，同一操作的计算代价天差地别**——这正是 18.065/Strang 的「基的选择」思想在算法复杂度的投影。注意「采样唯一决定 $n-1$ 次多项式」依赖 $n$ 个互异样本点，这是代数基本定理的推论：两个 $n-1$ 次多项式若有 $n$ 个公共零点则必恒等。

---

<!-- ===== PDF p3 ===== -->

在系数与采样之间转换（及反向）要想超越 $\Theta(n^2)$，我们需要为 $x_0, x_1, \ldots, x_{n-1}$ 选择**特殊值**——目前我们只假设了这些 $x_i$ 互不相同。

<span style="color:#c0392b;">**分治算法（Divide and Conquer Algorithm）**</span>

我们可以把多项式乘法表述为分治算法，对多项式 $A(x)$（对所有 $x \in X$）执行以下步骤：

1. **分解（Divide）**：把 $A$ 拆成偶系数与奇系数两部分：

```math
A_{\mathrm{even}}(x) = \sum_{k=0}^{\lfloor n/2 \rfloor - 1} a_{2k} x^k = (a_0, a_2, a_4, \ldots)
```

```math
A_{\mathrm{odd}}(x) = \sum_{k=0}^{\lfloor n/2 \rfloor - 1} a_{2k+1} x^k = (a_1, a_3, a_5, \ldots)
```

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b;">**分治算法（续）**</span>

2. **递归征服（Conquer）**：对 $y \in X^2$ 递归地求 $A_{\mathrm{even}}(y)$ 与 $A_{\mathrm{odd}}(y)$，其中 $X^2 = \{x^2 \mid x \in X\}$。
3. **合并（Combine）**：$A(x) = A_{\mathrm{even}}(x^2) + x \cdot A_{\mathrm{odd}}(x^2)$，对所有 $x \in X$。

然而，该算法的递推式为

```math
T(n, |X|) = 2 \cdot T\!\left(\frac{n}{2}, |X|\right) + O(n + |X|) = O(n^2)
```

并不比以前更好。

**如果 $X$ 是「塌缩」的（collapsing）**，我们就可以做得更好：要么 $|X| = 1$（基础情形 base case），要么 $|X^2| = \frac{|X|}{2}$ 且 $X^2$ 是（递归地）塌缩的。此时递推式为

```math
T(n) = 2 \cdot T\!\left(\frac{n}{2}\right) + O(n) = O(n \lg n)
```

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这里有一个非常容易被忽略的微妙点：为什么普通采样集不行，必须「塌缩」？关键在 $X^2$ 的规模。若 $X$ 是一般集合，$X^2 = \{x^2\}$ 可能有 $|X|$ 个不同元素，那么递归子问题里「对每个 $y \in X^2$ 求值」并不会缩小规模，导致 $T(n) = 2T(n/2) + O(n + |X|)$ 展开后仍是 $O(n^2)$。只有当「平方映射」把 $|X|$ 个点折叠成 $|X|/2$ 个点时（即 $X^2$ 的规模减半），递归每层的点集规模才指数收缩，递推才变成 $O(n \lg n)$。这解释了为什么必须选**单位根**：单位根的平方恰好让「上半圆」与「下半圆」重合，实现精确减半。这是「几何对称性 ⟹ 分治可行性」的绝佳实例——你会在后面的 vEB 树（LEC 4）里再次看到「平方根折叠」的变体。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**单位根（Roots of Unity）**</span>

塌缩集可以通过平方根来构造。下列每个塌缩集都是取前一个集合中所有元素的平方根得到的：

1. $\{1\}$
2. $\{1, -1\}$
3. $\{1, -1, i, -i\}$
4. $\left\{1, -1, \pm \frac{\sqrt{2}}{2}(1 + i), \pm \frac{\sqrt{2}}{2}(-1 + i)\right\}$，它们都位于单位圆（unit circle）上

我们可以重复这个过程，通过在圆上找到越来越多点，把集合越做越大。这些点被称为 **$n$ 次单位根（$n$th roots of unity）**。形式化地说，$n$ 次单位根是满足 $x^n = 1$ 的 $n$ 个 $x$。这些点在复平面（complex plane）上**均匀分布**在单位圆周围（包含 1）。由 Euler 公式（Euler's Formula），这些点形如

```math
(\cos \theta, \sin \theta) = \cos \theta + i \sin \theta = e^{i\theta}, \quad \theta = 0,\ \frac{1}{n}\tau,\ \frac{2}{n}\tau,\ \ldots,\ \frac{n-1}{n}\tau \quad (\text{其中 } \tau = 2\pi)
```

当 $n = 2^\ell$ 时，$n$ 次单位根构成一个塌缩集，因为

```math
(e^{i\theta})^2 = e^{i(2\theta)} = e^{i(2\theta \bmod \tau)}
```

因此**偶数次单位根等价于 $\frac{n}{2}$ 次单位根**。

🎥 *Devadas 在视频中[用单位圆上的复数演示塌缩性]*："So e to the i tau equals 1, so going around in circles—same thing as not. ... So you can draw on this picture for every number, what is its square?"（翻译：所以 $e^{i\tau} = 1$，转一整圈等于没转。……你可以在这张图上对每个数画出「它的平方在哪」。）——把「平方 = 辐角加倍」画在单位圆上，$e^{i\theta}$ 与 $e^{i(\theta+\pi)}$ 会落到同一点，正是点集规模减半的直观来源。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 「$e^{i\theta} = \cos\theta + i\sin\theta$」这条 Euler 公式把复平面上的旋转与指数统一起来：$n$ 次单位根就是绕原点转 $\frac{360°}{n}$ 的 $n$ 个等距点。为什么用 $\tau = 2\pi$（一个完整圆周）而不用 $\pi$？因为单位根 $e^{i\tau k/n}$ 的 $k$ 从 0 取到 $n-1$ 恰好覆盖一整圈——用 $\tau$ 让「转一整圈回到 1」的周期性（$e^{i\tau} = 1$）变得一目了然。塌缩性的几何解释：把 $n$ 个根平方后，$e^{i\theta}$ 与 $e^{i(\theta+\pi)}$（直径两端）撞到同一点 $e^{i2\theta}$，正好让点集规模减半。这与你在 18.065/Strang 里学的复数乘法的「模长相乘、辐角相加」直接呼应——平方 = 辐角加倍，所以单位圆上的点平方后必然「折叠」。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b;">**FFT、IFFT 与多项式乘法（FFT, IFFT, and Polynomial Multiplication）**</span>

我们可以利用 $n$ 次单位根来改进多项式乘法算法的运行时间。该算法的基础称为**离散傅里叶变换（Discrete Fourier Transform, DFT）**。

DFT 允许在系数与采样之间变换：对 $x_k = e^{i\tau k / n}$（其中 $n = 2^\ell$）计算 $A \to A^{*} = V \cdot A$，其中 $A$ 是系数集合，$A^{*}$ 是得到的采样。单个项为

```math
a^{*}_j = \sum_{j=0}^{n-1} e^{i\tau jk/n} \cdot a_j
```

> <span style="color:#7f8c8d;">[译注] 原文此处下标不严谨：左侧自由下标为 $j$，求和下标也为 $j$，指数里却出现未定义的 $k$。标准写法应为 $a^{*}_k = \sum_{j=0}^{n-1} e^{i\tau jk/n} a_j$，即第 $k$ 个采样值是各系数 $a_j$ 用「频率 $k$ 的根 $e^{i\tau k/n}$」加权求和。译文保留原式以便对照，读者按标准式理解即可。</span>

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**快速傅里叶变换（Fast Fourier Transform, FFT）**</span>

FFT 是用于 DFT 的 $O(n \lg n)$ 分治算法，由 **Gauss** 大约在 **1805** 年使用，后由 **Cooley 与 Tukey** 于 **1965** 年推广。Gauss 用该算法确定周期性小行星轨道（periodic asteroid orbits），而 Cooley 与 Tukey 用它从离岸读数中**探测苏联核试验**（detect Soviet nuclear tests）。

FFT 的一个实用实现是 **FFTW**，由 MIT 的 **Frigo 与 Johnson** 描述。该算法常被直接做成硬件实现（implemented directly in hardware），用于固定的 $n$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> FFT 的「分治」本质在历史里藏着一个反直觉的事实：**Gauss 1805 年就已提出**（甚至更早可能见于 16 世纪），但直到 1965 年 Cooley–Tukey 重新发表才引发计算革命——因为前计算机时代没有应用场景。这提醒我们：算法价值的高度依赖于「硬件的成熟度」这一外部条件。FFTW（Fastest Fourier Transform in the West）是 MIT 的 Frigo & Johnson 开发的 FFT 库，其核心洞察是「运行时自适应选择分治策略」：不是固定递归模式，而是根据 CPU 缓存层次自动拼装出最快的分解方案——这是「缓存无关（cache-oblivious）算法」（本课程 LEC 23–24）思想的实战先驱。你在 CSAPP 第五章学到的「性能即程序的特例化（specialization）」，在 FFTW 里被做到了极致。

---

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**逆离散傅里叶变换（Inverse Discrete Fourier Transform, IDFT）**</span>

逆离散傅里叶变换是把「已乘好的采样」还原成多项式系数的算法。变换形式为

```math
A^{*} \to V^{-1} \cdot A^{*} = A
```

要计算它，我们需要找出 $V^{-1}$，而 $V^{-1}$ 事实上有一个非常漂亮的结构。

<span style="color:#2471a3;">**[theorem]**</span> <span style="color:#c0392b;">**Claim 1（声明 1）.**</span>

```math
V^{-1} = \frac{1}{n} \overline{V}
```

其中 $\overline{V}$ 是 $V$ 的复共轭（complex conjugate）。^1

> <span style="color:#7f8c8d;">^1 回忆：$p + qi$ 的复共轭是 $p - qi$。</span>

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b;">**IDFT 证明（续）**</span>

<span style="color:#2471a3;">**[proof]**</span> *Proof（证明）.* 我们断言 $P = V \cdot \overline{V} = nI$：

```math
\begin{aligned}
p_{jk} &= (\text{第 } j \text{ 行 of } V) \cdot (\text{第 } k \text{ 列 of } \overline{V})\\
&= \sum_{m=0}^{n-1} e^{ij\tau m/n} \overline{e^{ik\tau m/n}}\\
&= \sum_{m=0}^{n-1} e^{ij\tau m/n} e^{-ik\tau m/n}\\
&= \sum_{m=0}^{n-1} e^{i(j-k)\tau m/n}
\end{aligned}
```

现在，若 $j = k$，则 $p_{jk} = \sum_{m=0}^{n-1} 1 = n$。否则它构成一个**等比级数**（geometric series）：

```math
p_{jk} = \sum_{m=0}^{n-1} \left(e^{i(j-k)\tau/n}\right)^m = \frac{\left(e^{i\tau(j-k)/n}\right)^n - 1}{e^{i\tau(j-k)/n} - 1} = 0
```

因为 $e^{i\tau} = 1$。因此 $V^{-1} = \frac{1}{n}\overline{V}$，因为 $V \cdot \overline{V} = nI$。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这个证明是「矩阵可逆性通过正交性（orthogonality）验证」的绝佳实例：$V \cdot \overline{V} = nI$ 说的是 $V$ 的行之间彼此「正交」（内积为 0），每行与自己「正交归一」（内积为 $n$）。这正是你在 6.042J/Strang 线性代数里见过的**正交基**概念——只不过这里的「内积」带复共轭。等比级数求和 $\sum_{m=0}^{n-1} r^m = \frac{r^n - 1}{r - 1}$ 是你在 6.042J 学过的有限几何级数公式；当 $r = e^{i(j-k)\tau/n}$ 且 $j \ne k$ 时，$r^n = e^{i(j-k)\tau} = 1$，分子为 0 而分母非 0，级数和恰为 0——「单位根的对称性」再一次通过「指数周期的几何求和」兑现。注意这个证明还顺带说明：**FFT 与 IFFT 是同构的**——IFFT 只是把 $x_k = e^{ik\tau/n}$ 换成其复共轭 $e^{-ik\tau/n}$ 再除以 $n$。

这个声明说明：**逆离散傅里叶变换等价于离散傅里叶变换**，只是把 $x_k$ 从 $e^{ik\tau/n}$ 换成它的复共轭 $e^{-ik\tau/n}$，并把结果向量除以 $n$。IFFT 的算法与 FFT 类似，结果是 $O(n \lg n)$ 的 IDFT 算法。

🎥 *Devadas 在视频中[强调 IFFT 的惊人之处]*："Very cool because what this tells us is we run exactly the same algorithm and do exactly the same transformation. If we want to do the inverse, we can actually just use V, but with a different choice of x_k. Namely, for the inverse, we just take the complex conjugate ... and then divide the whole thing by n."（翻译：非常酷的一点是，这告诉我们：我们运行完全相同的算法、做完全相同的变换。如果想要做逆变换，实际上只需用同一个 $V$，但换一组 $x_k$ 的选择——对逆变换，我们只需取复共轭……再整体除以 $n$。）——**一个算法、两种模式**，这正是「同一个分治核心 + 参数微调」的复用美学。

<span style="color:#2471a3;">**[section]**</span> <span style="color:#00838f;">**快速多项式乘法（Fast Polynomial Multiplication）**</span>

为了计算两个多项式 $A$ 与 $B$ 的乘积，我们执行以下步骤：

1. 计算 $A^{*} = \mathrm{FFT}(A)$ 与 $B^{*} = \mathrm{FFT}(B)$，把 $A$、$B$ 从系数向量转换为采样表示。
2. 在采样表示下用线性时间计算 $C^{*} = A^{*} \cdot B^{*}$：逐点相乘 $C^{*}_k = A^{*}_k \cdot B^{*}_k$（对所有 $k$）。
3. 计算 $C = \mathrm{IFFT}(C^{*})$，得到最终解的向量表示。

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 这就是完整的「系数 → 采样 → 逐点乘 → 逆采样」三段式算法，复杂度 $O(n \lg n) + O(n) + O(n \lg n) = O(n \lg n)$。把它与「为什么选采样表示」联系起来：乘法在采样表示下是 $n$ 个独立的逐点乘积（$C_k^* = A_k^* B_k^*$），因为「点积 = 函数值相乘」对每个样本点独立成立——这是多项式乘法等价于卷积、而卷积在频域退化为逐点乘的直接后果。整个流程的架构模式值得单独记住：**A-domain → 变换到 B-domain（O(n lg n)) → 在 B 域做简单操作（O(n)) → 逆变换回 A-domain（O(n lg n))**。这一模式在后续课程会以「对偶/谱方法」的形式反复出现，比如图论里的拉普拉斯谱、概率论里的矩生成函数。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#c0392b;">**应用（Applications）**</span>

傅里叶（频率）空间（Fourier / frequency space）有大量应用。多项式 $A^{*} = \mathrm{FFT}(A)$ 是复值（complex）的，其中幅值（amplitude）$|a^{*}_k|$ 表示频率为 $k$ 的信号（signal）的幅度，而 $\arg(a^{*}_k)$（二维向量的角度）表示该信号的**相位偏移（phase shift）**。例如，这个视角对音频处理（audio processing）特别有用，如 Adobe Audition、Audacity 等所使用的：

- **高通滤波器（High-pass filters）**：把高频（high frequencies）置零
- **低通滤波器（Low-pass filters）**：把低频（low frequencies）置零
- **音高移位（Pitch shifts）**：平移频率向量
- **用于 MP3 压缩（MP3 compression）**等

> <span style="color:#1e8449;">**[note] Note（译者注）:**</span> 最后一页把 FFT 从「多项式乘法的加速器」提升为「信号处理的通用语言」。理解「频域（frequency domain）」的关键直觉：任何一个随时间变化的信号（如麦克风振膜的位移）都可被分解为不同频率的正弦波之和——FFT 正是把「时间域（time domain）的采样值」变换为「频率域（frequency domain）的各分量幅度与相位」。高通/低通滤波在频域里就是「把对应频率分量的系数置零」，这比在时域做卷积滤波直观得多。你在 CSAPP 里见到过的数字滤波、音频编码，其底层都是这个框架。这条「同一对象，两种视角（时域/频域）」的桥，也是你在 18.065 学矩阵谱分解、在信号与系统课学 Laplace/z 变换时不断回响的主旋律。

---

<!-- ===== PDF p8 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
