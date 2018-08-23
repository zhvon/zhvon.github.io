---
title: phpmailer简单使用
tags:
  - php
  - phpmailer
  - composer
categories:
  - php
date: 2017-10-18 14:59:12
---
<Excerpt in index | 首页摘要> 

> 背景
- 使用``phpmailer``进行发送``email``
- 服务端使用CI（Codeigniter）框架
- 本次用到的第三方支付依赖是：phpmailer/phpmailer

# 安装和使用依赖管理工具：Composer #

详情看这篇文章，已经有比较全的介绍。[传送门](http://space.zhvon.com/2017/10/17/app-paid/)

# 邮件示例 #

- 以smtp为示例
- 更多examples可以参考 https://github.com/PHPMailer/PHPMailer/tree/master/examples

```php
	use PHPMailer\PHPMailer\PHPMailer;
	use PHPMailer\PHPMailer\Exception;
	
	// send mail by phpmailer
	function send_mail($data)
	{	
		$smtp = $data['smtp'];
		$port = $data['port'];
		$secure = $data['secure'];
		$from = $data['from'];
		$password = $data['password'];
		$to = $data['to'];
		$subject = $data['subject'];
		$body = $data['body'];
		
		//Create a new PHPMailer instance
		$mail = new PHPMailer;
		//Tell PHPMailer to use SMTP
		$mail->isSMTP();
		//Enable SMTP debugging
		// 0 = off (for production use)
		// 1 = client messages
		// 2 = client and server messages
		$mail->SMTPDebug = 2;
		//Set the hostname of the mail server
		$mail->Host = $smtp;
		// use
		// $mail->Host = gethostbyname('smtp.gmail.com');
		// if your network does not support SMTP over IPv6
		//Set the SMTP port number - 587 for authenticated TLS, a.k.a. RFC4409 SMTP submission
		$mail->Port = $port;
		//Set the encryption system to use - ssl (deprecated) or tls
		$mail->SMTPSecure = $secure;
		//Whether to use SMTP authentication
		$mail->SMTPAuth = true;
		//Username to use for SMTP authentication - use full email address for gmail
		$mail->Username = $from;
		//Password to use for SMTP authentication
		$mail->Password = $password;
		//Set who the message is to be sent from
		$mail->setFrom($from);
		//Set an alternative reply-to address
		// $mail->addReplyTo('replyto@example.com', 'First Last');
		//Set who the message is to be sent to
		$mail->addAddress($to);
		//Set the subject line
		$mail->Subject = $subject;
		//Read an HTML message body from an external file, convert referenced images to embedded,
		//convert HTML into a basic plain-text alternative body
		$mail->msgHTML($body);
		//Replace the plain text body with one created manually
		// $mail->AltBody = 'This is a plain-text message body';
		//Attach an image file
		// $mail->addAttachment('images/phpmailer_mini.png');
		//send the message, check for errors
		if (!$mail->send()) {
		    // echo "Mailer Error: " . $mail->ErrorInfo;
		    return false;
		} else {
			return true;
		    // echo "Message sent!";
		    //Section 2: IMAP
		    //Uncomment these to save your message in the 'Sent Mail' folder.
		    #if (save_mail($mail)) {
		    #    echo "Message saved!";
		    #}
		}
	}
```

<!-- more -->
<The rest of contents | 余下全文>
