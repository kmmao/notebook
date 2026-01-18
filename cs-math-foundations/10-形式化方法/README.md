# 第十阶段：形式化方法（持续学习）

## 概述

形式化方法用数学证明程序的正确性——这是软件工程的终极目标。

---

## 1. 为什么需要形式化方法？

### 测试的局限
> "Testing shows the presence, not the absence of bugs." — Dijkstra

测试只能发现错误，不能证明没有错误。

### 形式化验证的承诺
用数学证明程序满足规范——100%正确性保证。

### 成功案例
- **seL4**：形式化验证的操作系统内核
- **CompCert**：形式化验证的C编译器
- **AWS s2n**：形式化验证的TLS实现
- **Compcert**：编译器的每一步都有证明

---

## 2. 霍尔逻辑（Hoare Logic）

### 霍尔三元组

```
{P} C {Q}

P：前条件
C：程序
Q：后条件

含义：若P成立且C执行终止，则Q成立
```

### 推理规则

**赋值**：
```
{Q[x/E]} x := E {Q}
```

**顺序**：
```
{P} C₁ {R}    {R} C₂ {Q}
─────────────────────────
      {P} C₁;C₂ {Q}
```

**条件**：
```
{P ∧ B} C₁ {Q}    {P ∧ ¬B} C₂ {Q}
──────────────────────────────────
    {P} if B then C₁ else C₂ {Q}
```

**循环**：
```
{I ∧ B} C {I}
──────────────────────────
{I} while B do C {I ∧ ¬B}

I是循环不变式
```

### 例子：证明程序正确性

```
// 计算x的阶乘
{x ≥ 0}
y := 1;
z := 0;
while z ≠ x do
    z := z + 1;
    y := y * z
{y = x!}
```

循环不变式：y = z! ∧ z ≤ x

---

## 3. 模型检测（Model Checking）

### 思想
穷举所有可能状态，验证性质是否成立

### 时序逻辑

**LTL（线性时序逻辑）**：
- □P：总是P
- ◇P：最终P
- P U Q：P直到Q
- ○P：下一状态P

**CTL（计算树逻辑）**：
- AG P：所有路径总是P
- EF P：存在路径最终P

### 工具

| 工具 | 用途 |
|------|------|
| SPIN | 并发系统 |
| NuSMV | 硬件验证 |
| CBMC | C程序有界模型检测 |
| TLA+ | 分布式系统 |

### 状态爆炸问题
状态数随变量数指数增长

**缓解方法**：
- 符号模型检测（BDD）
- 有界模型检测（SAT/SMT）
- 抽象

---

## 4. 定理证明器

### 交互式定理证明器

| 工具 | 基础理论 | 特点 |
|------|----------|------|
| **Coq** | 构造演算 | 最广泛使用 |
| **Lean** | 依赖类型论 | 数学友好 |
| **Isabelle** | 高阶逻辑 | 自动化强 |
| Agda | 依赖类型 | 编程友好 |

### Coq示例

```coq
(* 定义自然数 *)
Inductive nat : Type :=
  | O : nat
  | S : nat -> nat.

(* 定义加法 *)
Fixpoint plus (n m : nat) : nat :=
  match n with
  | O => m
  | S n' => S (plus n' m)
  end.

(* 证明 0 + n = n *)
Theorem plus_O_n : forall n : nat, plus O n = n.
Proof.
  intros n. simpl. reflexivity.
Qed.
```

### Lean 4示例

```lean
-- 定义加法交换律
theorem add_comm : ∀ a b : Nat, a + b = b + a := by
  intros a b
  induction a with
  | zero => simp
  | succ a ih => simp [Nat.succ_add, ih]
```

---

## 5. SMT求解器

### SAT求解器
判定布尔公式可满足性

### SMT（Satisfiability Modulo Theories）
SAT + 背景理论（整数、实数、数组、位向量...）

### 工具

| 工具 | 特点 |
|------|------|
| Z3 | 微软，功能最全 |
| CVC5 | 开源 |
| Yices | 高效 |

### 应用
- 符号执行
- 程序验证
- 测试生成

---

## 6. 程序分析

### 静态分析
不运行程序，分析代码

**抽象解释**：用抽象域近似程序行为

### 动态分析
运行时分析

**符号执行**：用符号值代替具体值

---

## 7. 学习路径

### 入门
1. 学习Hoare逻辑
2. 尝试TLA+或SPIN
3. 学习Coq/Lean基础

### 进阶
1. 深入一个证明器
2. 完成Software Foundations
3. 参与形式化验证项目

---

## 推荐资源

| 资源 | 内容 |
|------|------|
| Software Foundations | Coq入门圣经，免费在线 |
| Theorem Proving in Lean 4 | Lean 4官方教程 |
| The Calculus of Computation | 形式化方法入门 |
| TLA+ Video Course | Lamport的TLA+教程 |
| Concrete Semantics | Isabelle入门 |
