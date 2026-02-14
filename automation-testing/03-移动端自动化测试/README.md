# 03-移动端自动化测试

## 核心工具对比

| 工具 | 平台 | 语言 | 特点 |
|------|------|------|------|
| **Appium** | iOS + Android | 多语言 | 跨平台标准，WebDriver协议 |
| **Espresso** | Android | Java/Kotlin | Google官方，速度快，白盒 |
| **XCUITest** | iOS | Swift/ObjC | Apple官方，稳定可靠 |
| **Detox** | React Native | JavaScript | 灰盒测试，自动同步 |
| **Maestro** | iOS + Android | YAML | 声明式，零代码上手 |
| **Flutter Test** | Flutter | Dart | 框架内置，Widget测试 |

## 知识点

### Appium
- Appium 2.0架构与Driver机制
- Desired Capabilities配置
- 元素定位：Accessibility ID / XPath / UIAutomator / Predicate
- 手势操作：滑动、长按、多点触控
- 混合应用（Hybrid App）测试：WebView切换
- Appium Inspector元素审查
- 云平台集成：BrowserStack / Sauce Labs / LambdaTest

### 原生框架
- **Espresso**：ViewMatcher / ViewAction / ViewAssertion
- **XCUITest**：XCUIApplication / XCUIElement / 查询链
- 原生框架 vs Appium 的选型策略

### 移动端测试特有挑战
- 设备碎片化与兼容性测试
- 网络条件模拟（弱网、断网、切换）
- 推送通知测试
- 权限弹窗处理
- 安装 / 卸载 / 升级测试
- 真机 vs 模拟器策略
- 设备农场（Device Farm）管理
