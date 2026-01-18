# GITHUB PROJECTS KNOWLEDGE BASE

## OVERVIEW

开源AI编程助手项目研究，三个相关项目的架构分析与学习笔记。

## STRUCTURE

```
github-projects/
├── 01-opencode/           # 基础框架（75k⭐）
│   ├── 架构分析/
│   ├── 核心模块/
│   ├── 工具系统/
│   └── 学习笔记/
├── 02-oh-my-opencode/     # 增强版（18k⭐）
│   ├── Agent系统/
│   ├── 工作流程/
│   ├── Hook系统/
│   └── 学习笔记/
└── 03-auto-claude/        # 自主化（8k⭐）
    ├── 多会话架构/
    ├── 自主执行/
    ├── 终端UI/
    └── 学习笔记/
```

## WHERE TO LOOK

| 研究主题 | 位置 |
|----------|------|
| 终端AI架构 | `01-opencode/架构分析/` |
| 多Agent协作 | `02-oh-my-opencode/Agent系统/` |
| 工作流Phase设计 | `02-oh-my-opencode/工作流程/` |
| Git Worktree隔离 | `03-auto-claude/多会话架构/` |
| YOLO自主执行 | `03-auto-claude/自主执行/` |

## CONVENTIONS

- **项目按星数排序** — 01最多，03最少
- **子目录按功能分** — 架构/模块/笔记
- **学习笔记有模板** — 源码阅读计划、发现记录、问题追踪

## KEY INSIGHTS

| 项目 | 核心创新 |
|------|----------|
| OpenCode | 终端原生 + 模型无关 |
| Oh-My-OpenCode | Sisyphus多Agent + 严格验证 |
| Auto-Claude | Worktree隔离 + YOLO模式 |

## ANTI-PATTERNS

- **不要只看README** — 深入源码
- **不要跳过学习笔记** — 记录发现
