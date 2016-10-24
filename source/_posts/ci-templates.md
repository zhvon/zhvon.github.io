---
title: ci实现templates实现多个模板目录切换
tags:
  - ci
  - php
categories:
  - ci
  - php
date: 2016-10-24 12:33:12
---
<Excerpt in index | 首页摘要> 

在CI中，我们为了方便平时的开发习惯，希望把所有的视图模板放置在根目录下的``templates``目录下。但是官方并没有给出明确的修改方法，因此我们需要自己对此进行修改。

在CI中，视图``view``的文件存放目录是在``system/core/Loader.php``中规定的，我们可以通过撰写自己的核心扩展类来修改这一规定。

假设：
+ 我们要有两个模板目录需要进行切换
+ ``trunk``为发布版本的模板目录
+ ``branch``为测试版本的模板目录

在``application/core``目录下增加文件``MY_Loader.php``，文件的内容如下：
```php
<?php
if (!defined('BASEPATH')) exit('No direct access allowed.');

class MY_Loader extends CI_Loader{
	public function __construct(){
		parent::__construct();
		$this->_ci_view_paths = array('templates/brank' => TRUE);
	}   
}
```

这样，你就可以把根目录下的templates目录当做视图目录了，按开发需要修改``_ci_view_paths``的路径即可。
enjoy it ！
<!-- more -->
<The rest of contents | 余下全文>
