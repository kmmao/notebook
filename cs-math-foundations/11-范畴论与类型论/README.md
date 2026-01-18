# 第十一阶段：范畴论与类型论（持续学习）

## 概述

这是最抽象、最硬核的方向——"数学的数学"和"证明即程序"。

---

## 1. 为什么学范畴论？

> "Category theory is the study of universal properties."

范畴论提供了一种统一的语言，可以描述：
- 各种数学结构之间的关系
- 编程语言中的抽象模式
- 类型系统的语义

---

## 2. 范畴基础

### 范畴的定义

一个范畴C包含：
- **对象**（Objects）：A, B, C, ...
- **态射**（Morphisms）：f: A → B
- **复合**：g ∘ f: A → C（若f: A→B, g: B→C）
- **恒等态射**：id_A: A → A

满足：
- 结合律：(h ∘ g) ∘ f = h ∘ (g ∘ f)
- 恒等律：f ∘ id = f = id ∘ f

### 常见范畴

| 范畴 | 对象 | 态射 |
|------|------|------|
| **Set** | 集合 | 函数 |
| **Grp** | 群 | 群同态 |
| **Top** | 拓扑空间 | 连续映射 |
| **Vect** | 向量空间 | 线性变换 |
| **Hask** | Haskell类型 | 函数 |

---

## 3. 函子（Functor）

### 定义
范畴之间的"结构保持映射"

F: C → D 满足：
- F(A) 是D中的对象
- F(f: A→B) 是 F(A)→F(B)
- F(id_A) = id_{F(A)}
- F(g ∘ f) = F(g) ∘ F(f)

### 编程中的Functor

```haskell
class Functor f where
    fmap :: (a -> b) -> f a -> f b

-- 例子
instance Functor [] where
    fmap = map

instance Functor Maybe where
    fmap f Nothing  = Nothing
    fmap f (Just x) = Just (f x)
```

---

## 4. 自然变换（Natural Transformation）

### 定义
函子之间的"结构保持映射"

η: F ⇒ G 是一族态射 η_A: F(A) → G(A)

使得对任意f: A→B，下图交换：
```
F(A) --η_A--> G(A)
 |             |
F(f)         G(f)
 |             |
 v             v
F(B) --η_B--> G(B)
```

---

## 5. 泛性质（Universal Property）

### 思想
用"最优"性质定义对象

### 积（Product）
A × B 是满足以下性质的对象：
- 有投影 π₁: A×B → A, π₂: A×B → B
- 对任何C和f: C→A, g: C→B，存在唯一h使得 π₁∘h = f, π₂∘h = g

### 余积（Coproduct）
A + B（在Set中是不交并）

### 编程对应

| 范畴论 | 编程 |
|--------|------|
| 积 | 元组、结构体 |
| 余积 | 枚举、联合类型 |
| 指数对象 | 函数类型 |

---

## 6. 单子（Monad）

### 定义
单子是自函子范畴上的幺半群

具体地，单子(T, η, μ)满足：
- T: C → C 是函子
- η: Id ⇒ T（unit）
- μ: T² ⇒ T（join）

### 编程中的Monad

```haskell
class Monad m where
    return :: a -> m a              -- η
    (>>=)  :: m a -> (a -> m b) -> m b

-- 例子：Maybe Monad
instance Monad Maybe where
    return = Just
    Nothing >>= f = Nothing
    Just x  >>= f = f x

-- 例子：IO Monad处理副作用
main :: IO ()
main = getLine >>= putStrLn
```

### Monad的意义
封装"计算效果"：
- Maybe：可能失败
- Either：错误处理
- List：非确定性
- State：状态
- IO：副作用

---

## 7. 类型论

### Curry-Howard同构

| 逻辑 | 类型论 | 范畴论 |
|------|--------|--------|
| 命题 | 类型 | 对象 |
| 证明 | 程序 | 态射 |
| P ∧ Q | P × Q | 积 |
| P ∨ Q | P + Q | 余积 |
| P → Q | P → Q | 指数 |
| ∀x.P(x) | Πx:A.P(x) | 依赖积 |
| ∃x.P(x) | Σx:A.P(x) | 依赖和 |

**深刻含义**：写程序就是做证明！

### 依赖类型

类型可以依赖于值：

```lean
-- 长度为n的向量
def Vec (α : Type) (n : Nat) : Type :=
  match n with
  | 0 => Unit
  | n + 1 => α × Vec α n

-- 类型保证安全的head
def head : Vec α (n + 1) → α
  | (x, _) => x
```

### Martin-Löf类型论
现代证明助手的基础

---

## 8. 同伦类型论（HoTT）

### 思想
- 类型 ≈ 空间
- 相等 ≈ 路径
- 高阶相等 ≈ 高阶同伦

### 单值公理（Univalence Axiom）
等价的类型是相等的：(A ≃ B) ≃ (A = B)

### 应用
- 合成同伦论
- 新的数学基础

---

## 9. 学习路径

### 范畴论入门
1. Category Theory for Programmers（免费在线）
2. Seven Sketches in Compositionality

### 类型论入门
1. Types and Programming Languages（TAPL）
2. Software Foundations

### 进阶
1. Categories for the Working Mathematician
2. Homotopy Type Theory（HoTT Book，免费）

---

## 推荐资源

| 资源 | 内容 |
|------|------|
| Category Theory for Programmers | 程序员友好，免费 |
| Seven Sketches | 应用导向，免费 |
| TAPL | 类型系统圣经 |
| HoTT Book | 同伦类型论，免费 |
| nLab | 范畴论维基百科 |
