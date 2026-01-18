# GitHub项目研究

> 研究优秀的开源AI编程助手项目，学习其架构设计和实现思路

## 项目列表

| 项目 | Stars | 描述 | 目录 |
|------|-------|------|------|
| [OpenCode](https://github.com/anomalyco/opencode) | 75.4k ⭐ | 开源AI编程Agent，终端原生 | [01-opencode](./01-opencode/) |
| [Oh-My-OpenCode](https://github.com/code-yeongyu/oh-my-opencode) | 18.7k ⭐ | OpenCode增强版，Sisyphus Agent | [02-oh-my-opencode](./02-oh-my-opencode/) |
| [Auto-Claude](https://github.com/AndyMik90/Auto-Claude) | 8k ⭐ | 自主多会话AI编程 | [03-auto-claude](./03-auto-claude/) |

## 项目关系

```
OpenCode (基础)
    ↓
Oh-My-OpenCode (增强/定制)
    ↓
Auto-Claude (自主化/多会话)
```

## 研究方向

1. **架构设计** - Agent系统如何组织
2. **工具集成** - LSP、AST、文件操作
3. **多模型支持** - 如何抽象不同LLM
4. **会话管理** - 多会话、上下文管理
5. **自主执行** - 如何实现自主编程
