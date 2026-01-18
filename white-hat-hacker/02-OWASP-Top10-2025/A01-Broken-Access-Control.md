# A01:2025 - Broken Access Control（访问控制失效）

## 概述

访问控制失效连续多年位居第一，是最常见的Web安全漏洞。

## 漏洞原理

当用户可以执行超出其预期权限的操作时，就发生了访问控制失效。

## 常见类型

### 1. IDOR (Insecure Direct Object Reference)
不安全的直接对象引用

```
# 原始请求
GET /api/user/123/profile

# 攻击：修改ID访问其他用户
GET /api/user/456/profile
```

### 2. 水平越权
同级用户之间的越权访问

### 3. 垂直越权
普通用户访问管理员功能

```
# 普通用户直接访问管理接口
GET /admin/users
POST /admin/delete-user
```

### 4. 功能级访问控制缺失
前端隐藏按钮≠后端权限控制

## 检测方法

1. 修改请求中的ID参数
2. 尝试直接访问管理路径
3. 修改JWT中的角色字段
4. 测试HTTP方法（GET→POST→PUT→DELETE）

## 利用工具

- Burp Suite - 请求拦截与修改
- Autorize (Burp插件) - 自动化越权检测

## 实战练习

- PortSwigger: Access Control vulnerabilities
- DVWA: Insecure CAPTCHA
- TryHackMe: OWASP Top 10 room

## 防御措施

1. 服务端强制权限检查
2. 默认拒绝原则
3. 使用不可预测的ID（UUID）
4. 记录访问控制失败日志
