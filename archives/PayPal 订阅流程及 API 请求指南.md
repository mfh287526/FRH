# PayPal 订阅流程及 API 请求指南

本文将详细介绍如何在 PayPal 后台创建订阅产品及计划，并通过 API 实现自动化订阅流程。无论是正式环境还是沙盒环境，本文都将为您提供清晰的步骤和代码示例。

## 1. PayPal后台创建产品及计划

### 正式环境创建订阅计划

访问以下地址创建正式环境的订阅计划：  
[PayPal 订阅计划创建页面](https://www.paypal.com/merchantapps/appcenter/acceptpayments/subscriptions)

### 沙盒环境创建订阅计划

如果您需要在沙盒环境中测试，请访问以下地址：  
[PayPal 沙盒订阅计划创建页面](https://www.sandbox.paypal.com/billing/plans)

![创建产品及计划界面](https://bbtdd.com/img/553172083.webp)

进入页面后，您可以根据提示创建产品及计划。请注意，沙盒环境的计划不会在此处显示。

![产品创建流程](https://bbtdd.com/img/264062056758504.webp)
![计划创建流程](https://bbtdd.com/img/31880602.webp)

除了通过后台手动创建，您还可以通过 API 实现产品及计划的创建。

---

## 2. 通过API创建产品及计划

### 获取 API 访问权限

首先，您需要在 PayPal 开发者平台上获取 API 的访问权限。访问以下地址查看 API 文档：  
[PayPal API 文档](https://developer.paypal.com/api/rest/)

### 1. 生成访问令牌

在调用 API 之前，您需要生成访问令牌（Access Token）。以下是生成令牌的请求地址：

**沙盒环境：**  
`https://api.sandbox.paypal.com/v1/oauth2/token`

**正式环境：**  
`https://api.paypal.com/v1/oauth2/token`

### 2. 创建产品

使用以下代码示例创建产品：

bash
curl -v -X POST https://api-m.sandbox.paypal.com/v1/catalogs/products \
-H "Content-Type: application/json" \
-H "Authorization: Bearer Access-Token" \
-H "PayPal-Request-Id: PRODUCT-18062020-001" \
-d '{
  "name": "Video Streaming Service",
  "description": "Video streaming service",
  "type": "SERVICE",
  "category": "SOFTWARE",
  "image_url": "",
  "home_url": "https://example.com/home"
}'


**参数说明：**
- `name`：产品名称
- `description`：产品描述
- `type`：产品类型（`PHYSICAL` 实物商品，`DIGITAL` 数字商品，`SERVICE` 服务）
- `category`：产品类别
- `image_url`：产品 Logo
- `home_url`：产品主页地址

### 3. 创建计划

使用以下代码示例创建计划：

bash
curl -v -X POST https://api-m.sandbox.paypal.com/v1/billing/plans \
-H "Content-Type: application/json" \
-H "Authorization: Bearer Access-Token" \
-H "PayPal-Request-Id: PLAN-18062019-001" \
-d '{
  "product_id": "PROD-XXCD1234QWER65782",
  "name": "Video Streaming Service Plan",
  "description": "Video Streaming Service basic plan",
  "status": "ACTIVE",
  "billing_cycles": [
    {
      "frequency": {
        "interval_unit": "MONTH",
        "interval_count": 1
      },
      "tenure_type": "REGULAR",
      "sequence": 1,
      "total_cycles": 12,
      "pricing_scheme": {
        "fixed_price": {
          "value": "6",
          "currency_code": "USD"
        }
      }
    }
  ],
  "payment_preferences": {
    "auto_bill_outstanding": true,
    "setup_fee": {
      "value": "6",
      "currency_code": "USD"
    },
    "setup_fee_failure_action": "CONTINUE",
    "payment_failure_threshold": 3
  },
  "taxes": {
    "percentage": "0",
    "inclusive": false
  }
}'


**参数说明：**
- `product_id`：产品 ID
- `name`：计划名称
- `description`：计划描述
- `status`：计划状态（`ACTIVE` 或 `INACTIVE`）
- `billing_cycles`：计费周期，包括试用期和常规周期
- `payment_preferences`：付款偏好设置
- `taxes`：税率设置

### 4. 创建订阅

使用以下代码示例创建订阅：

bash
curl -v -X POST https://api-m.sandbox.paypal.com/v1/billing/subscriptions \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <Access-Token>" \
-H "PayPal-Request-Id: SUBSCRIPTION-21092019-001" \
-d '{
  "plan_id": "P-5ML4271244454362WXNWU5NQ",
  "start_time": "2022-07-21T00:00:00Z",
  "quantity": "20",
  "shipping_amount": {
    "currency_code": "USD",
    "value": "10.00"
  },
  "application_context": {
    "brand_name": "walmart",
    "locale": "en-US",
    "shipping_preference": "SET_PROVIDED_ADDRESS",
    "user_action": "SUBSCRIBE_NOW",
    "payment_method": {
      "payer_selected": "PAYPAL",
      "payee_preferred": "IMMEDIATE_PAYMENT_REQUIRED"
    },
    "return_url": "https://example.com/returnUrl",
    "cancel_url": "https://example.com/cancelUrl"
  }
}'


**参数说明：**
- `plan_id`：计划 ID
- `start_time`：订阅开始时间
- `quantity`：订阅产品数量
- `shipping_amount`：运费金额
- `application_context`：支付上下文，包括品牌名称、语言、支付偏好等

### 5. 获取订阅详情

使用以下代码获取订阅详情：

bash
curl -v -X GET https://api-m.sandbox.paypal.com/v1/billing/subscriptions/I-BW452GLLEP1G \
-H "Content-Type: application/json" \
-H "Authorization: Bearer Access-Token"


### 6. 配置 Webhook

为了实时接收订阅事件通知，您需要配置 Webhook。以下是配置步骤的截图：

![Webhook配置界面](https://bbtdd.com/img/7196596195.webp)

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)

---

通过以上步骤，您可以轻松实现 PayPal 订阅的创建和管理。无论是手动操作还是通过 API 自动化，本文均提供了详细的指导。