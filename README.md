# 惠通支付宝商户对接文档



## 文档修改记录
版本号 | 日期 | 描述 | 修改人
:-:|:-:|:-:|:-:
v1.0 | 2019.4.20 | 创建文档 | yzhou

目录
[toc]


## 文档简介
### 特别声明
 - 未得到我司的书面许可，不得为任何目的、以任何形式或手段（包括但不限于机械的或电子的）复制或传播本文档的任何部分。对于本文档涉及的技术和产品，我司拥有其专利（或正在申请专利）、 商标、版权或其它知识产权。除非得到我司的书面许可协议，本文档不授予这些专利、商标、版权或其它知识产权的许可。  
 - 本文档因产品功能示例和描述的需要，所使用的任何人名、企业名和数据都是虚构的，并仅限于我司内部测试使用，不等于我司有对任何第三方的承诺和宣传。  
 - 本文档的解释权属于我司。若本文档内容变动，恕不另行通知。

### 阅读对象
贵公司技术部门的开发、维护或管理人员。他们需具备以下基本知识：
 - 了解网站设置和网页制作方法
 - 了解HTML语言以及ASP、.NET、PHP或JAVA等开发语言
 - 了解信息安全的基本概念

### 技术支持服务
如果遇到特殊情况，需要我司工作人员协助处理解决问题的话，可以联系您所属的销售人员，对应销售人员会提供给您所属区域的技术支持人员的联系方式。


## 签名加密
### 签名算法介绍
 - 为了保证网络数据的传输安全，防止数据在传输过程中被截取、篡改等，在调用API时双方约定使用MD5算法对通讯数据进行签名处理。
 - MD5为通用加密算法，算法描述如下:  
Step1：将所有参数名称（不包含sign本身）按照字母顺序排序，从a-z  
Step2：将这些参数的值连接成一个字符串，中文字符使用UTF-8编码  
Step3：将这个字符串作为源字符串，将商户MD5密钥（该密钥需在我司后台进行报备）作为key，使用MD5算法生成签名  
Step4：将生成的签名值添加到参数列表中，参数名称为“sign”
 - 明文范例:
```
body=货款&mch_id=MCH_f2ea34fd2&notify_url=http://merchant_notify_url&out_trade_no=2000-100001&return_url=user_return_url&subject=支付测试&total_amount=100
```  

### 名词解释
 - merchantMD5Key：我司后台随机生成MD5Key，用于MD5签名/验签
 - merchantno：商户编号(如:MCH_f2ea34fd2)，可从后台获取

### 商户端加解密步骤详解
#### 请求参数处理过程
Step1：生成基本业务参数，如下单接口中的基本业务参数值
Step2：业务请求参数按参数名排序（按照字母顺序排序，从a-z）
```
body=货款&mch_id=MCH_f2ea34fd2&notify_url=http://merchant_notify_url&out_trade_no=2000-100001&return_url=user_return_url&subject=支付测试&total_amount=100
```  
Step3：调用通用MD5签名方法计算出sign值
```
body=货款&mch_id=MCH_f2ea34fd2&notify_url=http://merchant_notify_url&out_trade_no=2000-100001&return_url=user_return_url&subject=支付测试&total_amount=100&sign=71F46542493B3D8C2A627430FD04C9F9
```  
Step4：向API接口发送HTTP请求，POST和GET均可

#### 同步/异步响应结果处理过程
Step1：接收接口处理结果（JSON格式），如下：
```
{"trade_no":"alp_0000000000000000001","status":"paying","out_trade_no":"2000-100001","mch_id":"MCH_f2ea34fd2","location":"https://openapi.alipay.com/gateway.do?biz_content=%7B%22total_amount%22%3A10.0%2C%22timeout_express%22%3A%22240m%22%2C%22subject%22%22product_code%22%3A%22QUICK_WAP_PAY%22%2C%22out_trade_no%22%3A%2000-100001%22%2C%22body%22%3A%22%E8%B4%A7%E6%AC%BE%22%7D&charset=utf-8&format=JSON&method=alipay.trade.wap.pay&notify_url=http%3A%2F%2F1.1.1.1%3A1111%2Falipay%2Fnotify&return_url=http%3A%2F%2F1.1.1.1%3A4230%2F&sign=0A32U%2FrGFy%2BzGJqnbCUyCnrgIAZq96w4EZ2s41ffalVRIM32IrfGiUP6rOG2ylcptSCf4TaiF5Hh%2FmT1g7uiU6%2BCcC4Cjpg46jdR4e3RYaDmiAthDOZg80fIKcYn61QM5EgUYwVmQv5WdaCJXObyLm3TSD1h0n78mtSfAFGT2Ezg6A5JVg5MOkLu4OREXCnT4pW20zxQc86DJ7WEEDfD%2FO0%2BetMT9d8RT1Ly9V7oUEaddd9PqzEJGwJZ1EPWEMwJvRkzFgenE7eZFIRAY7HmSH%2BzHe64%2FXPdnKF8%2But4P9wrNF5TOcvtxzdmsg%3D%3D&sign_type=RSA2&timestamp=2019-08-20+12%3A37%3A01&version=1.0","amount":10.00}
```
Step2：将除了sign之外的参数按键排序，并将参数值拼成字符串signData
Step3：用MD5验证签名是否正确
Step4：验证的签名通过后认可返回的内容


## 接口简介
### 支付宝H5下单接口
#### 说明
发起支付宝H5下单时，调用此接口
#### 请求地址
生产环境：http://{服务器url}/alipay/pay
#### 请求方式
POST 或 GET
#### 请求参数
名称 | 属性 | 类型 | 必填 | 备注
:-:|:-:|:-:|:-:|:-:
商户编号 | mch_id | string | YES |平台为商户分配的唯一标识
商户订单号 | out_trade_no | string | YES | 商户生成的唯一订单号
订单描述 | body | string | YES | 商户提供的订单描述
订单标题 | subject | string | YES | 商户提供的订单标题
订单金额 | total_amount | double | YES | 订单金额，单位 元
异步通知url | notify_url | string | YES | 商户接收异步通知的url
同步跳转url | return_url | string | YES | 商户支付完成后跳转的url  
#### 返回参数
名称 | 属性 | 类型 | 备注
:-:|:-:|:-:|:-:
平台流水号 | trade_no | string | 平台生成的订单流水号
订单状态 | status | string | 订单状态
商户订单号 | out_trade_no | string | 商户生成的唯一订单号
商户编号 | mch_id | string | 平台为商户分配的唯一标识
支付链接 | location | string | 平台生成的支付链接，用于调起支付宝
金额 | amount | double | 下单金额



