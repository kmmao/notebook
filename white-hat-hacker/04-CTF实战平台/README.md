# 第四阶段：CTF实战平台

## 平台对比（2025）

| 平台 | 难度 | 特点 | 费用 | 推荐度 |
|------|------|------|------|--------|
| TryHackMe | 入门→中级 | 手把手教学，学习曲线平缓 | 免费/VIP $14/月 | ⭐⭐⭐⭐⭐ 入门首选 |
| HackTheBox | 中级→高级 | 硬核实战，接近真实渗透 | 免费/VIP $14/月 | ⭐⭐⭐⭐⭐ 进阶必备 |
| HTB Academy | 系统学习 | 结构化课程，有认证 | 订阅制 | ⭐⭐⭐⭐ |
| PentesterLab | Web专项 | Web安全专精 | $20/月 | ⭐⭐⭐⭐ |
| VulnHub | 全难度 | 本地靶机，完全免费 | 免费 | ⭐⭐⭐⭐ |
| PortSwigger | Web专项 | 免费且质量极高 | 免费 | ⭐⭐⭐⭐⭐ |

## 学习路径建议

```
TryHackMe (1-2月)
    ↓
PortSwigger Web Academy (持续)
    ↓
HackTheBox Easy (1-2月)
    ↓
HackTheBox Medium (长期)
    ↓
参加CTF比赛
```

## TryHackMe推荐路径

### 入门阶段
1. Pre Security Path
2. Complete Beginner Path
3. Jr Penetration Tester Path

### 进阶阶段
4. Offensive Pentesting Path
5. Red Teaming Path

## HackTheBox入门指南

### 注册
1. 访问 hackthebox.com
2. 需要通过一个小挑战才能注册（检验你的基础）

### 开始打靶机
1. 连接VPN
2. 选择Active Machines中的Easy难度
3. 推荐首台靶机：Lame, Jerry, Blue

### Writeup资源
- IppSec YouTube - 每台退役靶机的详细讲解
- 0xdf博客 - 高质量Writeup

## CTF比赛

### 入门级比赛
- PicoCTF - 面向学生，适合入门
- OverTheWire - 经典Wargames

### 正式比赛
- CTFTime.org - CTF比赛日历
- DEF CON CTF - 顶级赛事

## 本地靶场搭建

### VulnHub靶机
```bash
# 下载.ova文件
# 导入VirtualBox/VMware
# 设置Host-Only网络
# 开始渗透
```

### Docker靶场
```bash
# DVWA
docker run -d -p 80:80 vulnerables/web-dvwa

# Juice Shop
docker run -d -p 3000:3000 bkimminich/juice-shop
```
