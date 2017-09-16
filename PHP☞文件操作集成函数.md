#PHP☞文件操作集成函数
***

###file_get_contents()
>是把整个文件读入一个字符串中。该函数返回字符串或FALSE。比如：ile_get_contents("http://www.baidu.com");

###file_get_contents()使用条件
>file_get_contents 需要php.ini里开启allow_url_fopen

###file_get_contents()函数和file()函数区别
>1.file()是把整个文件读入一个数组中，然后将文件作为一个数组返回。数组中的每个单元都是文件中相应的一行，包括换行符在内。如果失败，则返回 false。  
>2.file_get_contents()是返回一个字符串。

###file_get_contents()实现POST请求
	function phpPost($url,$post = null){
		if (is_array($post)) {
			ksort($post);
			$content = http_build_query($post);
			$content_length = strlen($content);
			$options = array(
				'http' => array(
					'method' => 'POST',
					'header' =>
					"Content-type: application/x-www-form-urlencoded\r\n" .
					"Content-length: $content_length\r\n",
					'content' => $content
					)
				);
			return file_get_contents($url, false, stream_context_create($options));
		}
		else{
			logResult('POST错误：'.$url."\n".print_r($post,true));
			return false;
		}
	}
	请求参数：
	$param = array("opt"=>"del", "data"=>$val,"user"=>$user);
	$return = $phpPost("http://121.201.12.60/dnspro/dnsbroker/", $param);

###file_put_content
>1.file_put_contents() 函数把一个字符串写入文件中。与依次调用 fopen()，fwrite() 以及 fclose() 功能一样。  

>2.该函数将返回写入到文件内数据的字节数。


###file_put_contents和f系列函数效率问题
>file_put_contents每次都有对product.csv进行打开、写入、关闭，重复100000次，效率可想而知多么低效。
>
	<?php
	for($p = 1; $p <= 100000; $p++){
		file_put_contents('product.csv', 'contents....', FILE_APPEND);
	}

>高效且保护文件在使用过程中受保护的写法是这样的【推荐】：
>
	$fp = fopen('product.csv', 'cb');
>
	flock($fp, LOCK_EX | LOCK_NB);
	fwrite($fp, "title1, title2\n");
	//fputcsv($fp, array('title1', 'title2'));
>
	for($p = 1; $p <= 100000; $p++){
		fwrite($fp, "content1, content2\n");
		//fputcsv($fp, array('content1', 'content2'));
	}
	flock($fp, LOCK_UN);
	fclose($fp);
