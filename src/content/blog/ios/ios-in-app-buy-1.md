---
author: LiQingYou
pubDatetime: 2023-10-12T00:57:52.737Z
title: iOS内购买自动续期订阅说明
featured: false
tags:
  - ios
description: 服务端验证自动续期订阅的收据过程如下：
---

服务端验证自动续期订阅的收据过程如下：

服务端收到客户端发送的收据后，向苹果服务器发送post请求

苹果测试环境 https://sandbox.itunes.apple.com/verifyReceipt，

苹果生产环境 https://buy.itunes.apple.com/verifyReceipt

请求的http body是json格式，receipt-data字段对应的就是收据数据，password字段对应苹果开发账号设置的秘钥,exclude-old-transactions字段为true时，可过滤掉大部分旧的购买，只显示新的订阅。例如：

```
{
"exclude-old-transactions":true,
"password": "***",
"receipt-data":"***"
}
```

苹果服务器返回的http body里的status字段对应的是收据状态，若为0，则收据为真。

在receipt字段里，bundle_id 是APP的固定ID，可用于检查收据是否属于我们的APP。

在latest_receipt_info数组里，遍历数据，找到最大的expires_date，这个是订阅过期的日期，也可用expires_date_ms或expires_date_pst字段。服务器应保存该字段到数据库

product_id 是商品id，要与购买的商品ID相符才有效。

web_order_line_item_id 是本次购买的ID

original_transaction_id 是第一次订阅的交易ID，服务器应保存该字段到数据库

例如

```
{

  "status": 0,

  "environment": "Sandbox",

  "receipt": {

    "receipt_type": "ProductionSandbox",

    "adam_id": 0,

    "app_item_id": 0,

    "bundle_id": "***",

    "application_version": "6",

    "download_id": 0,

    "version_external_identifier": 0,

    "receipt_creation_date": "2019-11-28 05:38:19 Etc/GMT",

    "receipt_creation_date_ms": "1574919499000",

    "receipt_creation_date_pst": "2019-11-27 21:38:19 America/Los_Angeles",

    "request_date": "2019-11-28 08:18:12 Etc/GMT",

    "request_date_ms": "1574929092579",

    "request_date_pst": "2019-11-28 00:18:12 America/Los_Angeles",

    "original_purchase_date": "2013-08-01 07:00:00 Etc/GMT",

    "original_purchase_date_ms": "1375340400000",

    "original_purchase_date_pst": "2013-08-01 00:00:00 America/Los_Angeles",

    "original_application_version": "1.0",

    "in_app": [

      ...

      {

        "quantity": "1",

        "product_id": "***",

        "transaction_id": "1000000598465716",

        "original_transaction_id": "1000000598465716",

        "purchase_date": "2019-11-28 05:38:19 Etc/GMT",

        "purchase_date_ms": "1574919499000",

        "purchase_date_pst": "2019-11-27 21:38:19 America/Los_Angeles",

        "original_purchase_date": "2019-11-28 05:38:19 Etc/GMT",

        "original_purchase_date_ms": "1574919499000",

        "original_purchase_date_pst": "2019-11-27 21:38:19 America/Los_Angeles",

        "expires_date": "2019-11-28 05:43:19 Etc/GMT",

        "expires_date_ms": "1574919799000",

        "expires_date_pst": "2019-11-27 21:43:19 America/Los_Angeles",

        "web_order_line_item_id": "1000000048591202",

        "is_trial_period": "false",

        "is_in_intro_offer_period": "false"

      }

    ]

  },

  "latest_receipt_info": [

    {

      "quantity": "1",

      "product_id": "***",

      "transaction_id": "1000000594693615",

      "original_transaction_id": "1000000594693615",

      "purchase_date": "2019-11-20 06:33:11 Etc/GMT",

      "purchase_date_ms": "1574231591000",

      "purchase_date_pst": "2019-11-19 22:33:11 America/Los_Angeles",

      "original_purchase_date": "2019-11-20 06:33:11 Etc/GMT",

      "original_purchase_date_ms": "1574231591000",

      "original_purchase_date_pst": "2019-11-19 22:33:11 America/Los_Angeles",

      "is_trial_period": "false"

    },

    {

      "quantity": "1",

      "product_id": "***",

      "transaction_id": "1000000598475362",

      "original_transaction_id": "1000000598465716",

      "purchase_date": "2019-11-28 06:03:19 Etc/GMT",

      "purchase_date_ms": "1574920999000",

      "purchase_date_pst": "2019-11-27 22:03:19 America/Los_Angeles",

      "original_purchase_date": "2019-11-28 05:38:19 Etc/GMT",

      "original_purchase_date_ms": "1574919499000",

      "original_purchase_date_pst": "2019-11-27 21:38:19 America/Los_Angeles",

      "is_trial_period": "false",

      "expires_date": "2019-11-28 06:08:19 Etc/GMT",

      "expires_date_ms": "1574921299000",

      "expires_date_pst": "2019-11-27 22:08:19 America/Los_Angeles",

      "web_order_line_item_id": "1000000048591627",

      "is_in_intro_offer_period": "false",

      "subscription_group_identifier": "20577287"

    }

  ],

  "latest_receipt": "***",

  "pending_renewal_info": [

    {

      "auto_renew_product_id": "jfldsjf",

      "original_transaction_id": "1000000598465716",

      "product_id": "jfldsjf",

      "auto_renew_status": "0",

      "expiration_intent": "1",

      "is_in_billing_retry_period": "0"

    }

  ]

}
```

收据应该保存在服务器里，当订阅到期时，再次用收据向苹果发送请求，更新用户的订阅到期时间。

在latest_receipt_info数组里，用original_transaction_id找到对应的交易，查看expires_date。若expires_date是过去的日期，表示用户没有续订。若expires_date是未来的日期，表示用户已续订。更新用户的过期日期。

latest_receipt是最新的收据，应保存到服务器数据库里，替换旧收据。

pending_renewal_info里的auto_renew_status表示是否还可能有后续的订阅，若为0表示用户已取消订阅

is_in_billing_retry_period为1表示用户支付信息有问题，苹果正在尝试重新扣款（最长持续60天），为0表示苹果停止重新扣款。

对于用户退款、更改订阅的行为的检查，服务器可与苹果建立服务端的连接，获取用户的订阅状态变化通知。开发账户中要设置服务器的URL。

> 作者：1150b290110c
>> 链接：https://www.jianshu.com/p/ebfeff00915d
>> 来源：简书
>>> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

