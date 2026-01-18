# A05:2025 - Injection（注入攻击）

## 概述

注入攻击是最经典的Web漏洞，虽然排名下降但仍然常见且危害严重。

## 注入类型

### 1. SQL注入 (SQLi)

最常见的注入类型

```sql
# 原始查询
SELECT * FROM users WHERE id = '用户输入'

# 攻击输入
1' OR '1'='1

# 结果
SELECT * FROM users WHERE id = '1' OR '1'='1'
```

#### SQL注入分类

| 类型 | 特点 |
|------|------|
| Union-based | 利用UNION合并查询结果 |
| Error-based | 通过错误信息获取数据 |
| Boolean-based Blind | 通过真/假响应判断 |
| Time-based Blind | 通过延时判断 |
| Out-of-band | 通过DNS/HTTP外带数据 |

### 2. 命令注入 (Command Injection)

```bash
# 原始命令
ping 用户输入

# 攻击输入
127.0.0.1; cat /etc/passwd

# 结果
ping 127.0.0.1; cat /etc/passwd
```

常用分隔符: `; | || & && \n $() \`\``

### 3. LDAP注入

### 4. XPath注入

### 5. NoSQL注入

```javascript
// MongoDB注入
{ "username": {"$gt": ""}, "password": {"$gt": ""} }
```

## 检测方法

1. 输入单引号 `'` 观察错误
2. 输入 `1 AND 1=1` vs `1 AND 1=2`
3. 输入 `; sleep 5` 观察延时
4. 使用SQLMap自动化检测

## 利用工具

| 工具 | 用途 |
|------|------|
| SQLMap | SQL注入自动化利用 |
| Commix | 命令注入自动化 |
| NoSQLMap | NoSQL注入 |

## SQLMap基础用法

```bash
# 基本检测
sqlmap -u "http://target.com/page?id=1"

# 获取数据库
sqlmap -u "http://target.com/page?id=1" --dbs

# 获取表
sqlmap -u "http://target.com/page?id=1" -D dbname --tables

# 获取数据
sqlmap -u "http://target.com/page?id=1" -D dbname -T users --dump
```

## 实战练习

- PortSwigger: SQL Injection labs (全部完成)
- DVWA: SQL Injection (Low→High)
- HackTheBox: 相关靶机

## 防御措施

1. **参数化查询/预编译语句** - 最有效
2. 输入验证与白名单
3. 最小权限数据库账户
4. WAF (Web Application Firewall)
