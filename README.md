## **易支付免签约支付产品接口（API）官方源码**

### **一站式接入支付宝,微信,财付通,QQ钱包,微信wap,帮助开发者/网站快速集成到自己相应产品，费率低。彩虹易支付,微极速支付,聚合支付API**。方便一键搭建支付系统彩虹易支付系统



> 传输方式：HTTP / POST
>
> 数据格式：JSON
>
> 签名算法：MD5 或者 SHA256
>
> 域名: **找运维人员要最新地址**
>
> 平台建议采用 V3.0 接口对接
>
> 如果是易支付对接就需要修改下单地址, 商户号, 以及密钥; 由于每个产品兼容易支付不同, 部分平台需要在下单地址后面拼接 submit.php 部分不需要, 请自行测试
>
>      

代收下单

提交地址:域名/api/in/createOrder

请求方式: POST/JSON

| 字段名     | 变量名          | 必填 | 类型   | 示例值                                     | 描述                                                         |
| :--------- | :-------------- | :--- | :----- | :----------------------------------------- | :----------------------------------------------------------- |
| 商户号     | appId           | 是   | String | 1634227918723108865                        | 平台分配的唯一ID                                             |
| 商户订单号 | merchantOrderNo | 是   | String | 1634227918723108899                        | 商户订单号                                                   |
| 订单金额   | amount          | 是   | String | 10.00                                      | 订单金额                                                     |
| 支付类型   | type            | 是   | String | **alipay** \| **wechat**                   | 支付类型                                                     |
| 异步地址   | notifyUrl       | 是   | String | https://api.xxx.com/api/local/callback | 服务器异步通知地址; 异步地址不要有参数                       |
| 同步地址   | returnUrl       | 否   | String | https://xxx.com                        | 页面跳转通知地址;                                            |
| 设备       | device          | 是   | String | pc \| wap(移动, H5)                        | 设备                                                         |
| 签名值     | sign            | 是   | String | 202cb962ac59075b964b07152d234b70           | 根据签名算法得到的值                                         |
| 签名方式   | signType        | 是   | String | MD5\|SHA256                                | 根据自己需求选择一种签名方式,平台的下单和回调都会采用此签名方式 |
| 版本       | version         | 是   | String | 3.0                                        | 固定值 3.0                                                   |
| 币种       | currency        | 是   | String | CNY                                        | CNY, INR…                                                    |
| 客户端IP   | clientIp        | 是   | String | 189.433.643.67                             | 客户端IP; 没有填写个假的                                     |
| 商品标题   | subject         | 否   | String | VIP会员                                    | 商品标题; 没有随便构造一个                                   |
| 额外参数   | body            | 否   | String | 1634227918723108865                        | 异步通知原路返回                                             |

> 下单返回数据结构

```
{
   "status": 200,
   "message": "success",
   "data": {
	  "appId": "1655087813533728768",
	  "merchantOrderNo": "799c7cb4996f48a6b38e5e6c8bc9b662",
	  "tradeNo": "1658067530754912258",
	  "amount": "100",
	  "createStatus": "1",
	  "payUrl": "http://82.71.136.45/proxy/redirect/goto/weibao/18f2256a8c744a5a9a99bf22ac1da76e",
	  "body": "6c91673723b647b2afa32625954b6255"
   }
}
```

> 当 status 值不是 200 时, message 内容就是错误信息; 当status 是 200 时, data 参数参照下表

| 参数            | 说明                                              |
| :-------------- | :------------------------------------------------ |
| appId           | 商户号                                            |
| merchantOrderNo | 商户订单号                                        |
| tradeNo         | 平台订单号                                        |
| amount          | 订单金额                                          |
| createStatus    | 订单创建状态; 0: 处理中; 1: 创建成功; 2: 创建失败 |
| payUrl          | 收银台地址                                        |
| body            | 额外参数                                          |

代收回调

##### 所有回调统一采用POST application/json 方式请求

###### 回调参数

| 参数            | 说明                                                         |
| :-------------- | :----------------------------------------------------------- |
| appId           | 商户号                                                       |
| merchantOrderNo | 商户订单号                                                   |
| tradeNo         | 平台订单号                                                   |
| amount          | 订单金额                                                     |
| payAmount       | 支付金额                                                     |
| payStatus       | 支付状态;1: 未支付; 2: 支付成功; 3: 退款成功; 4: 支付金额错误; |
| body            | 额外参数                                                     |
| signType        | 签名类型; 下单是填写的类型                                   |
| sign            | 签名值; 根据下单时添加的签名类型得到的值                     |

注意

收到回调后, 验证通过后请返回字符串: success

当用订单金额是100, 如果用户支付金额不是100时, 其他验证通过也请返回 success, 否则平台依然是当回调失败处理

回调失败平台会通知5次, 每次间隔10秒

> 回调校验大致流程

1. 校验订单号是不是自己平台的
2. 校验签名是否正确
3. 校验支付金额是否和下单金额一致
4. 根据支付金额完成平台自己的逻辑

当1和2校验通过后就应当返回 **success**

> 回调请求数据格式

```
{
   "amount": "98",
   "payAmount": "98",
   "tradeNo": "1698359690916380673",
   "appId": "1698206619242434561",
   "sign": "E2B9C0DDEDC97AFD4AAB2190F76FD78B",
   "signType": "MD5",
   "body": "1",
   "merchantOrderNo": "SN3069540284",
   "payStatus": "2"
}
```

代收订单查询

提交地址:域名/api/in/query

请求方式: POST/JSON

##### 请求参数

| 字段名     | 变量名          | 必填 | 类型   | 示例值                           | 描述                |
| :--------- | :-------------- | :--- | :----- | :------------------------------- | :------------------ |
| 商户号     | appId           | 是   | String | 1655087813533728768              | 平台分配的唯一ID    |
| 商户订单号 | merchantOrderNo | 是   | String | a450af7c001d4aa9935b81           | 商户唯一订单号      |
| 时间戳     | timestamp       | 是   | String | 684151945372                     | 3位时间戳; 毫秒级别 |
| 版本       | version         | 是   | String | 3.0                              | 固定值 3.0          |
| 签名类型   | signType        | 是   | String | MD5 或 SHA256                    | 签名方式            |
| 签名值     | sign            | 是   | String | a450af7c001d4aa9935b81c2f4a98fed | 签名结果            |

> 查询响应结果

```
{
   "status": 200,
   "message": "success",
   "data": {
      "amount": "100.00",
      "payAmount": "0",
      "tradeNo": "1658076569052213250",
      "payTime": null,
      "createTime": "1684151234298",
      "appId": "1655087813533728768",
      "outTradeNo": "a450af7c001d4aa9b42b81c2f4a98fed",
      "sign": "886ec49a1252ad3008ec8b737a80f93014244983eace4ed8c758c3da8a60778d",
      "signType": "SHA256",
      "payStatus": "1"
   }
}
```

> 当 status 值 为 200 时, data 内容参考下表; 如果不是 200, message 则为错误信息;

| 参数       | 参数说明                                               |
| :--------- | :----------------------------------------------------- |
| appId      | 商户号                                                 |
| outTradeNo | 商户订单号                                             |
| tradeNo    | 平台订单号                                             |
| amount     | 订单金额                                               |
| payAmount  | 支付金额                                               |
| payStatus  | 支付状态; 1: 未支付; 2: 支付成功; 4: 支付金额错误      |
| payTime    | 支付时间;13位时间戳, 未支付是 null                     |
| createTime | 订单创建时间; 13位时间戳                               |
| signType   | 签名方式                                               |
| sign       | 签名结果; 根据提交的签名类型得到的值; 具体参考签名方法 |

商户余额查询

提交地址:域名/query/balance

请求方式: POST/JSON

##### 请求参数

| 字段名   | 变量名    | 必填 | 类型   | 示例值                           | 描述                 |
| :------- | :-------- | :--- | :----- | :------------------------------- | :------------------- |
| 商户号   | appId     | 是   | String | 1655087813533728768              | 平台分配的唯一ID     |
| 时间戳   | timestamp | 是   | String | 684151945372                     | 13位时间戳; 毫秒级别 |
| 版本     | version   | 是   | String | 3.0                              | 固定值 3.0           |
| 签名类型 | signType  | 是   | String | MD5 或 SHA256                    | 签名方式             |
| 签名值   | sign      | 是   | String | a450af7c001d4aa9935b81c2f4a98fed | 签名结果             |

> 查询响应结果

```
{
  "status": 200,
  "message": "success",
    "data": {
      "balance": "1476.69",
      "appId": "1655087813563088897",
      "freezeMoney": "100.00"
  }
}
```

> 当 status 值 为 200 时, data 内容参考下表; 如果不是 200, message 则为错误信息;

| 参数        | 参数说明               |
| :---------- | :--------------------- |
| appId       | 商户号                 |
| balance     | 当前可用余额           |
| freezeMoney | 冻结余额(代付中的金额) |

错误对照码表

| 错误编码 | 描述                             |
| :------- | :------------------------------- |
| 200      | 正确码                           |
| -9999    | 所有错误码;错误消息请查看message |

签名方式介绍

> 平台自定义接口 3.0 下单签名方式

1. 1. 将发送或接收到的所有参数按照参数名按照ASCII码从小到大排序（a-z）空值和sign不参与签名.
2. 2. 将排序后的参数拼接成URL键值对的格式; 如: k1=v1&k2=v2&k3=v3..... 得到待签名字符串 strA.
3. 3. 将签名字符串 strA 后面拼接 &key=密钥得到待签名字符串 strB.
4. 4. 将待签名字符串 strB 进行合理加密 (MD5 或 SHA256) 就得到签名值 sign.
5. 5. 平台目前只支持MD5和SHA256两种签名方式, 具体用那种用户自行选择;平台的回调也会依据用户选择的签名方式进行签名.
6. 6. U R L 不 要 编 码 



> 易支付签名方式

MD5签名算法

1、将发送或接收到的所有参数按照参数名ASCII码从小到大排序（a-z），sign、sign_type、和空值不参与签名！

2、将排序后的参数拼接成URL键值对的格式，例如 a=b&c=d&e=f，参数值 不 要 进 行 u r l 编 码。

3、再将拼接好的字符串与商户密钥KEY进行MD5加密得出sign签名参数，sign = md5 (a=b&c=d&e=f + 商户密钥) （注意：+ 为各语言的拼接符，不是字符！），md5结果为小写。

4、具体签名与发起支付的示例代码可下载SDK查看。

> submit.php 下单

提交地址:域名/submit.php

请求方式: POST/GET 建议采用 POST/JSON 请求

| 字段名       | 变量名       | 必填 | 类型   | 示例值                            | 描述                      |
| :----------- | :----------- | :--- | :----- | :-------------------------------- | :------------------------ |
| 商户ID       | pid          | 是   | Int    | 1001                              |                           |
| 支付方式     | type         | 否   | String | alipay                            | wechat \| alipay          |
| 商户订单号   | out_trade_no | 是   | String | 20160806151343349                 |                           |
| 异步通知地址 | notify_url   | 是   | String | http://www.pay.com/notify_url.php | 服务器异步通知地址        |
| 跳转通知地址 | return_url   | 是   | String | http://www.pay.com/return_url.php | 页面跳转通知地址          |
| 商品名称     | name         | 是   | String | VIP会员                           | 如超过127个字节会自动截取 |
| 商品金额     | money        | 是   | String | 1.00                              | 单位：元，最大2位小数     |
| 业务扩展参数 | param        | 否   | String | 没有请留空                        | 支付后原样返回            |
| 签名字符串   | sign         | 是   | String | 202cb962ac59075b964b07152d234b70  | 签名算法                  |
| 签名类型     | sign_type    | 是   | String | MD5                               | 默认为MD5                 |

> mapi.php 下单

提交地址:域名/mapi.php

请求方式: POST|GET / JSON; 建议采用 POST/JSON

| 字段名       | 变量名       | 必填 | 类型   | 示例值                            | 描述                                                         |
| :----------- | :----------- | :--- | :----- | :-------------------------------- | :----------------------------------------------------------- |
| 商户ID       | pid          | 是   | Int    | 1001                              |                                                              |
| 支付方式     | type         | 是   | String | wechat \| alipay                  | 支付方式                                                     |
| 商户订单号   | out_trade_no | 是   | String | 20160806151343349                 |                                                              |
| 异步通知地址 | notify_url   | 是   | String | http://www.pay.com/notify_url.php | 服务器异步通知地址                                           |
| 跳转通知地址 | return_url   | 否   | String | http://www.pay.com/return_url.php | 页面跳转通知地址                                             |
| 商品名称     | name         | 是   | String | VIP会员                           | 如超过127个字节会自动截取                                    |
| 商品金额     | money        | 是   | String | 1.00                              | 单位：元，最大2位小数                                        |
| 用户IP地址   | clientip     | 是   | String | 192.168.1.100                     | 用户发起支付的IP地址                                         |
| 设备类型     | device       | 否   | String | pc                                | 根据当前用户浏览器的UA判断， 传入用户所使用的浏览器 或设备类型，默认为pc [设备类型列表](https://doc.xxx.com/doc#pay5) |
| 业务扩展参数 | param        | 否   | String | 没有请留空                        | 支付后原样返回                                               |
| 签名字符串   | sign         | 是   | String | 202cb962ac59075b964b07152d234b70  | 签名算法[点此查看](https://doc.xxx.com/doc#pay3)            |
| 签名类型     | sign_type    | 是   | String | MD5                               | 默认为MD5                                                    |

> mapi.php 是接口模式返回支付地址, 需要平台自行跳转, 建议采用 submit.php 下单, 这样简化流程

> 返回结果(JSON格式)

| 字段名        | 变量名    | 类型   | 示例值                               | 描述                                                    |
| :------------ | :-------- | :----- | :----------------------------------- | :------------------------------------------------------ |
| 返回状态码    | code      | Int    | 1                                    | 1为成功，其它值为失败                                   |
| 返回信息      | msg       | String |                                      | 失败时返回原因                                          |
| 订单号        | trade_no  | String | 20160806151343349                    | 支付订单号                                              |
| 支付跳转url   | payurl    | String | https://pay.com/pay/wxpay/202010903/ | 如果返回该字段，则直接跳转到该url支付                   |
| 二维码链接    | qrcode    | String | weixin://wxpay/bizpayurl?pr=04IPMKM  | 如果返回该字段，则根据该url生成二维码                   |
| 小程序跳转url | urlscheme | String | weixin://dl/business/?ticket=xxx     | 如果返回该字段，则使用js跳转该url，可发起微信小程序支付 |

> api.php 查单

提交地址:域名/api.php

请求方式: 建议POST/JSON

| 字段名     | 变量名       | 必填 | 类型   | 示例值                           | 描述             |
| :--------- | :----------- | :--- | :----- | :------------------------------- | :--------------- |
| 操作类型   | act          | 是   | order  | 固定值(order)                    | 操作类型         |
| 商户号     | pid          | 是   | String | 10000                            | 平台分配的商户号 |
| 商户密钥   | key          | 是   | String | 89unJUB8HZ54Hj7x4nUj56HN4nUzUJ8i | 商户密钥         |
| 系统订单号 | trade_no     | 是   | String | 20160806151343349                | 平台订单号       |
| 商户订单号 | out_trade_no | 是   | String | 20160806151343349                | 商户订单号       |

> 返回结果

| 字段名       | 变量名       | 类型   | 示例值              | 描述                     |
| :----------- | :----------- | :----- | :------------------ | :----------------------- |
| 返回状态码   | code         | Int    | 1                   | 1为成功，其它值为失败    |
| 返回信息     | msg          | String | 查询订单号成功！    |                          |
| 易支付订单号 | trade_no     | String | 2016080622555342651 | 平台订单号               |
| 商户订单号   | out_trade_no | String | 20160806151343349   | 商户系统内部的订单号     |
| 第三方订单号 | api_trade_no | String | 20160806151343349   | 支付宝微信等接口方订单号 |
| 支付方式     | type         | String | alipay              | wechat \| alipay         |
| 商户ID       | pid          | Int    | 1001                | 发起支付的商户ID         |
| 创建订单时间 | addtime      | String | 2016-08-06 22:55:52 |                          |
| 完成交易时间 | endtime      | String | 2016-08-06 22:55:52 |                          |
| 商品名称     | name         | String | VIP会员             |                          |
| 商品金额     | money        | String | 1.00                |                          |
| 支付状态     | status       | Int    | 0                   | 1为支付成功，0为未支付   |
| 业务扩展参数 | param        | String |                     | 默认留空                 |
| 支付者账号   | buyer        | String |                     | 默认留空                 |

> 易支付回调请求参数

通知类型：服务器异步通知（notify_url）

请求方式: GET

| 字段名       | 变量名       | 必填 | 类型   | 示例值                           | 描述                    |
| :----------- | :----------- | :--- | :----- | :------------------------------- | :---------------------- |
| 商户ID       | pid          | 是   | Int    | 1001                             |                         |
| 易支付订单号 | trade_no     | 是   | String | 20160806151343349021             | Futoon Pay订单号        |
| 商户订单号   | out_trade_no | 是   | String | 20160806151343349                | 商户系统内部的订单号    |
| 支付方式     | type         | 是   | String | alipay                           | wechat \| alipay        |
| 商品名称     | name         | 是   | String | VIP会员                          |                         |
| 商品金额     | money        | 是   | String | 1.00                             |                         |
| 支付状态     | trade_status | 是   | String | TRADE_SUCCESS                    | 只有TRADE_SUCCESS是成功 |
| 业务扩展参数 | param        | 否   | String |                                  |                         |
| 签名字符串   | sign         | 是   | String | 202cb962ac59075b964b07152d234b70 | 签名算法MD5             |
| 签名类型     | sign_type    | 是   | String | MD5                              | 默认为MD5               |



请联系客服人员索取最新的接口地址：

Telegram: [@jingge_v](https://t.me/jingge_v)

