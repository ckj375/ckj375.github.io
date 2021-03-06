---
title: 剖析扫码支付流程
date: 2017-03-02 15:00:12
tags: 二维码
categories: 支付
---
最近项目上需要集成第三方支付，采用扫二维码的方式进行付费。于是开始研读支付宝和微信支付平台的开发文档，根据官方文档总结了下扫码支付的通用流程：商户前台将商品参数发送至商户后台，商户后台生成内部订单号并用于请求支付平台创建预下单，支付平台创建完预订单后将订单二维码信息返还给商户，此时用户即可扫取二维码进行付款操作。  

支付业务流程图：
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/analyze-qr-code-payment/picture1.png)

<!-- more -->
支付业务时序图：
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/analyze-qr-code-payment/picture2.png)

**预下单请求**  
对于扫码支付场景支付平台提供的SDK是服务端的，所以首先由商户客户端向商户后台请求预下单，商户后台收到请求后再调用支付平台提供的标准预下单接口。由于微信要求商户订单号长度32个字符以内，故商户订单号采用系统时间+随机数的方式生成。商户后台收到预下单请求后首先生成商户系统内部订单号，然后请求支付平台的预下单接口创建订单，支付平台会将二维码信息返回给商户平台，商户平台收到二维码信息后再返回给客户端。

请求参数：

参数          | 描述
------------ | -----------
out_trade_no | 商户内部交易号
productTitle | 产品名称
productPrice | 产品价格

返回参数：

参数          | 描述
------------ | -----------
qr_code      | 二维码串

**异步通知**  
客户端收到二维码信息后，将二维码展示给用户，用户扫描二维码后进行支付确认，当支付平台完成这笔订单后，会将订单信息异步通知到商户预留的回调地址，商户收到通知后需应答接收成功，若未应答，支付平台将按一定时间间隔持续发送，直至收到商户的应答或发送次数超限。

返回参数：

参数          | 描述
------------ | -----------
trade_no     | 支付平台的交易号
out_trade_no | 商户内部交易号
productTitle | 产品名称
productPrice | 产品价格

**订单查询**  
考虑到接收异步通知失败的场景，商户可通过支付平台提供的订单查询接口主动查询订单状态及信息，支付平台交易号和商户交易号不能同时为空。

请求参数：

参数          | 描述
------------ | -----------
trade_no     | 支付平台的交易号
out_trade_no | 商户内部交易号

**对账相关**  
通过对账接口商户可以下载账单。  

请求参数：  

参数          | 描述
------------ | -----------
bill_type    | 账单日期
bill_date    | 账单类型

**参考文档**  
（1）[支付宝扫码支付开发文档](https://doc.open.alipay.com/docs/doc.htm?treeId=194&articleId=105072&docType=1)  
（2）[微信扫码支付开发文档](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=6_1)
