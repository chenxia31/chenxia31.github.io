---
title: 小白的在线支付方式折腾
tags:
  - 数字移民
categories:
  - 生活杂谈
abbrlink: 16132
date: 2024-11-17 10:23:28
---

因为希望开通 OPENAI-API，需要有美区发行的信用卡，由此折腾了虚拟信用卡 wildcard、国内 MasterCard、美区 PayPal 来完成 OPENAI、App Store、Amazon 海淘等过程，以下为小白经验贴，如有不对的地方欢迎指正

## 一、支付方式简介

支付方式通常可以分为：银行卡支付、移动支付、数字钱包、银行转账、加密货币、预付卡、电子支票等方式，这里注重解释前三者：

1. 银行卡支付（Bank card payment），进一步分为信用卡（Credit card）和借记卡（Debit card）
2. 移动支付（Mobile payment），包括 Apple pay、Google Pay、支付宝（Alipay）、微信支付（WeChat Pay）
3. 数字钱包（Digital wallet and Online Payment），包括 Paypal、Venmo 等等

支付中的参与方包括：

1. 发卡机构（Issuer），通常为银行，负责提供卡片
2. 支付网络（Payment Network），连接发卡机构和收单机构的中介，处理交易请求和验证
3. 收单机构（Acquirer），为商户提供银行卡支付处理服务的金融机构
4. 商户（Merchant），提供商品或者服务的商家

支付网络可以分为

1. **全球性支付网络 (Global Payment Networks)**：这些网络覆盖全球，适用于国际和跨境交易。常见的全球性支付网络包括：

•	**Visa**：覆盖全球的大型信用卡和借记卡支付网络。

•	**MasterCard**：全球通用的支付网络，支持信用卡、借记卡等多种支付方式。

•	**American Express**：提供信用卡支付，覆盖全球多个国家，主要采用三方模式。

•	**UnionPay (银联)**：在中国广泛使用，近年来也在全球扩展，支持信用卡、借记卡和二维码支付。

1. **地区性支付网络 (Regional Payment Networks)**：这些支付网络主要在特定区域内使用，通常只支持该地区的银行账户或支付工具。

•	**JCB**（日本）：在亚洲和一些欧洲、北美市场使用广泛。

•	**Interac**（加拿大）：用于加拿大的借记卡支付。

•	**Elo**（巴西）：在巴西使用的支付网络，支持借记和信用支付。

•	**RuPay**（印度）：主要用于印度国内支付。

## 二、支付需求

### 2.1 OPENAI 支付要求

支付要求包括

> OpenAI 的充值支付方式要求包括以下几点：
>
> 1. **国际信用卡或借记卡**：支持 Visa、MasterCard 和 American Express 等主流国际卡。确保卡片具有国际支付功能，国内银行的银联卡通常不支持。
> 2. **PayPal**：在部分地区，OpenAI 也支持通过 PayPal 支付。需要一个经过验证的 PayPal 账户，并关联到支持国际支付的信用卡或银行账户。
> 3. **Apple Pay 和 Google Pay**：部分移动端支持 Apple Pay 或 Google Pay 付款，但前提是这些支付账户也必须关联到支持国际支付的信用卡或借记卡。
> 4. **虚拟卡**：部分虚拟卡（如虚拟预付卡）可能会被接受，但成功率不高，因为 OpenAI 对支付账户的真实性验证较为严格。
> 5. **支付地区限制**：目前 OpenAI 充值服务并非对所有国家和地区开放，部分地区可能无法使用其服务，需参考 OpenAI 官网的支持列表。
>
> 建议在充值前确保支付方式符合以上条件，并检查账户的国际支付功能是否开启，以避免支付失败。

### 2.2 Amazon 支付要求

> 1. **信用卡和借记卡**：亚马逊支持主要的国际信用卡和借记卡品牌，如 Visa、MasterCard、American Express、Discover 等。在中国，您可以使用带有银联标识的信用卡或借记卡进行支付。
> 2. **第三方支付平台**：在亚马逊中国（[Amazon.cn](http://Amazon.cn)），您可以使用支付宝和微信支付等本地支付方式。

### 2.3 Apple Store 美区支付要求

> •	**美国发行的信用卡或借记卡**：Apple Store 接受由美国银行发行的 Visa、MasterCard、American Express 等信用卡或借记卡。
>
> •	**美国 PayPal 账户**：您可以将美国 PayPal 账户绑定到您的 Apple ID 作为支付方式。
>
> •	**Apple 礼品卡**：购买并兑换美国地区的 Apple 礼品卡，可将其余额用于支付。

### 2.4 Paypal 支付方式要求

> 1. **有效的支付方式**：
>
> •	**美国银行账户**：您可以将美国的银行账户关联到您的 PayPal 账户，用于充值和支付。
>
> •	**信用卡或借记卡**：PayPal 支持 Visa、MasterCard、American Express 等主要信用卡和借记卡。确保您的卡片已关联到 PayPal 账户。
>
> 1. **美国账单地址**：在设置支付方式时，通常需要提供一个有效的美国账单地址。您可以使用真实的美国地址，或通过地址生成器获取。
> 2. **美国电话号码**：在某些情况下，可能需要提供一个美国电话号码。您可以使用虚拟号码服务，或通过其他方式获取。
> 3. **账户验证**：为确保账户安全，PayPal 可能要求您验证关联的银行卡或银行账户。验证过程可能包括小额扣款或其他确认步骤。

## 三、折腾记录

### 3.1 信用卡

**第一种是开通虚拟卡**，这里选择 wildcard 平台

非常方便但是需要付出手续费，我刚用不到 5 分钟升级了 openai plus 和绑了 api 付费，对于小白来说很方便，因为邀请有奖励这里做一些分享

- 官网地址：https://bewildcard.com/card
- 创建账户费用： 11.99/两年，16.99/三年
- 手续费：3.5%
- 限额：3000 美元/天
- 使用邀请码可以优惠 https://bewildcard.com/i/MP40YHIM

开通之后可以 openai api platform 绑定自己的 card 作为支付方式，或者利用wildcard 提供的服务来绑定，官方提供 ChatGPT 一键升级的功能

![image-20241117102454663](https://chenxia31blog.oss-cn-hangzhou.aliyuncs.com/blog/image-20241117102454663.png)

第二种是实际信用卡，这里不再赘述。

【上述两种方式都可以支付购买服务了！比如 Amazon、软件购买】

【但是中国发卡机构发放的支持支付组织的卡大部分情况不能直接完成美国相关线上服务的购买，比如 Apple Store、Google Pay 等】

### 3.2 美区Paypal

这里需要有一个非虚拟号的美国手机号，但是大部分都没有，这里有一个操作是可以通过商户支付页面进行注册，可以是为 Wiki 捐赠页面

https://donate.wikimedia.org/w/index.php?title=Special:LandingPage&country=HK&uselang=en&wmf_medium=spontaneous&wmf_source=fr-redir&wmf_campaign=spontaneous

【注意注册 PayPal 过程中全程美区 IP + 无痕浏览模式】

【之后可以通过 PayPal 来绑定美区 Apple store】
