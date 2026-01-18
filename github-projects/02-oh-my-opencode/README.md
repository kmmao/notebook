# Oh-My-OpenCode

> The Best Agent Harness - Meet Sisyphus: The Batteries-Included Agent

## 基本信息

| 属性 | 值 |
|------|-----|
| GitHub | https://github.com/code-yeongyu/oh-my-opencode |
| Stars | 18.7k ⭐ |
| Forks | 1.3k |
| 作者 | YeonGyu Kim |
| 基于 | OpenCode |

## 项目简介

Oh-My-OpenCode是OpenCode的增强版，引入了**Sisyphus** Agent系统 - 一个"像你一样编程"的全功能Agent。

> "Humans roll their boulder every day. So do you. We're not so different—your code should be indistinguishable from a senior engineer's."

## 核心特性：Sisyphus Agent

### Agent架构

```
Sisyphus (主协调器)
    ├── Planning Triad (规划三元组)
    │   ├── Prometheus Planner (规划者)
    │   ├── Metis Plan Consultant (顾问)
    │   └── Momus Plan Reviewer (审查者)
    │
    └── Specialized Agents (专业Agent)
        ├── Oracle Agent (高级推理)
        ├── Librarian Agent (文档/代码库搜索)
        ├── Explore Agent (代码探索)
        ├── Frontend UI/UX Engineer (前端)
        ├── Document Writer (文档)
        └── Multimodal Looker (多模态)
```

### 工作流程阶段

1. **Intent Gate** - 意图分类
2. **Codebase Assessment** - 代码库评估
3. **Exploration & Research** - 探索与研究
4. **Implementation** - 实现
5. **Failure Recovery** - 失败恢复
6. **Completion** - 完成

### 委托策略

| Agent | 触发场景 |
|-------|----------|
| Oracle | 架构决策、调试失败2次后、自我审查 |
| Librarian | 不熟悉的包/库、查找文档 |
| Explore | 查找代码库结构和模式 |
| Frontend | 视觉/样式变更（非逻辑变更） |
| Document Writer | README、API文档 |

## 工具系统

### LSP工具
- `lsp_hover` - 获取符号信息
- `lsp_goto_definition` - 跳转定义
- `lsp_find_references` - 查找引用
- `lsp_document_symbols` - 文档符号
- `lsp_workspace_symbols` - 工作区符号搜索
- `lsp_diagnostics` - 诊断信息
- `lsp_rename` - 重命名
- `lsp_code_actions` - 代码动作

### AST-Grep工具
- `ast_grep_search` - AST感知搜索
- `ast_grep_replace` - AST感知替换

### 后台任务工具
- `background_task` - 启动后台任务
- `background_output` - 获取任务输出
- `background_cancel` - 取消任务

### 会话管理工具
- `session_list` - 列出会话
- `session_read` - 读取会话
- `session_search` - 搜索会话
- `session_info` - 会话信息

## Hook系统

### 生命周期事件
- 上下文注入
- 目录上下文钩子
- Todo延续强制执行
- 关键词检测
- 会话恢复

## MCP集成

支持Claude Code兼容的MCP（Model Context Protocol）

## 配置

项目使用`.opencode/`目录存放配置：
- Agent配置
- 技能定义
- 钩子脚本

## 研究笔记

### 值得学习的设计

1. **多Agent协作** - 不同Agent负责不同任务
2. **委托策略** - 明确的任务分配规则
3. **验证保证** - 强制验证工作完成
4. **Todo管理** - 追踪多步骤任务
5. **后台执行** - 并行探索/研究

### 关键代码位置

<!-- 在这里记录关键代码位置 -->

## 相关链接

- [DeepWiki文档](https://deepwiki.com/code-yeongyu/oh-my-opencode)
- [配置参考](https://deepwiki.com/code-yeongyu/oh-my-opencode/13.1-configuration-schema-reference)
