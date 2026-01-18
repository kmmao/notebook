# Oh-My-OpenCode Agent系统分析

## Sisyphus：核心协调Agent

> "Humans roll their boulder every day. So do you."

Sisyphus是整个系统的主协调器，负责理解用户意图、分配任务、验证结果。

## Agent架构

```
┌─────────────────────────────────────────────────────────────┐
│                     Sisyphus (主协调器)                      │
│  - 意图解析                                                  │
│  - 任务分解                                                  │
│  - 委托决策                                                  │
│  - 结果验证                                                  │
└─────────────────────────────┬───────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│  Planning     │    │  Specialized  │    │   Support     │
│    Triad      │    │    Agents     │    │   Agents      │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ • Prometheus  │    │ • Oracle      │    │ • Explore     │
│   (规划)      │    │   (推理)      │    │   (探索)      │
│ • Metis       │    │ • Frontend    │    │ • Librarian   │
│   (咨询)      │    │   (UI/UX)     │    │   (文档)      │
│ • Momus       │    │ • DocWriter   │    │ • Multimodal  │
│   (审查)      │    │   (文档)      │    │   (多模态)    │
└───────────────┘    └───────────────┘    └───────────────┘
```

## Planning Triad（规划三元组）

### Prometheus Planner（普罗米修斯 - 规划者）
- **职责**：创建详细的执行计划
- **输入**：用户需求 + 代码库上下文
- **输出**：步骤化的任务计划

### Metis Plan Consultant（墨提斯 - 顾问）
- **职责**：审查计划的可行性
- **关注**：资源需求、风险评估、替代方案

### Momus Plan Reviewer（摩莫斯 - 审查者）
- **职责**：批评性审查
- **关注**：遗漏、漏洞、边界情况

## Specialized Agents（专业Agent）

### Oracle Agent（神谕 - 高级推理）

```yaml
触发条件:
  - 复杂架构决策
  - 完成重要工作后的自我审查
  - 2次以上调试失败
  - 不熟悉的代码模式
  - 安全/性能顾虑
  - 多系统权衡

特点:
  - 使用高级推理模型（如GPT-5.2）
  - 成本较高，谨慎使用
  - 需要先宣布"Consulting Oracle for [reason]"
```

### Frontend UI/UX Engineer（前端工程师）

```yaml
触发条件:
  - 视觉/样式变更
  - 布局、动画、颜色
  - CSS/Tailwind相关

不触发:
  - 纯逻辑变更（API调用、状态管理）
  - 数据流处理

关键词触发:
  - style, className, tailwind
  - color, background, border
  - margin, padding, flex, grid
  - animation, transition, hover
```

### Librarian Agent（图书管理员 - 文档搜索）

```yaml
职责:
  - 搜索外部资源（文档、OSS、Web）
  - 查找库的最佳实践
  - 获取官方API文档
  - 查找开源实现示例

触发短语:
  - "How do I use [library]?"
  - "What's the best practice for..."
  - "Find examples of..."
```

### Explore Agent（探索者 - 代码探索）

```yaml
职责:
  - 搜索内部代码库
  - 查找模式和实现
  - 理解项目结构

使用场景:
  - 需要多角度搜索
  - 不熟悉的模块结构
  - 跨层模式发现
```

## Agent委托策略

| 域 | 委托给 | 触发条件 |
|----|--------|----------|
| 架构决策 | Oracle | 多系统权衡、不熟悉模式 |
| 自我审查 | Oracle | 完成重要实现后 |
| 困难调试 | Oracle | 2+次修复失败 |
| 库/文档 | Librarian | 不熟悉的包、奇怪行为 |
| 代码探索 | Explore | 查找结构、模式、风格 |
| 前端UI | Frontend | 视觉变更（非逻辑） |
| 文档 | DocWriter | README、API文档 |

## 委托Prompt结构（强制7部分）

```markdown
1. TASK: 原子级、具体目标（每次委托一个动作）
2. EXPECTED OUTCOME: 具体交付物和成功标准
3. REQUIRED SKILLS: 需要调用的技能
4. REQUIRED TOOLS: 显式工具白名单
5. MUST DO: 详尽的要求 - 不留隐式空间
6. MUST NOT DO: 禁止的动作 - 预防失控行为
7. CONTEXT: 文件路径、现有模式、约束
```

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
