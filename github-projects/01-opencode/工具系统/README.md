# OpenCode 工具系统分析

## 工具概览

OpenCode提供了一套完整的工具集，让LLM能够与代码库交互。

## 工具分类

### 1. 文件操作工具

| 工具 | 功能 | 参数 |
|------|------|------|
| `read` | 读取文件内容 | filePath, offset?, limit? |
| `write` | 写入文件 | filePath, content |
| `edit` | 编辑文件（替换） | filePath, oldString, newString |
| `glob` | 文件模式匹配 | pattern, path? |

#### Read工具详解

```typescript
interface ReadParams {
  filePath: string;      // 绝对路径
  offset?: number;       // 起始行（0-based）
  limit?: number;        // 读取行数
}

// 特点：
// - 支持读取图片
// - 超过2000行自动截断
// - 返回带行号的内容
```

#### Edit工具详解

```typescript
interface EditParams {
  filePath: string;
  oldString: string;     // 要替换的内容
  newString: string;     // 新内容
  replaceAll?: boolean;  // 是否全部替换
}

// 特点：
// - 必须先Read才能Edit
// - oldString必须唯一匹配
// - 保持缩进
```

---

### 2. 搜索工具

| 工具 | 功能 | 参数 |
|------|------|------|
| `grep` | 内容搜索 | pattern, path?, include? |
| `glob` | 文件名匹配 | pattern, path? |
| `ast_grep_search` | AST感知搜索 | pattern, lang, paths? |

#### Grep工具详解

```typescript
interface GrepParams {
  pattern: string;       // 正则表达式
  path?: string;         // 搜索路径
  include?: string;      // 文件过滤 "*.ts"
}

// 特点：
// - 支持正则表达式
// - 按修改时间排序
// - 60秒超时，10MB输出限制
```

#### AST-Grep详解

```typescript
interface AstGrepParams {
  pattern: string;       // AST模式，如 "console.log($MSG)"
  lang: string;          // 语言：typescript, python等
  paths?: string[];      // 限制路径
  globs?: string[];      // 文件模式
}

// 特点：
// - 语法感知，不是纯文本匹配
// - 使用元变量：$VAR, $$$
// - 支持25种语言
```

---

### 3. 命令执行工具

| 工具 | 功能 | 参数 |
|------|------|------|
| `bash` | 执行Shell命令 | command, workdir?, timeout? |

#### Bash工具详解

```typescript
interface BashParams {
  command: string;       // 命令
  workdir?: string;      // 工作目录
  timeout?: number;      // 超时（毫秒）
  description?: string;  // 命令描述
}

// 安全限制：
// - 不用于文件操作（用专门的工具）
// - 避免交互式命令（-i标志）
// - 2分钟默认超时
```

---

### 4. LSP工具

| 工具 | 功能 |
|------|------|
| `lsp_hover` | 获取符号类型信息 |
| `lsp_goto_definition` | 跳转到定义 |
| `lsp_find_references` | 查找所有引用 |
| `lsp_document_symbols` | 文档符号大纲 |
| `lsp_workspace_symbols` | 工作区符号搜索 |
| `lsp_diagnostics` | 获取诊断信息 |
| `lsp_rename` | 重命名符号 |
| `lsp_code_actions` | 获取代码动作 |

#### LSP集成原理

```
OpenCode
    ↓
Language Server Protocol
    ↓
┌─────────────────────────────────────┐
│  TypeScript   Python   Go   Rust   │
│    Server     Server  Server Server │
└─────────────────────────────────────┘
```

---

### 5. 网络工具

| 工具 | 功能 |
|------|------|
| `webfetch` | 获取网页内容 |
| `websearch_web_search_exa` | Exa搜索 |
| `grep_app_searchGitHub` | GitHub代码搜索 |

---

## 工具调用流程

```
1. LLM决定调用工具
    ↓
2. 生成工具调用JSON
    {
      "name": "read",
      "arguments": {"filePath": "/path/to/file"}
    }
    ↓
3. OpenCode执行工具
    ↓
4. 返回结果给LLM
    ↓
5. LLM继续处理或响应用户
```

## 工具安全设计

1. **路径验证** - 防止路径遍历攻击
2. **超时控制** - 防止无限执行
3. **输出限制** - 防止内存溢出
4. **权限检查** - 敏感操作确认

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
