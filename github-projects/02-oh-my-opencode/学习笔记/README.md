# Oh-My-OpenCode 学习笔记

## 源码阅读计划

### Week 1: 项目结构
- [ ] 理解目录结构
- [ ] 阅读package.json依赖
- [ ] 找到入口文件

### Week 2: Agent系统
- [ ] Sisyphus主协调器实现
- [ ] 专业Agent的定义方式
- [ ] 委托决策逻辑

### Week 3: 工作流程
- [ ] Phase 0-3的实现
- [ ] Todo管理系统
- [ ] 验证机制

### Week 4: Hook系统
- [ ] Hook注册与执行
- [ ] 上下文注入机制
- [ ] 自定义Hook开发

---

## 关键发现

### 发现1: Sisyphus的命名

**来源**: 希腊神话

**含义**: 西西弗斯被惩罚永远推巨石上山。暗喻：
- 开发者每天都在"推石头"
- AI也在做同样的事
- 我们并没有那么不同

**代码体现**:

```markdown
"Humans roll their boulder every day. So do you."
```

---

### 发现2: 严格的验证保证

**设计哲学**: "无证据 = 未完成"

**实现方式**:
1. 必须运行lsp_diagnostics
2. 必须有构建通过证据
3. 必须有测试通过证据
4. 不允许"应该能工作了"这种回答

---

### 发现3: 委托模式

**问题**: 单一Agent能力有限

**解决**: 专业化Agent + 明确的委托规则

**关键规则**:
- 前端视觉 → Frontend Agent
- 不熟悉的库 → Librarian Agent
- 困难问题 → Oracle Agent

---

## 可复用的设计模式

### 模式1: 意图门控（Intent Gate）

**场景**: 处理用户请求前需要分类

**实现**: 
1. 首先检查skill匹配
2. 然后分类请求类型
3. 检查是否需要澄清
4. 决定执行路径

**可应用于**: 任何需要处理多种类型请求的系统

---

### 模式2: Planning Triad

**场景**: 复杂任务需要规划

**实现**:
1. Planner生成计划
2. Consultant评估可行性
3. Reviewer批评性审查

**可应用于**: 任何需要高质量规划的系统

---

### 模式3: 强制Todo追踪

**场景**: 多步骤任务容易遗忘

**实现**:
1. 检测多步骤任务
2. 强制创建todo列表
3. 每步完成立即标记
4. 不允许跳过

**可应用于**: 任何需要任务追踪的系统

---

## 问题记录

| 问题 | 状态 | 答案/进展 |
|------|------|-----------|
| Agent如何序列化？ | 待研究 | |
| 后台任务如何管理？ | 待研究 | |
| MCP如何集成？ | 待研究 | |

---

## 与OpenCode的差异

| 方面 | OpenCode | Oh-My-OpenCode |
|------|----------|----------------|
| Agent系统 | 简单单一 | 多Agent协作 |
| 工作流程 | 基础 | 完整Phase系统 |
| 验证 | 可选 | 强制 |
| Todo管理 | 可选 | 强制 |
| Hook系统 | 基础 | 完整 |

---

## 待深入研究

1. [ ] Ralph Loop是什么？
2. [ ] 实验性功能有哪些？
3. [ ] 模型配置如何工作？
4. [ ] 并行执行模式详解

---

## 参考资料

- [GitHub源码](https://github.com/code-yeongyu/oh-my-opencode)
- [DeepWiki文档](https://deepwiki.com/code-yeongyu/oh-my-opencode)
- [作者博客](https://github.com/code-yeongyu)
