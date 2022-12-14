# BiYong商户平台接口文档

返回值统一格式：

    {
      "status":"0",                 // 0 代表接口请求成功
      "message":"",                 // 错误信息，请求成功无此字段
      "timestamp":"1525616709780",  // 时间戳
      "data": {}                    // JSON格式业务数据，每个接口返回数据格式请参考以下文档
    }

部分错误码：

|`status`|`描述`|
|---|---|
|100000|通用业务错误，具体信息参考 message|
|100100|调用异常|
|100101|商户无权访问此接口|
|100102|用户未授权商户访问此数据|
|100400|请求时间戳与服务器误差超过限制|
|100401|验签失败|

## 1. BiYong用户相关接口

### 1. 用户授权

> /biyong-user/auth

请求参数:

    {
      "authToken":"EuQkJ3mx4kinvHLFR3DPQ15SjVyh9" // 必填 授权临时biytoken
    }

返回data:

    {
      "authList": [   // 用户授予的权限列表
        "PUBLIC_INFO",// 查看公开信息(昵称、头像等)权限
        "PHONE",      // 查看手机号权限
        "BALANCE",    // 查看资金流水权限
        "ADDRESS",    // 查看查看用户充值地址权限
      ],
      "openId":"9bd0fda7d0dad3411fed9c488bd4b8a7",
      "userInfo" : {
        // 与 /biyong-user/info 接口返回 data 相同
      }
    }

### 2. 上传用户token

> /biyong-user/update

请求参数:

    {
      "openId":"9bd0fda7d0dad3411fed9c488bd4b8a7", // 必填 用户openId
      // 以下参数不能全为空
      "token":"00000000000000000000000000000000"  // 商户生成token(必须为1~64位字母、数字、下划线的组合)，可用于保持用户在商户页面登录态
    }

返回data: null

### 3. 用户基本信息

> /biyong-user/info

请求参数:

    {
      "openId":"387f6d41e60449cb84c8587b1d6d4104" // 必填 用户openId
    }

返回data:

    {
      "auth":"true",              // true/false 是否有查看此用户信息权限
      "pubInfoAuth":"true",       // true/false 是否有查看用户公开信息权限
      "firstName":"John",         // 如果 pubInfoAuth 为 false，此字段不返回
      "lastName":"Due",           // 如果 pubInfoAuth 为 false，此字段不返回
      "selfieUrl":"http://..."    // 用户头像链接，如果 pubInfoAuth 为 false 或用户无头像，此字段不返回
      "isKycPass":"true",         // 如果 pubInfoAuth 为 false，此字段不返回，true/false 用户是否进行了实名认证
      "phoneAuth":"true",         // true/false 是否有查看用户手机号
      "phone":"86-13300000000",   // 如果 phoneAuth 为 false，此字段不返回
    }

### 4. 获取用户资金流水

> /biyong-user/balance/page

请求参数:

    {
      "openId":"387f6d41e60449cb84c8587b1d6d4104", // 必填 用户openId
      "pageNo":"0",                                // 必填 页码，从0计数
      "pageSize":"100",                            // 必填 每页条数，最大1000
      "startTime":"1525335516797",                 // 可选 数据起始时间
      "endTime":"1525335516797"                    // 可选 数据截止时间
    }

返回data:

    {
      "total":1200, // 总条数
      "records" : [
        {
          "tokenSymbol":"ACT",   // 代币BiYong内部代码
          "balance":10.32,       // 资产变更数量，负数代表资产扣减
          "time":1525335516797   // 资产变更时间
        },
        ...(more records)...
      ]
    }

### 5. 获取用户充值地址

> /biyong-user/address

请求参数:

    {
      "openId":"387f6d41e60449cb84c8587b1d6d4104", // 必填 用户openId
      "coinName":"GRAM",                           // 必填 代币BiYong内部代码（内部代码不会更改，显示名称可能更改）
    }

返回data:

    {
      "address" : "0xabcdef......",               // 地址
      "chainName" : "ethereum",                   // 主链类型
      "coinDisplayName" : "ETH",                  // 代币BiYong显示名称
      "projectName": "Ethereum",                  // 项目名称
      "isDepositOpen": true/false                 // 是否开放转入
    }

### 6. 获取用户资产余额

> /biyong-user/balance/coin

请求参数:

    {
      "openId":"387f6d41e60449cb84c8587b1d6d4104", // 必填 用户openId
      "coinName":"GRAM",                           // 必填 代币BiYong内部代码（内部代码不会更改，显示名称可能更改）
    }

返回data:

    {
      "coinName" : "ETH",
      "balance": 10.0001,
    }

### 7. 获取用户资产余额列表

> /biyong-user/balance/coin/list

请求参数:

    {
      "openId":"387f6d41e60449cb84c8587b1d6d4104", // 必填 用户openId
    }

返回data:

    [
        {
          "coinName" : "ETH",
          "balance": 10.00001,
        },
        {
          "coinName" : "USDT",
          "balance": 10.00001,
        }
    ]

## 2. 其它接口

### 1. 通信测试接口

> /common/test

请求参数:

    {
      "message":"Hello, BiYong"
    }

返回data:

    {
      "message":"Hello, Merchant"
    }

### 2. BiYong支持的代币列表查询

> /common/token-info/page/v2 (统一字段名)

请求参数:

    {
      "pageNo":"0",                                // 必填 页码，从0计数
      "pageSize":"100",                            // 必填 每页条数，最大1000
    }

返回data:

    {
      "total":123, // 总条数
      "records" : [
        {
          "coinName":"ETH",          // 代币BiYong内部代码（内部代码不会更改，显示名称可能更改）
          "coinDisplayName":"ETH",   // 代币显示名称
          "chainName":"ethereum",    // 主链名称
          "projectName":"Ethereum",  // 代币项目名称
        },
        ...(more records)...
      ]
    }

> /common/token-info/page (第一版旧接口)

请求参数:

    {
      "pageNo":"0",                                // 必填 页码，从0计数
      "pageSize":"100",                            // 必填 每页条数，最大1000
    }

返回data:

    {
      "total":123, // 总条数
      "records" : [
        {
          "symbol":"ACT",          // 代币BiYong内部代码（内部代码不会更改，显示名称可能更改）
          "name":"ACT",            // 代币显示名称
          "projectName":"Achain",  // 代币项目名称
        },
        ...(more records)...
      ]
    }

## 3. BiYong支付相关接口

### 1. 支付信息查询

> /b-pay/config/query

请求参数:

    {}

返回data:

    {
      // 允许支付的币种及其相关限制
      "coinConfigs" : [ 
        {
          "coinName" : "GRAM",          // 币种代码，创建订单时使用此参数选择指定Token
          "coinDisplayName" : "GRAM",   // 币种显示名称
          "dayLimit" : 100000,          // 每日支付限额
          "feeRate" : 0.001,            // 每笔费率
          "perMin" : 0.01               // 单笔最低支付数量
          "perMax" : 100                // 单笔最高支付数量
        },
        ...
      ],
      "maxCoinNum" : 9,                 // 每次支付最多允许选择Token数量
      
      "settleDays" : 15,                // 结算周期(天)
      
      // 回调配置
      "callback" : { 
        "callbackUrl" : "https://a.b.c", // 回调地址
        "rsaSignHashMode" : "SHA256",    // RSA签名Hash算法
        "aesMode" : "CBC/PKCS5Padding    // AES加密模式
      }
    }

### 2. 创建 BiYong 支付订单

> /b-pay/order/create

请求参数:

    {
      "outOrderCode":"ORDER123456", // 必填 外部订单号 10~32位A-Z或数字组成
      "orderName":"Some Item",      // 必填 订单名称
      "multiPrice": [               // 必填 定价
        {
          "coinName" : "GRAM", // 币种代码
          "balance" : 10.5,    // 币种数量
        },
        {
          "coinName" : "ETH",
          "balance" : 0.01,
        },
        ...
      ],
      "remark" : "some remark",      // 可选 订单备注，长度128以内
      "expireSec" : 120,             // 可选 订单超时时间，可选范围60~7200(1m~2h)，如果不填默认300(5m)
    }

返回data:

    {
      "payToken" : "DzyBU...",         // 订单token，biyong://biypay? 协议传输此参数拉起支付
      "outOrderCode" : "ORDER123456",  // 传入的外部订单号
      "orderCode" : "64108...",        // BiYong内部订单号
      "createTime" : 1547454102000,    // 订单创建时间
      "expireTime" : 1547464102000,    // 订单超时时间
    }

### 3. 查询单笔 BiYong 支付订单

> /b-pay/order/query

请求参数:

    {
      "outOrderCode" : "ORDER123456",  // 传入的外部订单号
      "orderCode" : "64108...",        // BiYong内部订单号
      // 从以上2个参数中选择1个进行查询
    }

返回data:

    {
      "orderName" : "Some Item",       // 订单名称
      "outOrderCode" : "ORDER123456",  // 传入的外部订单号
      "orderCode" : "64108...",        // BiYong内部订单号
      "multiPrice" : [                 // 创建订单时设置的支付币种信息
        {
          "coinName" : "GRAM", // 币种代码
          "balance" : 10.5,    // 币种数量
        },
        {
          "coinName" : "ETH",
          "balance" : 0.01,
        },
        ...
      ],
      "remark" : "some remark",        // 订单备注
      "expireTime" : 1547464102000,    // 订单超时时间
      "createTime" : 1547454102000,    // 订单创建时间
      "updateTime" : 1547494102000,    // 订单最后更新时间
      "status" : "SETTLED",            // status含义参考下表
      
      // 以下信息在用户支付成功后会出现 (status: USER_PAY_SUCCESS)
      "coinName" : "GRAM",             // 用户支付币种
      "balance" : 10.5,                // 用户支付数量
      "fee" : "0.0525",                // 扣除手续费
      "feeRate" : "0.005",             // 手续费费率
      "payTime" : 1547455102000,       // 用户支付时间
      "expireTime" : 1547464102000,    // 订单超时时间
      
      // 以下信息在订单结算后会出现 (status: SETTLED)
      "settleTime" : 1547494102000,    // 订单结算时间
    }

|`status`|`描述`|
|---|---|
|DEFAULT|新订单|
|USER_PAYING|用户支付中|
|USER_PAY_SUCCESS|用户已成功支付|
|EXPIRE|无用户支付，订单超时|
|MERCHANT_CLOSE|商户主动关闭订单，只有DEFAULT状态订单才可以关闭|
|REFUNDING|退款中，USER_PAY_SUCCESS状态订单可以发起退款|
|REFUND_SUCCESS|退款成功|
|SETTLING|结算中，USER_PAY_SUCCESS支付成功状态的订单在满足结算周期后会进行结算|
|SETTLED|结算完毕|
|SETTLED_REFUNDING|退款中(已结算订单发起退款)|
|SETTLED_REFUND_SUCCESS|退款成功(已结算订单)|

### 4. BiYong 支付订单关闭

> /b-pay/order/close

请求参数:

    {
      "outOrderCode" : "ORDER123456",  // 外部订单号
      "orderCode" : "64108...",        // BiYong内部订单号
      // 从以上2个参数中选择1个进行操作
    }

返回data: 与 /b-pay/order/query 接口相同

注意，只有 DEFAULT 待支付状态的订单才可以被主动关闭

### 5. BiYong 支付订单退款

> /b-pay/order/refund

请求参数:

    {
      "outOrderCode" : "ORDER123456",  // 外部订单号
      "orderCode" : "64108...",        // BiYong内部订单号
      // 从以上2个参数中选择1个进行退款
    }

返回data: 与 /b-pay/order/query 接口相同

注意，只有 USER_PAY_SUCCESS 已支付 和 SETTLED 已结算状态的订单才可以退款；
结算完毕后的订单进行退款，将由商户自行承担该笔订单的手续费

### 6. 批量查询 BiYong 支付订单

> /b-pay/order/list

请求参数:

    {
      "startTime" : 1547454102000,  // 必填 开始时间
      "endTime"   : 1547454102000,  // 必填 结束时间
      "pageNo"    : 0,              // 必填 页码
      "pageSize"  : 100,            // 必填 每页数据，最大为1000条
    }

返回data:

    {
      "list" : [
        { ... }, // 每条订单数据与 /b-pay/order/query 接口 data 相同
        { ... }, //
        ...
      ]
    }

### 7. 设置回调信息

> /b-pay/callback/set

请求参数:

    {
      // 必填 回调地址
      "callbackUrl" : "http://www.xxxx.com/b-pay/callback",
      // 可选 选择RSA签名算法，不填默认使用SHA256
      "rsaSignHashMode": "SHA256",
      // 可选 选择AES加密模式
      //     CFB|CTR|OFB|PCBC|CBC
      //     PKCS5Padding|ISO10126Padding
      // 不填此项则回调不使用AES进行加密
      "aesMode": "CBC/PKCS5Padding",
    }

返回data: null

`注意` 设置回调信息时，会立刻调用此接口。
url中添加参数 test=true（如 http://www.xxxx.com/b-pay/callback?test=true），
验证接口是否能正常访问。返回 http status: 200 则BiYong服务器认为接口可用

### 7.1 支付成功回调

如果设置了回调信息，在用户支付成功后会进行推送通知

> 请求方式 Http POST

> 请求 header

|key|value|备注|
|---|---|---|
|RsaSignHashMode|SHA256|RSA签名算法
|AesEncryptMode|CBC/PKCS5Padding|AES加密模式，设置时为null则无此header

> RequestBody JSON:

    {
      // 与 /b-pay/order/query 接口相同
    }

回调数据使用通信统一加密模式，参考 [BiYong商户平台通信规范](https://github.com/openbiyong/biyong-developer/blob/master/BiYong商户平台通信规范.md)

不同的是请求是由币用后台发起，使用的加密私钥是`BiYongRSA私钥`，商户需在服务端用`BiYongRSA公钥`进行验签；如果选择使用AES加密模式，用于加密AES密钥的公钥则是`商户RSA公钥`，商户需要使用自己的私钥进行解密

可以直接使用 SDK 中的 Utils.MessageCipher#serverDecrypt 方法对进行解密及验签

> 回调接口返回 http status: 200 视为通知成功

### 企业付款-创建订单

> /merchant-pay/order/pay

请求参数:

    {
      "sourceOrderCode":"ORDER123456", // 必填 外部订单号 10~32位A-Z或数字组成
      "coinName" : "ETH",
      "amount" : 0.01,
      "openId":"9bd0fda7d0dad3411fed9c488bd4b8a7",//用户id
      "remark" : "some remark",      // 可选 订单备注，长度40以内
    }

返回data:

    {
        "sourceOrderCode": "B1234567890",// 传入的外部订单号
        "outOrderCode": "131275350067565576192",// 内部订单号【商户付款】
        "inOrderCode": "231275350067565576193",// 内部订单号【用户收款】
        "status": "SUCCESS",//订单状态
        "outStatus": "SUCCESS",//内部订单【商户付款】状态
        "inStatus": "SUCCESS",//内部订单【用户收款】状态
        "coinName": "GRAM",
        "amount": 1,
        "fee": 0,
        "remark": "奖励一个GRAM",
        "createTime": 1592902126000,// 订单创建时间
        "updateTime": 1592902126000// 订单更新时间
    }

outOrderCode，inOrderCode，可以只记录一个，用于后续与biyong核对订单。
调用付款接口后，需要关注状态字段"status"，如果不为SUCCESS，则需要使用相同参数，相同订单号，继续调用pay接口，直到成功

status，outStatus，inStatus定义如下

|`status`|`描述`|
|---|---|
|PROCESSING|进行中|
|SUCCESS|成功|

### 企业付款-查询订单

> /merchant-pay/order/query

请求参数:

    {
      "sourceOrderCode":"ORDER123456", // 必填 外部订单号 10~32位A-Z或数字组成
    }

返回data:

    {
        "sourceOrderCode": "B1234567890",// 传入的外部订单号
        "outOrderCode": "131275350067565576192",// 内部订单号【商户付款】
        "inOrderCode": "231275350067565576193",// 内部订单号【用户收款】
        "status": "SUCCESS",//订单状态
        "outStatus": "SUCCESS",//内部订单【商户付款】状态
        "inStatus": "SUCCESS",//内部订单【用户收款】状态
        "coinName": "GRAM",
        "amount": 1,
        "fee": 0,
        "remark": "奖励一个GRAM",
        "createTime": 1592902126000,// 订单创建时间
        "updateTime": 1592902126000// 订单更新时间
    }

字段含义跟上面的接口一致。

## 4. 策略助手相关

### 1. 获取可配置币种

> /strategy-assistant/coin/get-all

请求参数:

    {}

返回data:

    [
        {
            "coinName": "SHIB",// 币种名称(不会更改)
            "contractAddress": "0x95ad61b0a150d79219dcf64e1e6cc01f0b64c4ce" // 合约地址
            "chain": "ethereum" // 链名(ethereum,bsc)
            "coinFullName": "SHIB",// 币种全称
            "contractDecimal": 18
            "coinIconUrl": "https://bna-pic.oss-cn-beijing.aliyuncs.com/ico/S20221017184546.png" //币种logo
        },
        {
            "coinName": "DOGE",// 币种名称(不会更改)
            "contractAddress": "0x95ad61b0a150d79219dcf64e1e6cc01f0b64c4ce" // 合约地址
            "chain": "bsc" // 链名(ethereum,bsc)
            "coinFullName": "ADOGE",// 币种全称
            "contractDecimal": 18
            "coinIconUrl": "https://bna-pic.oss-cn-beijing.aliyuncs.com/ico/S20221017184546.png" //币种logo
        }
    ]

### 2. 用户添加或者取消关注币种

> /strategy-assistant/user/change-coin

请求参数:

    {
        "openId": "aas123sdqwe",// 用户openId
        "coinName": "SHIB",// 币种名称
        "operationType": 0 // 操作类型（0:取消关注，1:添加关注）
    }

返回data:

    {
        "status":"SUCCESS", //此次操作处理状态,SUCCESS:成功，FAILURE：失败
        "coinNames":["SHIB","BNB","ETH"] //此次操作后当前关注的币种id
    }

### 3. 消息通知

> /strategy-assistant/coin/message

请求参数:

    {
        "messageId": "asd1292484124",// 消息唯一标识，做幂等性
        "code": "BUY_STOCK",// 预警信息code
        "coinName": "SHIB",// 币种名称
        "timestamp": 1659940250559 // 消息创建时间,时间戳，毫秒级
    }

返回data:

    {
        "status":"SUCCESS" //此次操作处理状态,SUCCESS:成功，FAILURE：失败
    }
