# 订单管理操作指南

> **订单号前缀识别**：
> - **主订单**：以 `m` 开头，如 `m2029797402509643776` → 操作入口：订单管理页
> - **子订单**：以 `sub` 开头，如 `sub_2031189165657604096` → 操作入口：子订单管理页

---

## 一、主订单管理

### 页面固定元素表

#### 查询区域固定元素

| 元素用途 | Role + Name（稳定选择器） | 说明 |
|---------|--------------------------|------|
| 筛选 Tab | `tab "筛选"` | 默认选中，组合条件筛选 |
| 搜索 Tab | `tab "搜索"` | 按订单号/用户id精确搜索 |
| 搜索输入框 | `textbox "按订单号 用户id精确搜索"` | 搜索 Tab 下的输入框 |
| 搜索按钮 | `button "搜 索"` | 搜索 Tab 下的搜索按钮 |
| 重置按钮 | `button "重 置"` | 筛选 Tab 下重置所有条件 |

#### 筛选区域固定元素

| 元素用途 | Role + Name（稳定选择器） | 默认值 |
|---------|--------------------------|--------|
| 订单状态 | `combobox "订单状态 :"` | 全部 |
| 创建时间-开始 | `textbox "开始日期"` | 近7天起始 |
| 创建时间-结束 | `textbox "结束日期"` | 当天 |
| 选择销售 | `button "选择销售 :"` | 请选择销售 |
| 订单类型 | `combobox "订单类型 :"` | 全部 |
| 选择SKU | `button "选择SKU :"` | 选择 |
| 近60天是否添加过高级助教 | `combobox "近60天是否添加过高级助教 :"` | 全部 |
| 回访状态 | `combobox "回访状态 :"` | 全部 |

**⚠️ 按订单号查找时，始终使用「搜索」Tab，不要在筛选列表中翻找。**

#### 订单表格列

| 列名 | 说明 |
|------|------|
| 订单号 | 订单唯一标识，如 m2029797402509643776 |
| sku | 产品 SKU 名称 |
| 微信用户 | 微信昵称 |
| 用户 | 用户姓名 |
| 订单金额 | 原始金额 |
| 优惠券抵扣 | 优惠金额 |
| 应付金额 | 实际应付 |
| 已付金额 | 已支付金额 |
| 订单状态 | 如：已关闭、已完成 |
| 创建时间 | 订单创建时间 |
| 支付完成时间 | 支付完成时间（未支付则为空） |
| 归属销售 | 关联的销售人员 |
| 签字状态 | 如：签字未开始、已签字 |
| 近60天是否添加过高级助教 | -- 或具体值 |
| 回访状态 | 如：--、需人工回访、已完成问卷、人工回访不通过 |
| 操作 | 「详情」按钮 + 「更多」下拉菜单 |

#### 表格行操作按钮

每行末尾有两个操作入口：
1. **详情** — `button "详情"`，点击查看订单详情
2. **更多** — 带 `img "down"` 的下拉触发器，点击展开更多操作菜单（无需签字、重新签字、关闭订单、状态同步、冻结订单）

#### 分页

表格底部分页组件：
- 记录统计：「第 1-20 条/总共 XXX 条」
- 页码按钮：1, 2, 3, 4, 5 ... N
- 上一页 `button "left"` / 下一页 `button "right"`
- 向前/向后 5 页快捷跳转

### 操作流程

#### 搜索特定订单（推荐）

按订单号精确查找，始终使用搜索 Tab。

```bash
# 1. 直接导航到订单管理页
playwright-cli --session=crm goto $CRM_ADMIN_URL/order-center/order-management
playwright-cli --session=crm snapshot

# 2. 切换到搜索 Tab
playwright-cli --session=crm click <搜索tab_ref>
playwright-cli --session=crm snapshot

# 3. 输入订单号并搜索
playwright-cli --session=crm fill <搜索输入框_ref> "订单号"
playwright-cli --session=crm click <搜索按钮_ref>
playwright-cli --session=crm snapshot
```

#### 按条件筛选订单

```bash
playwright-cli --session=crm snapshot
# 修改筛选条件，如选择订单状态
playwright-cli --session=crm click <订单状态combobox_ref>
playwright-cli --session=crm snapshot
playwright-cli --session=crm click <目标状态option_ref>
playwright-cli --session=crm snapshot
```

**注意**：筛选模式下默认按创建时间近7天过滤。如果目标订单不在此范围内，需要调整日期范围或直接使用搜索 Tab。

#### 查看订单详情

订单详情使用 Ant Design Drawer 组件，从右侧滑出。

**Drawer 结构**：
- 根元素：`dialog` role
- 标题栏：显示「订单详情」+ 订单状态标签
- Tab 切换：订单详情、支付明细、审核记录、回访、相关退费
- 内容区：订单流程时间线 + 详情表格

**关闭 Drawer 的方式**（任选其一）：

1. **点击遮罩**（推荐）— 点击 Drawer 外部的遮罩区域关闭
   ```bash
   # 方法一：通过 JavaScript 点击遮罩元素
   playwright-cli --session=crm eval "document.querySelector('.ant-drawer-mask')?.click()"
   
   # 方法二：通过鼠标坐标点击左侧遮罩区域
   playwright-cli --session=crm mousemove 100 300
   playwright-cli --session=crm mousedown
   playwright-cli --session=crm mouseup
   ```

2. **按 Escape 键**
   ```bash
   playwright-cli --session=crm press Escape
   ```

**完整流程**：
```bash
# 1. 点击详情按钮打开 Drawer
playwright-cli --session=crm click <详情button_ref>
playwright-cli --session=crm snapshot

# 2. 查看/操作详情内容...

# 3. 关闭 Drawer（点击遮罩）
playwright-cli --session=crm eval "document.querySelector('.ant-drawer-mask')?.click()"
```

#### 关闭订单

1. **导航到订单管理** → `goto` 直接跳转
2. **搜索目标订单** → 使用搜索 Tab 按订单号查找
3. **检查订单状态** → 从搜索结果的「订单状态」列确认当前状态：
   - 「已关闭」→ 无需操作，直接告知用户订单已关闭
   - 「已完成」→ 通常不建议关闭，先确认用户意图
   - 「未完成」/「待支付」→ 可以执行关闭操作
4. 点击该行的「更多」下拉触发器 → snapshot → 点击「关闭订单」
5. 处理确认弹窗，点击「确定」或「确认」
6. 截图确认操作结果

**⚠️ 业务限制**：
- 下拉菜单中的操作按钮状态取决于订单当前状态，不符合条件的会显示为 disabled
- 有已开通的子订单时无法关闭主订单
- 已支付的订单通常不能直接关闭

**⚠️ 常见错误处理**：

| 错误提示 | 原因 | 处理方式 |
|---------|------|----------|
| 主订单状态已更新，请刷新重试 | 订单状态在操作期间被其他用户/系统修改 | 执行 `reload` 刷新页面，重新搜索订单确认最新状态 |
| 有已开通的子订单，无法关闭订单 | 存在已支付的子订单 | 告知用户无法关闭，需先处理子订单 |

---

## 二、子订单管理

### 页面固定元素表

#### 查询区域固定元素

| 元素用途 | Role + Name（稳定选择器） | 说明 |
|---------|--------------------------|------|
| 筛选 Tab | `tab "筛选"` | 默认选中，按状态/日期筛选 |
| 搜索 Tab | `tab "搜索"` | 按子订单号精确搜索 |
| 订单状态筛选 | `combobox "订单状态 :"` | 全部 |
| 创建时间 | `textbox "创建时间 :"` | 当天 |
| 重置按钮 | `button "重 置"` | 重置所有筛选条件 |

#### 子订单表格列

| 列名 | 说明 |
|------|------|
| 支付单号 | 子订单唯一标识，以 `sub_` 开头 |
| 金额 | 支付金额 |
| 微信用户 | 微信昵称 |
| 支付方式 | 如：微信、转账单 |
| 接收商户 | 收款渠道，如：拉卡拉-微信-非智能路由 |
| 支付账号 | 支付账号（脱敏显示） |
| 订单号 | 关联的主订单号（m 开头） |
| 创建时间 | 子订单创建时间 |
| 支付时间 | 支付完成时间（未支付则为 --） |
| 关闭时间 | 关闭时间（未关闭则为 --） |
| 订单状态 | 如：待支付、已支付、已关闭 |
| 退款时间 | 退款时间（未退款则为 --） |
| 操作 | 「详情」、「关闭订单」、「标记退款」按钮 |

#### 表格行操作按钮

每行末尾有三个操作按钮（按钮可用性取决于当前订单状态）：
1. **详情** — `button "详情"`，任何状态均可点击
2. **关闭订单** — `button "关闭订单"`，仅「待支付」状态可用；已支付/已关闭状态为 disabled
3. **标记退款** — `button "标记退款"`，仅「已支付」状态可用；其他状态为 disabled

### 操作流程

#### 搜索特定子订单

**⚠️ 子订单页默认筛选当天数据。若子订单不在当天，需先切换到搜索 Tab 精确查找。**

```bash
# 1. 直接导航到子订单管理页
playwright-cli --session=crm goto $CRM_ADMIN_URL/order-center/sub-order-management
playwright-cli --session=crm snapshot

# 2. 如需精确搜索，切换到搜索 Tab
playwright-cli --session=crm click <搜索tab_ref>
playwright-cli --session=crm snapshot

# 3. 输入子订单号并搜索
playwright-cli --session=crm fill <搜索输入框_ref> "sub_xxxx"
playwright-cli --session=crm click <搜索按钮_ref>
playwright-cli --session=crm snapshot
```

#### 关闭子订单

1. **导航到子订单管理页** → `goto` 直接跳转
2. **找到目标子订单** → 在当天列表中查找，或使用搜索 Tab 精确查找
3. **检查订单状态** → 如果 cell 显示「已关闭」或「已支付」则无法关闭，直接告知用户
4. 点击该行的 `button "关闭订单"` → snapshot → 出现确认弹窗
5. 点击弹窗中的「确 定」按钮
6. 确认页面提示「关闭订单成功」，截图确认

```bash
# 1. 导航到子订单管理页
playwright-cli --session=crm goto $CRM_ADMIN_URL/order-center/sub-order-management
playwright-cli --session=crm snapshot

# 2. 点击目标子订单行的「关闭订单」按钮
playwright-cli --session=crm click <关闭订单button_ref>
playwright-cli --session=crm snapshot

# 3. 确认弹窗 → 点击「确 定」
playwright-cli --session=crm click <确定button_ref>
playwright-cli --session=crm snapshot
```

**⚠️ 业务限制**：
- 仅「待支付」状态的子订单可以关闭
- 「已支付」状态的子订单「关闭订单」按钮为 disabled，不可操作
- 「已关闭」状态的子订单所有操作按钮均为 disabled

---

## 三、转账单申请

转账单用于记录客户通过银行转账等方式支付的款项，需关联后台已有的主订单。

### 页面固定元素表

#### 列表页元素

| 元素用途 | Role + Name（稳定选择器） | 说明 |
|---------|--------------------------|------|
| 新建转账单按钮 | `button "plus 新建转账单"` | 点击进入新建流程 |
| 筛选 Tab | `tab "筛选"` | 默认选中 |
| 搜索 Tab | `tab "搜索"` | 按订单号精确搜索 |
| 提交时间 | `textbox "提交时间 :"` | 日期范围筛选 |
| 审核状态 | `combobox "审核状态 :"` | 全部/待审核/已通过/已拒绝 |
| 重置按钮 | `button "重 置"` | 重置筛选条件 |

#### 新建转账单流程（三步骤）

**步骤1：填写转账信息**

| 元素用途 | Role + Name（稳定选择器） | 说明 |
|---------|--------------------------|------|
| 返回按钮 | `button "返回"` | 返回列表页 |
| 订单编号输入框 | `textbox "订单编号 :"` | 输入主订单号 |
| 搜索按钮 | `button "搜 索"` | 搜索订单信息 |
| 转账金额输入框 | `textbox "* 客户本次转账金额 :"` | 必填，输入转账金额 |
| 收款商户下拉框 | `combobox "* 收款商户 :"` | 必填，选择收款商户类型 |
| 上传截图按钮 | `button "plus 上传"` | 必填，上传转账相关截图 |
| 备注输入框 | `textbox "* 备注 :"` | 必填，填写备注信息 |
| 下一步按钮 | `button "下一步"` | 进入确认页面 |

**收款商户选项**

点击下拉框后从页面动态获取。

**步骤2：确认转账信息**

| 元素用途 | Role + Name（稳定选择器） | 说明 |
|---------|--------------------------|------|
| 上一步按钮 | `button "上一步"` | 返回修改信息 |
| 提交按钮 | `button "提 交"` | 提交转账单 |

**步骤3：完成**

提交成功后显示完成页面。

### 操作流程

#### 新建转账单

**⚠️ 填写前必须询问用户提供以下信息**：

| 必填字段 | 询问内容 | 示例 |
|---------|----------|------|
| 转账金额 | 客户本次转账金额是多少？ | 0.03 |
| 收款商户 | 请选择收款商户类型（点击下拉框后从页面选项中选择） | - |
| 相关截图 | 请提供转账截图文件的绝对路径 | /Users/xxx/screenshot.png |
| 备注 | 请输入转账单备注内容 | 订单转账 |

**注意**：以上四个字段均为必填项，必须向用户询问后才能继续操作。

```bash
# 1. 导航到转账单申请页
playwright-cli --session=crm goto $CRM_ADMIN_URL/sale/transfer-order-create
playwright-cli --session=crm snapshot

# 2. 点击「新建转账单」按钮
playwright-cli --session=crm click <新建转账单button_ref>
playwright-cli --session=crm snapshot

# 3. 输入订单号并搜索
playwright-cli --session=crm fill <订单编号textbox_ref> "m2031218796473016320"
playwright-cli --session=crm click <搜索button_ref>
playwright-cli --session=crm snapshot

# 4. 填写转账金额
playwright-cli --session=crm fill <转账金额textbox_ref> "0.03"

# 5. 选择收款商户（先点击下拉框获取选项，再让用户选择）
playwright-cli --session=crm click <收款商户combobox_ref>
playwright-cli --session=crm snapshot
# 从snapshot中获取收款商户选项，询问用户选择哪个
playwright-cli --session=crm click <用户选择的option_ref>

# 6. 上传截图（需先点击上传按钮触发文件选择器）
playwright-cli --session=crm click <上传button_ref> && sleep 0.5 && playwright-cli --session=crm upload /path/to/screenshot.png

# 7. 填写备注
playwright-cli --session=crm fill <备注textbox_ref> "转账备注内容"
playwright-cli --session=crm snapshot

# 8. 点击「下一步」进入确认页
playwright-cli --session=crm click <下一步button_ref>
playwright-cli --session=crm snapshot

# 9. 确认信息后点击「提交」
playwright-cli --session=crm click <提交button_ref>
playwright-cli --session=crm snapshot
```

### 业务限制

**⚠️ 订单状态限制**：
- 只能为「待支付」或「未完成」状态的订单创建转账单
- 「已关闭」状态的订单无法创建转账单，提交时会失败
- 「已冻结」状态的订单可能无法创建转账单

**⚠️ 金额限制**：
- 转账金额不能超过订单的待付金额

**⚠️ 必填字段**：
- 客户本次转账金额
- 收款商户
- 相关截图（必须上传图片）
- 备注

### 常见错误处理

| 错误提示 | 原因 | 解决方案 |
|---------|------|----------|
| 转账单创建失败，请重试 | 订单状态不符合要求（如已关闭） | 检查订单状态，使用有效订单 |
| 订单不存在 | 输入的订单号无效 | 确认订单号正确 |
| 文件访问受限 | 上传的图片不在允许的目录 | 确保图片在项目目录内 |
