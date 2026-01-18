# 第五阶段：专项深入

根据兴趣选择一个或多个方向深入学习。

## 方向选择

| 方向 | 适合人群 | 难度 | 就业前景 |
|------|----------|------|----------|
| Web安全 | 有Web开发背景 | ⭐⭐⭐ | 高 |
| 网络安全 | 网络工程背景 | ⭐⭐⭐ | 高 |
| 二进制安全 | 喜欢底层，有C语言基础 | ⭐⭐⭐⭐⭐ | 中高 |
| 云安全 | 有云平台经验 | ⭐⭐⭐⭐ | 非常高 |
| Active Directory | 企业环境渗透 | ⭐⭐⭐⭐ | 高 |
| 移动安全 | 移动开发背景 | ⭐⭐⭐⭐ | 中 |

---

## Web安全深入

### 高级话题
- API安全（REST/GraphQL攻击）
- OAuth/OIDC攻击
- SSRF深入利用
- XXE高级利用
- 反序列化漏洞
- WebSocket安全
- 缓存投毒

### 学习资源
- PortSwigger高级实验室
- Bug Bounty平台（HackerOne, Bugcrowd）

---

## 网络安全深入

### 核心技能
- MITM攻击（ARP欺骗、DNS劫持）
- 无线安全（WPA/WPA2破解）
- VLAN跳跃
- 网络嗅探与分析

### 工具
- Ettercap, Bettercap
- Aircrack-ng套件
- Responder

---

## 二进制安全/逆向工程

### 学习路径
1. 汇编语言基础（x86/x64）
2. C语言与内存管理
3. 栈溢出基础
4. ROP链构造
5. 堆利用
6. 逆向工程

### 工具
- GDB + pwndbg/GEF
- IDA Pro / Ghidra
- pwntools (Python)
- radare2

### 学习资源
- pwn.college（免费，系统化）
- Nightmare（CTF PWN教程）
- LiveOverflow YouTube

---

## 云安全

### AWS安全
- IAM配置错误
- S3桶权限
- EC2元数据服务攻击
- Lambda安全

### 练习平台
- CloudGoat (AWS)
- Damn Vulnerable Cloud Application
- flaws.cloud

---

## Active Directory安全

### 攻击技术
- Kerberoasting
- AS-REP Roasting
- Pass-the-Hash
- Pass-the-Ticket
- DCSync
- Golden Ticket

### 工具
- BloodHound
- Mimikatz
- Rubeus
- Impacket

### 练习
- HackTheBox Pro Labs
- TryHackMe AD Path
- 自建AD实验环境
