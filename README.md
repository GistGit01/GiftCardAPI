
# GoldLink GiftCard API

Base URL:

* <a href="https://www.realglpay.com">`https://www.realglpay.com`</a>

# 1. 请求约束

接口请求均通过Json格式发送。

*HTTP Head：*

| Key | Value |
| --- | --- |
| Content-Type | application/json |


# 2. 签名认证

API中的所有接口均需要签名。
签名方式如下：
<Steps>
  <Step title="准备变量">
    参与签名的变量：
 
| 变量名 | 类型 | 说明 |
| --- | --- | --- |
|  clientId  | String | 从管理员处获取 |
| secretKey | String | 从管理员处获取 |
| nonce | Integer | 10000000-99999999的随机数 |
| timestamp | Long | 时间戳（毫秒） |
  </Step>
  <Step title="拼接字符串">
    将以上变量按照 clientId,nonce,timestamp,secretKey的顺序拼接成字符串，之后字符串转为大写。
  </Step>
  <Step title="签名">
    用SHA256算法将上一步得到的字符串进行加密，得到签名字符串变量sign。
  </Step>
</Steps>

*签名示例：*
```javascript
let clientId = '18531674913121264';
let secretKey = '90e9296e3312cfd123481cea612378d';
let nonce = 56464561;
let timestamp = new Date().getTime();
let originString = `${clientId}${nonce}${timestamp}${secretKey}`.toUpperCase();
let sign = CryptoJS.SHA256(originString).toString();
```

# 3. 接口

> ## 3.1. 请求支付


| `POST` | <a href="https://www.realglpay.com/api/v1/pay">https://www.realglpay.com/api/v1/pay</a> |
| --- | --- |

### 请求参数

|名称|类型|必选|说明|
|---|---|---|---|
| sign|string| 是 |签名字符串|
| clientId|string| 是 |API客户端ID|
| timestamp|string| 是 | 时间戳 |
| nonce|string| 是 | 随机字符串 |
| hxCode|string| 是 | 核销码 |
| merchantOrderId|string| 是 | 商户订单号，请保证唯一 |

> Body 请求参数示例

```json
{
  "sign": "185370108007258521656464561173079309111990E9296E37434CCD82E13AFA03F93224",
  "clientId": "18531674913121264",
  "timestamp": "1730793091119",
  "nonce": "56464561",
  "hxCode": "ZeuFcIx",
  "merchantOrderId": "ORD2411014901CRD"
}
```
### 返回结果

|状态码|状态码含义|说明|数据模型|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||Inline|

### 返回数据结构：
状态码 **200**

|名称|类型|说明|
|---|---|---|
|» success|boolean|是否成功|
|» data|object||
|»» order|[订单](#schema%e8%ae%a2%e5%8d%95)||

### 返回示例：

> `请求成功`  状态码： `200` 
> 

```json
{
  "data": {
    "order": {
      "id": 1231243324324234,
      "merchantOrderId": "ORD2411014901CRD",
      "payTime": "string",
      "status": "string",
      "amount": 0,
      "hxCode": "string",
      "merchantId": 1233212312
    }
  },
  "success": true
}
```



> ## 3.2 获取订单列表

| `POST` | <a href="https://www.realglpay.com/api/v1/orders">https://www.realglpay.com/api/v1/orders</a> |
| --- | --- |

### 请求参数

|名称|位置|类型|必选|中文名|说明|
|---|---|---|---|---|---|
|body|body|object| 否 |||
|» sign|body|string| 是 | 签名||
|» clientId|body|string| 是 | API客户端ID||
|» timestamp|body|string| 是 | 时间戳||
|» nonce|body|string| 是 | 随机字符串||
|» merchantOrderId|body|string| 否 | 商户订单号||
|» beginTime|body|string| 否 | 支付起始时间||
|» endTime|body|string| 否 | 支付结束时间||
|» hxCode|body|string| 否 | 核销码||
|» orderId|body|string| 否 | 订单ID||
|» isContainFailed|body|boolean| 否 | 是否包含失败订单|若该项为true，则包含状态为`NotPaid`和`PayFailed`状态的订单|
|» pageSize|body|integer| 是 | 页大小||
|» pageIndex|body|integer| 是 | 页码||

> Body 请求参数示例

```json
{
  "sign": "185370108007258521656464561173079309111990E9296E37434CCD82E13AFA03F93224",
  "clientId": "18531674913121264",
  "timestamp": "1730793091119",
  "nonce": "56464561",
  "merchantOrderId": "ORD2411014901CRD",
  "beginTime": "2024-11-01 01:33:33",
  "endTime": "2024-11-02 01:33:33",
  "hxCode": "ZeuFcIx",
  "orderId": "1231243324324234",
  "isContainFailed": true,
  "pageSize": 2,
  "pageIndex": 1
}
```

### 返回结果

|状态码|状态码含义|说明|数据模型|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||Inline|

### 返回数据结构

状态码 **200**

|名称|类型|必选|中文名|说明|
|---|---|---|---|---|
|» success|boolean|true|是否成功||
|» pageData|object|true|分页数据||
|»» totalRecords|integer|true|总记录数||
|»» totalPages|integer|true|总页数||
|»» pageSize|integer|true|页大小||
|»» pageIndex|integer|true|页码||
|»» data|[[订单](#schema%e8%ae%a2%e5%8d%95)]|true||


> 返回示例

> `请求成功`  状态码： `200` 

```json
{
    "pageData": {
        "totalRecords": 5,
        "totalPages": 3,
        "pageSize": 2,
        "pageIndex": 1,
        "data": [
            {
                "id": 1853718562455007232,
                "merchantOrderId": "ZeuScIx",
                "payTime": "2024-11-05T16:38:40",
                "status": "PaySuccessed",
                "amount": 100.00000000,
                "hxCode": "8fQz1KU6JkiU",
                "merchantId": 1233212312
            },
            {
                "id": 1853709554375593984,
                "merchantOrderId": "dSD9c8av",
                "payTime": "2024-11-05T16:02:53",
                "status": "PayFailed",
                "amount": 0.00000000,
                "hxCode": "123123",
                "merchantId": 1233212312
            }
        ]
    },
    "success": true
}
```


> ## 3.3 获取结算列表

| `POST` | <a href="https://www.realglpay.com/api/v1/settlements">https://www.realglpay.com/api/v1/settlements</a> |
| --- | --- |

### 请求参数

|名称|位置|类型|必选|中文名|说明|
|---|---|---|---|---|---|
|body|body|object| 否 |||
|» sign|body|string| 是 | 签名||
|» clientId|body|string| 是 | API客户端ID||
|» timestamp|body|string| 是 | 时间戳||
|» nonce|body|string| 是 | 随机字符串||
|» startFinanceDate|body|string| 否 | 起始财务日期||
|» endFinanceDate|body|string| 否 | 结束财务日期||
|» settlementId|body|integer| 否 | 结算单ID||
|» isAmountAboveZero|body|boolean| 否 | 是否金额>0|若该项为true，则只返回结算金额>0的结算单|
|» payoutStatus|body|string| 否 | 付款状态|`NotPaid`: 未付款<br>`Paid`: 已付款|
|» pageSize|body|integer| 是 | 页大小||
|» pageIndex|body|integer| 是 | 页码||

> Body 请求参数示例

```json
{
  "sign": "185370108007258521656464561173079309111990E9296E37434CCD82E13AFA03F93224",
  "clientId": "18531674913121264",
  "timestamp": "1730793091119",
  "nonce": "56464561",
  "startFinanceDate": "2024-11-01",
  "endFinanceDate": "2024-11-10",
  "settlementId": 123123123123,
  "isAmountAboveZero": true,
  "payoutStatus": "Paid",
  "pageSize": 2,
  "pageIndex": 1
}
```

### 返回结果

|状态码|状态码含义|说明|数据模型|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)||Inline|

### 返回数据结构

状态码 **200**

|名称|类型|必选|中文名|说明|
|---|---|---|---|---|
|» success|boolean|true|是否成功||
|» pageData|object|true|分页数据||
|»» totalRecords|integer|true|总记录数||
|»» totalPages|integer|true|总页数||
|»» pageSize|integer|true|页大小||
|»» pageIndex|integer|true|页码||
|»» data|[[结算单](#schema%e7%bb%93%e7%ae%97%e5%8d%95)]|true|||


> 返回示例

> `请求成功`  状态码： `200` 

```json
{
    "pageData": {
        "totalRecords": 1,
        "totalPages": 1,
        "pageSize": 2,
        "pageIndex": 1,
        "data": [
            {
                "settleDateTime": "2024-11-01T00:00:00",
                "financeDate": "2024-11-01",
                "merchantId": 18537010800123123123,
                "status": "NotPaid",
                "orderAmount": 100.00000000,
                "feeAmount": 10.00000000,
                "settleAmount": 90.00000000,
                "orderCount": 10,
                "orderCurrency": "CNY",
                "payCurrency": "USD",
                "exchangeRate": 7.14555000,
                "payableAmount": 12.595251
            }
        ]
    },
    "success": true
}
```

# 4. 数据模型

> <h2 id="tocS_订单">订单</h2>

<a id="schema订单"></a>
<a id="schema_订单"></a>
<a id="tocS订单"></a>
<a id="tocs订单"></a>

|名称|类型|必选|中文名|说明|
|---|---|---|---|---|
|id|integer|true|订单ID||
|merchantOrderId|string|true|商户订单号||
|payTime|string|true|支付时间||
|status|string|true|状态|NoPaid: 未支付<br />PaySuccessed: 支付成功<br />PayFailed: 支付失败<br />Settled: 已结算|
|amount|number|true|金额||
|hxCode|string|true|核销码||
|merchantId|integer|true|商户ID||

> <h2 id="tocS_结算单">结算单</h2>

<a id="schema结算单"></a>
<a id="schema_结算单"></a>
<a id="tocS结算单"></a>
<a id="tocs结算单"></a>

|名称|类型|必选|中文名|说明|
|---|---|---|---|---|
|settleDateTime|string|false|结算时间||
|financeDate|string|false|结算财务日期||
|merchantId|integer|false|商户ID||
|status|string|false|状态|NotPaid: 未支付<br />Paid: 已支付|
|orderAmount|number|false|订单金额||
|feeAmount|integer|false|手续费||
|settleAmount|integer|false|结算金额|结算金额 = 订单金额 - 手续费|
|orderCount|integer|false|订单数量||
|orderCurrency|string|false|订单货币||
|payCurrency|string|false|付款货币||
|exchangeRate|number|false|汇率|应付金额 = 结算金额 / 汇率|
|payableAmount|integer|false|应付金额|按付款货币计价|



