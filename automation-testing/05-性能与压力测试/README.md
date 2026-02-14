# 05-性能与压力测试

## 核心工具对比

| 工具 | 语言 | 特点 | 适用场景 |
|------|------|------|---------|
| **JMeter** | Java/GUI | 功能全面，插件丰富 | 企业级，协议覆盖广 |
| **k6** | JavaScript | 现代化，开发者友好 | 云原生，CI/CD集成 |
| **Locust** | Python | 代码驱动，分布式 | Python团队，灵活场景 |
| **Gatling** | Scala/Java | 高性能，代码化 | 高并发场景 |
| **Artillery** | JavaScript/YAML | 轻量级，快速上手 | 微服务、Serverless |
| **wrk / wrk2** | Lua | 极致轻量 | 快速基准测试 |

## 知识点

### 性能测试类型
- 负载测试（Load Testing）
- 压力测试（Stress Testing）
- 浸泡测试（Soak Testing）
- 峰值测试（Spike Testing）
- 容量测试（Capacity Testing）

### 关键指标
- 响应时间：平均 / P50 / P90 / P95 / P99
- 吞吐量（TPS / RPS）
- 并发用户数
- 错误率
- 资源利用率（CPU / 内存 / 网络 / 磁盘）

### 实践技能
- 性能测试脚本编写与参数化
- 关联与动态数据处理
- 分布式压测部署
- 性能瓶颈分析与定位
- 性能测试报告编写
- 前端性能测试（Lighthouse / Web Vitals）

### 监控与分析
- Grafana + InfluxDB 实时监控
- APM工具：New Relic / Datadog / SkyWalking
- 火焰图分析
- 数据库慢查询分析
