# Oh-My-OpenCode Hook系统分析

## Hook概述

Hook系统允许在特定生命周期事件时注入自定义行为。

## Hook生命周期事件

```
会话开始
    ↓
[Directory Context Hooks] ← 注入目录特定上下文
    ↓
消息处理
    ↓
[Keyword Detection] ← 检测关键词触发模式
    ↓
[Todo Continuation Enforcer] ← 确保todo列表延续
    ↓
Agent执行
    ↓
[Non-Interactive Environment] ← 处理非交互环境
    ↓
会话结束/中断
    ↓
[Session Recovery] ← 会话恢复
```

## 核心Hooks

### 1. Context Injection System（上下文注入）

**目的**：在对话开始时注入相关上下文

```typescript
interface ContextHook {
  // 决定是否应该注入
  shouldInject(session: Session): boolean;
  
  // 获取要注入的内容
  getContext(session: Session): string;
}
```

### 2. Directory Context Hooks（目录上下文钩子）

**目的**：基于项目目录加载特定配置

```
.opencode/
├── agents/           # Agent配置
├── skills/           # 技能定义
├── hooks/            # 自定义钩子
└── INSTRUCTIONS.md   # 项目指令
```

### 3. Todo Continuation Enforcer（Todo延续强制器）

**目的**：确保多步骤任务的todo列表持续追踪

```yaml
触发条件:
  - 检测到多步骤任务
  - 用户请求包含多个项目
  - 复杂单任务需要分解

行为:
  - 在开始工作前创建todos
  - 每完成一步立即标记完成
  - 不允许批量完成
  - 确保只有一个todo处于in_progress
```

### 4. Keyword Detection（关键词检测）

**目的**：检测特定关键词触发模式切换

```yaml
关键词模式:
  "ulw" / "ultrawork":
    - 启用ULTRAWORK模式
    - 最大精度要求
    - 强制并行Agent利用
    
  "analyze-mode":
    - 启用分析模式
    - 先收集上下文再深入
    
  自定义关键词:
    - 可在配置中定义
```

### 5. Non-Interactive Environment（非交互环境）

**目的**：处理CI/CD等非交互场景

```yaml
检测条件:
  - CI=true 环境变量
  - 无TTY
  - GitHub Actions环境

行为调整:
  - 禁用交互式确认
  - 自动选择默认选项
  - 详细日志输出
```

### 6. Session Recovery（会话恢复）

**目的**：处理会话中断后的恢复

```yaml
触发条件:
  - 检测到未完成的todos
  - 上次会话异常结束
  
恢复策略:
  - 加载上次会话状态
  - 显示未完成任务
  - 询问是否继续
```

## Hook配置

### 在.opencode/hooks/目录下定义

```yaml
# example-hook.yaml
name: my-custom-hook
event: session_start
priority: 100

condition:
  directory_contains: "package.json"
  
action:
  inject_context: |
    This is a Node.js project.
    Use npm for package management.
```

## Hook执行顺序

1. 按priority排序（数字越小越先执行）
2. 同优先级按字母顺序
3. 可以在hook中阻止后续hook执行

## 内置Hook列表

| Hook | 事件 | 描述 |
|------|------|------|
| `system-prompt-loader` | session_start | 加载系统提示 |
| `directory-context` | session_start | 加载目录上下文 |
| `todo-enforcer` | message_process | 强制todo追踪 |
| `keyword-detector` | message_receive | 检测关键词 |
| `session-recovery` | session_start | 恢复未完成会话 |
| `ci-mode` | session_start | 检测CI环境 |

## 自定义Hook开发

```typescript
// packages/hooks/src/my-hook.ts
import { Hook, HookContext } from '@opencode/hooks';

export const myHook: Hook = {
  name: 'my-custom-hook',
  event: 'message_process',
  priority: 50,
  
  async execute(context: HookContext) {
    // 检查条件
    if (!context.message.includes('特定关键词')) {
      return; // 不处理
    }
    
    // 注入上下文
    context.injectSystemMessage('额外的指令...');
    
    // 或修改行为
    context.setMode('special-mode');
  }
};
```

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
