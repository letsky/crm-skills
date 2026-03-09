# 登录流程

仅在 `state-load` 失败或 URL 为 `/user/login` 时需要执行。

## 登录页元素表（URL: `/user/login`）

| 元素用途 | Role + Name |
|---------|-------------|
| 用户名输入框 | `textbox "用户名"` |
| 密码输入框 | `textbox "密码"` |
| 登录按钮 | `button "登 录"` |
| 验证码输入框 | `textbox "请输入验证码"` |
| 验证按钮 | `button "验 证"` |
| 账户密码登录 Tab | `tab "账户密码登录"` |
| 钉钉扫码登录 Tab | `tab "钉钉扫码登录"` |

## 登录方式选择

询问用户选择登录方式：
1. **账号密码登录** → 方式一
2. **钉钉扫码登录** → 方式二

## 方式一：账号密码登录

```bash
playwright-cli --session=crm open $CRM_ADMIN_URL --headed
playwright-cli --session=crm snapshot
# 找到 textbox "用户名"、textbox "密码"、button "登 录" 的 ref
playwright-cli --session=crm fill <用户名_ref> "账号"
playwright-cli --session=crm fill <密码_ref> "密码"
playwright-cli --session=crm click <登录_ref>
```

登录后进入 **短信验证码** 步骤。

## 方式二：钉钉扫码登录

```bash
playwright-cli --session=crm open $CRM_ADMIN_URL --headed
playwright-cli --session=crm snapshot
# 找到 tab "钉钉扫码登录" 的 ref
playwright-cli --session=crm click <钉钉扫码_tab_ref>
playwright-cli --session=crm snapshot
# 截图展示二维码，请用户使用钉钉 APP 扫码
playwright-cli --session=crm screenshot --filename qrcode.png
```

提示用户扫码后，等待页面跳转到 `/welcome`，然后直接进入 **保存登录态** 步骤。

## 短信验证码

点击登录后页面切换，需重新 snapshot：

```bash
playwright-cli --session=crm snapshot
```

此时询问用户获取短信验证码（安全验证只有用户本人能完成），然后：

```bash
playwright-cli --session=crm fill <验证码_ref> "用户提供的验证码"
playwright-cli --session=crm click <验证_ref>
```

验证成功后跳转到 `/welcome`。

## 保存登录态

登录成功后立即保存，后续可跳过登录直接复用：

```bash
playwright-cli --session=crm state-save crm-auth.json
```
