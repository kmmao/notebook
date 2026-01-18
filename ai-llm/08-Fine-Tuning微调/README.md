# 第八阶段：Fine-Tuning微调（4-6周）

## 概述

微调是让LLM适应特定任务的关键技术。

---

## 1. 何时需要微调

### RAG vs Fine-Tuning vs Prompt Engineering

| 方法 | 适用场景 | 成本 | 效果持久性 |
|------|----------|------|------------|
| Prompt Engineering | 快速原型、简单任务 | 低 | 每次推理都需要 |
| RAG | 需要外部知识、实时数据 | 中 | - |
| Fine-Tuning | 特定风格、领域专业性 | 高 | 永久 |

### 微调的适用场景
- 特定的输出格式/风格
- 领域专业术语
- 提高特定任务的性能
- 减少推理成本（小模型微调替代大模型）

---

## 2. 微调方法概览

### 全参数微调（Full Fine-Tuning）
- 更新所有参数
- 需要大量显存
- 效果最好但成本最高

### 参数高效微调（PEFT）

| 方法 | 原理 | 可训练参数 |
|------|------|------------|
| LoRA | 低秩分解 | ~0.1% |
| QLoRA | LoRA + 量化 | ~0.1% |
| Prefix Tuning | 可学习前缀 | ~0.1% |
| Adapter | 插入适配层 | ~1-5% |
| IA3 | 激活缩放 | 更少 |

---

## 3. LoRA详解

### 核心思想
```
原始权重W不变，学习低秩更新：
W' = W + BA

其中：
- W: 原始权重 (d × d)
- B: (d × r) 矩阵
- A: (r × d) 矩阵
- r << d (秩远小于维度)
```

### 代码示例

```python
from peft import LoraConfig, get_peft_model

lora_config = LoraConfig(
    r=8,                        # 秩
    lora_alpha=32,              # 缩放因子
    target_modules=["q_proj", "v_proj"],  # 应用到哪些层
    lora_dropout=0.1,
    task_type="CAUSAL_LM"
)

model = get_peft_model(base_model, lora_config)
print(f"可训练参数: {model.print_trainable_parameters()}")
```

### QLoRA
在LoRA基础上，将基座模型量化到4-bit：
- 显存需求大幅降低
- 可在消费级GPU上微调大模型

---

## 4. 监督式微调（SFT）

### 数据格式

```json
{
  "instruction": "将以下文本翻译成英文",
  "input": "你好世界",
  "output": "Hello World"
}
```

或对话格式：
```json
{
  "conversations": [
    {"role": "user", "content": "你好"},
    {"role": "assistant", "content": "你好！有什么可以帮助你的？"}
  ]
}
```

### 训练代码

```python
from transformers import Trainer, TrainingArguments
from trl import SFTTrainer

training_args = TrainingArguments(
    output_dir="./output",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    learning_rate=2e-5,
    warmup_steps=100,
    logging_steps=10,
    save_steps=500,
    bf16=True,  # 混合精度
)

trainer = SFTTrainer(
    model=model,
    args=training_args,
    train_dataset=dataset,
    tokenizer=tokenizer,
    max_seq_length=2048,
)

trainer.train()
```

---

## 5. RLHF（人类反馈强化学习）

### 三阶段流程

```
阶段1: SFT
预训练模型 → 监督微调 → SFT模型

阶段2: Reward Model
收集人类偏好数据 → 训练奖励模型

阶段3: PPO
SFT模型 + 奖励模型 → PPO训练 → 最终模型
```

### DPO（Direct Preference Optimization）
跳过奖励模型，直接从偏好数据学习：

```python
from trl import DPOTrainer

trainer = DPOTrainer(
    model=model,
    ref_model=ref_model,
    train_dataset=dataset,  # 包含chosen和rejected
    tokenizer=tokenizer,
    beta=0.1,  # KL惩罚系数
)
```

---

## 6. 微调工具

| 工具 | 特点 |
|------|------|
| HuggingFace PEFT | 官方PEFT库 |
| TRL | 强化学习微调 |
| Axolotl | 配置驱动，易用 |
| LLaMA-Factory | 中文友好，功能全 |
| Unsloth | 速度优化，显存优化 |
| DeepSpeed | 分布式训练 |

---

## 7. 微调最佳实践

### 数据质量
- 质量 > 数量
- 多样性很重要
- 格式一致性

### 超参数
- 学习率：1e-5 到 5e-5
- Epochs：3-5（小数据集可更多）
- Batch size：根据显存调整
- LoRA rank：8-64

### 避免灾难性遗忘
- 混入部分通用数据
- 使用较小学习率
- 监控验证集损失

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| HuggingFace PEFT | https://huggingface.co/docs/peft |
| LLaMA-Factory | https://github.com/hiyouga/LLaMA-Factory |
| Axolotl | https://github.com/OpenAccess-AI-Collective/axolotl |
| Unsloth | https://github.com/unslothai/unsloth |

## 检验标准

1. 能用LoRA微调一个开源模型
2. 理解LoRA的数学原理
3. 知道SFT和RLHF的区别
4. 能准备合适的微调数据集
