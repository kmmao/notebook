# Oh-My-OpenCode 工作流程分析

## 完整工作流程

```
Phase 0: Intent Gate (意图门控)
         ↓
Phase 1: Codebase Assessment (代码库评估)
         ↓
Phase 2A: Exploration & Research (探索与研究)
         ↓
Phase 2B: Implementation (实现)
         ↓
Phase 2C: Failure Recovery (失败恢复)
         ↓
Phase 3: Completion (完成)
```

## Phase 0: Intent Gate（意图门控）

**每条消息都必须经过此阶段**

### Step 0: 检查Skills（阻塞）
```
IF 请求匹配skill触发器:
  → 立即调用skill工具
  → 不继续Step 1
```

### Step 1: 分类请求类型

| 类型 | 信号 | 动作 |
|------|------|------|
| Skill Match | 匹配skill触发短语 | 立即调用skill |
| Trivial | 单文件、已知位置 | 直接使用工具 |
| Explicit | 指定文件/行、明确命令 | 直接执行 |
| Exploratory | "X怎么工作？"、"找Y" | 并行启动explore |
| Open-ended | "改进"、"重构"、"加功能" | 先评估代码库 |
| GitHub Work | Issue提及、"看看X并创建PR" | 完整工作周期 |
| Ambiguous | 范围不清、多种解释 | 问一个澄清问题 |

### Step 2: 检查歧义性

| 情况 | 动作 |
|------|------|
| 单一有效解释 | 继续 |
| 多种解释，工作量相近 | 继续并说明假设 |
| 多种解释，工作量差2x+ | **必须询问** |
| 缺少关键信息 | **必须询问** |
| 用户设计有缺陷 | **必须提出顾虑** |

---

## Phase 1: Codebase Assessment（代码库评估）

**仅用于Open-ended任务**

### 快速评估
1. 检查配置文件：linter、formatter、类型配置
2. 采样2-3个相似文件检查一致性
3. 注意项目年龄信号

### 状态分类

| 状态 | 信号 | 行为 |
|------|------|------|
| **Disciplined** | 一致的模式、配置存在、有测试 | 严格遵循现有风格 |
| **Transitional** | 混合模式、部分结构 | 询问遵循哪种模式 |
| **Legacy/Chaotic** | 无一致性、过时模式 | 提议新约定 |
| **Greenfield** | 新/空项目 | 应用现代最佳实践 |

---

## Phase 2A: Exploration & Research（探索与研究）

### 工具选择优先级
```
Skills → Direct Tools → Agents
```

### Explore Agent = 上下文Grep（免费）
- 需要多角度搜索时
- 不熟悉模块结构时
- 跨层模式发现时

### Librarian Agent = 参考Grep（低成本）
- 搜索外部资源
- 查找其他仓库示例
- 获取官方文档

### 并行执行（默认行为）

```typescript
// 正确：始终后台、始终并行
background_task(agent="explore", prompt="在我们代码库中查找auth实现...")
background_task(agent="explore", prompt="查找错误处理模式...")
background_task(agent="librarian", prompt="查找JWT最佳实践...")
// 立即继续工作，需要时用background_output收集

// 错误：顺序或阻塞
result = task(...)  // 永远不要同步等待explore/librarian
```

### 搜索停止条件
- 有足够上下文继续
- 相同信息重复出现
- 2次搜索迭代无新信息
- 找到直接答案

---

## Phase 2B: Implementation（实现）

### Pre-Implementation
1. 如果任务2+步骤 → **立即**创建todo列表
2. 开始前标记当前任务为`in_progress`
3. 完成后**立即**标记为`completed`

### 代码变更规则
- 匹配现有模式（如果代码库有纪律）
- 先提议方案（如果代码库混乱）
- **永远不**用`as any`、`@ts-ignore`压制类型错误
- **永远不**主动提交（除非明确要求）
- **Bugfix规则**：最小修复，**永远不**边修边重构

### 验证
在以下时机运行`lsp_diagnostics`：
- 逻辑任务单元结束时
- 标记todo完成前
- 向用户报告完成前

### 证据要求

| 动作 | 必需证据 |
|------|----------|
| 文件编辑 | `lsp_diagnostics`无错误 |
| 构建命令 | 退出码0 |
| 测试运行 | 通过（或说明已存在的失败） |
| 委托 | Agent结果已接收并验证 |

**无证据 = 未完成**

---

## Phase 2C: Failure Recovery（失败恢复）

### 修复失败时
1. 修复根本原因，不是症状
2. 每次修复后重新验证
3. 永远不要shotgun调试（随机改动希望有效）

### 连续失败3次后
1. **停止**所有进一步编辑
2. **回滚**到上次已知工作状态
3. **记录**尝试过什么和失败原因
4. **咨询**Oracle（附完整失败上下文）
5. 如果Oracle无法解决 → **询问用户**

---

## Phase 3: Completion（完成）

### 完成检查清单
- [ ] 所有计划的todo项已完成
- [ ] 变更文件的诊断无错误
- [ ] 构建通过（如适用）
- [ ] 用户原始请求完全满足

### 验证失败时
1. 修复你的变更导致的问题
2. **不要**修复预先存在的问题（除非被要求）
3. 报告："完成。注意：发现N个与我的变更无关的预先存在的lint错误。"

### 最终回答前
- 取消所有运行中的后台任务：`background_cancel(all=true)`

## 研究笔记

<!-- 在这里记录你的研究笔记 -->
