# 页面导航规则

## 系统页面

| 页面 | URL |
|------|-----|
| 登录页 | /user/login |
| 欢迎页（首页） | /welcome |

## 直接 URL 导航（推荐）

已知页面 URL 时，直接 `goto` 跳转，跳过侧边栏菜单交互。

## 菜单导航（备选）

仅在不知道目标 URL 时使用：依次点击一级菜单 menuitem → snapshot → 点击子菜单 menuitem。

## 侧边栏菜单结构

登录成功后，侧边栏显示以下一级菜单（每个 menuitem 可展开子菜单）：

```yaml
menus:
  - name: 内容管理
    icon: translation
  - name: 直播聊天管理
    icon: sound
  - name: 企微RPA群发
    icon: comment
  - name: 服务管理
    icon: smile
  - name: 销售管理
    icon: monitor
  - name: 开户管理
    icon: filter
  - name: 订单管理
    icon: property-safety
    children:
      - name: 订单管理
        url: /order-center/order-management
      - name: 子订单管理
        url: /order-center/sub-order-management
      - name: 我的订单
        url: /order-center/my-order
      - name: 产品销售
        url: /order-center/product-sales
      - name: 产品销售新
        url: /order-center/product-sales-new
      - name: 转账单申请
        url: /sale/transfer-order-create
      - name: 转账单审核
        url: /business/transfer-audit
      - name: 自动开票管理
        url: /finance/invoice-manage
      - name: 订单信息修正
        url: /compliance/order-amendment-information
      - name: 订单归属信息管理
        url: /order-center/order-ownerShip-information-management
      - name: 订单标记记录
        url: /order-center/order-tag-record
  - name: 退款管理
    icon: code
  - name: 合规管理
    icon: sisternode
  - name: 商务管理
    icon: shop
  - name: 报表管理
    icon: line-chart
  - name: 客户管理
    icon: team
  - name: 推广管理
    icon: ungroup
  - name: 运营管理
    icon: fire
  - name: 量化组合
    icon: sliders
  - name: 运维管理
    icon: api
  - name: 资产管理
    icon: pay-circle
  - name: 星云-企微群控营销
    icon: wechat-work
```

顶部还有一个全局搜索框：`textbox "search..."`
