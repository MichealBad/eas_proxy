
## 部署说明

- 拷贝代码到本地: git clone http://47.104.165.194:3000/yhglobal.com/easproxyservice.git
- 进入 src/main 下，使用 maven 构建项目: mvn clean install
- 将构建好的 war (gateway 项目下的 target) 放置于 tomcat 目录的 webapps/ROOT/ 下
- 启动 tomcat : ./bin/startup.sh
- 关闭 tomcat 的时候，因为本项目有线程池，得用 kill -9 PID 杀掉(ps -aux\|grep eas_proxy 可以查到 PID)。

## EAS 代理接口

## 部署环境说明

说明       |url       
------------|-----------
正式环境 | 稍微等候
测试环境 | asked

## <span id="exchangeMsg">消息结构</span>

字段 |字段类型 |字段说明
---|---|---
index | long | 消息下标
msgCode | string | 消息唯一业务编码
[bizType](#bizType) | string | 业务类型
originPoj | string | 来源系统
msgJson | json | 来源业务 json
originCallbackUrl | string | 来源回调 url
createDate | date | 创建日期
resultJson | json | 结果 json
msgState | integer | 消息状态
exchangeUrl | string | 代理回调交换 url
exchangeState | integer | 交换状态
exchangeDate | date | 交换日期
updateDate | date | 更新日期

## <span id="bizType">业务类型</span>


编码	| 说明
---|---
PO | 采购订单
PI | 采购入库
PR | 采购退货
SO | 销售订单
SS | 销售出库
SR | 销售退货
IT | 调拨订单
ST | 库存调拨单
TO | 调拨出库单
TI | 调拨入库单
AO | 其他出库单
AI | 其他入库单
AR | 应收单
AP | 应付单
CO | 收款单
BP | 付款单
LM | 库位移动单
CS | 确认签收日期

## <span id="http">Http 响应</span>

字段 |字段类型 |字段说明
---|---|---
code | integer | 编码(0 成功 其它失败)
data | object | 根据不同的请求返回
msg | string | 描述


## 推送消息接口

> 请求方式：POST<br>
请求URL ：[/msg/exchange](#)


字段 |字段类型 |字段说明
---|---|---
[bizType](#bizType) | string | 业务类型
msgCode | string | 消息唯一业务编码
originCallbackUrl | string | 来源回调 url
originPoj | string | 来源系统
sign | string | 签名(通过参数自然排序拼接而成的 md5( key1=val1&key2=val2&...&projectKey=xxx))

### 请求例子
```json
{
	"bizType":"SO",
	"msgCode":"SQ20180901022",
	"msgJson":"{}",
	"originCallbackUrl":"http://127.0.0.1:8080/callback/test",
	"originPoj":"XPS",
	"sign": "855b600cf5b8cc0fcc6b794181cd2878"
}
(signed string: bizType=SO&msgCode=SQ20180901023&msgJson={}&originCallbackUrl=http://127.0.0.1:8080/callback/test&originPoj=XPS&projectKey=xxx)
```

### 返回参数

[Http 响应](#http)

#### data 参数

[完整消息类型](#exchangeMsg)

### 返回例子
```json
{
    "code": 0,
    "data": {
        "bizType": "SO",
        "createDate": "2019-01-11 03:01:48",
        "exchangeDate": null,
        "exchangeState": 0,
        "exchangeUrl": "http://127.0.0.1:8080/msg/notify",
        "index": 24,
        "msgCode": "SQ20180901023",
        "msgJson": "{}",
        "msgState": 0,
        "originCallbackUrl": "http://127.0.0.1:8080/callback/test",
        "originPoj": "XPS",
        "resultJson": null,
        "updateDate": null
    },
    "desc": "调用成功"
}
```

## 消息拉取接口

> 请求方式：POST<br>
请求URL ：[/msg/pull](#)

字段 |字段类型 |字段说明
---|---|---
index | long | 开始抓取的消息下标(>=)
limit | long | 抓取的最大大小(<=)
originPoj | string | 来源系统
sign | string | 签名(通过参数自然排序拼接而成的 md5( key1=val1&key2=val2&...&projectKey=xxx))

### 请求例子
```json
{
	"index": 0,
	"limit": 23,
	"originPoj": "eas",
	"sign": "16c5303a42a50b3936870f0492e0a575"
}
(sign string:index=0&limit=23&originPoj=eas&projectKey=xxx)
```

### 返回参数

[Http 响应](#http)

#### data 参数

字段 |字段类型 |字段说明
---|---|---
maxIndex | long | 目前消息最大下标
msgList | array | 拉取的消息集合

#### msgList 参数

[完整消息类型](#exchangeMsg)

### 返回例子
```json
{
    "code": 0,
    "data": {
        "maxIndex": 24,
        "msgList": [
            {
                "bizType": "SO",
                "createDate": "2019-01-11 03:01:49",
                "exchangeDate": null,
                "exchangeState": 0,
                "exchangeUrl": "http://127.0.0.1:8080/msg/notify",
                "index": 24,
                "msgCode": "SQ20180901023",
                "msgJson": "{}",
                "msgState": 0,
                "originCallbackUrl": "http://127.0.0.1:8080/callback/test",
                "originPoj": "XPS",
                "resultJson": null,
                "updateDate": null
            }
        ]
    },
    "desc": "调用成功"
}
```
## EAS 通知 Proxy 接口

> 请求方式：POST<br>
请求URL ：[/msg/notify](#)

### 请求参数

字段 |字段类型 |字段说明
---|---|---
index | long | 消息下标
msgCode | string | 消息唯一业务编码
originPoj | string | 来源系统
resultJson | string | eas 调用的结果 json
sign | string | 签名(通过参数自然排序拼接而成的 md5( key1=val1&key2=val2&...&projectKey=xxx))

### 请求例子
```json
{
	"index": 3,
	"msgCode": "SQ20180901003",
	"originPoj": "eas",
	"resultJson": "{}",
	"sign": "bab99b8e38b56736077b90ca53e07327"
}
(sign string:index=3&msgCode=SQ20180901003&originPoj=eas&resultJson={}&projectKey=xxx)
```

### 返回参数

[Http 响应](#http)

## 消息 callback 接口

该接口由 Proxy 获得 EAS 答复以后回调 originCallbackUrl

### 回调参数

[完整消息类型](#exchangeMsg)

### 回调例子
```json
{
  "resultJson": "{}",
  "exchangeDate": "2019-01-11 04:01:21",
  "updateDate": "2019-01-12 12:01:21",
  "bizType": "SO",
  "msgState": 0,
  "originCallbackUrl": "http://127.0.0.1:8080/callback/test",
  "exchangeUrl": "http://127.0.0.1:8080/msg/notify",
  "sign": "d0cbc1dbeacd4c32ee934aa36dabc722",
  "index": 4,
  "msgJson": "{}",
  "originPoj": "XPS",
  "exchangeState": 2,
  "msgCode": "SQ20180901004",
  "createDate": "2019-01-11 10:01:38"
}
```
### 需要应答参数

字段 |字段类型 |字段说明
---|---|---
state | integer | 消息接收状态，0 代表成功接收
desc | string | 说明

### 应答例子

注意此状态为消息的「接收」状态，成功接收就为 0，跟业务成功与否没有关系

```json
{
  "desc": "success",
  "state": 0
}
```
