# 第十一阶段：工具与框架

## 核心工具清单

---

## 1. 深度学习框架

| 框架 | 特点 | 使用场景 |
|------|------|----------|
| **PyTorch** | 动态图，研究首选 | 模型开发、训练 |
| TensorFlow | 静态图，生产部署 | 大规模部署 |
| JAX | 函数式，TPU友好 | Google系研究 |

### PyTorch核心模块

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, Dataset
```

---

## 2. Hugging Face生态

### Transformers

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

inputs = tokenizer("Hello, world!", return_tensors="pt")
outputs = model.generate(**inputs, max_new_tokens=50)
print(tokenizer.decode(outputs[0]))
```

### 核心库

| 库 | 用途 |
|-----|------|
| transformers | 模型加载与推理 |
| datasets | 数据集处理 |
| tokenizers | 高效分词 |
| accelerate | 分布式训练 |
| peft | 参数高效微调 |
| trl | 强化学习微调 |
| evaluate | 评估指标 |

### Hugging Face Hub
- 模型托管与分享
- 数据集托管
- Spaces（应用部署）

---

## 3. LLM应用框架

| 框架 | 特点 | 适用场景 |
|------|------|----------|
| **LangChain** | 功能全面，生态丰富 | 复杂应用 |
| **LlamaIndex** | 数据连接专精 | RAG应用 |
| Haystack | 企业级搜索 | 搜索系统 |
| Semantic Kernel | 微软出品 | .NET/企业 |

---

## 4. 推理引擎

| 引擎 | 特点 | 适用场景 |
|------|------|----------|
| **vLLM** | 高吞吐，PagedAttention | 生产服务 |
| **Ollama** | 易用，本地部署 | 开发测试 |
| llama.cpp | CPU友好，量化支持 | 边缘设备 |
| TensorRT-LLM | NVIDIA优化 | GPU推理 |
| SGLang | 高效调度 | 高并发 |
| Text Generation Inference | HuggingFace出品 | 云部署 |

### vLLM使用

```python
from vllm import LLM, SamplingParams

llm = LLM(model="meta-llama/Llama-2-7b-hf")
sampling_params = SamplingParams(temperature=0.8, max_tokens=256)

outputs = llm.generate(prompts, sampling_params)
```

---

## 5. 向量数据库

| 数据库 | 特点 | 部署方式 |
|--------|------|----------|
| **Chroma** | 轻量级，易上手 | 嵌入式/服务 |
| **Pinecone** | 全托管 | 云服务 |
| Milvus | 高性能，可扩展 | 自托管/云 |
| Weaviate | 功能丰富 | 自托管/云 |
| Qdrant | Rust实现，高效 | 自托管/云 |
| pgvector | PostgreSQL扩展 | 已有PG环境 |
| FAISS | 本地向量索引 | 嵌入式 |

---

## 6. 实验与监控

| 工具 | 用途 |
|------|------|
| **Weights & Biases** | 实验追踪 |
| MLflow | 模型生命周期 |
| **LangSmith** | LLM应用监控 |
| Langfuse | 开源LLM监控 |
| Phoenix | 可观测性 |

---

## 7. 标注工具

| 工具 | 用途 |
|------|------|
| Label Studio | 通用标注 |
| Argilla | LLM数据标注 |
| Prodigy | 高效标注 |

---

## 8. 开发环境

### GPU云平台

| 平台 | 特点 |
|------|------|
| Lambda Labs | 性价比高 |
| RunPod | 按需计费 |
| Vast.ai | 市场化定价 |
| Google Colab | 免费入门 |
| Kaggle Notebooks | 免费GPU |

### 本地环境

```bash
# Conda环境
conda create -n llm python=3.10
conda activate llm

# 核心依赖
pip install torch transformers accelerate
pip install langchain langchain-openai
pip install chromadb sentence-transformers
```

---

## 9. 常用命令速查

### Hugging Face CLI

```bash
# 登录
huggingface-cli login

# 下载模型
huggingface-cli download meta-llama/Llama-2-7b-hf

# 上传模型
huggingface-cli upload my-model ./model-path
```

### Ollama

```bash
# 运行模型
ollama run llama3

# 拉取模型
ollama pull mistral

# 列出模型
ollama list
```

---

## 工具选型建议

### 入门阶段
- PyTorch + Transformers
- Ollama（本地测试）
- Chroma（向量库）
- LangChain（应用开发）

### 生产阶段
- vLLM（推理服务）
- Pinecone/Milvus（向量库）
- LangSmith（监控）
- Docker/K8s（部署）
