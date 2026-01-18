# Auto-Claude 自主执行分析

## YOLO模式

> "You Only Live Once" - 让Claude自主执行，无需人工确认

### 启用方式

```bash
# 使用--dangerously-skip-permissions标志
auto-claude --dangerously-skip-permissions

# 或在终端UI中选择YOLO模式
```

### 权限跳过

正常模式下Claude需要确认：
- 文件写入
- 命令执行
- Git操作
- 网络请求

YOLO模式下：
- 所有操作自动确认
- 无需人工干预
- 完全自主执行

## 安全边界

### 即使在YOLO模式下也禁止的操作

```yaml
forbidden_operations:
  - rm -rf /
  - 删除.git目录
  - 推送到main/master（无force）
  - 修改系统文件
  - 访问敏感目录
  - 暴露密钥/凭证
```

### 操作审计

```python
class AuditLog:
    def log_operation(self, operation: Operation):
        entry = {
            "timestamp": datetime.now(),
            "session_id": operation.session_id,
            "type": operation.type,
            "details": operation.details,
            "risk_level": self.assess_risk(operation),
            "auto_approved": operation.yolo_mode
        }
        self.entries.append(entry)
        
        if entry["risk_level"] == "high":
            self.alert_user(entry)
```

## 自主执行流程

```
接收任务
    ↓
分析任务范围
    ↓
创建执行计划
    ↓
[循环开始]
    ↓
执行下一步
    ↓
[需要确认?] ──YOLO模式──→ 自动确认
    │
    正常模式
    ↓
等待用户确认
    ↓
[继续?] ──否──→ 暂停/取消
    │
    是
    ↓
[任务完成?] ──否──→ [循环开始]
    │
    是
    ↓
生成报告
    ↓
创建PR（可选）
```

## 任务范围限制

### 任务定义

```yaml
task:
  id: "task-001"
  description: "实现用户认证功能"
  scope:
    files:
      - "src/auth/**"
      - "tests/auth/**"
    operations:
      - read
      - write
      - create
      - delete
    commands:
      allowed:
        - "npm test"
        - "npm run build"
      forbidden:
        - "npm publish"
        - "rm -rf"
```

### 范围强制执行

```python
class ScopeEnforcer:
    def __init__(self, task: Task):
        self.scope = task.scope
    
    def can_access_file(self, path: str) -> bool:
        return any(
            fnmatch(path, pattern) 
            for pattern in self.scope.files
        )
    
    def can_execute_command(self, command: str) -> bool:
        if any(cmd in command for cmd in self.scope.commands.forbidden):
            return False
        return any(
            cmd in command 
            for cmd in self.scope.commands.allowed
        )
```

## 失败恢复

### 自动回滚

```python
class AutoRollback:
    def __init__(self, worktree: Worktree):
        self.worktree = worktree
        self.checkpoints = []
    
    def create_checkpoint(self):
        commit_hash = git.commit("checkpoint", allow_empty=True)
        self.checkpoints.append(commit_hash)
    
    def rollback_to_last(self):
        if self.checkpoints:
            last = self.checkpoints.pop()
            git.reset(last, hard=True)
```

### 错误处理策略

| 错误类型 | 策略 |
|----------|------|
| 语法错误 | 自动修复尝试（最多3次） |
| 测试失败 | 分析失败原因，尝试修复 |
| 构建失败 | 回滚到上个检查点 |
| 运行时错误 | 记录日志，暂停任务 |
| API限流 | 等待后重试 |

## 进度报告

### 实时状态

```
┌────────────────────────────────────────────┐
│ Task: 实现用户认证功能                      │
├────────────────────────────────────────────┤
│ Status: Running                            │
│ Progress: ████████░░ 80%                   │
│ Current: 编写测试用例                       │
│ Files Modified: 5                          │
│ Time Elapsed: 12m 34s                      │
│ Tokens Used: 45,230                        │
└────────────────────────────────────────────┘
```

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
