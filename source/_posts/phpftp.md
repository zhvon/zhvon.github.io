---
title: phpftp上传和下载多个文件
tags:
  - php 
  - ftp
categories:
  - php
  - ftp
date: 2016-09-18 15:44:18
---
<Excerpt in index | 首页摘要> 
最近接到任务分前后台
前台：一个`表单`数据入库 + 资源上传（``ftp``）
后台：``list``所有表单 + 下载对应资源（``ftp``）

因为之前比较少（甚至没做过-。-）``ftp``相关编程
表单入库就不多说，这里主要记录下``ftp``的实现

本次开发没用什么框架和前端插件，主要都用``php``源码实现。
<!-- more -->
<The rest of contents | 余下全文>
# php ftp 多资源上传 #
## 上传html表单 ##
```html
<form enctype="multipart/form-data" action="" method="POST">
    <input name="upload_file[]" type="file">
    <input type="submit" value="提交">
</form> 
```

注意:``enctype="multipart/form-data"``这个是必须要写的,否则``$_FILES``数组是空的,得不到值.

## $_FILES ##
> ``$_FILES``数组内容如下: 
``$_FILES['upload_file']['name']`` 客户端文件的原名称。 
``$_FILES['upload_file']['type']``文件的 MIME 类型，需要浏览器提供该信息的支持，例如"image/gif"。 
``$_FILES['upload_file']['size']`` 已上传文件的大小，单位为字节。 
``$_FILES['upload_file']['tmp_name']`` 文件被上传后在服务端储存的临时文件名，一般是系统默认。可以在php.ini的upload_tmp_dir 指定，但用 putenv() 函数设置是不起作用的。 
``$_FILES['upload_file']['error']`` 和该文件上传相关的错误代码。

---

> ``['error']`` 是在 PHP 4.2.0 版本中增加的。下面是它的说明：(它们在PHP3.0以后成了常量) 
``UPLOAD_ERR_OK``
值：0; 没有错误发生，文件上传成功。 
``UPLOAD_ERR_INI_SIZE`` 
 值：1; 上传的文件超过了 php.ini 中 upload_max_filesize 选项限制的值。 
``UPLOAD_ERR_FORM_SIZE`` 
值：2; 上传文件的大小超过了 HTML 表单中 MAX_FILE_SIZE 选项指定的值。 
``UPLOAD_ERR_PARTIAL``
值：3; 文件只有部分被上传。 
``UPLOAD_ERR_NO_FILE``
值：4; 没有文件被上传。 
值：5; 上传文件大小为0. 

文件上传结束后，默认地被存储在了``临时目录``中，这时您必须将它从临时目录中删除或``移动``到其它地方，如果没有，则会被``删除``。也就是不管是否上传成功，脚本执行完后临时目录里的文件肯定会被删除。所以在删除之前要用PHP上传到ftp服务器，此时，才算完成了上传文件过程。


## ftp上传 ##

思路：
+ 利用表单提交，``$_FILE``获取提交到服务器中的临时文件
+ 判断提交的状态, 没有提交成功的资源，就不执行上传
+ 连接、登录``ftp``服务器
+ 利用``ftp_put``上传到ftp服务器

```php
//判断有没文件要上传
$has_upload = false;
foreach ($_FILES['upload_file']['error'] as $key => $error) {
    //有一个就证明需要上传
    if ($error == UPLOAD_ERR_OK) {
        $has_upload = true;
        break;
    }     
}
//上传文件处理
if ($has_upload) {
    $tar_path = your_path//目标路径 利用申请编号作为目录存放文件
    //$tar_path = 'test/';//测试路径
    $arrFile = $_FILES['upload_file'];//上传文件数组

    //连接ftp
    $ftp_server = "your_ftp_server";
    $user_name = "your_user_name";
    $passwd = "your_password";
    //连接ftp服务器
    $con_id = ftp_connect($ftp_server) or die("Couldn't connect to $ftp_server");
    //登录ftp
    $login_res = ftp_login($con_id, $user_name, $passwd);

    //利用ftp创建目录
    $mkres = ftp_mkdir($con_id,'flie/');
    $mkres = ftp_mkdir($con_id,'flie/'.$tar_path);
    foreach ($arrFile['tmp_name'] as $key => $tmp_name) {
    //上传到缓存成功了才进行put
    if ($arrFile['error'][$key] == UPLOAD_ERR_OK) {
        //ftp连接，目标文件，源文件，传送模式，二进制形式
        $res = ftp_put($con_id, 'flie/'.$tar_path.$arrFile['name'][$key], $tmp_name, FTP_BINARY);
        if ($res == 1) {
            echo 'upload'.' '.$arrFile['name'][$key].' '.'success!'."<br>";
        } else {
            echo 'upload'.' '.$arrFile['name'][$key].' '.'fail!'."<br>";
        }   
    }

}

ftp_close($con_id);
}
```

# php ftp 多资源下载 #


思路：
+ 连接``ftp``服务器
+ ``打包压缩``所需要的文件资源目录成``zip``
+ 把文件下载到``php``当前目录下临时文件
+ 断开``ftp``连接
+ 把刚下载回来文件通过``header``返回给浏览器端用户
+ ``删除``临时文件

```php
//递归压缩文件
function addFileToZip($path,$zip){  
    $handler=opendir($path); //打开当前文件夹由$path指定。
    while(($filename=readdir($handler))!==false){
        if($filename != "." && $filename != ".."){//文件夹文件名字为'.'和‘..’，不要对他们进行操作
            if(is_dir($path."/".$filename)){// 如果读取的某个对象是文件夹，则递归
                addFileToZip($path."/".$filename, $zip);
            }else{ //将文件加入zip对象
                $zip->addFile($path."/".$filename);
            }
        }
    }
    @closedir($path);
}

//服务器地址
$phpftp_host = "ftplocalhost";
//服务器端口
$phpftp_port = 21;
//用户名
$phpftp_user = "name";
//口令
$phpftp_passwd = "passwrd";
//获取文件名
$select_file = 'images.zip';

//连接ftp服务器
$ftp = ftp_connect($phpftp_host, $phpftp_port);
if ($ftp) {
    //登录
    if (ftp_login($ftp, $phpftp_user, $phpftp_passwd)) {
        //压缩资源目录 这里是 images/
        $zip=new ZipArchive();
        if($zip->open(select_file, ZipArchive::OVERWRITE)=== TRUE){
            addFileToZip('images/', $zip); //调用方法，对要打包的根目录进行操作，并将ZipArchive的对象传递给方法
            $zip->close(); //关闭处理的zip文件的
        }
        //创建唯一的临时文件
        $tmpfile = tempnam( getcwd()."/", "temp" );
        //下载指定的文件到临时文件
        if (ftp_get($ftp, $tmpfile, $select_file, FTP_BINARY)) {
            //关闭连接
            ftp_quit($ftp);
            header("content-type: application/octet-stream");
            //content-disposition:inline; 表示可以在线打开文件！
            header("content-disposition: attachment; filename=" . $select_file);
            readfile($tmpfile);
            //删除临时文件
            unlink($tmpfile);
            exit;
        }
        unlink($tmpfile);
    }
}
ftp_quit($ftp);
```
---

目前实现方式不是最优，日后有机会再优化。
