# 学习执行计划

> 英语 + AI/LLM 并行，用英文学AI

## 当前状态

- 英语阅读：OK
- 英语听力：弱
- AI基础：零

## 核心策略

```
用英文材料学AI → 同时提升两者
听力专项突破 → 每天1小时磨耳朵
```

---

## 每日时间分配

| 时段 | 内容 | 时长 | 语言 |
|------|------|------|------|
| 早起 | 英文技术播客（磨耳朵） | 30min | EN |
| 通勤/碎片 | 播客继续或Hacker News | 30min-1h | EN |
| 主学习时间 | AI/LLM课程学习 | 2-3h | EN |
| 睡前 | 轻量阅读/复习 | 30min | EN |

**每日最低投入：3小时**
**建议投入：4-5小时**

---

## Phase 1: 基础建立（Week 1-8）

### AI/LLM 主线

**教材**：3Blue1Brown + Andrej Karpathy

| 周 | 内容 | 材料 |
|----|------|------|
| Week 1-2 | 神经网络直觉 | [3Blue1Brown Neural Networks](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) |
| Week 3-4 | 从零实现神经网络 | [Andrej Karpathy: Neural Networks Zero to Hero](https://www.youtube.com/playlist?list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ) - Part 1-2 |
| Week 5-6 | Backprop + 语言模型 | Karpathy Part 3-4 |
| Week 7-8 | GPT from scratch | Karpathy Part 5-7 |

**每个视频的学习方法**：
1. 第一遍：纯听，不暂停，练听力
2. 第二遍：开英文字幕，做笔记
3. 第三遍：跟着敲代码

### 英语听力 专项

**核心资源**：

| 难度 | 资源 | 用途 |
|------|------|------|
| 入门 | Karpathy视频（语速慢，技术清晰） | 主力 |
| 中级 | [Latent Space Podcast](https://www.latent.space/podcast) | AI领域播客 |
| 中级 | [Lex Fridman](https://www.youtube.com/@lexfridman) | 长对话 |
| 高级 | 技术会议视频（NeurIPS, ICML） | 后期 |

**听力训练法**：
1. 第一遍盲听 → 记录听懂百分比
2. 开字幕再听 → 标记没听出的词
3. 跟读（Shadowing）→ 模仿语调节奏
4. 一周后重听 → 验证进步

---

## Phase 2: 深入理解（Week 9-20）

### AI/LLM 主线

| 周 | 内容 | 材料 |
|----|------|------|
| Week 9-12 | 机器学习基础 | [Stanford CS229](https://www.youtube.com/playlist?list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU) 选看 |
| Week 13-16 | Transformer深入 | [Attention Is All You Need](https://arxiv.org/abs/1706.03762) + 解读视频 |
| Week 17-20 | LLM原理 | [Stanford CS324](https://stanford-cs324.github.io/winter2022/) |

### 实践项目

- [ ] 用NumPy实现一个简单神经网络
- [ ] 用PyTorch复现一个小型语言模型
- [ ] 在HuggingFace上微调一个模型

---

## 每周检验点

### 英语听力

| 周 | 目标 |
|----|------|
| Week 2 | Karpathy视频盲听懂60% |
| Week 4 | Karpathy视频盲听懂80% |
| Week 8 | Latent Space播客听懂50% |
| Week 12 | Lex Fridman技术访谈听懂60% |

### AI/LLM

| 周 | 目标 |
|----|------|
| Week 2 | 能解释什么是神经网络、梯度下降 |
| Week 4 | 能手写backpropagation |
| Week 8 | 能解释GPT的核心原理 |
| Week 12 | 能用PyTorch实现简单模型 |

---

## 立即行动（今天）

- [ ] 打开 [Karpathy Neural Networks: Zero to Hero](https://www.youtube.com/playlist?list=PLAqhIrjkxbuWI23v9cThsA9GvCAUhRvKZ)
- [ ] 第一个视频 "The spelled-out intro to neural networks and backpropagation"（2.5小时）
- [ ] 第一遍纯听，不开字幕，记录听懂多少
- [ ] 第二遍开字幕，做笔记，跟着敲代码

---

## 工具准备

```bash
# Python环境
brew install python3
pip install numpy matplotlib jupyter torch

# 笔记
# 用英文记，哪怕很烂，逼自己写
```

---

## 禁止事项

| 禁止 | 原因 |
|------|------|
| 看中文翻译视频 | 浪费英语练习机会 |
| 开中文字幕 | 大脑会偷懒 |
| "先背单词再开始" | 拖延借口 |
| 等"准备好"再写代码 | 边学边写 |
