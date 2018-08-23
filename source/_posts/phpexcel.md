---
title: phpexcel简单应用
tags:
  - php
  - phpexcel
  - composer
categories:
  - php
date: 2017-10-18 16:15:41
---
<Excerpt in index | 首页摘要> 

> 背景
- 使用``phpexcel``进行数据导出导入
- 服务端使用CI（Codeigniter）框架
- 本次用到的第三方支付依赖是：phpoffice/phpexcel

# 安装和使用 #

## 依赖管理工具：Composer ##

详情看这篇文章，已经有比较全的介绍。[传送门](http://space.zhvon.com/2017/10/17/app-paid/)

## 从github中下载 ##

[github地址](https://github.com/PHPOffice/PHPExcel)

# 把excel导入到数据库 #
- 上传``excel``文件到服务器
- ``file_path``为文件路径
```php
	function get_excel_data($file_path){
		// 使用composer安装的，用require_once()引入
		include  APPPATH.'libraries/PHPExcel/IOFactory.php';
		$inputFileType = PHPExcel_IOFactory::identify($file_path);
		$objReader = PHPExcel_IOFactory::createReader($inputFileType);
		$objPHPExcel = $objReader->load($file_path);
		$sheetData = $objPHPExcel->getActiveSheet()->toArray(null,true,true,true);
		return $sheetData;
	}
```

<!-- more -->
<The rest of contents | 余下全文>

# 导出到 excel #

```php
	function export_to_excel($data, $type='email'){
		// 使用composer安装的，用require_once()引入
		include APPPATH.'libraries/PHPExcel.php';
		// include  APPPATH.'libraries/PHPExcel/IOFactory.php';
		
		$objPHPExcel = new PHPExcel();
		$date = date("Y-m-d");
		$fileName = $type."-export {$date}.xls";
		//适合把表中数据导入Excel文件中，多数据循环设置值
		switch ($type) {
			// 单列无表头
			case 'email':
				foreach($data as $key=> $value) {
					$key+=1;
					$objPHPExcel->setActiveSheetIndex(0)->setCellValue('A'.$key,$value[1]);
				}
				break;
			// 多列列有表头
			case 'search':
				$objPHPExcel->setActiveSheetIndex(0)->setCellValue('A1','#')
													->setCellValue('B1','客户名称')
													->setCellValue('C1','Email 地址')
													->setCellValue('D1','状态')
													->setCellValue('E1','创建时间')
													->setCellValue('F1','最后修改时间');

				foreach($data as $key=> $value) {
					$key+=2;
					$objPHPExcel->setActiveSheetIndex(0)->setCellValue('A'.$key,$value[0])
														->setCellValue('B'.$key,$value[1])
														->setCellValue('C'.$key,$value[2])
														->setCellValue('D'.$key,$value[3])
														->setCellValue('E'.$key,$value[4])
														->setCellValue('F'.$key,$value[5]);
				}
				break;
			default:
				# code...
				break;
		}
		// 重命名表  
		$objPHPExcel->getActiveSheet()->setTitle('email');

		// 设置活动单指数到第一个表,所以Excel打开这是第一个表  
		$objPHPExcel->setActiveSheetIndex(0);
		// 将输出重定向到一个客户端web浏览器(Excel2007) 
		// header('Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet');
		// 将输出重定向到一个客户端web浏览器(Excel2005)
		header('Content-Type: application/vnd.ms-excel');
		header('Content-Disposition: attachment;filename="'.$fileName.'"');
		header('Cache-Control: max-age=0');
		// If you're serving to IE 9, then the following may be needed
		header('Cache-Control: max-age=1');

		// If you're serving to IE over SSL, then the following may be needed
		header ('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); // Date in the past
		header ('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT'); // always modified
		header ('Cache-Control: cache, must-revalidate'); // HTTP/1.1
		header ('Pragma: public'); // HTTP/1.0
		//要是输出为Excel2007,使用 Excel2007对应的类，生成的文件名为.xlsx.如果是Excel2005,使用Excel5,对应生成.xls文件
		// $objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel2007');
		$objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel5');
		//支持浏览器下载生成的文档
		$objWriter->save('php://output');
		// return true;
	}
```
