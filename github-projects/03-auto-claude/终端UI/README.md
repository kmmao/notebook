# Auto-Claude 终端UI分析

## UI概览

Auto-Claude提供了丰富的终端UI来管理多个自主会话。

## 主界面布局

```
┌──────────────────────────────────────────────────────────────────┐
│  Auto-Claude v2.7.4                                    [H]elp   │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─ Active Sessions ─────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  [1] feature-auth     Running   ████████░░ 80%  12m 34s  │  │
│  │  [2] fix-bug-123      Running   ██████░░░░ 60%   8m 12s  │  │
│  │  [3] refactor-api     Paused    ████░░░░░░ 40%   5m 45s  │  │
│  │  [4] add-tests        Pending   ░░░░░░░░░░  0%   0m  0s  │  │
│  │                                                           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌─ Task Worktrees ──────────────────────────────────────────┐  │
│  │                                                           │  │
│  │  main                     ← base                          │  │
│  │  ├── auto-claude/auth     ← session 1                     │  │
│  │  ├── auto-claude/bug-123  ← session 2                     │  │
│  │  ├── auto-claude/api      ← session 3                     │  │
│  │  └── auto-claude/tests    ← session 4                     │  │
│  │                                                           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌─ Actions ─────────────────────────────────────────────────┐  │
│  │  [N]ew Task  [P]ause  [R]esume  [K]ill  [Y]OLO  [Q]uit   │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## 会话详情视图

```
┌──────────────────────────────────────────────────────────────────┐
│  Session: feature-auth                              [B]ack      │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Status: Running                                                 │
│  Worktree: .worktrees/auto-claude-auth                          │
│  Branch: auto-claude/feature-auth                               │
│  Started: 2024-01-15 10:23:45                                   │
│  Duration: 12m 34s                                              │
│                                                                  │
│  ┌─ Progress ────────────────────────────────────────────────┐  │
│  │  [✓] 1. 创建认证模块结构                                   │  │
│  │  [✓] 2. 实现用户注册                                       │  │
│  │  [✓] 3. 实现用户登录                                       │  │
│  │  [→] 4. 编写测试用例                                       │  │
│  │  [ ] 5. 更新文档                                           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌─ Recent Activity ─────────────────────────────────────────┐  │
│  │  10:35:12  Modified src/auth/login.ts                     │  │
│  │  10:35:08  Created src/auth/register.ts                   │  │
│  │  10:34:55  Running: npm test                              │  │
│  │  10:34:50  Modified src/auth/index.ts                     │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  [P]ause  [K]ill  [L]ogs  [D]iff  [Y]OLO                       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## 日志视图

```
┌──────────────────────────────────────────────────────────────────┐
│  Logs: feature-auth                       [F]ilter  [B]ack      │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  10:35:12 [INFO]  Writing file: src/auth/login.ts               │
│  10:35:10 [DEBUG] Generating login function...                  │
│  10:35:08 [INFO]  Creating file: src/auth/register.ts           │
│  10:35:05 [DEBUG] Analyzing existing code patterns...           │
│  10:35:00 [INFO]  Starting task: 编写测试用例                    │
│  10:34:58 [SUCCESS] Task completed: 实现用户登录                 │
│  10:34:55 [INFO]  Running command: npm test                     │
│  10:34:50 [INFO]  Modified file: src/auth/index.ts              │
│  10:34:45 [DEBUG] Adding export for register function...        │
│  10:34:40 [INFO]  Task started: 实现用户登录                     │
│                                                                  │
│  ─────────────────────────────────────────────────────────────  │
│  Filter: [All] [Info] [Debug] [Error] [Success]                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## 新任务创建

```
┌──────────────────────────────────────────────────────────────────┐
│  New Task                                           [Esc]Cancel │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Description:                                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │ 实现用户个人资料页面，包括头像上传和信息编辑功能           │  │
│  │ █                                                          │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  Branch Name: auto-claude/user-profile                          │
│                                                                  │
│  Base Branch: [main ▼]                                          │
│                                                                  │
│  Options:                                                        │
│  [x] Enable YOLO mode                                           │
│  [ ] Create PR when done                                        │
│  [ ] Run tests before completing                                │
│                                                                  │
│  Priority: [Normal ▼]                                           │
│                                                                  │
│                              [Create Task]  [Cancel]            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## 技术实现

### 使用的库

| 库 | 用途 |
|----|------|
| Rich | Python终端UI |
| Textual | 现代终端应用框架 |
| Click | 命令行参数解析 |

### 核心组件

```python
# 伪代码
class TerminalUI:
    def __init__(self):
        self.sessions = SessionManager()
        self.layout = Layout()
    
    def render(self):
        self.layout.update(
            header=self.render_header(),
            sessions=self.render_sessions(),
            worktrees=self.render_worktrees(),
            actions=self.render_actions()
        )
    
    def handle_input(self, key: str):
        actions = {
            'n': self.new_task,
            'p': self.pause_selected,
            'r': self.resume_selected,
            'k': self.kill_selected,
            'y': self.toggle_yolo,
            'q': self.quit
        }
        if key in actions:
            actions[key]()
```

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
