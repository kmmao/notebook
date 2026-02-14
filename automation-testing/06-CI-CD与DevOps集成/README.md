# 06-CI/CD与DevOps集成

## 知识点

### CI/CD平台
- **GitHub Actions** — YAML工作流，免费额度大
- **Jenkins** — 企业级标准，插件生态
- **GitLab CI** — 内置CI/CD，DevOps全流程
- **Azure DevOps** — 微软生态，企业友好
- **CircleCI** — 云原生，配置简洁

### 测试集成实践
- 自动化测试在CI/CD中的触发策略
- 测试并行化与分片执行
- 测试环境管理（Docker Compose / Testcontainers）
- 测试结果收集与通知（Slack / 邮件 / 钉钉）
- 失败重试机制与Flaky Test治理

### Docker化测试
- 测试环境容器化
- Docker Compose编排测试依赖
- Testcontainers：代码控制容器生命周期
- 浏览器容器化（Selenium Grid Docker / Playwright Docker）

### 测试报告与可观测性
- Allure Report：美观、多语言支持
- HTML Report / JUnit XML
- 测试趋势与历史分析
- 测试覆盖率报告集成（Codecov / Coveralls）

### 测试环境策略
- 开发 / 测试 / 预发 / 生产环境管理
- 特性分支测试（Preview Environments）
- 数据库迁移与测试数据管理
- 配置管理与环境变量
