# 第四阶段：Transformer架构（2-4周）

## 概述

Transformer是现代LLM的核心架构，**必须深入理解**。

## 论文原文

> "Attention Is All You Need" - Vaswani et al., 2017

这篇论文开创了一个时代，必读。

---

## 1. 为什么需要Transformer

### RNN的问题
- 串行计算，无法并行
- 长距离依赖困难
- 梯度消失/爆炸

### Transformer的优势
- 完全并行化
- 直接建模任意距离的依赖
- 更好的梯度流动

---

## 2. 核心组件

### Self-Attention（自注意力）

核心公式：
```
Attention(Q, K, V) = softmax(QK^T / √d_k) V
```

- **Q (Query)**: 我要找什么
- **K (Key)**: 我有什么可以被匹配
- **V (Value)**: 我的实际内容

```python
def self_attention(Q, K, V):
    d_k = Q.shape[-1]
    scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(d_k)
    weights = F.softmax(scores, dim=-1)
    output = torch.matmul(weights, V)
    return output
```

### Multi-Head Attention

多个注意力头并行，捕获不同类型的关系：

```python
class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, num_heads):
        super().__init__()
        self.num_heads = num_heads
        self.d_k = d_model // num_heads
        
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
    
    def forward(self, Q, K, V, mask=None):
        # 线性投影并分头
        # 计算注意力
        # 合并多头
        # 输出投影
        pass
```

### Position Encoding（位置编码）

Transformer没有位置概念，需要显式注入：

```python
# 正弦位置编码（原始论文）
PE(pos, 2i) = sin(pos / 10000^(2i/d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d_model))

# 现代方法：RoPE（旋转位置编码）
# LLaMA、Qwen等使用
```

### Feed-Forward Network（FFN）

```python
FFN(x) = GELU(xW_1 + b_1)W_2 + b_2
```

现代变体：
- SwiGLU（LLaMA使用）
- GeGLU

### Layer Normalization

```python
# Pre-LN（现代LLM常用）
x = x + Attention(LayerNorm(x))
x = x + FFN(LayerNorm(x))

# Post-LN（原始论文）
x = LayerNorm(x + Attention(x))
```

---

## 3. 完整架构

### Encoder-Decoder（原始Transformer）
- 用于机器翻译
- BERT使用Encoder
- T5使用Encoder-Decoder

### Decoder-Only（GPT风格）
- 现代LLM主流
- GPT、LLaMA、Claude、Qwen都是这种
- 自回归生成

```
[输入tokens] → Embedding → [Decoder Block × N] → Linear → [输出概率]

Decoder Block:
├── Masked Multi-Head Attention
├── Add & Norm
├── Feed-Forward Network
└── Add & Norm
```

---

## 4. 关键技术细节

### Causal Mask（因果掩码）

确保生成时只能看到之前的token：

```python
# 下三角矩阵
mask = torch.tril(torch.ones(seq_len, seq_len))
```

### KV Cache

推理加速的关键：
- 缓存已计算的K和V
- 新token只需计算自己的Q

---

## 5. 动手实现

强烈推荐从零实现一个Transformer：

| 资源 | 链接 |
|------|------|
| Annotated Transformer | https://nlp.seas.harvard.edu/annotated-transformer/ |
| nanoGPT | https://github.com/karpathy/nanoGPT |
| minGPT | https://github.com/karpathy/minGPT |
| Build GPT from Scratch | Andrej Karpathy YouTube |

---

## 推荐学习顺序

1. 看3Blue1Brown的Attention可视化
2. 读Annotated Transformer
3. 自己从零实现一个小Transformer
4. 跑通nanoGPT训练

## 检验标准

1. 能手写Self-Attention的代码
2. 能解释Q、K、V的作用
3. 理解为什么要除以√d_k
4. 能说明Causal Mask的作用
5. 理解KV Cache的原理
