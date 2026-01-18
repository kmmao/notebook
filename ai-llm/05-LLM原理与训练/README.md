# 第五阶段：LLM原理与训练（4-6周）

## 概述

理解LLM是如何从Transformer架构发展而来，以及如何训练。

---

## 1. LLM发展历程

```
GPT-1 (2018) → GPT-2 (2019) → GPT-3 (2020) → ChatGPT (2022) → GPT-4 (2023)
         ↓
     BERT (2018) → RoBERTa → ALBERT → ...
         ↓
     T5 (2019) → Flan-T5 → ...
         ↓
     LLaMA (2023) → LLaMA-2 → LLaMA-3 → ...
         ↓
     开源生态：Mistral, Qwen, DeepSeek, Yi, ...
```

---

## 2. Tokenization（分词）

### 为什么需要Tokenization
- 神经网络只能处理数字
- 需要将文本转换为token序列

### 主流方法

| 方法 | 描述 | 使用者 |
|------|------|--------|
| BPE | Byte Pair Encoding | GPT系列 |
| WordPiece | 类似BPE | BERT |
| SentencePiece | 语言无关 | LLaMA, T5 |
| Tiktoken | OpenAI优化版BPE | GPT-3.5/4 |

### BPE算法

```python
# 核心思想：迭代合并最频繁的字符对
vocabulary = list(all_characters)
for i in range(num_merges):
    pair = most_frequent_pair(corpus)
    new_token = merge(pair)
    vocabulary.append(new_token)
    corpus = replace_all(corpus, pair, new_token)
```

---

## 3. 预训练（Pre-training）

### 训练目标：Next Token Prediction

```python
# 给定前面的token，预测下一个
input:  "The cat sat on the"
target: "cat sat on the mat"

loss = CrossEntropy(model(input), target)
```

### 训练数据

| 数据集 | 规模 | 来源 |
|--------|------|------|
| Common Crawl | PB级 | 网页 |
| The Pile | 800GB | 混合 |
| RedPajama | 1.2T tokens | 开源复现 |
| FineWeb | 15T tokens | HuggingFace |

### Scaling Laws

> 性能随模型大小、数据量、计算量的幂律增长

**Chinchilla定律**：
- 模型参数和训练token数应该同比例增长
- 最优比例约为1:20（1B参数需要20B tokens）

---

## 4. 模型架构演进

### GPT风格基础配置

| 组件 | 常见选择 |
|------|----------|
| 位置编码 | RoPE (旋转位置编码) |
| 归一化 | RMSNorm (更高效) |
| 激活函数 | SwiGLU |
| 注意力 | GQA (分组查询注意力) |

### 参数规模

| 模型 | 参数量 | 层数 | 隐藏维度 | 头数 |
|------|--------|------|----------|------|
| GPT-2 | 1.5B | 48 | 1600 | 25 |
| LLaMA-7B | 7B | 32 | 4096 | 32 |
| LLaMA-70B | 70B | 80 | 8192 | 64 |

---

## 5. 训练技术

### 分布式训练

| 技术 | 描述 |
|------|------|
| 数据并行(DP) | 多卡复制模型，分数据 |
| 模型并行(MP) | 模型切分到多卡 |
| 流水线并行(PP) | 按层切分 |
| ZeRO | 优化器状态分片 |
| FSDP | PyTorch官方分布式 |

### 混合精度训练

```python
# FP16/BF16 训练，FP32 主权重
with autocast():
    output = model(input)
    loss = criterion(output, target)

scaler.scale(loss).backward()
scaler.step(optimizer)
```

### 梯度检查点

用计算换显存：
```python
# 不保存中间激活，反向时重新计算
from torch.utils.checkpoint import checkpoint
output = checkpoint(layer, input)
```

---

## 6. 关键论文

| 论文 | 核心贡献 |
|------|----------|
| Attention Is All You Need | Transformer架构 |
| BERT | 双向预训练 |
| GPT-2 | 展示语言模型能力 |
| GPT-3 | In-context Learning |
| Scaling Laws | 规模定律 |
| Chinchilla | 最优训练配比 |
| LLaMA | 开源高质量基座 |

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| Andrej Karpathy - Let's build GPT | YouTube必看 |
| LLM From Scratch (Sebastian Raschka) | 书籍 |
| Hugging Face NLP Course | https://huggingface.co/learn/nlp-course |
| LLM Course (mlabonne) | https://github.com/mlabonne/llm-course |

## 检验标准

1. 能解释BPE分词的工作原理
2. 理解预训练的目标函数
3. 知道Scaling Laws的含义
4. 能说出现代LLM的常见架构选择
