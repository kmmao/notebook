# 第九阶段：复杂度理论（6-8周）

## 概述

复杂度理论回答"计算有多难？"——这是理论计算机科学的核心问题。

---

## 1. 时间复杂度

### 大O记号

```
O(g(n))：上界，f(n) ≤ c·g(n)
Ω(g(n))：下界，f(n) ≥ c·g(n)
Θ(g(n))：紧界，O和Ω都满足
o(g(n))：严格上界
ω(g(n))：严格下界
```

### 常见复杂度

```
O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)
```

---

## 2. 复杂度类

### P类
多项式时间可判定的问题

```
P = ∪ TIME(nᵏ)
```

**例子**：排序、最短路径、素性测试

### NP类
多项式时间可验证的问题

```
NP = {L : 存在多项式时间验证器V和多项式p，
      x ∈ L ⟺ ∃c, |c| ≤ p(|x|): V(x,c) = 1}
```

**例子**：SAT、哈密顿路径、图着色

### co-NP类
补语言在NP中

**例子**：UNSAT、图非同构

---

## 3. P vs NP问题

> P = NP？

**克雷数学研究所千禧年问题之一，悬赏100万美元**

### 含义
- 若P = NP：所有能"快速验证"的问题都能"快速解决"
- 若P ≠ NP：有些问题验证容易但求解困难

### 现状
- 大多数人相信P ≠ NP
- 但没有人能证明

---

## 4. NP完全性

### 定义
L是NP完全的，当且仅当：
1. L ∈ NP
2. ∀L' ∈ NP: L' ≤ₚ L（NP困难）

### Cook-Levin定理
SAT是NP完全的

### 经典NP完全问题

| 问题 | 描述 |
|------|------|
| SAT | 布尔公式可满足性 |
| 3-SAT | 每个子句3个文字 |
| CLIQUE | 图中的完全子图 |
| VERTEX COVER | 最小顶点覆盖 |
| HAMILTONIAN PATH | 哈密顿路径 |
| TSP (判定版) | 旅行商问题 |
| SUBSET SUM | 子集和问题 |
| KNAPSACK | 背包问题 |
| GRAPH COLORING | 图着色 |

### 证明NP完全性
1. 证明L ∈ NP
2. 选择已知NP完全问题L'
3. 证明L' ≤ₚ L

---

## 5. 归约

### 多项式时间归约
A ≤ₚ B：存在多项式时间可计算的f，x ∈ A ⟺ f(x) ∈ B

### 归约的传递性
若A ≤ₚ B且B ≤ₚ C，则A ≤ₚ C

### 经典归约

```
3-SAT ≤ₚ CLIQUE ≤ₚ VERTEX COVER
3-SAT ≤ₚ HAMILTONIAN PATH ≤ₚ TSP
```

---

## 6. 空间复杂度

### 空间类

| 类 | 定义 |
|----|------|
| L | O(log n)空间 |
| NL | 非确定O(log n)空间 |
| PSPACE | 多项式空间 |
| NPSPACE | 非确定多项式空间 |

### Savitch定理
NPSPACE = PSPACE

### 关系
```
L ⊆ NL ⊆ P ⊆ NP ⊆ PSPACE ⊆ EXPTIME
```

### PSPACE完全
**TQBF**（量化布尔公式）是PSPACE完全的

---

## 7. 更多复杂度类

### 随机化类

| 类 | 错误类型 |
|----|----------|
| RP | 单边错误（false negative） |
| co-RP | 单边错误（false positive） |
| BPP | 双边有界错误 |
| ZPP | 零错误，期望多项式时间 |

### 交互式证明
- **IP**：交互式证明
- **IP = PSPACE**（Shamir定理）

### 计数类
- **#P**：计数问题
- **#P完全**：计算SAT解的数量

---

## 8. 近似算法

### 近似比
对最优解OPT，算法给出的解ALG满足：
- 最大化问题：ALG ≥ OPT/r
- 最小化问题：ALG ≤ r·OPT

### 近似类

| 类 | 定义 |
|----|------|
| APX | 常数近似 |
| PTAS | 多项式时间近似方案 |
| FPTAS | 完全多项式时间近似方案 |

### 例子
- 顶点覆盖：2-近似
- TSP（三角不等式）：1.5-近似
- 一般TSP：不可近似

---

## 9. 复杂度理论的意义

| 结果 | 意义 |
|------|------|
| 问题在P中 | 存在高效算法 |
| 问题NP完全 | 可能没有高效算法，考虑近似/启发式 |
| 问题PSPACE完全 | 比NP更难 |
| 问题不可判定 | 不存在算法 |

---

## 推荐资源

| 资源 | 内容 |
|------|------|
| Computational Complexity (Arora & Barak) | 现代圣经，免费在线 |
| Introduction to the Theory of Computation (Sipser) | 入门教材 |
| The P=NP Question and Gödel's Lost Letter | 有趣的博客 |
