# 第九阶段：LLM应用开发（4-6周）

## 概述

将LLM集成到实际应用中，构建AI产品。

---

## 1. LLM API调用

### OpenAI API

```python
from openai import OpenAI

client = OpenAI(api_key="your-key")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手"},
        {"role": "user", "content": "你好"}
    ],
    temperature=0.7,
    max_tokens=1000
)

print(response.choices[0].message.content)
```

### 流式输出

```python
stream = client.chat.completions.create(
    model="gpt-4",
    messages=messages,
    stream=True
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

### 其他API
- Anthropic Claude
- Google Gemini
- 本地模型（Ollama、vLLM）

---

## 2. LangChain框架

### 核心概念

```
Prompts → Models → Output Parsers
    ↓
Chains（链式调用）
    ↓
Agents（自主决策）
    ↓
Memory（对话记忆）
```

### 基础链

```python
from langchain.prompts import ChatPromptTemplate
from langchain.chat_models import ChatOpenAI
from langchain.schema.output_parser import StrOutputParser

prompt = ChatPromptTemplate.from_template(
    "用一句话解释什么是{topic}"
)

chain = prompt | ChatOpenAI() | StrOutputParser()

result = chain.invoke({"topic": "机器学习"})
```

### LCEL（LangChain Expression Language）

```python
# 链式组合
chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | output_parser
)
```

---

## 3. Agent开发

### ReAct Agent

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain.tools import Tool

tools = [
    Tool(
        name="Search",
        func=search_function,
        description="搜索互联网获取信息"
    ),
    Tool(
        name="Calculator",
        func=calculator_function,
        description="执行数学计算"
    )
]

agent = create_react_agent(llm, tools, prompt)
executor = AgentExecutor(agent=agent, tools=tools)

result = executor.invoke({"input": "2024年中国GDP是多少？"})
```

### Function Calling

```python
functions = [
    {
        "name": "get_weather",
        "description": "获取指定城市的天气",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "城市名"}
            },
            "required": ["city"]
        }
    }
]

response = client.chat.completions.create(
    model="gpt-4",
    messages=messages,
    functions=functions,
    function_call="auto"
)
```

---

## 4. 本地模型部署

### Ollama

```bash
# 安装
curl -fsSL https://ollama.com/install.sh | sh

# 运行模型
ollama run llama3

# API调用
curl http://localhost:11434/api/generate -d '{
  "model": "llama3",
  "prompt": "你好"
}'
```

### vLLM（高性能推理）

```python
from vllm import LLM, SamplingParams

llm = LLM(model="meta-llama/Llama-2-7b-hf")
sampling_params = SamplingParams(temperature=0.8, max_tokens=100)

outputs = llm.generate(["你好，请介绍一下你自己"], sampling_params)
```

### 推理优化技术

| 技术 | 描述 |
|------|------|
| KV Cache | 缓存已计算的Key/Value |
| Continuous Batching | 动态批处理 |
| PagedAttention | vLLM的内存管理 |
| 量化（GPTQ/AWQ） | 减少模型大小 |
| 投机解码 | 小模型加速大模型 |

---

## 5. 应用架构

### 典型架构

```
┌─────────────────────────────────────────┐
│                 Frontend                 │
│            (Web/Mobile App)              │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────▼──────────────────────┐
│              API Gateway                 │
│         (认证、限流、路由)                │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────▼──────────────────────┐
│           Application Layer              │
│   (LangChain/LlamaIndex + 业务逻辑)      │
├──────────────────────────────────────────┤
│  ┌─────────┐ ┌─────────┐ ┌─────────┐   │
│  │  RAG    │ │ Agents  │ │ Memory  │   │
│  └─────────┘ └─────────┘ └─────────┘   │
└──────────────────┬──────────────────────┘
                   │
┌──────────────────▼──────────────────────┐
│            Model Layer                   │
├─────────────┬─────────────┬─────────────┤
│   OpenAI    │   Claude    │  本地模型   │
└─────────────┴─────────────┴─────────────┘
```

### 生产注意事项

- **错误处理**：LLM调用可能失败
- **成本控制**：token计数、缓存
- **延迟优化**：流式输出、预加载
- **安全性**：Prompt注入防护
- **可观测性**：日志、监控、追踪

---

## 6. 评估与监控

### LLM评估

| 工具 | 用途 |
|------|------|
| LangSmith | LangChain官方监控 |
| Weights & Biases | 实验追踪 |
| Langfuse | 开源替代 |
| Phoenix (Arize) | 可观测性 |

### 评估指标
- 回答准确性
- 响应延迟
- Token使用量
- 用户满意度

---

## 实战项目建议

1. **智能文档问答**：RAG + 对话
2. **代码助手**：代码生成 + 解释
3. **数据分析Agent**：自然语言查询数据库
4. **多模态应用**：图像 + 文本

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| LangChain文档 | https://python.langchain.com/docs/ |
| LlamaIndex | https://docs.llamaindex.ai/ |
| OpenAI Cookbook | https://cookbook.openai.com/ |
| Ollama | https://ollama.com/ |

## 检验标准

1. 能使用LangChain构建一个完整应用
2. 能部署本地模型并调用
3. 理解Agent的工作原理
4. 知道如何优化LLM应用的性能
