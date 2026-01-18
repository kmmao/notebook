# Auto-Claude

> Autonomous multi-session AI coding - 自主多会话AI编程

## 基本信息

| 属性 | 值 |
|------|-----|
| GitHub | https://github.com/AndyMik90/Auto-Claude |
| Stars | 8k ⭐ |
| Forks | 1.2k |
| 作者 | AndyMik90 (Andy, Norway) |
| License | AGPL-3.0 |
| 最新版本 | v2.7.4 |

## 项目简介

Auto-Claude是一个自主多会话AI编程系统，允许Claude自主执行编程任务，支持多个并行会话。

## 核心特性

### 1. 多会话支持
- 同时运行多个Claude会话
- 每个会话独立的工作树（worktree）
- 任务并行执行

### 2. YOLO模式
```bash
# 使用--dangerously-skip-permissions标志
# 允许Claude自主执行而无需确认
```

### 3. 多模型支持（开发中）
- Claude（主要）
- OpenAI
- Gemini
- Kimi

### 4. 终端集成
- 任务工作树界面
- 实时状态显示
- YOLO模式快速调用

## 项目结构

```
Auto-Claude/
├── .claude/commands/    # Claude命令定义
├── .design-system/      # 设计系统
├── apps/                # 应用
├── guides/              # 指南
├── run.py              # 主入口
├── scripts/            # 脚本
├── shared_docs/        # 共享文档
├── tests/              # 测试
└── CLAUDE.md           # Claude配置
```

## 使用场景

### 1. 自主开发
让Claude自主完成开发任务，无需持续监督

### 2. 并行任务
多个Claude实例同时处理不同的功能/修复

### 3. CI/CD集成
在GitHub Actions中运行Claude

## v2.7.4 新特性

- 添加任务工作树终端界面
- 支持YOLO模式调用
- 工作流增强

## 研究笔记

### 核心问题

1. **如何实现自主执行？**
   - 跳过权限确认
   - 预定义的安全边界
   - 任务范围限制

2. **多会话如何协调？**
   - Git worktree隔离
   - 独立的上下文
   - 任务队列管理

3. **如何保证安全？**
   - AGPL许可证
   - 危险操作警告
   - 审计日志

### 与其他项目的对比

| 特性 | OpenCode | Oh-My-OpenCode | Auto-Claude |
|------|----------|----------------|-------------|
| 多会话 | ✓ | ✓ | ✓✓✓ |
| 自主执行 | - | - | ✓✓✓ |
| Agent系统 | 基础 | 完整 | 中等 |
| 多模型 | ✓ | ✓ | 开发中 |
| 终端UI | ✓ | ✓ | ✓✓ |

### 关键实现

<!-- 在这里记录关键实现细节 -->

## 相关链接

- [Releases](https://github.com/AndyMik90/Auto-Claude/releases)
- [Issues](https://github.com/AndyMik90/Auto-Claude/issues)
