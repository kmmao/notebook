# 第一阶段：基础强化（2-4周）

## 学习目标

确保以下核心技能扎实，为后续渗透测试打基础。

## 知识点清单

### 1. Linux命令行
- [ ] 文件系统导航与权限管理
- [ ] 进程管理与服务控制
- [ ] 网络命令（netstat, ss, ip, iptables）
- [ ] 文本处理（grep, awk, sed, cut）
- [ ] Shell脚本编写

**实战**: 安装 Kali Linux / Parrot OS，日常使用

### 2. 网络协议深度理解
- [ ] OSI七层模型实战理解
- [ ] TCP三次握手/四次挥手
- [ ] HTTP/HTTPS协议详解（请求方法、状态码、Header）
- [ ] DNS解析过程与攻击面
- [ ] ARP协议与欺骗原理

**实战**: Wireshark抓包分析各种协议

### 3. Python安全编程
- [ ] Socket编程（端口扫描器）
- [ ] HTTP请求库（requests）
- [ ] 正则表达式
- [ ] 子进程调用（subprocess）
- [ ] 简单漏洞利用脚本编写

**实战**: 写一个多线程端口扫描器

### 4. Bash脚本
- [ ] 变量与控制结构
- [ ] 函数与参数处理
- [ ] 管道与重定向
- [ ] 自动化渗透辅助脚本

## 推荐资源

| 资源 | 链接 |
|------|------|
| TryHackMe Pre Security | https://tryhackme.com/path/outline/presecurity |
| Linux Journey | https://linuxjourney.com |
| OverTheWire Bandit | https://overthewire.org/wargames/bandit/ |

## 检验标准

完成以下任务说明基础过关：
1. 能在纯命令行环境下完成日常操作
2. 能看懂Wireshark抓包内容
3. 能用Python写简单的网络工具
4. 完成OverTheWire Bandit全部关卡
