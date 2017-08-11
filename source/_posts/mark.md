---
title: 人在江湖（持续更新）
tags:
  - php
  - php
  - 积累
  - app
  - api
categories:
  - php
date: 2017-08-11 15:20:09
top: 3333
---
<Excerpt in index | 首页摘要> 

# 前菜 #
> 脱离游戏开发也差不多两年，在进入外包公司（website，app）开发以来，学到了很多技术和非技术的东西。就在这里落下烙印。

> 创文的时候比较辛苦，大大小心也经历过三四个项目了，整理起来比较吃力，也比较杂乱，之后会根据日期和出处项目进行整理。
<!-- more -->
<The rest of contents | 余下全文>

# 正片 #

## 2017.08.11 更新 ##

### ``API``返回格式 ###
- **项目：stage app**
- **内容：``API``返回格式**
```json
{
    "success":"true",
    "message":"message",
    "data"   :[],
}
```

---

### 打印数组变量 ###
- **项目：stage app**
- **内容：打印数组变量**
```php
function show($thisArray){
	echo '<pre>';
	print_r($thisArray);
	echo '</pre>';
}
```

---

### ``PHP``封装``JSON``返回接口 ###
- **项目：stage app**
- **内容：``PHP``封装``JSON``返回接口**

```php 
function return_JSON_format($thisData){
	header("Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept, dataType");
	header("Access-Control-Allow-Origin: *");
	header('Access-Control-Allow-Methods: GET, POST, PUT');
	header('Content-Type: application/json');
	echo json_encode($thisData, JSON_PRETTY_PRINT);
	exit;
}
```

---

### 根据经纬度获取附近的目标 ###
- **项目：stage app**
- **内容：根据经纬度获取附近的目标**
```php 
function get_location_nearby($latitude, $longitude, $table, $index_id=false){
	$CI =& get_instance();
	$CI->load->database('default');
	$where = '';
	$select = '';
	$now = time();
	if (!empty($index_id)) {
		$where = $where.' AND '.$table.'_id <> '.$index_id;
	}
	if ($table=='member') {
		$select = "member_last_use";
		$where = $where." AND ($now-member_last_use)<(60*60*24)";
	}
	$sql = "
			SELECT ".$table."_id, ".$table."_latitude, ".$table."_longitude, ".$select.", SQRT(POW(69.1 * (".$table."_latitude - $latitude), 2) + POW(69.1 * ($longitude - ".$table."_longitude) * COS(".$table."_latitude / 57.3), 2)) AS distance FROM ".$table." WHERE ".$table."_deleted = 'N' ".$where." ORDER BY distance LIMIT 50
			";
	// echo $sql;
	$query = $CI->db->query($sql); 
    return $query->result_array();
}
```

---

### resize图片生成cache文件 ###
- **项目：stage app**
- **内容：``resize``图片生成``cache``文件**
```php
function get_cache_path($dir_name, $original_path, $image_name, $date_modify){
	$CI =& get_instance();
	$image_cache_path = FCPATH.'assets/images/cache/';

	// Load the image library
	$CI->load->library('image_lib');

	$config['image_library'] = 'gd2';
	$config['maintain_ratio'] = TRUE;
	$config['quality'] = 80;
	$config['width'] = 400;
	$config['height'] = 400;

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
```

---

### 在数组中获取特定前缀的字段 ###
- **项目：stage app**
- **内容：在数组中获取特定前缀的字段**
```php
function get_array_prefix($thisPrefix, $thisData = array()){
	$thisArray = array();
	if(!empty($thisData)){
		foreach($thisData as $key => $value){
			if(strpos($key, $thisPrefix, 0) === 0){
				$thisArray[$key] = $value;
			}
		}
	}
	return $thisArray;
}
```

---

### 获取该 location_id 下所有子location ###
- **项目：stage app**
- **内容：获取该``location_id``下所有子``location``**
```php
function get_location_child($location_id=1){
	$CI =& get_instance();
	$CI->load->database('default');
	$sql = "
			SELECT location_id, location_name, location_location_id
			FROM (
				SELECT * 
				FROM location
				ORDER BY location_location_id, location_id) AS sorted, 
				(SELECT @pv :=$location_id) AS initialisation
			WHERE (
				FIND_IN_SET( location_location_id, @pv ) >0
				AND @pv := CONCAT( @pv ,  ',', location_id )
			)
			";
	$query = $CI->db->query($sql); 
    return $query->result_array();
}
```

---

### 生成和检查``token``用于登录检验 ###
- **项目：stage app**
- **内容：生成和检查``token``用于登录检验**

**生成``token``**
```php
$thisMessageArray = array(
	'member_id'         => $data['members']->member_id,
	'member_login_name' => $data['members']->member_login_name,
	// 'member_expire'  => (60 * 60 * 24 * 7),
	'member_expire'     => $this->config->item('member_expire'),
	'member_device_id'  => $thisValue['member_device_id']
);
$thisMessage = serialize($thisMessageArray);
$thisSecret = 'DreamOver';
$encrypted_string = $this->encrypt->encode($thisMessage, $thisSecret);

$thisPOST['member_id']        = $data['members']->member_id;
$thisPOST['member_password']  = '';
$thisPOST['member_device_id'] = $thisValue['member_device_id'];
$thisPOST['member_token']     = base64_encode($encrypted_string);
```
**校验``token``**
```php
function check_token($return_login_success = false){
	$CI =& get_instance();
	$CI->load->model('member_model');
	
	$thisValue = ($CI->input->method(true) == 'POST') ? $CI->input->post() : $CI->uri->uri_to_assoc(4);
	// log_message('debug','check_token...thisValue...'.json_encode($thisValue));
	if (!isset($thisValue['member_token'])) {
		$result['success'] = false;
		$result['message'] = 'MEMBER_TOKEN_GONE';
		return_JSON_format($result);
	}
	$thisSelect = array(
		'where' => array(
			'member_token' => $thisValue['member_token']
		),
		'return' => 'row'
	);
	$members = $CI->member_model->select($thisSelect);
	if($members){
		$thisMessageArray = resolve_token($thisValue['member_token']);

		/* check device id */
		if($members->member_device_id != $thisMessageArray['member_device_id']){
			$result['success'] = false;
			$result['message'] = 'WRONG_USER_DEVICE_ID';
			return_JSON_format($result);
		}

		/* check last use */
		if($members->member_last_use + $thisMessageArray['member_expire'] >= time()){
			if($members->member_last_use + (60 * 60) >= time()){
				// update user last use per hour
				$thisPOST['member_id'] = $members->member_id;
				$thisPOST['member_password'] = '';
				$thisPOST['member_last_use'] = time();
				$CI->member_model->update($thisPOST);
			}

			// return $data['members'][0]->member_id;
			if($return_login_success){
				$result['success'] = true;
				$result['message'] = 'LOGIN_SUCCESS';
				return_JSON_format($result);	
			}
		}else{
			$result['success'] = false;
			$result['message'] = 'MEMBER_TOKEN_EXPIRED';
			return_JSON_format($result);
		}
	}else{
		$result['success'] = false;
		$result['message'] = 'WRONG_MEMBER_TOKEN';
		return_JSON_format($result);
	}
}
```

---

### 生成验证码和订单号 ###
- **项目：stage app**
- **内容：生成验证码和订单号**
```php
$length=6;
$code = rand(pow(10,($length-1)), pow(10,$length)-1);
```
```php
//生成16位訂單號
private function gen_booking_no()
{
  $yCode = array('A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J');
	$orderSn = $yCode[intval(date('Y')) - 2017] . strtoupper(dechex(date('m'))) . date('d') . substr(time(), -5) . substr(microtime(), 2, 5) . sprintf('%02d', rand(0, 99)); 
	return $orderSn;
}
```

---

### 根据用户类型或用户组生成获取权限 ###
- **项目：stage app**
- **内容：根据用户类型或用户组生成获取权限**
```php
// 模块_操作（ selec update insert delete or more）
function get_permission($user_type){
	$CI =& get_instance();
	$sql = "SELECT concat(permission_class, '_', permission_action) as permission_name 
			FROM permission 
			LEFT JOIN permission_usertype on permission_usertype_permission_id = permission_id 
			WHERE permission_deleted = 'N' 
			AND permission_usertype_usertype = '$user_type'
		";
	$query = $CI->db->query($sql);
	return convert_object_to_array($query->result(), 'permission_name');;
}
```

---

### linux定时任务 ###
- **项目：stage app**
- **内容：linux定时任务**
```php
// linux 定时任务 检查是否过期
// crontab -e 编辑
// crontab -r 清除所有任务
// * * * * * /usr/bin/curl http://localhost/stage/api/ds_function_helper/test
```

---

### 根据数组中特定key排序 ###
- **项目：stage app**
- **内容：根据数组中特定key排序**
```php
/*
* array([0]=>{key=>value,key1=>value1...})
*/
// order for pice
if (!empty($order)) {
	$tmp[] = $thisData['stadiums'][$key]->stadium_min_price;
}
// 降序
array_multisort($tmp, SORT_DESC, $thisData['stadiums']);
// 升序
array_multisort($tmp, SORT_ASC, $thisData['stadiums']);
```

---

### SQL查找字符串中是否存在某个value ###
- **项目：stage app**
- **内容：SQL查找字符串中是否存在某个value**
```sql
eg:
# $today=2
# plan_day 为逗号分隔的集合 1,2,3,4,5
find_in_set('$today',plan_day)
```

---

### 解析上传图片为``base64`` ###
- **项目：stage app**
- **内容：解析上传图片为``base64``**
```php
$picture = $thisPOST['picture'];
$url = explode(',',$picture);

if (isset($url[1])) {
	$result = file_put_contents($user_path.$thisInsert, base64_decode($url[1]));//返回的是字节数
	$thisPOST['user_image'] = base_url('assets/images/users').'/'.$thisInsert;
	$this->user_model->update(array('user_id'=>$thisInsert,'user_image'=>base_url('assets/images/users').'/'.$thisInsert));
}
```

---


### 检验邮箱和手机号码 ###
- **项目：stage app**
- **内容：检验邮箱和手机号码**
```php

// 校验邮箱格式
public function check_email($email) {
    $reg = "/^([0-9A-Za-z\\-_\\.]+)@([0-9a-z\\-_]+\\.[a-z]{2,3}(\\.[a-z]{2})?)$/i";
    if(preg_match($reg, $email)) return TRUE;
    return FALSE;
}

// 校验手机号码格式
public function check_phone($phone) {
    $reg = "/^[1][34578][0-9]{9}$/";
    if (preg_match($reg, $phone)) return TRUE;
}

```
