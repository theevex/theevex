<?php

error_reporting(0);
header('content-type:application/json;charset=utf8');
// header('charset=utf-8');
header('Access-Control-Allow-Origin: *');
header("Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept");
header("Cache-Control: no-store, no-cache, must-revalidate, max-age=0");
header("Cache-Control: post-check=0, pre-check=0", false);
header("Pragma: no-cache");


class Services
{
	public static function GetParam2Request()
	{
		$request = json_decode(file_get_contents('php://input'), true);
		if (isset($request)) :
			foreach ($request as $key => $value) {
				$_REQUEST[$key] = $value;
			}
		endif;
	}
}

Services::GetParam2Request();

switch ($_REQUEST['command']) {

	case "welcome":

		$result_arr = array(
			'code' => '200',
			'message' => 'ยินดีต้อนรับสู่ PHP Backend',
			'data' => []
		);

		echo json_encode($result_arr);
		break;
	case "upload-image":

		// var_dump($_REQUEST['images']);
		// exit();

		if (!empty($_REQUEST['images'])) {


			try {
				if (strpos($_REQUEST['images'], "base64")) {
					$lenght = strpos($_REQUEST['images'], "base64");
					$image64 = substr($_REQUEST['images'], $lenght + 7);
				} else {
					$image64 = $_REQUEST['images'];
				}

				$realImage = base64_decode($image64);
				$imagename = rand();
				// สร้างชื่อรูป
				$imageme = "/imageupload/" . $imagename . ".jpg";
				// คำสั0่ง insert รูปลงเครื่อง
				file_put_contents($imageme, $realImage);

				$result_arr = array(
					'code' => '200',
					'message' => 'อัปโหลดรูปภาพสำเร็จ',
					'data' => ["imagename" => $imageme]
				);
				// echo json_encode(array("code" => "200", "message" => "อัพโหลดรูปส่วนตัวสำเร็จ", "data" => array()));
			} catch (Exception $e) {
				$result_arr = array(
					'code' => '500',
					'message' => 'อัปโหลดรูปภาพไม่สำเร็จ',
					'data' => [$e]
				);
				// echo json_encode(array("code" => "400", "message" => "อัพโหลดรูปส่วนตัวไม่สำเร็จ", "data" => $e));
			}
		} else {
			$result_arr = array(
				'code' => '400',
				'message' => 'ไม่พบรูปภาพ',
				'data' => []
			);
		}
		echo json_encode($result_arr);
		break;

		// ไม่พบข้อมูลที่ค้นหา
	default:
		$result_arr = array(
			'code' => '404',
			'message' => 'ไม่พบข้อมูลที่ต้องการ',
			'data' => [],

		);
		echo json_encode($result_arr);

		break;
}

