# 第六阶段：Prompt Engineering（2-3周）

## 概述

Prompt Engineering是与LLM交互的艺术，是最快产出价值的技能。

---

## 1. 基础技巧

### Zero-Shot Prompting
直接提问，不给示例

```
请将以下英文翻译成中文：
"Hello, how are you?"
```

### Few-Shot Prompting
提供几个示例

```
将英文翻译成中文：
English: Hello → Chinese: 你好
English: Thank you → Chinese: 谢谢
English: How are you? → Chinese:
```

### 指令格式

```
你是一个专业的Python开发者。

任务：审查以下代码并指出潜在问题。

代码：
{code}

请按以下格式输出：
1. 问题描述
2. 严重程度（高/中/低）
3. 修复建议
```

---

## 2. 高级技巧

### Chain-of-Thought (CoT)

让模型展示推理过程：

```
问题：一个商店有23个苹果，卖出了17个，又进货了12个，现在有多少个？

让我们一步步思考：
1. 初始：23个苹果
2. 卖出17个：23 - 17 = 6个
3. 进货12个：6 + 12 = 18个
答案：18个苹果
```

### Self-Consistency
多次采样，取多数答案

### Tree of Thoughts (ToT)
探索多个推理路径

### ReAct (Reasoning + Acting)
结合推理和行动

```
问题：2024年奥运会在哪里举办？

思考：我需要查找2024年奥运会的举办地
行动：搜索 "2024 Olympics host city"
观察：巴黎是2024年夏季奥运会的举办城市
思考：我找到了答案
答案：2024年夏季奥运会在法国巴黎举办
```

---

## 3. 提示词模板

### 系统提示词结构

```
# Role（角色）
你是一个{专业角色}，擅长{技能}。

# Context（背景）
{相关背景信息}

# Task（任务）
{具体要完成的任务}

# Format（格式）
请按以下格式输出：
{输出格式要求}

# Constraints（约束）
注意事项：
- {约束1}
- {约束2}

# Examples（示例）[可选]
示例输入：{input}
示例输出：{output}
```

### 代码生成模板

```
语言：Python 3.10+
任务：{task_description}
要求：
- 使用类型提示
- 包含docstring
- 处理边界情况
- 不使用外部库（除非必要）

只输出代码，不要解释。
```

---

## 4. 常见问题与技巧

### 提高准确性
- 明确具体的指令
- 提供示例
- 要求格式化输出
- 使用分隔符区分不同部分

### 减少幻觉
- 要求引用来源
- 让模型表达不确定性
- 提供相关上下文（RAG）

### 处理长文本
- 分块处理
- 摘要后再处理
- 使用结构化提取

---

## 5. Prompt工具与平台

| 工具 | 用途 |
|------|------|
| LangChain | Prompt模板管理 |
| LlamaIndex | 与数据交互 |
| PromptFlow | 微软的提示流程编排 |
| Anthropic Workbench | Claude测试平台 |
| OpenAI Playground | GPT测试平台 |

---

## 6. 实战练习

1. **文本分类**：设计一个情感分析prompt
2. **信息提取**：从非结构化文本提取结构化数据
3. **代码生成**：让LLM生成特定功能的代码
4. **多轮对话**：设计一个客服机器人的系统提示词

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| Prompt Engineering Guide | https://www.promptingguide.ai/ |
| Learn Prompting | https://learnprompting.org/ |
| OpenAI Prompt Engineering | https://platform.openai.com/docs/guides/prompt-engineering |
| Anthropic Prompt Guide | https://docs.anthropic.com/claude/docs/prompt-engineering |

## 检验标准

1. 能设计有效的Few-Shot提示词
2. 理解Chain-of-Thought的原理和使用场景
3. 能结构化地编写系统提示词
4. 知道如何减少LLM的幻觉
