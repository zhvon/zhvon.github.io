---
title: summernote 上传文件配置
tags:
  - php
  - summernote
categories:
  - php
  - summernote
date: 2017-07-27 11:03:44
---
<Excerpt in index | 首页摘要> 

[summernote](http://summernote.org/)是一款简单而又强大的基于js的富文本编辑器，常用于各个论坛及网站的发言编辑区域，可自定制性极强 。

** 默认情况下 **
``summernote``将图片以``base64``编码的方式变成字符串，只是存储到数据库的时候你会发现，一篇文章会占用极大的空间，尤其是在文章图片较多而文字较少的时候。

** 修改之后 **
在插入编辑区域之前就让图片可以上传到服务器某个路径下，然后将图片的路径存储到数据库里，而不是将整个图片直接存储。

<!-- more -->
<The rest of contents | 余下全文>

# 配置Summernote #

- 首先先配置一个简单的``summernote``

```javascript
$(function(){
  
  $('.summernote').summernote({
    toolbar: [
      ['style', ['style']],
      ['font', ['bold', 'italic', 'underline', 'clear']],
      ['color', ['color']],
      ['para', ['ul', 'ol', 'paragraph']],
      ['height', ['height']],
      ['table', ['table']],
      ['insert', ['link', 'hr','picture']],
      ['view', ['fullscreen', 'codeview']]
    ],
    height: 700,
  });
});
```
```html
 <textarea id="help_content" name="help_content" class="form-control input-sm summernote" placeholder="内容" rows="3"></textarea>
```

# 修改Summernote配置 #

## 前端 ##

```javascript
$('.summernote').summernote({
	toolbar: [
	  ['style', ['style']],
	  ['font', ['bold', 'italic', 'underline', 'clear']],
	  ['color', ['color']],
	  ['para', ['ul', 'ol', 'paragraph']],
	  ['height', ['height']],
	  ['table', ['table']],
	  ['insert', ['link', 'hr','picture']],
	  ['view', ['fullscreen', 'codeview']]
	],
	height: 700,

	//上传图片的接口
	callbacks:{      //本文的主题来了，调用官方提供的callbacks接口，用来自定义
	  onImageUpload: function(files) {      //onImageUpload代表图片上传事件，默认将图片变为base64的那个事件
	      var data=new FormData();        //html5提供的formdata对象，将图片加载为数据的容器
	      data.append('image_up',files[0]);  //加载选中的第一张图片，并给这图片数据标记一个'image_up'的名称
	      //调用上传图片
	      $.ajax({
	          url: '',     //上传图片请求的路径
	          method: 'POST',            //方法
	          data:data,                 //数据
	          processData: false,        //告诉jQuery不要加工数据
	          contentType: false,        //<code class="javascript comments"> 告诉jQuery,在request head里不要设置Content-Type
	          success: function(data) {  //图片上传成功之后，对返回来的数据要做的事情
	              if (data['message']=='success') {
	                  $('.summernote').summernote('insertImage',data['url']);       //调用内部api——insertImage以路径的形式插入图片到文本编辑区
	                  console.log(data['url']);
	              }
	              else{
	                  // alert(data['message']);
	                  console.log(data['message']);
	                  console.log(data['url']);
	              }
	          }
	      });
	   }
	}
	//上传图片回调结束
});
```

## 服务端 ##

基于php``codeigniter``框架
- 接受``POST``过来的图片流数据
- 在``image``表新增一条数据，来源不同的图片用``type``区分
- 保存图片，生成处理过的``cache image``（基于``codeigniter``框架的``image_tools``library）
- 返回``json``格式的反馈数据

```php
if (isset($_FILES['image_up']) && $_FILES['image_up']['error']==UPLOAD_ERR_OK) {
	$json['message'] = 'success';
	// insert the image data
	$imageInsertData['image_index_id'] = 0;
	$imageInsertData['image_sort'] = 0;
	$imageInsertData['image_type'] = 'help';
	$imageInsertId = $this->image_model->insert($imageInsertData);

	if ($imageInsertId) {
		// upload the image
		$res = @move_uploaded_file($_FILES['image_up']['tmp_name'], $image_path.$imageInsertId);
		if (!empty($res)) {
			// $json['url'] = base_url('assets/images/images').'/'.$imageInsertId;
			$data['quality'] = 85;
			$data['width'] = 1280;
			// $data['height'] = 720;
			// 内置自定义函数 处理图片
			$json['url'] = get_cache_path('images', $image_path, $imageInsertId, time(), $data);
			if (empty($json['url'])) {
				$json['message'] = '图片地址获取失败';
			}
		}else{
			$json['message'] = '上传失败';
		}
	}else{
		$json['message'] = '新增数据失败';
	}
	// 内置自定义函数
	return_JSON_format($json);
}
```
** 处理图片接口 ** 
```php

if(!function_exists('get_cache_path'))
{
	function get_cache_path($dir_name, $original_path, $image_name, $date_modify, $data=array()){
		$CI =& get_instance();
		$image_cache_path = FCPATH.'assets/images/cache/';

		// Load the image library
		$CI->load->library('image_lib');

		$config['image_library'] = 'gd2';
		$config['maintain_ratio'] = TRUE;
		$config['quality'] = empty($data)?40:$data['quality'];
		$config['width'] = empty($data)?400:$data['width'];
		$config['height'] = empty($data)?400:$data['height'];

		$image_filename = $image_name.'_'.$config['width'].'x'.$config['height'].'_'.$date_modify;
		$resize_image = '';


		if( !file_exists($image_cache_path.$dir_name.'/'.$image_filename) )
		{

			// Check and create the styles folder in cache folder
			if( !is_dir($image_cache_path.$dir_name) )
			{
				mkdir($image_cache_path.$dir_name, 0775, TRUE);
			}

			// Create the config for image library
			if(file_exists($original_path.$image_name))
			{
				$config['source_image'] = $original_path.$image_name;
				$config['new_image'] = $image_cache_path.$dir_name.'/'.$image_filename.'.jpg';

				$CI->image_lib->initialize($config);

				if ( !$CI->image_lib->resize() ){
				  echo $CI->image_lib->display_errors();
				}else{
					// 刪除圖片後綴
					rename($image_cache_path.$dir_name.'/'.$image_filename.'.jpg', $image_cache_path.$dir_name.'/'.$image_filename);
					$resize_image = base_url('assets/images/cache/'.$dir_name.'/'.$image_filename);
				}

			}
		}else{
			$resize_image = base_url('assets/images/cache/'.$dir_name.'/'.$image_filename);
		}

		return $resize_image;
		
	}
}

```

---

至此，``summernote``的上传图片功能修改完毕。