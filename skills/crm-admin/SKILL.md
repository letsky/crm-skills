---
name: crm-admin
description: >
  Automate CRM admin operations on 管理系统.
  Handles login, order search, order close, customer management, and common 
  admin tasks using Playwright CLI browser automation. Use this skill whenever 
  the user mentions CRM后台, 查订单, 关闭订单, 订单管理, 搜索订单, CRM登录, 
  后台, or any operation involving the CRM admin system — even 
  if they don't explicitly say "CRM". Also trigger when they paste an order ID 
  like "m20297..." and want to look it up or take action on it.
version: 1.5.0
skillKey: crm-admin
emoji: 🏢
os: darwin, linux, win32
allowed-tools: Bash(playwright-cli:*)
metadata: {"openclaw":{"requires":{"bins":["playwright-cli"]}}}
---

# CRM 后台管理自动化

通过 Playwright CLI 自动化操作管理系统。

## 环境变量配置

使用前需设置以下环境变量：

```bash
export CRM_ADMIN_URL="https://your-crm-domain.com"
```

后续所有命令中的 `$CRM_ADMIN_URL` 会自动替换为配置的域名。

## 前置要求

- 所有命令统一使用 `--session=crm`
- 确保已配置 `CRM_ADMIN_URL` 环境变量

## 核心工作流

所有操作遵循最短路径原则——能直接 URL 跳转就不走菜单点击，能复用登录态就不重新登录。

1. **检查会话** → `playwright-cli list`
2. **有会话** → `state-load crm-auth.json` 恢复登录态，再 `goto` 目标 URL
3. **无会话** → `open $CRM_ADMIN_URL --headed`
4. **判断是否需要登录** → snapshot 检查 URL 是否为 `/user/login`，是则参考 `references/login-flow.md`
5. **执行操作** → 搜索、筛选、详情、关闭等
6. **验证结果** → 截图确认

## 登录

提供两种登录方式：账号密码登录、钉钉扫码登录。登录成功后保存状态供后续复用。

完整的登录流程（包括页面元素表和每一步的命令）在 **`references/login-flow.md`** 中。
当 snapshot 显示 URL 为 `/user/login` 或 `state-load` 失败时，阅读该文件。

## 导航

已知目标 URL 时，直接 `goto` 跳转（跳过菜单交互）。

页面 URL 映射表和侧边栏菜单结构在 **`references/navigation.md`** 中。
当需要查找某个页面的 URL 或探索未知菜单时，阅读该文件。

## 业务模块

| 模块 | 参考文件 | 何时阅读 |
|------|----------|----------|
| 订单管理（搜索、筛选、详情、关闭等） | `modules/order-management.md` | 用户提及订单相关操作时 |

## Ant Design 组件特征

这个系统基于 Ant Design 构建，了解其组件特征有助于准确定位元素：

- **菜单项**：`menuitem` role
- **表格**：`table > rowgroup > row > cell` 结构
- **弹窗确认**：通常包含 `button "确定"` 或 `button "确认"`
- **Tab 切换**：`tab` role，选中状态有 `[selected]` 标记
- **日期选择器**：`textbox "开始日期"` / `textbox "结束日期"`
- **下拉选择**：点击后 DOM 变化，需重新 snapshot 获取展开后的选项

## 错误处理

- **登录态丢失**（URL 跳到 `/user/login`）→ 先 `state-load crm-auth.json`，失败再走完整登录流程（见 `references/login-flow.md`）
- **短信验证码** → 截图并询问用户，不可跳过（安全验证只有用户本人能提供）
