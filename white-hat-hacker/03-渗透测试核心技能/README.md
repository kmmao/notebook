# 第三阶段：渗透测试核心技能（8-12周）

## 渗透测试流程

```
信息收集 → 漏洞扫描 → 漏洞利用 → 权限提升 → 后渗透 → 报告
```

## 技能模块

| 模块 | 目录 | 核心工具 |
|------|------|----------|
| 信息收集 | [01-信息收集](./01-信息收集.md) | Nmap, Shodan, Amass |
| 漏洞扫描 | [02-漏洞扫描](./02-漏洞扫描.md) | Nessus, Nuclei, Nikto |
| Web渗透 | [03-Web渗透](./03-Web渗透.md) | Burp Suite, SQLMap, ffuf |
| 密码攻击 | [04-密码攻击](./04-密码攻击.md) | Hydra, John, Hashcat |
| 权限提升 | [05-权限提升](./05-权限提升.md) | LinPEAS, WinPEAS |
| 后渗透 | [06-后渗透](./06-后渗透.md) | Metasploit, mimikatz |

## 信息收集（Reconnaissance）

### 被动信息收集
不与目标直接交互

- WHOIS查询
- DNS记录
- 搜索引擎（Google Dorks）
- Shodan/Censys
- 社交媒体OSINT

### 主动信息收集
与目标直接交互

- 端口扫描（Nmap）
- 服务识别
- 目录爆破
- 子域名枚举

## Nmap核心用法

```bash
# 基础扫描
nmap -sV -sC target.com

# 全端口扫描
nmap -p- target.com

# UDP扫描
nmap -sU target.com

# 漏洞脚本扫描
nmap --script vuln target.com

# 输出保存
nmap -oA output target.com
```

## 权限提升

### Linux提权检查点

1. SUID/SGID文件
2. Sudo权限配置
3. Cron任务
4. 可写的敏感文件
5. 内核漏洞
6. 服务配置错误

### Windows提权检查点

1. 未引用的服务路径
2. 弱服务权限
3. AlwaysInstallElevated
4. 存储的凭据
5. 内核漏洞
6. UAC绕过

## 实战资源

- HackTheBox靶机（按难度刷）
- TryHackMe渗透路径
- VulnHub本地靶机
