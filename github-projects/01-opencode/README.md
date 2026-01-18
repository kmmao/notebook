# OpenCode

> The open source coding agent - 开源AI编程Agent

## 基本信息

| 属性 | 值 |
|------|-----|
| GitHub | https://github.com/anomalyco/opencode |
| 官网 | https://opencode.ai |
| Stars | 75.4k ⭐ |
| Forks | 6.6k |
| Contributors | 500+ |
| License | MIT |
| 语言 | TypeScript/Go |

## 项目简介

OpenCode是一个开源的AI编程助手，专为终端环境设计。它可以：
- 在终端、IDE或桌面应用中帮助编写代码
- 支持多种LLM模型（Claude、GPT、Gemini等）
- 理解整个代码库上下文
- 执行命令、编辑文件

## 核心特性

| 特性 | 描述 |
|------|------|
| **LSP集成** | 自动加载正确的语言服务器 |
| **多会话** | 同一项目并行启动多个Agent |
| **分享链接** | 分享任意会话供参考或调试 |
| **Claude Pro** | 支持Anthropic账户登录 |
| **ChatGPT Plus/Pro** | 支持OpenAI账户登录 |
| **75+模型** | 通过Models.dev支持包括本地模型 |
| **多编辑器** | 终端、桌面应用、IDE扩展 |

## 安装

```bash
# curl
curl -fsSL https://opencode.ai/install | bash

# npm
npm install -g @anthropic/opencode

# bun
bun install -g @anthropic/opencode

# brew
brew install opencode

# paru (Arch)
paru -S opencode
```

## 使用

```bash
# 在项目目录运行
cd your-project
opencode

# 开始与AI对话编程
```

## 架构研究点

### 1. 项目结构
```
opencode/
├── packages/          # 核心包
├── sdks/vscode/       # VSCode扩展
├── themes/            # 主题
├── specs/             # 规范
├── infra/             # 基础设施
└── script/            # 脚本
```

### 2. 核心能力
- 文件读写
- 命令执行
- 代码搜索（grep、glob）
- LSP集成（定义跳转、引用查找）
- Git操作

### 3. 模型抽象
支持通过统一接口调用不同模型提供商

## 隐私设计

- 不存储任何代码或上下文数据
- 可在隐私敏感环境中运行

## 研究笔记

<!-- 在这里添加你的研究笔记 -->

## 相关链接

- [官方文档](https://opencode.ai/docs)
- [GitHub Releases](https://github.com/anomalyco/opencode/releases)
- [Discord社区](https://discord.gg/opencode)
