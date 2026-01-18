# 第六阶段：数论与密码学（6-8周）

## 概述

数论是"数学皇后"，是现代密码学的数学基础。

---

## 1. 整除与素数

### 整除

```
a | b (a整除b) ⟺ ∃k: b = ak
```

### 素数

```
素数：只有1和自身两个因子的正整数（>1）
算术基本定理：每个正整数都唯一分解为素数乘积
```

### 素数分布

```
π(n) ≈ n/ln(n)  （素数定理）
```

### 素性测试

| 算法 | 类型 | 复杂度 |
|------|------|--------|
| 试除法 | 确定性 | O(√n) |
| Miller-Rabin | 概率性 | O(k log³n) |
| AKS | 确定性 | O(log^6 n) |

---

## 2. 最大公约数

### 欧几里得算法

```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```

### 扩展欧几里得

求 ax + by = gcd(a,b) 的解

```python
def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0
    g, x, y = extended_gcd(b, a % b)
    return g, y, x - (a // b) * y
```

### 贝祖定理
gcd(a,b) = d ⟺ ∃x,y: ax + by = d

---

## 3. 模运算

### 基本性质

```
(a + b) mod n = ((a mod n) + (b mod n)) mod n
(a × b) mod n = ((a mod n) × (b mod n)) mod n
```

### 同余

```
a ≡ b (mod n) ⟺ n | (a - b)
```

### 模逆元

a⁻¹ mod n 存在 ⟺ gcd(a, n) = 1

求法：扩展欧几里得

---

## 4. 费马小定理与欧拉定理

### 费马小定理
若p是素数，gcd(a,p) = 1：
```
a^(p-1) ≡ 1 (mod p)
```

### 欧拉函数
φ(n) = 小于n且与n互素的正整数个数

```
φ(p) = p - 1              （p是素数）
φ(pᵏ) = pᵏ - pᵏ⁻¹
φ(mn) = φ(m)φ(n)          （gcd(m,n)=1）
```

### 欧拉定理
若gcd(a,n) = 1：
```
a^φ(n) ≡ 1 (mod n)
```

---

## 5. 中国剩余定理（CRT）

若 gcd(nᵢ, nⱼ) = 1 对所有i≠j：

```
x ≡ a₁ (mod n₁)
x ≡ a₂ (mod n₂)
...
x ≡ aₖ (mod nₖ)
```

有唯一解 x mod (n₁n₂...nₖ)

---

## 6. 原根与离散对数

### 原根
g是模n的原根 ⟺ g的阶是φ(n)

### 离散对数问题（DLP）
给定g, h, p，求x使得 g^x ≡ h (mod p)

**困难性**：没有已知的多项式时间算法（密码学基础）

---

## 7. RSA密码系统

### 密钥生成
1. 选择大素数p, q
2. n = pq
3. φ(n) = (p-1)(q-1)
4. 选e使得gcd(e, φ(n)) = 1
5. 计算d = e⁻¹ mod φ(n)
6. 公钥：(n, e)，私钥：d

### 加密解密
```
加密：c = m^e mod n
解密：m = c^d mod n
```

### 安全性
基于大整数分解的困难性

---

## 8. 椭圆曲线密码学（ECC）

### 椭圆曲线
```
y² = x³ + ax + b (mod p)
```

### 点的加法
几何定义 → 代数公式

### ECDLP
给定点P和Q = kP，求k

### 优势
- 相同安全级别下密钥更短
- 256位ECC ≈ 3072位RSA

---

## 9. 格密码学（Lattice Cryptography）

### 格（Lattice）
```
L = {Σ aᵢvᵢ : aᵢ ∈ ℤ}，其中v₁,...,vₙ是基向量
```

### 困难问题
- **SVP**：找最短向量
- **CVP**：找最近向量
- **LWE**：Learning with Errors

### 后量子密码
格密码被认为能抵抗量子计算机攻击

---

## 10. 密码学应用总结

| 数学问题 | 密码应用 |
|----------|----------|
| 大整数分解 | RSA |
| 离散对数（有限域） | Diffie-Hellman, DSA |
| 椭圆曲线离散对数 | ECDH, ECDSA |
| 格问题（LWE） | Kyber, Dilithium（后量子） |

---

## 推荐资源

| 资源 | 内容 |
|------|------|
| An Introduction to Mathematical Cryptography | Silverman等，数学密码学入门 |
| A Computational Introduction to Number Theory and Algebra | Shoup，免费在线 |
| Elliptic Curves: Number Theory and Cryptography | Washington |
| 格密码学入门 | Lyubashevsky的教程 |
