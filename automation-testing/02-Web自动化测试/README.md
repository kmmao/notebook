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

---

## UI视觉验证方案对比

> 核心问题：页面"长得对不对"，怎么用自动化验证？

### 三种验证方式

#### 1. DOM断言（主力方式，推荐）

不"看"页面，直接查HTML结构，验证元素、文本、状态：

```typescript
// 元素存不存在
await expect(page.getByTestId('login-btn')).toBeVisible()

// 文本对不对
await expect(page.getByTestId('user-name')).toContainText('张三')

// 数量对不对
await expect(page.locator('tbody tr')).toHaveCount(10)

// 状态对不对
await expect(page.getByTestId('submit-btn')).toBeDisabled()

// 关键样式对不对
await expect(page.getByTestId('error-text')).toHaveCSS('color', 'rgb(255, 0, 0)')

// URL跳转对不对
await expect(page).toHaveURL('/dashboard')
```

**优点：** 极其稳定，不受环境、字体渲染、分辨率影响。
**覆盖面：** 能覆盖99%的UI验证需求。

#### 2. 截图像素对比（toHaveScreenshot，不推荐）

拍截图和基准图逐像素对比：

```typescript
await expect(page).toHaveScreenshot('login-page.png')
```

**实际项目中的坑：**

| 问题 | 说明 |
|------|------|
| 环境差异误报 | Mac/Linux/Windows字体渲染不同，同一代码截图不一样 |
| 动态内容 | 时间戳、随机头像、广告位，每次截图都变 |
| 维护噩梦 | 50个页面 × 3浏览器 = 150张基准图，每次UI调整都要更新 |
| 一像素触发失败 | 改了按钮圆角4px→6px，几十张截图全部报错 |

**结论：** 除非是纯UI组件库（Storybook）且在Docker统一环境下跑，否则不要用。

#### 3. AI视觉验证（Claude Code看截图，推荐作为补充）

Claude Code是多模态AI，能直接"看"Playwright截取的截图：

```typescript
// 测试中截图
await page.screenshot({ path: 'test-results/current-page.png' })
```

然后Claude Code用Read工具读取截图，用视觉能力判断页面：

```
Claude Code内部流程：
1. Playwright截图 → page.screenshot()
2. Read('test-results/current-page.png')  ← 直接看图
3. 视觉判断：布局对不对？元素都在吗？有没有明显错乱？
4. 发现问题 → 修CSS → 重新截图 → 再看
```

**AI视觉能做到 vs 做不到：**

| 能做到 | 做不到 |
|--------|--------|
| 看出元素有没有出现 | 精确判断间距16px还是18px |
| 看出布局明显错乱（重叠、溢出） | 检测1-2px的对齐偏差 |
| 看出颜色明显不对（红变蓝） | 判断颜色是#333还是#444 |
| 看出页面空白/未加载 | 和设计稿精确像素级对比 |
| 看出缺失的功能元素 | 判断字体是否完全一致 |

### 商业AI视觉测试工具

| 工具 | 特点 | 价格 |
|------|------|------|
| **Applitools Eyes** | AI理解布局，忽略动态内容和亚像素差异 | 贵，按截图量收费 |
| **Percy (BrowserStack)** | 快照对比 + 审批流程 | 中等 |
| **Chromatic (Storybook)** | 专为组件库设计 | 有免费额度 |

大多数个人项目和中小团队不需要花这个钱。

### 最佳实践：推荐组合

```
DOM断言（必选，基础）
  验证功能对不对：元素存在、文本正确、状态正确
  +
AI视觉验证（推荐，补充）
  验证长得对不对：截图 → Claude Code看 → 判断布局
```

#### CLAUDE.md 配置模板（加入AI视觉验证）

```markdown
## UI验证（开发完页面后执行）

### 第一步：Playwright DOM断言
- 验证关键元素可见（toBeVisible）
- 验证文本内容正确（toContainText）
- 验证交互行为正确（点击后状态变化）
- 验证异常路径有反馈

### 第二步：AI视觉验证
- 用 page.screenshot() 截取页面截图
- 用 Read 工具查看截图
- 用视觉能力检查：
  - 布局是否合理（没有重叠、溢出、大面积空白）
  - 关键元素是否可见且位置正确
  - 没有明显样式错误（空白页、错位、乱码）
- 发现视觉问题则修复后重新截图验证

### 不要用截图像素对比（toHaveScreenshot）
- 环境差异导致大量误报
- 维护成本极高
- 用DOM断言 + AI视觉验证替代
```

#### 完整测试示例

```typescript
import { test, expect } from '@playwright/test'

test.describe('用户列表页面', () => {
  // DOM断言：验证功能
  test('正确显示用户列表', async ({ page }) => {
    await page.goto('/users')

    // 表头
    await expect(page.getByText('用户名')).toBeVisible()
    await expect(page.getByText('邮箱')).toBeVisible()

    // 数据
    const rows = page.locator('tbody tr')
    await expect(rows).toHaveCount(10)
    await expect(rows.first()).toContainText('admin')
  })

  // DOM断言：验证交互
  test('搜索用户', async ({ page }) => {
    await page.goto('/users')
    await page.getByTestId('search-input').fill('张三')
    await page.getByTestId('search-btn').click()

    await expect(page.locator('tbody tr')).toHaveCount(1)
    await expect(page.locator('tbody tr').first()).toContainText('张三')
  })

  // AI视觉验证：截图供Claude Code查看
  test('页面视觉检查', async ({ page }) => {
    await page.goto('/users')
    // 等待数据加载完成
    await expect(page.locator('tbody tr')).toHaveCount(10)
    // 截图供AI视觉验证
    await page.screenshot({
      path: 'test-results/users-page.png',
      fullPage: true
    })
  })
})
```

Claude Code跑完测试后，会自动Read截图文件，用视觉能力做最终检查。
如果看到明显的布局问题（元素重叠、内容溢出、样式错乱），会自行修复并重新验证。
