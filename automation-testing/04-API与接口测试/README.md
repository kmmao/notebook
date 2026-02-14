# 04-API与接口测试

## 核心工具对比

| 工具 | 语言 | 特点 | 适用场景 |
|------|------|------|---------|
| **Postman / Newman** | JavaScript | GUI + CLI，团队协作 | 手工 + 自动化、快速验证 |
| **Rest Assured** | Java | 流畅API，BDD风格 | Java技术栈企业项目 |
| **Supertest** | JavaScript | Express集成测试 | Node.js后端 |
| **HttpRunner** | Python/YAML | 数据驱动，低代码 | 大规模接口测试 |
| **Karate DSL** | Gherkin语法 | BDD + 性能测试一体 | API+性能综合测试 |
| **Pactum** | JavaScript | 契约测试 + API测试 | 微服务架构 |
| **requests + pytest** | Python | 灵活，生态强 | Python技术栈 |

## 知识点

### API测试基础
- RESTful API测试要点
- GraphQL API测试
- gRPC接口测试
- WebSocket测试
- HTTP方法、状态码、Header深入理解

### 测试设计
- 正向 / 反向测试用例设计
- 参数化与数据驱动
- 认证测试：OAuth2 / JWT / API Key
- 请求链与依赖管理（Token传递、ID关联）
- 响应断言：状态码 / Body / Schema / 性能

### 契约测试
- 消费者驱动契约测试（Pact）
- Provider验证
- 契约测试 vs 集成测试
- 微服务架构下的测试策略

### Mock与Stub
- WireMock / MockServer / json-server
- 服务虚拟化（Service Virtualization）
- 第三方API的Mock策略
