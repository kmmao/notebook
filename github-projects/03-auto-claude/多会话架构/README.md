# Auto-Claude 多会话架构分析

## 核心概念：自主多会话

Auto-Claude的核心创新是让多个Claude实例同时自主工作。

## 架构概览

```
┌─────────────────────────────────────────────────────────────┐
│                    Auto-Claude 控制台                        │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                    任务管理器                            ││
│  │  - 任务队列                                              ││
│  │  - 优先级调度                                            ││
│  │  - 资源分配                                              ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────┬───────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│   Session 1   │    │   Session 2   │    │   Session 3   │
│  ┌─────────┐  │    │  ┌─────────┐  │    │  ┌─────────┐  │
│  │ Worktree│  │    │  │ Worktree│  │    │  │ Worktree│  │
│  │  (分支1) │  │    │  │  (分支2) │  │    │  │  (分支3) │  │
│  └─────────┘  │    │  └─────────┘  │    │  └─────────┘  │
│  ┌─────────┐  │    │  ┌─────────┐  │    │  ┌─────────┐  │
│  │ Claude  │  │    │  │ Claude  │  │    │  │ Claude  │  │
│  │ Instance│  │    │  │ Instance│  │    │  │ Instance│  │
│  └─────────┘  │    │  └─────────┘  │    │  └─────────┘  │
└───────────────┘    └───────────────┘    └───────────────┘
```

## Git Worktree隔离

### 什么是Worktree？
Git worktree允许在同一仓库下创建多个工作目录，每个目录可以checkout不同分支。

```bash
# 创建worktree
git worktree add ../feature-1 feature-branch-1
git worktree add ../feature-2 feature-branch-2

# 列出worktrees
git worktree list

# 删除worktree
git worktree remove ../feature-1
```

### 为什么使用Worktree？

| 好处 | 说明 |
|------|------|
| **隔离性** | 每个Claude实例有独立的文件系统 |
| **并行性** | 可以同时修改不同功能 |
| **安全性** | 一个实例的错误不影响其他 |
| **可追踪** | 每个任务有独立的git历史 |

### Auto-Claude的Worktree管理

```python
# 伪代码
class WorktreeManager:
    def create_for_task(self, task: Task) -> Worktree:
        branch_name = f"auto-claude/{task.id}"
        worktree_path = f".worktrees/{task.id}"
        
        # 创建分支
        git.branch(branch_name, from_branch="main")
        
        # 创建worktree
        git.worktree.add(worktree_path, branch_name)
        
        return Worktree(path=worktree_path, branch=branch_name)
    
    def cleanup(self, worktree: Worktree):
        git.worktree.remove(worktree.path)
        git.branch.delete(worktree.branch)
```

## 会话生命周期

```
创建任务
    ↓
创建Worktree
    ↓
启动Claude实例
    ↓
[YOLO模式?] ──是──→ 跳过确认，自主执行
    │
    否
    ↓
交互式执行
    ↓
任务完成
    ↓
创建PR / 合并
    ↓
清理Worktree
```

## 会话状态管理

```typescript
interface Session {
  id: string;
  task: Task;
  worktree: Worktree;
  status: 'pending' | 'running' | 'paused' | 'completed' | 'failed';
  claudeProcess: Process;
  logs: Log[];
  startedAt: Date;
  completedAt?: Date;
}

interface SessionManager {
  create(task: Task): Session;
  start(sessionId: string): void;
  pause(sessionId: string): void;
  resume(sessionId: string): void;
  terminate(sessionId: string): void;
  getStatus(sessionId: string): SessionStatus;
}
```

## 并发控制

### 资源限制

```yaml
concurrency:
  max_sessions: 5           # 最大并行会话数
  max_tokens_per_minute: 100000  # API限流
  max_file_operations: 10   # 并行文件操作数
```

### 冲突处理

当多个会话修改相同文件：
1. 检测潜在冲突
2. 暂停后启动的会话
3. 等待先启动的会话完成
4. 合并或rebase

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
