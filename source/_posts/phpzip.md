---
title: php实现打包下载资源
tags:
  - php 
  - linux
categories:
  - php
date: 2017-03-20 19:02:17
---
<Excerpt in index | 首页摘要> 

 应用场景为
- 用户上传资源到服务器
- 根据特定id下载对应id的资源目录

注意事项
- 上传的资源可能``name``字段可能不带后续，要在``type``字段中截取。（php的``$_FILE``）
- 若是跨目录（php文件和资源目录不在同一级），获取目录必须要用绝对路径，压缩文件时要注意获取想要的路径。（eg：资源目录为 /var/www/project/assets/file/1/xx.txt。想要压缩是 /file/1/xxx.txt）
<!-- more -->
<The rest of contents | 余下全文>

# php实现代码 #

```php
// 压缩打包文件
public function do_addFileToZip($path,$zip){
    $handler=opendir($path);
    while(($filename=readdir($handler))!==false){
        if($filename != "." && $filename != ".."){
            if(is_dir($path.'/'.$filename)){
                $this->do_addFileToZip($path.'/'.$filename, $zip);                
            }else { //将文件加入zip对象
                $new_path = str_replace("/var/www/uat/unimem/assets/images/photos/","",$path);
                / 添加后续
                $tmpArr = explode('_', $filename);
                $zip->addFile($path."/".$filename, $new_path."/".$filename.".".$tmpArr[0]);
           }
                        
        }
                
    }
    @closedir($path);
        
}

    public function do_down_all_order_photos($order_id=NULL)
    {
        if ($order_id) {
            $photos_path = $your_url;
            if (file_exists($photos_path)) {
                   // /打包文件
                   $select_file = $order_id.".zip";
                   $zip = new ZipArchive();
                if($zip->open($select_file, ZipArchive::OVERWRITE)=== TRUE){
                   $this->do_addFileToZip($photos_path, $zip); 
                   $zip->close(); 
    
                }
                //下面是输出下载;
                header ( 'Content-disposition: attachment; filename=' . basename ($select_file)  );
                header ( "Content-Type: application/octet-stream"  );
                header ( 'Content-Length: ' . filesize ($select_file) ); // 告诉浏览器，文件大小
                @readfile ( $select_file  );//输出文件;
                unlink($select_file)//删除打包文件
            } else {
                return_JSON_format('对不起,您要下载的文件不存在');
            }

        }
            
     }
```
