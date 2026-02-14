# 08-测试架构与设计模式

## 知识点

### 页面对象模型（Page Object Model / POM）
- POM的核心思想与实现
- Page Factory（Java Selenium）
- POM的变体：Page Component / Page Section
- POM的优缺点与适用边界

### Screenplay模式
- Actor / Task / Question / Ability
- 比POM更面向行为的抽象
- Serenity BDD框架实践

### 数据驱动测试
- 外部数据源：CSV / Excel / JSON / YAML / 数据库
- pytest.mark.parametrize / TestNG @DataProvider
- 数据与逻辑分离原则

### 关键字驱动测试
- Robot Framework的关键字体系
- 自定义关键字封装
- 适用于非技术人员参与的场景

### 混合框架设计
- 分层架构：测试层 → 业务层 → 页面层 → 基础层
- 配置管理：环境切换、多套配置
- 日志与报告集成
- 失败重试与截图机制
- 测试数据管理策略

### 测试代码质量
- DRY原则在测试中的应用与误区
- 测试的可读性 > 代码复用
- 测试命名规范：should_doX_when_conditionY
- 测试的独立性与隔离性
- 避免测试间依赖（共享状态是大敌）
