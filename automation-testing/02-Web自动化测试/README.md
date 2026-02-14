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

---

## Playwright + Claude Code 自我校验实战

> 核心目标：让Claude Code开发完功能后，自动写E2E测试、自动跑、自动修，形成闭环。

### 工作流全景

```
Claude Code 写功能代码
       ↓
Claude Code 写 Playwright E2E 测试
       ↓
Claude Code 运行 npx playwright test
       ↓
  ┌── 通过 → 任务完成 ✓
  └── 失败 → 读错误信息/截图 → 修代码 → 再跑 → 循环直到通过
```

### 环境准备（一次性）

#### 安装Playwright

```bash
npm init playwright@latest
```

生成的目录结构：

```
tests/                # 测试文件
playwright.config.ts  # 配置文件
test-results/         # 测试结果（截图、视频、trace）
```

#### playwright.config.ts 推荐配置

```typescript
import { defineConfig } from '@playwright/test'

export default defineConfig({
  testDir: './tests',
  timeout: 30000,
  retries: 1,
  use: {
    baseURL: 'http://localhost:5173',  // Vite默认端口
    screenshot: 'only-on-failure',     // 失败自动截图
    video: 'retain-on-failure',        // 失败保留视频
    trace: 'retain-on-failure',        // 失败保留trace
  },
  webServer: {
    command: 'npm run dev',
    port: 5173,
    reuseExistingServer: true,
  },
})
```

### CLAUDE.md 配置模板

在项目根目录的 `CLAUDE.md` 中加入以下内容，Claude Code会自动读取并遵守：

```markdown
# 前端自我校验规则

## 核心原则
每个页面级功能开发完成后，必须编写Playwright E2E测试并运行通过。
测试不通过不算完成，必须根据错误修复代码后重新运行。

## Playwright测试规范

### 写测试时必须遵守
- 使用 data-testid 定位元素，不要用CSS类名或文本（会变）
- 每个测试用 test.describe 按功能分组
- 用 expect 断言关键结果：页面跳转、元素可见、文本内容
- 失败时自动截图：await page.screenshot({ path: 'test-results/debug.png' })

### 测试覆盖要求
每个页面至少验证：
1. 页面能正常打开，关键元素可见
2. 用户主流程能走通（填表单 → 提交 → 看到成功提示）
3. 异常路径有反馈（不填必填项 → 看到错误提示）

### 执行命令
- 运行全部测试：npx playwright test
- 运行单个文件：npx playwright test tests/xxx.spec.ts
- 查看报告：npx playwright show-report
- 调试模式：npx playwright test --debug

### 自我修复流程
1. 运行测试
2. 如果失败，阅读错误信息和截图
3. 判断是测试写错了还是功能代码有bug
4. 修复后重新运行
5. 重复直到全部通过
```

### Hooks 配置（可选增强）

在 `~/.claude/settings.json` 中配置，Vue文件修改后自动提醒跑测试：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "if echo \"$CLAUDE_FILE_PATH\" | grep -q '\\.vue$'; then echo '⚠️ Vue文件已修改，请运行 npx playwright test 验证'; fi"
          }
        ]
      }
    ]
  }
}
```

### 实战示例：开发用户登录页面

**Prompt写法：**

> 帮我开发一个用户登录页面，包含用户名、密码输入和登录按钮。开发完后自己写E2E测试验证。

#### Claude Code 产出的功能代码

```vue
<!-- src/views/LoginPage.vue -->
<template>
  <form @submit.prevent="handleLogin">
    <input data-testid="username" v-model="username" placeholder="用户名" />
    <input data-testid="password" v-model="password" type="password" placeholder="密码" />
    <button data-testid="login-btn" type="submit">登录</button>
    <p data-testid="error-msg" v-if="error">{{ error }}</p>
  </form>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { login } from '@/api/auth'

const router = useRouter()
const username = ref('')
const password = ref('')
const error = ref('')

async function handleLogin() {
  try {
    await login(username.value, password.value)
    router.push('/dashboard')
  } catch (e) {
    error.value = '用户名或密码错误'
  }
}
</script>
```

#### Claude Code 产出的E2E测试

```typescript
// tests/login.spec.ts
import { test, expect } from '@playwright/test'

test.describe('登录页面', () => {
  test('页面正常渲染', async ({ page }) => {
    await page.goto('/login')
    await expect(page.getByTestId('username')).toBeVisible()
    await expect(page.getByTestId('password')).toBeVisible()
    await expect(page.getByTestId('login-btn')).toBeVisible()
  })

  test('正确登录后跳转到首页', async ({ page }) => {
    await page.goto('/login')
    await page.getByTestId('username').fill('admin')
    await page.getByTestId('password').fill('123456')
    await page.getByTestId('login-btn').click()
    await expect(page).toHaveURL('/dashboard')
  })

  test('密码错误显示错误提示', async ({ page }) => {
    await page.goto('/login')
    await page.getByTestId('username').fill('admin')
    await page.getByTestId('password').fill('wrong')
    await page.getByTestId('login-btn').click()
    await expect(page.getByTestId('error-msg')).toBeVisible()
  })
})
```

#### 自我修复过程

Claude Code运行测试后看到：

```
✗ 正确登录后跳转到首页
  Error: expect(page).toHaveURL('/dashboard')
  Received: '/login'
```

Claude Code分析：
> "登录后没跳转，handleLogin里router.push没执行，
>  检查发现login API的baseURL配置缺失。"

修复 → 再跑 → 全部通过 ✓

### 测试结果产出

```
test-results/
├── login-页面正常渲染/
├── login-正确登录后跳转/
│   ├── screenshot.png         # 失败截图（仅失败时）
│   └── video.webm             # 失败录屏（仅失败时）
└── login-密码错误显示提示/

playwright-report/
└── index.html                 # 可视化HTML报告
```

### 你需要做 vs 不需要做

| 你需要做（一次性） | 你不需要做（Claude Code自动完成） |
|-------------------|-------------------------------|
| 装Playwright | 写测试代码 |
| 写CLAUDE.md规则 | 跑测试 |
| Prompt里提一句"自己验证" | 看报错信息 |
| Review最终结果 | 修bug再跑 |

### 注意事项

1. **前端dev server必须跑着** — playwright.config.ts的webServer配置会自动启动
2. **data-testid是关键** — 在CLAUDE.md里规定用data-testid定位，否则AI可能用不稳定的选择器
3. **不要测所有东西** — E2E只测主流程，细节逻辑用Vitest组件测试
4. **测试也可能写错** — Claude Code修代码前要先判断是功能bug还是测试写错了
