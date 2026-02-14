# 02-Web自动化测试

## 核心工具对比

| 工具 | 语言支持 | 特点 | 适用场景 |
|------|---------|------|---------|
| **Selenium** | Java/Python/C#/JS/Ruby | 行业标准，生态最大 | 企业级、跨浏览器兼容 |
| **Playwright** | JS/TS/Python/Java/C# | 现代架构，自动等待 | 新项目首选，E2E测试 |
| **Cypress** | JavaScript/TypeScript | 开发者友好，快速反馈 | 前端团队、SPA应用 |
| **WebDriverIO** | JavaScript/TypeScript | Selenium生态+现代API | Node.js技术栈 |
| **Puppeteer** | JavaScript/TypeScript | Chrome专精，轻量 | 爬虫、PDF生成、Chrome测试 |
| **TestCafe** | JavaScript/TypeScript | 零配置，无WebDriver | 快速上手、小型项目 |

## 知识点

### Selenium WebDriver
- WebDriver协议与架构
- 元素定位策略（ID / CSS / XPath / 链接文本）
- 显式等待 vs 隐式等待 vs Fluent Wait
- 浏览器选项与Capabilities配置
- Selenium Grid分布式执行
- 各语言绑定：Java / Python / C# / JavaScript

### Playwright
- 安装与多浏览器配置（Chromium / Firefox / WebKit）
- 自动等待机制与Locator API
- 网络拦截与Mock
- 多页面、多上下文、多浏览器测试
- Trace Viewer调试
- codegen录制工具
- 视觉对比测试

### Cypress
- 架构原理（进程内运行）
- cy.intercept() 网络拦截
- 自定义命令与插件
- Component Testing
- Cypress Cloud与并行化

### 通用技能
- 元素定位最佳实践（data-testid优先）
- 页面加载与动态内容等待策略
- iframe / Shadow DOM / 弹窗处理
- 文件上传下载测试
- 多标签页 / 多窗口处理
- 无头模式（Headless）运行
- 截图与录屏
