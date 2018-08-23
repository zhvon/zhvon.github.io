---
title: PHP使用第三方支付依赖对接App支付
tags:
  - php
  - 微信
  - 支付宝
  - app
categories:
  - php
  - app
date: 2017-10-17 18:52:44
---
<Excerpt in index | 首页摘要> 
> 背景：仅记录服务端对接，不涉及app技术。分两种情况：
- 使用``CI``（``Codeigniter``）框架作为服务端
- 使用``OpenCart``框架作为服务端

# 依赖来源和安装 #

## 依赖来源 ##
- 有一个很不错的``PHP``依赖包地方：[Packagist](https://packagist.org/)
- 本次用到的第三方支付依赖是：[omnipay/omnipay](https://packagist.org/packages/omnipay/omnipay)
- 本次使用的``PHP``版本为``5.5``或``5.6``，安装时要注意版本。

## 依赖安装 ##

如果项目里已经存在``composer.json`` 文件，则添加以下依赖关系即可：
```json
"omnipay/paypal": "~2.0",
"lokielse/omnipay-wechatpay": "^1.0",
"lokielse/omnipay-alipay": "^2.0",
```
如果没有该文件，则创建一个并添加即可。
 
## 安装 ##
- 使用``composer``进行安装，一个非常好用的``PHP``依赖管理工具

### 如何安装和使用``composer`` ###
> 安装前请务必确保已经正确安装了 PHP。打开命令行窗口并执行 php -v 查看是否正确输出版本号。

<!-- more -->
<The rest of contents | 余下全文>

#### 局部安装 ####

打开命令行并依次执行下列命令安装最新版本的``Composer``：
```bash
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
```
```bash
php composer-setup.php
```
```bash
php -r "unlink('composer-setup.php');"
```
#### 全局安装 ####
全局安装是将``Composer`` 安装到系统环境变量``PATH`` 所包含的路径下面，然后就能够在命令行窗口中直接执行``composer`` 命令了。

** Mac 或 Linux 系统： **
打开命令行窗口并执行如下命令将前面下载的``composer.phar``文件移动到``/usr/local/bin/``目录下面：
```bash
sudo mv composer.phar /usr/local/bin/composer
```
** Windows 系统：**
- 找到并进入 PHP 的安装目录（和你在命令行中执行的 php 指令应该是同一套 PHP）。
- 将``composer.phar``复制到 PHP 的安装目录下面，也就是和``php.exe`` 在同一级目录。
- 在``PHP``安装目录下新建一个``composer.bat`` 文件，并将下列代码保存到此文件中。
```bash
@php "%~dp0composer.phar" %*
```
#### 使用 Composer ####
```bash
php composer.phar install
```
如果你进行了全局安装，并且没有phar文件在当前目录，请使用下面的命令代替：
```bash
composer install
```

最后重新打开一个命令行窗口试一试执行``composer --version``看看是否正确输出版本号。

详情请到[Composer入门指南](http://docs.phpcomposer.com/00-intro.html)


# 支付接入 #

除了库的下载，``Composer``还准备了一个自动加载文件，它可以加载``Composer``下载的库中所有的类文件。使用它，你只需要将下面这行代码添加到你项目的引导文件中：
```php
require 'vendor/autoload.php';
```
一般情况下，都会做成自动加载。

** 特别注意： **``设置回调地址时候（以下setNotifyUrl），不能有?和=之类的符号，不然会造成第三方解析错误，支付宝不能付款，会返回验签错误；微信能付款，但无法回调。``

```php
use Omnipay\Omnipay;
// 初始化支付參數設置
private function init_params($method = "", $type = "")
{
	if(!empty($method))
	{
		switch ($method) {
			case 'Alipay':
				/**
				 * @var $gateway
				 */
				$this->gateway = Omnipay::create( $method.( (!empty($type)) ? '_'.$type : '' ) );
				$this->gateway->setEnvironment('production');
				$this->gateway->setAppId( $this->input->post('app_id') );
				if(!$this->input->post('app_id')) $this->gateway->setAppId('你的app_id');
				$this->gateway->setPrivateKey('你的私钥');
				$this->gateway->setAlipayPublicKey('你的支付宝公钥');
				$this->gateway->setSignType("RSA2");
				// 回调地址不能有?和=之类的符号，不然会造成第三方解析错误
				// 支付宝不能付款，会返回验签错误
				// 微信能付款，但无法回调
				$this->gateway->setNotifyUrl(current_url().'_'.$type.'_callback');
				break;

			case 'WechatPay':
				/**
				 * @var $gateway
				 */
				$this->gateway = Omnipay::create($method.'_'.$type);
				$this->gateway->setAppId( $this->input->post('app_id') );
				if(!$this->input->post('app_id')) $this->gateway->setAppId('你的app_id'); // 微信支付appid
				$this->gateway->setMchId('你的商户id'); // 商户id
				$this->gateway->setApiKey('你的商户密钥'); // 商户密钥 32位
				// 回调地址不能有?和=之类的符号，不然会造成第三方解析错误
				// 支付宝不能付款，会返回验签错误
				// 微信能付款，但无法回调
				$this->gateway->setNotifyUrl(current_url().'_'.$type.'_callback');
				break;
			
			default:
				# code...
				break;
		}

		return true;
	}

	return false;
	
}
public function Alipay()
{
	if( $this->init_params('Alipay', $this->request->get['type']) )
	{
		// 服务端创建订单
		$request = $this->gateway->purchase();
		// out_trade_no 为订单号，由客户端传订单id来获取订单数据
		// ci 使用 $this->input->post()
		// opencart 使用 $_POST OR file_get_contents('php://input')
		$request->setBizContent([
		    'subject'      => 'test',
		    'out_trade_no' => '64RS'.date('YmdHis'),//$order_id,//$this->gen_order_code(),// todo
		    'total_amount' => 0.01,//$order_info['total'], //todo
		    'product_code' => 'QUICK_MSECURITY_PAY',
		]);

		/**
		 * @var AopTradeAppPayResponse $response
		 */
		$response = $request->send();
		// 返回给app的支付字符串 call app
		$orderString = $response->getOrderString();
		$result = array(
			'result' => $orderString,
			'success' => TRUE
		);

		return $result;
	}
	
}
// 處理 APP 接口的回調
public function Alipay_AopApp_callback()
{
	if( $this->init_params('Alipay', 'AopApp') )
	{
		$this->Alipay_callback_handle();
	}
	
}
private function Alipay_callback_handle()
{

	$request = $this->gateway->completePurchase();
	// ci 使用 $this->input->post()
	// opencart 使用 $_POST
	$request->setParams($_POST); //Optional
	/**
	 * @var AopCompletePurchaseResponse $response
	 */
	try {
	    $response = $request->send();
	    if($response->isPaid()){
	        /**
	         * Payment is successful
	         */
	        // $response->getAlipayResponse();

	        /**
	         *  {"total_amount":"0.01","buyer_id":"2088002662696784","trade_no":"2017040921001004780231380348","notify_time":"2017-04-09 12:14:44","subject":"INNOAPP\u72ec\u5bb6\u8bb0\u5fc6","sign_type":"RSA","buyer_logon_id":"owe***@gmail.com","auth_app_id":"2017020805573464","charset":"UTF-8","notify_type":"trade_status_sync","invoice_amount":"0.01","out_trade_no":"A409112791116169","trade_status":"TRADE_SUCCESS","gmt_payment":"2017-04-09 12:14:44","version":"1.0","point_amount":"0.00","sign":"qvYaKkNasUvVsSBcdzIMOB7TtUmN75Zb5qFfx3X6ExYeOpPhMUtVxOsxp383pSiCH1ueFsCAzmY1Z2BpV5+yyeOM1JoyQ1IDF3MnbBphATgQr\/kZaUMkW743qtpXeHw4OGPX\/K25wz3Y1Zah6qUUqI\/wwZTSz\/k9NNG0CQ5Ubos=","gmt_create":"2017-04-09 12:14:43","buyer_pay_amount":"0.01","receipt_amount":"0.01","fund_bill_list":"[{\"amount\":\"0.01\",\"fundChannel\":\"ALIPAYACCOUNT\"}]","app_id":"2017020805573464","seller_id":"2088521549780793","notify_id":"dbe949f01b6abe5d19d62f08a12e2a5m0q","seller_email":"admin@innoapp.net.cn"}
	         */
			$data = $response->data(); //Take the data reponsed from alipay
			$arr = explode('RS', $data['out_trade_no']);

			if (!empty($data)) {
				// 检查和更新订单状态 
			}	

	        die('success'); //The response should be 'success' only
	    }else{
	        /**
	         * Payment is not successful
	         */
	        /**
					 * TODO: 訂單失敗之後，需要把訂單狀態和交易記錄做出狀態改變，并推送通知
					 */


	        die('fail');
	    }
	} catch (Exception $e) {
	    /**
	     * Payment is not successful
	     */

	    /**
			 * TODO: 訂單失敗之後，需要把訂單狀態和交易記錄做出狀態改變，并推送通知
			 */


	    // die('fail');
	}
	
}
public function WechatPay()
{
	if( $this->init_params('WechatPay', $this->request->get['type']) )
	{
		//服务端创建订单
		// out_trade_no 为订单号，由客户端传订单id来获取订单数据
		// ci 使用 $this->input->post()
		// opencart 使用 $_POST OR file_get_contents('php://input')
		$order = [
		    'body'              => 'test',
		    'out_trade_no'      => '64RS'.date('YmdHis'),//$this->gen_order_code(),
		    'total_fee'         => 1, //=0.01
		    'spbill_create_ip'  => '192.168.1.1',
		    'fee_type'          => 'CNY'
		];

		/**
		 * @var Omnipay\WechatPay\Message\CreateOrderRequest $request
		 * @var Omnipay\WechatPay\Message\CreateOrderResponse $response
		 */
		$request  = $this->gateway->purchase($order);
		$response = $request->send();

		if( $response->isSuccessful() )
		{
			$result = array(
				'result' => $response->getAppOrderData(),
				'success' => TRUE,
				'url' => $this->gateway->getNotifyUrl(),
			);
		}
		else
		{
			$result = array(
				'error' => 'PAYMENT_FAIL',
				'success' => FALSE,
			);
		}

		return $result;
	}
}
// 處理接口的回調
public function WechatPay_App_callback()
{
	if( $this->init_params('WechatPay') )
	{
		$this->WechatPay_callback_handle();
	}
}
private function WechatPay_callback_handle()
{
	// ci 使用 $this->input->post()
	// opencart 使用 file_get_contents('php://input')
	$response = $this->gateway->completePurchase([
	'request_params' => file_get_contents('php://input')
	])->send();
	if ($response->isPaid()) {
	    //pay success
		/**
   * Payment is successful
   */
  // $response->getAlipayResponse();

  /**
   *  {"appid":"wxe4092309e413444b","bank_type":"ICBC_DEBIT","cash_fee":"1","fee_type":"CNY","is_subscribe":"N","mch_id":"1457794502","nonce_str":"d4c0b08e178bb410dbe8b8086995f10d","openid":"oNwEkv15PzX-oQKTxi_3vwrpX9IM","out_trade_no":"A410084672788553","result_code":"SUCCESS","return_code":"SUCCESS","sign":"4F7ACBDA5055A1B431898D2C6E180D10","time_end":"20170410151433","total_fee":"1","trade_type":"APP","transaction_id":"4008602001201704106528307660"}
   */
		$data = $response->getRequestData(); //Take the data reponsed from wechat
		/**
		 * TODO: 訂單成功之後，需要把訂單狀態和交易記錄做出狀態改變，并推送通知
		 */
		
		$arr = explode('RS', $data['out_trade_no']);

		if (!empty($data)) {
			// 检查和更新订单状态 
		}	


	}else{
	    //pay fail

	}
}
```