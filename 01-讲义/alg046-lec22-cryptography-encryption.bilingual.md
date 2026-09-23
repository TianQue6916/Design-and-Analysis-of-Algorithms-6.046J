<!-- ===== PDF p1 ===== -->

<span style="color:#c0392b">**讲次 22（Lecture 22）：密码学：加密（Cryptography: Encryption）**</span> <span style="color:#7f8c8d;">（Spring 2015，2015 春季学期）</span>

本讲大纲：
- **对称密钥加密（Symmetric key encryption）**
- **密钥交换（Key exchange）**
- **非对称密钥加密（Asymmetric key encryption）**
- **RSA**
- **NP 完全问题与密码学（NP-complete problems and cryptography）**
  - 图着色（graph coloring）
  - 背包（knapsack）

<span style="color:#c0392b">**对称密钥加密（Symmetric key encryption）**</span>

```math
c = e_k(m) \qquad m = d_k(c)
```

这里 $c$ 是**密文（ciphertext）**，$m$ 是**明文（plaintext）**，$e$ 是**加密函数**，$d$ 是**解密函数**，$k$ 是**秘密密钥（secret key）**。$e, d$ 置换并反置换**所有消息的空间**。

**可逆操作（Reversible operations）**：$\oplus$、$+/-$、左移/右移。

**对称算法（Symmetric algorithms）**：AES、RC5、DES

> <span style="color:#1e8449;">**[note] Note（译者注，对称加密）:**</span> 对称加密的核心是：**加密与解密用同一个秘密密钥 $k$**，$e$ 与 $d$ 互为逆置换（对消息空间做「打乱」与「还原」）。可逆操作（XOR、加减、移位）是构造这些置换的积木——你会在 CSAPP 里看到这些位操作在机器层面的实现。**AES**（当前标准，Rijndael）、**RC5**、**DES**（早期标准，已被暴力破解取代）都是对称密码。对称加密高效，但有个根本问题：**通信双方必须先共享同一个密钥 $k$**——这就是本讲马上要处理的「**密钥管理问题（Key Management Question）**」：在公开信道上，两个素不相识的人怎么安全地共享一个秘密密钥？这看似循环（要加密就得先共享密钥），正是密码学要解决的第一道坎。

<span style="color:#c0392b">**密钥管理问题（Key Management Question）**</span>

**秘密密钥 $k$ 如何交换/共享？**

Alice 想给 Bob 发一条消息。Alice 和 Bob 之间有**海盗（pirates）**，他们会拿走**未上锁盒子**里的任何密钥或消息，但**不碰上了锁的盒子**。Alice 怎样才能把消息或密钥发给 Bob（且海盗不知道发了什么）？

**解法（Solution）**：
- Alice 把 $m$ 放入盒子，用 $k_A$ 上锁
- 盒子寄给 Bob

---

<!-- ===== PDF p2 ===== -->

<span style="color:#c0392b">**密钥管理问题（续）**</span>

- Bob 用 $k_B$ 给盒子**再加一把锁**
- 盒子寄回 Alice
- Alice 解开 $k_A$
- 盒子寄给 Bob
- Bob 解开 $k_B$，读取 $m$

注意这个方法依赖**锁的可交换性（commutativity）**：即，上锁与解锁操作的**顺序无关紧要**。

> <span style="color:#1e8449;">**[note] Note（译者注，双重锁方案）:**</span> 海盗问题是个绝妙的教学隐喻：「海盗不碰上了锁的盒子」= 公开信道上的窃听者只能看到密文、看不到明文。Alice 和 Bob 的「双锁往返」方案（$A$ 锁 → $B$ 锁 → $A$ 解锁 → $B$ 解锁）的核心是**锁的可交换性**——两把锁的解锁顺序不影响结果。这正是**对称密码无法直接做密钥交换的原因**：普通对称加密的「锁」不可交换（$d_{k_A}(e_{k_B}(e_{k_A}(m))) \ne m$），所以必须另想办法。Diffie-Hellman（下一节）用**模幂运算**构造了一个「可交换的锁」：$(g^a)^b = (g^b)^a$，让两个人都能算出共享密钥而不泄露各自的 $a, b$。这个「**可交换的一向函数**」正是公钥密码学的数学根基。

<span style="color:#c0392b">**Diffie-Hellman 密钥交换（Diffie-Hellman Key Exchange）**</span>

$G = \mathbb{F}_p^*$

这里 $\mathbb{F}_p^*$ 是**有限域（finite field）**（模素数 $p$）。$*$ 表示**仅可逆元素**（$\{1, 2, \ldots, p-1\}$）。

| Alice | 公开 | Bob |
|:---|:---:|:---:|
| | $g$ 公开 | |
| | $p$ 公开 | |
| | $2 \le g \le p-2$ | |
| 选择 $a$ | | 选择 $b$ |
| 计算 $g^a$ | $g^a$ | |
| | $g^b$ | 计算 $g^b$ |
| | $1 \le a, b \le p-2$ | |
| Alice 可计算 $(g^b)^a \bmod p = k$ | | Bob 可计算 $(g^a)^b \bmod p = k$ |

假设**离散对数问题（Discrete Log Problem）是困难的**（给定 $g^a$，计算 $a$）且 **Diffie-Hellman 问题是困难的**（给定 $g^a, g^b$，计算 $g^{ab}$）。

**我们能攻击它吗？中间人（Man-in-the-middle）**：
- Alice 不知道她在与 Bob 通信。
- Alice 同意与 Eve 做密钥交换（以为她是 Bob）。
- Bob 同意与 Eve 做密钥交换（以为她是 Alice）。
- Eve 能看到所有通信。

> <span style="color:#1e8449;">**[note] Note（译者注，Diffie-Hellman）:**</span> Diffie-Hellman 密钥交换（Diffie & Hellman，1976）解决了「无共享秘密的两方如何安全建立共享密钥」，是现代 TLS/HTTPS 等协议的基础（维基百科 Diffie-Hellman 词条）。数学：双方选公共原根 $g$ 和素数 $p$，Alice 秘密选 $a$ 发 $g^a$、Bob 秘密选 $b$ 发 $g^b$，两人各自算出共享密钥 $g^{ab}$；窃听者只有 $g^a, g^b, g, p$，要算 $g^{ab}$ 需要解离散对数（目前无多项式算法）。**安全建立在「离散对数/DDH 假设」上**——这是计算复杂性假设（与 P≠NP 同族），不是被证明的事实。**中间人攻击**是 DH 的致命弱点：DH 只协商密钥、**不认证身份**，Eve 可以同时跟 Alice 和 Bob 各建一个密钥、转发所有消息（读改一切）。所以现代协议（TLS）在 DH 之上叠加**证书/数字签名**做身份认证——你会在 Lec 21 讲的哈希签名、以及 CS144 网络课里见到完整故事。历史上 GCHQ 的 Ellis、Cocks、Williamson 早在 1969 年就独立发明了公钥密码，但保密未公开。

---

<!-- ===== PDF p3 ===== -->

<span style="color:#c0392b">**公钥加密（Public Key Encryption）**</span>

```math
\text{message} + \text{public key} = \text{ciphertext}
```

```math
\text{ciphertext} + \text{private key} = \text{message}
```

两个密钥需要以**数学方式关联**。知道公钥应该**无法推导出私钥**的任何信息。

<span style="color:#c0392b">**RSA**</span>

- Alice 选两个大的**秘密素数** $p$ 和 $q$。
- Alice 计算 $N = p \cdot q$。
- 选择**加密指数** $e$，满足 $\gcd(e, (p-1)(q-1)) = 1$，$e = 3, 17, 65537$。
- Alice 的**公钥** = $(N, e)$。
- Alice 用**扩展欧几里得算法（Extended Euclidean Algorithm）**求**解密指数** $d$，使 $e \cdot d \equiv 1 \bmod (p-1)(q-1)$。
- Alice 的**私钥** = $(d, p, q)$（存储 $p$ 和 $q$ 并非绝对必要，但为了效率我们这样做）。

<span style="color:#c0392b">**RSA 的加密与解密（Encryption and Decryption with RSA）**</span>

```math
c = m^e \bmod N \quad \text{（加密 encryption）}
```

```math
m = c^d \bmod N \quad \text{（解密 decryption）}
```

<span style="color:#c0392b">**为什么有效（Why it works）**</span>

令 $\varphi = (p-1)(q-1)$。因为 $ed \equiv 1 \bmod \varphi$，存在整数 $k$ 使 $ed = 1 + k\varphi$。分两种情况：

**情形 1（Case 1）** $\gcd(m, p) = 1$。由**费马小定理（Fermat's theorem）**，$m^{p-1} \equiv 1 \bmod p$。于是

```math
m^{ed} = m^{1+k(p-1)(q-1)} = m \cdot (m^{p-1})^{k(q-1)} \equiv m \bmod p
```

---

<!-- ===== PDF p4 ===== -->

<span style="color:#c0392b">**RSA（续）**</span>

**情形 2（Case 2）** $\gcd(m, p) = p$。这意味着 $m \bmod p = 0$，所以 $m^{ed} \equiv m \bmod p$。

因此，两种情况下都有 $m^{ed} \equiv m \bmod p$。类似地，$m^{ed} \equiv m \bmod q$。因为 $p, q$ 是不同的素数，$m^{ed} \equiv m \bmod N$。所以 $c^d = (m^e)^d \equiv m \bmod N$。$\square$

<span style="color:#c0392b">**RSA 的困难性（Hardness of RSA）**</span>

- **因子分解（Factoring）**：给定 $N$，分解成 $p, q$ 是困难的。
- **RSA 问题（RSA Problem）**：给定满足 $\gcd(e, (p-1)(q-1)) = 1$ 的 $e$ 和 $c$，找到使 $m^e \equiv c \bmod N$ 的 $m$。

<span style="color:#c0392b">**NP 完全性（NP-completeness）**</span>

- **$N$ 是合数且有一个因子落在某个区间内吗？** —— 是否 NP 完全**未知**。
- **图可以 $k$-着色吗？** 换句话说：能否给每个顶点分配 $k$ 种颜色之一，使**没有一条边连接的两个顶点共享相同颜色**？—— **NP 完全**。
- **给定一堆 $n$ 个物品，各有权重 $w_i$，能否把物品放进背包使总重恰好为 $S$？** —— **NP 完全**。

<span style="color:#c0392b">**NP 完全性与密码学（NP-completeness and Cryptography）**</span>

- **NP 完全性**：关于**最坏情形（worst-case）**复杂度。
- **密码学**：想要一个**参数选取得当**的问题实例，它在**平均情形（average）**下是困难的。

**大多数背包密码系统都失败了**。

判断一个图是否 **3-可着色** 是 NP 完全的，但**平均情形下非常容易**。这是因为**超过一定规模的随机图通常不是 3-可着色的**！

考虑用标准**回溯搜索（backtracking search）**判断 3-可着色性：
- 把顶点排序 $v_1, \ldots, v_t$。颜色 = $\{1, 2, 3\}$
- 按顶点顺序遍历图。
- 访问一个顶点时，选择**可行**的最小颜色。
- 若卡住，**回溯**到上一个选择，尝试下一个选择。

---

<!-- ===== PDF p5 ===== -->

<span style="color:#c0392b">**NP 完全性与密码学（续）**</span>

- 第 1 个顶点的颜色用完 → 输出「NO」
- 最后一个顶点成功着色 → 输出「YES」

在 $t$ 个顶点的**随机图**上，平均遍历的顶点数 $< 197$，**与 $t$ 无关**！

> <span style="color:#1e8449;">**[note] Note（译者注，最坏 vs 平均：密码学的核心张力）:**</span> 这一页是整个密码学设计的**哲学核心**：NP 完全性是**最坏情形**概念（存在一个难实例），而密码学需要**平均情形困难**（随机实例也难）。两者**不同**！经典例证：**3-着色**是 NP 完全的（存在难实例），但**随机图几乎总不是 3-可着色的**——回溯搜索平均只需探索约 197 个顶点就得出「NO」（超过某规模，随机图几乎必然含 $K_4$ 或足够密的子图使其不可 3-着色），**无论 $t$ 多大**。所以「NP 完全」**不保证**适合做密码学——这正是**绝大多数背包密码系统（如 Merkle-Hellman）失败**的原因：它们基于 NP 完全问题的**特定随机实例族**，但那个实例族有隐藏结构可被攻破（见 p6 的格攻击）。**「最坏困难 ≠ 平均困难」**是密码学与算法理论的交界处最容易被误解的命题；现代密码学（如基于格的密码）转而寻求**最坏情形困难 + 平均情形归约**的强保证。

<span style="color:#c0392b">**背包密码学（Knapsack Cryptography）**</span>

**一般背包问题（General knapsack problem）**：NP 完全。

**超递增背包（Super-increasing knapsack）**：**线性时间**可解。在此问题中，权重受如下约束：

```math
w_j \ge \sum_{i=1}^{j-1} w_i
```

<span style="color:#c0392b">**Merkle-Hellman 密码系统（Merkle Hellman Cryptosystem）**</span>

**私密变换（Private transform）**

```math
\text{私钥} \to \text{超递增背包问题} \xrightarrow{\text{私密变换}} \text{「困难」的一般背包问题} \to \text{公钥}
```

**变换（Transform）**：两个私有整数 $N, M$，满足 $\gcd(N, M) = 1$。把序列中所有值乘以 $N$ 再取模 $M$。

**例（Example）**：$N = 31$，$M = 105$，私钥 $=\{2, 3, 6, 14, 27, 52\}$，公钥 $=\{62, 93, 81, 88, 102, 37\}$。

<span style="color:#c0392b">**Merkle-Hellman 示例（Merkle Hellman Example）**</span>

消息 = `011000` `110101` `101110`

密文：
- `011000`：$93 + 81 = 174$
- `110101`：$62 + 93 + 88 + 37 = 280$
- `101110`：$62 + 81 + 88 + 102 = 333$
- = 174, 280, 333

接收者知道 $N = 31$、$M = 105$、$\{2, 3, 6, 14, 27, 52\}$。把每个密文块乘以 $N^{-1} \bmod M$。本例中 $N^{-1} = 61 \bmod 105$。
- $174 \cdot 61 = 9 = 3 + 6 = 011000$
- $280 \cdot 61 = 70 = 2 + 3 + 13 + 52 = 110101$
- $333 \cdot 61 = 48 = 2 + 6 + 13 + 27 = 101110$

> <span style="color:#1e8449;">**[note] Note（译者注，必须澄清的 M-H 示例数字错误）:**</span> 这里的示例存在**多处数字不一致**，我逐一用模算术验证过，原讲义的数值无法自洽，正确版本如下。**① 公钥第四项应为 14 而非 88**：$14 \cdot 31 = 434 \equiv 434 - 4 \cdot 105 = 14 \pmod{105}$（不是 88）；而且 $\gcd(14, 105) = 7 \ne 1$，$14 \cdot N \bmod 105$ 只能得到 7 的倍数，**永远得不到 88**——任何 $N$ 都无法产生讲义所印的 88。故正确的公钥是 $\{62, 93, 81, 14, 102, 37\}$。**② 密文随之更正**：`110101` $\to 62+93+14+37 = 206$，`101110` $\to 62+81+14+102 = 259$（前两块 `011000` 不变，$174$）。**③ 解密更正**：$206 \cdot 61 = 12566 \equiv 71 = 2+3+14+52 = 110101$，$259 \cdot 61 = 15799 \equiv 49 = 2+6+14+27 = 101110$（讲义写的 70、48 用了私钥里**不存在的「13」**，且与位模式不符：`110101` 应等于 $2+3+14+52 = 71$）。**④ 私钥仅是弱超递增**：$2+3+6+14+27 = 52$，故 $w_6 = 52 \not> 52$，严格超递增要求 $w_j > \sum_{i<j} w_i$ 被破坏（这正是经典示例的固有瑕疵，说明此例只是教学演示）。这提醒我们：**密码学示例的每个数字都要按模算术独立验证**，不能盲信排版。

> <span style="color:#1e8449;">**[note] Note（译者注，M-H 原理与「美丽但已破」）:**</span> **Merkle-Hellman（Merkle & Hellman, 1978）**是最早的公钥密码系统之一，也是「**用 NP 完全问题造密码**」的第一次大胆尝试（维基百科 Merkle–Hellman 词条）：加密用**公钥**（被变换「伪装」成一般背包的序列）解 NP 完全的子集和；解密用**私钥**（超递增序列 + 陷门 $N, M$）把「难背包」还原成「易背包」（贪心即可线性解）。加密时每个明文字节对应公钥序列的若干位，密文就是选中项之和；解密时乘以 $N^{-1}$ 消去变换，回到超递增背包用贪心恢复位模式。**「美丽但已破」**：Shamir（1984）给出多项式时间攻击；核心原因是 M-H 产生的公钥背包**密度（density）过低**——密度 $d = \frac{n}{\max\{\log_2 w_i\}}$，而 **LLL 格基约简算法（Lenstra-Lenstra-Lovász, 1982）**能多项式时间求解低密度背包。也就是说：**「伪装」得不够彻底**——超递增结构在变换后仍留下可被格攻击利用的痕迹。这是密码学史上最有教育意义的失败案例：**「基于 NP 完全问题」不等于「安全」**，还必须有精确的困难性论证（现代「基于格/LWE 的密码学」正是吸取此教训的继承者）。

---

<!-- ===== PDF p6 ===== -->

<span style="color:#c0392b">**美丽但已破（Beautiful but broken）**</span>

**基于格的技巧（Lattice based techniques）**可以攻破这个方案。

背包密度（density）

```math
d = \frac{n}{\max\{\log_2 w_i : 1 \le i \le n\}}
```

**格基约简（Lattice basis reduction）**可以求解**低密度**的背包。不幸的是，M-H 方案**总是**产生低密度的背包。

> <span style="color:#1e8449;">**[note] Note（译者注，格攻击与密度）:**</span> 密度 $d = \frac{n}{\max \log_2 w_i}$ 度量「背包的拥挤程度」：分子是物品数，分母是最大物品的比特数。**低密度**意味着「物品数量相对比特宽度太少」——即超递增结构压缩后仍「稀疏」，给格攻击留下了可乘之机。**LLL 算法**（Lenstra-Lenstra-Lovász，1982）在多项式时间内找到一个格的「短且近正交」基，而背包求解可归结为「在格中找短向量」（最短向量问题 SVP）——低密度背包的短向量解恰好对应解密。**为什么 M-H 必然低密度**：超递增序列的项按 $2^i$ 量级增长，变换后仍是 $O(\log w_i)$ 比特，$n$ 相对 $\max \log_2 w_i$ 太小，密度 $d \approx \frac{n}{n} = 1$ 附近……实际总是低于格攻击的可行阈值。这个「**密度判据**」成为后世背包密码设计的定量教训。你在讲次 16 学过的「NP 困难 ≠ 平均困难」在这里有了具体兑现：M-H 的「困难」只停留在最坏情形，其随机实例族恰好落入格攻击的「易」区。

---

<!-- ===== PDF p7 ===== -->

<span style="color:#7f8c8d;">**MIT OpenCourseWare**</span>

<span style="color:#7f8c8d;">http://ocw.mit.edu</span>

<span style="color:#7f8c8d;">**6.046J / 18.410J 算法设计与分析（Design and Analysis of Algorithms）**</span>

<span style="color:#7f8c8d;">Spring 2015（2015 春季学期）</span>

<span style="color:#7f8c8d;">有关引用这些材料的信息或我们的使用条款（Terms of Use），请访问：http://ocw.mit.edu/terms。</span>
