# 第七阶段：RAG系统（3-4周）

## 概述

RAG（Retrieval-Augmented Generation）是让LLM使用外部知识的关键技术。

---

## 1. 为什么需要RAG

### LLM的局限性
- 知识截止日期
- 无法访问私有数据
- 幻觉问题
- 上下文长度限制

### RAG的优势
- 接入最新信息
- 使用私有知识库
- 提供可溯源的答案
- 减少幻觉

---

## 2. RAG基本架构

```
用户查询
    ↓
[查询处理] → [向量化]
    ↓
[向量检索] ← 向量数据库（文档chunks的embeddings）
    ↓
[获取相关文档]
    ↓
[构造Prompt] = 系统提示 + 检索到的上下文 + 用户问题
    ↓
[LLM生成回答]
    ↓
返回给用户
```

---

## 3. 核心组件

### 文档处理

```python
# 文档加载
from langchain.document_loaders import PyPDFLoader
loader = PyPDFLoader("document.pdf")
documents = loader.load()

# 文本切分
from langchain.text_splitter import RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200
)
chunks = splitter.split_documents(documents)
```

### Embedding模型

| 模型 | 提供者 | 特点 |
|------|--------|------|
| text-embedding-3-small | OpenAI | 高质量，付费 |
| BGE | BAAI | 开源，中文友好 |
| E5 | Microsoft | 开源，多语言 |
| Jina Embeddings | Jina AI | 长文本支持 |
| Cohere Embed | Cohere | 多语言 |

### 向量数据库

| 数据库 | 特点 |
|--------|------|
| Chroma | 轻量，适合原型 |
| Pinecone | 托管服务，易用 |
| Weaviate | 功能丰富 |
| Milvus | 高性能，可扩展 |
| Qdrant | Rust实现，高效 |
| pgvector | PostgreSQL扩展 |
| FAISS | Meta出品，本地向量检索 |

```python
# Chroma示例
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings

vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=OpenAIEmbeddings()
)

# 检索
results = vectorstore.similarity_search(query, k=5)
```

---

## 4. 高级RAG技术

### 查询改写（Query Rewriting）
```
原始查询："它是什么时候发布的？"
改写后："GPT-4是什么时候发布的？"（根据对话历史）
```

### 混合检索（Hybrid Search）
结合向量检索和关键词检索：
```
最终得分 = α × 向量相似度 + (1-α) × BM25得分
```

### 重排序（Reranking）
用更精确的模型对检索结果重新排序：
```python
from sentence_transformers import CrossEncoder
reranker = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')
scores = reranker.predict([(query, doc) for doc in retrieved_docs])
```

### 分块策略

| 策略 | 适用场景 |
|------|----------|
| 固定大小 | 简单文档 |
| 句子/段落 | 保持语义完整 |
| 递归分割 | 层次化文档 |
| 语义分块 | 根据内容语义切分 |

### Parent Document Retrieval
检索小块，返回大块上下文

### Self-RAG
模型自己决定是否需要检索

---

## 5. 评估RAG系统

### 评估维度

| 维度 | 描述 |
|------|------|
| 检索质量 | Precision@K, Recall@K, MRR |
| 生成质量 | 相关性、准确性、完整性 |
| 端到端 | 最终回答的正确率 |

### 评估工具
- RAGAS
- TruLens
- LangSmith

---

## 6. 实战项目

### 基础RAG

```python
from langchain.chains import RetrievalQA
from langchain.chat_models import ChatOpenAI

qa_chain = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4"),
    chain_type="stuff",
    retriever=vectorstore.as_retriever()
)

answer = qa_chain.run("什么是机器学习？")
```

### 对话式RAG

```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationalRetrievalChain

memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

qa_chain = ConversationalRetrievalChain.from_llm(
    llm=ChatOpenAI(),
    retriever=vectorstore.as_retriever(),
    memory=memory
)
```

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| LangChain RAG教程 | https://python.langchain.com/docs/tutorials/rag/ |
| LlamaIndex | https://docs.llamaindex.ai/ |
| RAG论文 | https://arxiv.org/abs/2005.11401 |

## 检验标准

1. 能搭建一个基础的RAG系统
2. 理解Embedding和向量检索的原理
3. 知道常见的RAG优化技术
4. 能评估RAG系统的效果
