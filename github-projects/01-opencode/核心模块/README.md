# OpenCode 核心模块分析

## 模块概览

```
packages/
├── core/           # 核心逻辑
├── cli/            # 命令行界面
├── desktop/        # 桌面应用
├── shared/         # 共享代码
└── models/         # 模型抽象
```

## 1. Session Manager（会话管理器）

### 职责
- 创建/销毁会话
- 会话状态管理
- 会话持久化
- 多会话协调

### 核心接口

```typescript
interface Session {
  id: string;
  projectPath: string;
  messages: Message[];
  context: Context;
  model: ModelConfig;
  createdAt: Date;
  lastActiveAt: Date;
}

interface SessionManager {
  create(projectPath: string): Session;
  get(id: string): Session | null;
  list(): Session[];
  delete(id: string): void;
  save(session: Session): void;
  load(id: string): Session;
}
```

### 研究笔记

<!-- 阅读源码后在这里记录 -->

---

## 2. Agent Runtime（Agent运行时）

### 职责
- 消息处理循环
- 工具调用执行
- 流式响应处理
- 错误恢复

### 核心流程

```
用户输入
    ↓
构建消息 + 上下文
    ↓
调用LLM
    ↓
解析响应
    ↓
[工具调用?] ──是──→ 执行工具 → 返回结果 → 继续调用LLM
    │
    否
    ↓
输出响应
```

### 研究笔记

<!-- 阅读源码后在这里记录 -->

---

## 3. Context Manager（上下文管理器）

### 职责
- 项目文件索引
- 相关文件检索
- 上下文压缩
- Token计数

### 上下文构建策略

1. **当前文件** - 用户正在编辑的文件
2. **相关文件** - 通过import/require关联的文件
3. **最近文件** - 最近访问的文件
4. **搜索结果** - grep/glob搜索命中的文件

### 研究笔记

<!-- 阅读源码后在这里记录 -->

---

## 4. Model Abstraction（模型抽象层）

### 统一接口

```typescript
interface LLMProvider {
  name: string;
  chat(messages: Message[], options: ChatOptions): AsyncIterable<Chunk>;
  countTokens(text: string): number;
  maxTokens: number;
}

interface ChatOptions {
  model: string;
  temperature?: number;
  maxTokens?: number;
  tools?: Tool[];
  systemPrompt?: string;
}
```

### 支持的提供商

| 提供商 | 模型 |
|--------|------|
| Anthropic | Claude 3.5, Claude 3 |
| OpenAI | GPT-4, GPT-4o |
| Google | Gemini Pro, Gemini Ultra |
| Ollama | Llama, Mistral, etc. |
| Together | 各种开源模型 |

### 研究笔记

<!-- 阅读源码后在这里记录 -->

---

## 5. 消息格式

### OpenCode内部消息

```typescript
interface Message {
  role: 'user' | 'assistant' | 'system' | 'tool';
  content: string | ContentBlock[];
  toolCalls?: ToolCall[];
  toolResults?: ToolResult[];
}

interface ToolCall {
  id: string;
  name: string;
  arguments: Record<string, any>;
}

interface ToolResult {
  toolCallId: string;
  result: string;
  isError?: boolean;
}
```

### 研究笔记

<!-- 阅读源码后在这里记录 -->
