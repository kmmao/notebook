# 第三阶段：深度学习基础（4-6周）

## 概述

神经网络是LLM的基础，必须扎实掌握。

## 1. 神经网络基础

### 感知机到多层网络

```
输入层 → 隐藏层(s) → 输出层
     权重W    激活函数    权重W
```

### 前向传播

```python
# 单层前向传播
z = np.dot(W, x) + b  # 线性变换
a = activation(z)      # 非线性激活
```

### 激活函数

| 函数 | 公式 | 特点 |
|------|------|------|
| Sigmoid | 1/(1+e^-x) | 输出0-1，梯度消失 |
| Tanh | (e^x-e^-x)/(e^x+e^-x) | 输出-1到1 |
| ReLU | max(0,x) | 简单高效，死亡ReLU |
| GELU | x·Φ(x) | Transformer常用 |
| SiLU/Swish | x·sigmoid(x) | 现代LLM常用 |

### 反向传播

- 链式法则计算梯度
- 从输出层向输入层传播误差
- 更新每层的权重

---

## 2. 优化器

| 优化器 | 特点 |
|--------|------|
| SGD | 基础，需要调学习率 |
| Momentum | 加速收敛 |
| Adam | 自适应学习率，最常用 |
| AdamW | Adam + 权重衰减，LLM首选 |

### Adam优化器

```python
# Adam核心思想
m = β1 * m + (1 - β1) * gradient      # 一阶动量
v = β2 * v + (1 - β2) * gradient²     # 二阶动量
θ = θ - lr * m / (√v + ε)             # 参数更新
```

---

## 3. CNN（卷积神经网络）

虽然LLM不直接使用CNN，但理解其思想有助于理解深度学习。

### 核心组件
- 卷积层：局部特征提取
- 池化层：下采样
- 全连接层：分类

### 关键概念
- 参数共享
- 局部连接
- 平移不变性

---

## 4. RNN（循环神经网络）

Transformer出现前的序列建模主流方法。

### 基本结构

```
h_t = tanh(W_hh · h_{t-1} + W_xh · x_t + b)
```

### 问题
- 长距离依赖困难
- 梯度消失/爆炸
- 无法并行计算

### LSTM/GRU
- 引入门控机制
- 缓解梯度问题
- 但仍是串行计算

**这就是为什么Transformer革命性地取代了RNN**

---

## 5. 正则化技术

| 技术 | 描述 |
|------|------|
| Dropout | 随机丢弃神经元 |
| Batch Normalization | 层间归一化 |
| Layer Normalization | LLM常用 |
| Weight Decay | L2正则化 |

---

## 6. PyTorch实战

### 基本模型定义

```python
import torch
import torch.nn as nn

class SimpleNN(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.fc1 = nn.Linear(input_dim, hidden_dim)
        self.fc2 = nn.Linear(hidden_dim, output_dim)
        self.relu = nn.ReLU()
    
    def forward(self, x):
        x = self.relu(self.fc1(x))
        x = self.fc2(x)
        return x
```

### 训练循环

```python
model = SimpleNN(784, 256, 10)
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
criterion = nn.CrossEntropyLoss()

for epoch in range(epochs):
    for batch_x, batch_y in dataloader:
        optimizer.zero_grad()
        output = model(batch_x)
        loss = criterion(output, batch_y)
        loss.backward()
        optimizer.step()
```

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| 3Blue1Brown神经网络 | https://www.3blue1brown.com/topics/neural-networks |
| CS231n (Stanford) | https://cs231n.stanford.edu/ |
| Deep Learning Book | https://www.deeplearningbook.org/ |
| PyTorch官方教程 | https://pytorch.org/tutorials/ |
| fast.ai | https://www.fast.ai/ |

## 检验标准

1. 能从零用PyTorch实现一个MLP
2. 能解释反向传播的工作原理
3. 理解为什么需要非线性激活函数
4. 能说出Adam优化器相比SGD的优势
