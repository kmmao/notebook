# 第六阶段：工具清单

## 渗透测试操作系统

| 系统 | 特点 | 下载 |
|------|------|------|
| Kali Linux | 最流行，工具最全 | https://www.kali.org/ |
| Parrot OS | 轻量，隐私导向 | https://www.parrotsec.org/ |
| BlackArch | 基于Arch，工具最多 | https://blackarch.org/ |

## 核心工具分类

### 信息收集

| 工具 | 用途 | 命令示例 |
|------|------|----------|
| Nmap | 端口扫描、服务识别 | `nmap -sV -sC target` |
| Masscan | 快速端口扫描 | `masscan -p1-65535 target` |
| Amass | 子域名枚举 | `amass enum -d target.com` |
| theHarvester | 邮箱、子域收集 | `theHarvester -d target.com -b all` |
| Shodan | 互联网设备搜索 | shodan.io |

### Web渗透

| 工具 | 用途 |
|------|------|
| Burp Suite | Web代理、漏洞扫描（必备） |
| OWASP ZAP | 免费替代Burp |
| SQLMap | SQL注入自动化 |
| ffuf | 目录/参数爆破 |
| Nikto | Web服务器扫描 |
| WhatWeb | Web指纹识别 |
| Nuclei | 漏洞扫描（模板化） |

### 密码攻击

| 工具 | 用途 |
|------|------|
| Hydra | 在线密码爆破 |
| John the Ripper | 离线密码破解 |
| Hashcat | GPU加速密码破解 |
| CeWL | 从网站生成字典 |

### 漏洞利用

| 工具 | 用途 |
|------|------|
| Metasploit | 漏洞利用框架 |
| searchsploit | 本地漏洞库搜索 |
| pwntools | Python漏洞开发库 |

### 权限提升

| 工具 | 平台 | 用途 |
|------|------|------|
| LinPEAS | Linux | 自动化提权检查 |
| WinPEAS | Windows | 自动化提权检查 |
| linux-exploit-suggester | Linux | 内核漏洞建议 |
| BeRoot | 跨平台 | 提权路径检查 |

### 后渗透

| 工具 | 用途 |
|------|------|
| Mimikatz | Windows凭据提取 |
| BloodHound | AD环境分析 |
| Chisel | 端口转发 |
| Ligolo-ng | 隧道代理 |

### 网络分析

| 工具 | 用途 |
|------|------|
| Wireshark | 图形化抓包分析 |
| tcpdump | 命令行抓包 |
| Responder | LLMNR/NBT-NS投毒 |
| Bettercap | MITM攻击框架 |

## 工具安装一键脚本

```bash
# Kali Linux已预装大部分工具

# 额外安装常用工具
sudo apt update
sudo apt install -y \
    gobuster \
    feroxbuster \
    seclists \
    wordlists \
    chisel \
    bloodhound \
    neo4j

# Python工具
pip3 install pwntools impacket

# 下载LinPEAS/WinPEAS
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
```

## 字典推荐

| 字典 | 用途 | 路径(Kali) |
|------|------|------------|
| rockyou.txt | 密码爆破 | /usr/share/wordlists/rockyou.txt |
| SecLists | 综合字典集 | /usr/share/seclists/ |
| dirb | 目录爆破 | /usr/share/wordlists/dirb/ |
