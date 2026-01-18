# 第二阶段：OWASP Top 10 2025（4-6周）

## 概述

OWASP Top 10 是Web安全的核心框架，2025版是最新发布。

## OWASP Top 10 2025 完整列表

| 排名 | 漏洞类型 | 状态 | 目录 |
|------|----------|------|------|
| A01 | Broken Access Control | 稳定 | [详情](./A01-Broken-Access-Control.md) |
| A02 | Security Misconfiguration | 上升 | [详情](./A02-Security-Misconfiguration.md) |
| A03 | Software Supply Chain Failures | 🆕新增 | [详情](./A03-Supply-Chain-Failures.md) |
| A04 | Cryptographic Failures | 稳定 | [详情](./A04-Cryptographic-Failures.md) |
| A05 | Injection | 下降 | [详情](./A05-Injection.md) |
| A06 | Insecure Design | 稳定 | [详情](./A06-Insecure-Design.md) |
| A07 | Authentication Failures | 稳定 | [详情](./A07-Authentication-Failures.md) |
| A08 | Software/Data Integrity Failures | 稳定 | [详情](./A08-Integrity-Failures.md) |
| A09 | Security Logging & Alerting Failures | 稳定 | [详情](./A09-Logging-Failures.md) |
| A10 | Mishandling of Exceptional Conditions | 🆕新增 | [详情](./A10-Exception-Handling.md) |

## 2025版主要变化

1. **新增 A03: Software Supply Chain Failures**
   - 依赖投毒攻击
   - CI/CD管道安全
   - 第三方组件风险

2. **新增 A10: Mishandling of Exceptional Conditions**
   - 异常处理不当导致的安全问题
   - 错误信息泄露

## 实战靶场

| 靶场 | 特点 | 链接 |
|------|------|------|
| DVWA | 经典入门，难度可调 | https://github.com/digininja/DVWA |
| WebGoat | OWASP官方出品 | https://owasp.org/www-project-webgoat/ |
| PortSwigger Academy | 免费且质量极高 | https://portswigger.net/web-security |
| OWASP Juice Shop | 现代Web应用 | https://owasp.org/www-project-juice-shop/ |

## 学习顺序建议

1. **先学注入类** (A05) - SQL注入、命令注入
2. **再学访问控制** (A01) - IDOR、越权
3. **然后认证问题** (A07) - 会话、密码
4. **最后其他类型** - 按兴趣深入

## 官方资源

- OWASP Top 10 2025 官网: https://owasp.org/Top10/
- OWASP Cheat Sheet: https://cheatsheetseries.owasp.org/
