# 惠通支付宝商户对接文档

## 文档修改记录
版本号 | 日期 | 描述 | 修改人
:-:|:-:|:-:|:-:
v1.0 | 2019.4.20 | 创建文档 | 


<br/> <br/> 

## 目录
- [惠通支付宝商户对接文档](#惠通支付宝商户对接文档)
    - [文档修改记录](#文档修改记录)
    - [文档简介](#文档简介)
        - [特别声明](#特别声明)
        - [阅读对象](#阅读对象)
        - [技术支持服务](#技术支持服务)
    - [签名加密](#签名加密)
        - [签名算法介绍](#签名算法介绍)
        - [名词解释](#名词解释)
        - [商户端加解密步骤详解](#商户端加解密步骤详解)
            - [请求参数处理过程](#请求参数处理过程)
            - [同步/异步响应结果处理过程](#同步异步响应结果处理过程)
    - [接口简介](#接口简介)
        - [支付宝H5下单接口](#支付宝h5下单接口)
            - [说明](#说明)
            - [请求地址](#请求地址)
            - [请求方式](#请求方式)
            - [请求参数](#请求参数)
            - [返回参数](#返回参数)
            - [示例代码](#示例代码)
        - [异步通知](#异步通知)
            - [说明](#说明-1)
            - [策略](#策略)
            - [发送方式](#发送方式)
            - [通知内容](#通知内容)
        - [订单查询接口](#订单查询接口)
            - [说明](#说明-2)
            - [请求地址](#请求地址-1)
            - [请求方式](#请求方式-1)
            - [请求参数](#请求参数-1)
            - [返回参数](#返回参数-1)
        - [附录](#附录)
            - [订单状态说明](#订单状态说明)
            - [签名示例](#签名示例)


<br/> <br/> <br/>


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
<br/> <br/> <br/> 

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
<br/> <br/> <br/> 

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
签名串 | sign | string |YES | 签名串

#### 返回参数
名称 | 属性 | 类型 | 备注
:-:|:-:|:-:|:-:
平台流水号 | trade_no | string | 平台生成的订单流水号
订单状态 | status | string | 订单状态
商户订单号 | out_trade_no | string | 商户生成的唯一订单号
商户编号 | mch_id | string | 平台为商户分配的唯一标识
支付链接 | location | string | 平台生成的支付链接，返回给前端/手机端，通过浏览器调起支付宝
金额 | amount | double | 下单金额
签名串 | sign | string | 签名串


#### 示例代码
```
Map<String, String> params = new  TreeMap<String,String>();
params.put("mch_id", "MCH_12345");
params.put("out_trade_no", "100001");
params.put("body", "货款");
params.put("subject", "支付测试");
params.put("total_amount", "1.0"); 
params.put("notify_url", "http://myurl/notify");
params.put("return_url", "http://appurl/return");

String stringA = CREATE-SIGN-STRING-WITHOUT-URL-ENCODE(params); // map转为 http form 形式的 验签串
String stringSignTemp = stringA + merchantKey; // 商户密钥附加到验签串末尾
params.put("sign",md5(stringSignTemp)); // 计算签名

String res = STANDARD-HTTP-POST-FORM("http://payorderurl", NormalPaymentKit.getRequestStrNoEncode(params), "utf-8");
System.out.println("收到响应：" + res);
```
<br/> <br/> 

### 异步通知
#### 说明
支付成功后或失败，平台通过商户提供的异步通知地址发送此通知

#### 策略
此通知只发送一次，商户处理成功后请返回 'success'

#### 发送方式
POST 

#### 通知内容
名称 | 属性 | 类型 |  备注
:-:|:-:|:-:|:-:
平台流水号 | trade_no | string | 平台生成的订单流水号
订单状态 | status | string | 订单状态
商户订单号 | out_trade_no | string | 商户生成的唯一订单号
金额 | amount | double | 下单金额
签名串 | sign | string | 签名串

<br/> <br/> 


### 订单查询接口
#### 说明
查询商户订单情况

#### 请求地址
生产环境：http://{服务器url}/alipay/query

#### 请求方式
POST 或 GET

#### 请求参数
名称 | 属性 | 类型 | 必填 | 备注
:-:|:-:|:-:|:-:|:-:
商户编号 | mch_id | string | YES |平台为商户分配的唯一标识
商户订单号 | out_trade_no | string | YES | 商户生成的唯一订单号
签名串 | sign | string | YES |签名串


#### 返回参数
名称 | 属性 | 类型 | 备注
:-:|:-:|:-:|:-:
平台流水号 | trade_no | string | 平台生成的订单流水号
订单状态 | status | string | 订单状态
商户订单号 | out_trade_no | string | 商户生成的唯一订单号
订单生成时间 | create_time | long | 订单生成时间
金额 | amount | double | 下单金额
签名串 | sign | string | 签名串

<br/> <br/> <br/> 


### 附录
#### 订单状态说明
状态名称 | 说明
:-:|:-:
unpay | 未支付
paying | 准备支付
paid ok | 成功支付
pay error | 支付失败
pay timeout | 超时未支付

<br/> <br/>

#### 签名示例
 - 生成签名串
```
public static String getRequestStrNoEncode(TreeMap<String,String> map) throws UnsupportedEncodingException{
 StringBuffer sb = new StringBuffer("");
    String val = "";
    for (String key : map.keySet()) {
        val = map.get(key);
        if (Tool.isNotNullOrEmpty(val)) {
            sb.append(key).append("=").append(val).append("&");
        }
    }
    return sb.toString().substring(0, sb.toString().length() - 1);
}
```
<br/>

 - http post 示例
```
    public static String httpPost(String url, String req, String enc) throws Exception {

	    BasicHttpClientConnectionManager connManager = 
	    	new BasicHttpClientConnectionManager(
                RegistryBuilder.<ConnectionSocketFactory>create()
                        .register("http", PlainConnectionSocketFactory.getSocketFactory())
                        .register("https", SSLConnectionSocketFactory.getSocketFactory())
                        .build(),
                null,
                null,
                null
        );
    
		CloseableHttpClient httpClient = HttpClientBuilder.create()
                .setConnectionManager(connManager)
                .build();
        
        HttpPost httpPost = new HttpPost(url);
        int socktimeoutms = 1000000;

        RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(socktimeoutms).setConnectTimeout(socktimeoutms).build();
        httpPost.setConfig(requestConfig);

        StringEntity postEntity = new StringEntity(req, enc);
        httpPost.addHeader("Content-Type", "application/x-www-form-urlencoded");
        httpPost.setEntity(postEntity);

        HttpResponse httpResponse = httpClient.execute(httpPost);
        HttpEntity httpEntity = httpResponse.getEntity();

        return EntityUtils.toString(httpEntity, enc);
    }
```
<br/>

 - http get 示例
```
	public static String httpGet(String req, Map<String , String> header) throws Exception {
    	SSLContextBuilder builder = new SSLContextBuilder();
    	 builder.loadTrustMaterial(null, new TrustSelfSignedStrategy());
    	 SSLConnectionSocketFactory sslConnectionSocketFactory = new SSLConnectionSocketFactory(builder.build(), NoopHostnameVerifier.INSTANCE);
    	 Registry<ConnectionSocketFactory> registry = RegistryBuilder.<ConnectionSocketFactory>create()
    	            .register("http", new PlainConnectionSocketFactory())
    	            .register("https", sslConnectionSocketFactory)
    	            .build();

    	 PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager(registry);
    	 cm.setMaxTotal(100);
		CloseableHttpClient httpClient = HttpClientBuilder.create()
	            .setSSLSocketFactory(sslConnectionSocketFactory)
	            .setConnectionManager(cm)
	            .build();

		HttpGet httpGet = new HttpGet(req);
        int socktimeoutms = 1000000;

        RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(socktimeoutms).setConnectTimeout(socktimeoutms).build();
        httpGet.setConfig(requestConfig);

        httpGet.addHeader("Content-Type", "application/x-www-form-urlencoded");
        if (header!=null) {
            for (String key : header.keySet()){
            	httpGet.setHeader(key , header.get(key));
            }
        }

        HttpResponse httpResponse = httpClient.execute(httpGet);
        HttpEntity httpEntity = httpResponse.getEntity();

        return EntityUtils.toString(httpEntity, "UTF-8");
    }
```
