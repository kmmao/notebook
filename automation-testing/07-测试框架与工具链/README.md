# 07-测试框架与工具链

## 按语言分类

### Python
| 框架 | 特点 | 适用场景 |
|------|------|---------|
| **pytest** | 最强生态，插件丰富 | 首选，几乎所有Python测试 |
| **unittest** | 标准库，xUnit风格 | 老项目、无额外依赖 |
| **Robot Framework** | 关键字驱动，低代码 | 团队含非开发人员 |
| **Behave** | BDD框架，Gherkin语法 | 需求驱动测试 |

### Java
| 框架 | 特点 | 适用场景 |
|------|------|---------|
| **JUnit 5** | 现代化，模块化 | Java单元测试标准 |
| **TestNG** | 灵活分组，并行执行 | 企业级、复杂测试套件 |
| **Cucumber-JVM** | BDD，Gherkin语法 | 业务驱动测试 |
| **AssertJ** | 流畅断言API | 搭配JUnit/TestNG |

### JavaScript / TypeScript
| 框架 | 特点 | 适用场景 |
|------|------|---------|
| **Jest** | 零配置，快照测试 | React生态首选 |
| **Mocha + Chai** | 灵活组合 | Node.js后端 |
| **Vitest** | Vite原生，极速 | Vite项目 |
| **Jasmine** | 独立，BDD风格 | Angular项目 |

### 其他语言
| 框架 | 语言 | 特点 |
|------|------|------|
| **RSpec** | Ruby | BDD，DSL优雅 |
| **Go testing** | Go | 内置，table-driven |
| **xUnit / NUnit** | C# | .NET生态 |
| **PHPUnit** | PHP | PHP标准 |

## 通用工具

### 断言库
- AssertJ（Java）、Hamcrest（多语言）
- Chai（JS）、Should.js（JS）
- pytest的assert重写机制

### Mock框架
- Mockito（Java）、unittest.mock（Python）
- Jest Mocks（JS）、Sinon.js（JS）
- WireMock / MockServer（HTTP Mock）

### 测试数据
- Faker（多语言）— 生成假数据
- Factory Boy（Python）/ FactoryGirl（Ruby）
- Fixture管理策略
