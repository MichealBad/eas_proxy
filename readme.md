
## EAS 代理接口

## 部署环境说明

说明       |url       
------------|-----------
正式环境 | 稍微等候
测试环境 | asked

## 消息结构

字段       |字段类型      |字段说明
----------|-----------|------
index | bigint | 消息下标
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
