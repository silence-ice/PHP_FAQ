#cURL的使用
***

###cURL概念
>cURL可以使用URL的语法模拟浏览器来传输数据，因为它是模拟浏览器，因此它同样支持多种协议，FTP, FTPS, HTTP, HTTPS,  TELNET, FILE 等协议都可以很好的支持.主要使用场景如下：
>
>>网页资源：编写网页爬虫，获取京东商城的商品数量、价格、评论等。

>>WebService数据接口资源：动态获取接口数据，比如天气、号码归属地、身份证正确格式等。

>>FTP服务器资源：下载FTP服务器的文件

>>其他资源：所有网络上的资源都可以用CURL访问和下载。

###cURL请求步骤
>1.初始化，创建一个新cURL资源
>
>2.设置URL和相应的选项
>
>3.抓取URL并把它传递给浏览器
>
>4.接收数据，关闭cURL资源并释放系统资源

###使用GET获取数据

	public function curlGetShow(){
	  $ch  = curl_init();
	  curl_setopt($ch, CURLOPT_URL, 'http://www.baidu.com');
	  curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
	  curl_setopt($ch, CURLOPT_TIMEOUT, 2);
	  $data = curl_exec($ch);
	  curl_close($ch);
	  // echo $data;
	  echo str_replace("百度", "silence", $data);
	}

###浏览器的编码方式：
>application/x-www-form-urlencoded： 窗体数据被编码为名称/值对。这是标准的编码格式。
>
>multipart/form-data： 窗体数据被编码为一条消息，上传二进制数据; form里面的input的值都会以二进制的方式传过去。
>
>text/plain： 窗体数据以纯文本形式进行编码，其中不含任何控件或格式字符。
	
###form的常用的编码方式：
>>application/x-www-form-urlencoded和multipart/form-data，默认为application/x-www-form-urlencoded。
>
	i. 如果action为get时候，浏览器用x-www-form-urlencoded的编码方式把form数据转换成一个字串
		（name1=value1&name2=value2...），然后把这个字串append到url后面，用?分割，加载这个新的url。
	ii. 如果action为post时候，浏览器把form数据封装到http body中，然后发送到server。 
>>如果没有type=file的控件，用默认的application/x-www-form-urlencoded就可以了。如果有type=file的话，就要用到multipart/form-data了。浏览器会把整个表单以控件为单位分割，并为每个部分加上Content-Disposition,Content-Type等信息，并加上分割符(boundary)。

###使用POST方式获取数据
>CURLOPT_USERAGENT参数：
>>什么是UserAgent？UserAgent简称（UA），是一个只读的字符串，它声明了浏览器用于 HTTP 请求的用户代理头的值。简单来说，就是“声明用什么浏览器来打开目标网页”。
>
>>不同的UserAgent会得到不同的网页请求了，例如手机浏览器和PC浏览器，就会得到不同的网页文件。举个例子，假如PC浏览器和手机浏览器，打开www.webkaka.com，就会得到不同的结果页，这其实就是UserAgent的不同的结果。

>CURLOPT_URL参数：设置访问的URL
>
>CURLOPT_HEADER参数：将头文件的信息作为数据流输出
>
>CURLOPT_RETURNTRANSFER参数：执行后不直接打印出来
>
>CURLOPT_POST参数：发送POST请求
>
>CURLOPT_HTTPHEADER参数：发送header头部信息
>
>CURLOPT_USERAGENT参数：发送浏览器头部信息
	public function curlPostShow(){
	  $data = 'theCityCode=2013&theUserID=';
	  $url = 'http://www.webxml.com.cn/WebServices/WeatherWS.asmx/getWeather';
	  $header_date = array("application/x-www-form-urlencoded;charset=utf-8","Content-Length: ".strlen($data));
	  $agent_date = $_SERVER['HTTP_USER_AGENT'];
	  $curlobj = curl_init();
	  curl_setopt($curlobj, CURLOPT_URL, $url);
	  curl_setopt($curlobj, CURLOPT_HEADER, 0);
	  curl_setopt($curlobj, CURLOPT_RETURNTRANSFER, 1);
	  curl_setopt($curlobj, CURLOPT_POST, 1);
	  curl_setopt($curlobj, CURLOPT_POSTFIELDS, $data);
	  curl_setopt($curlobj, CURLOPT_HTTPHEADER, $header_date); 
	  curl_setopt($curlobj, CURLOPT_USERAGENT, $agent_date); 
	  $rtn = curl_exec($curlobj);
	  if(!curl_errno($curlobj)){
	      print_r($rtn);
	  } else {
	      echo 'cURL error: '.curl_error($curlobj);
	  }
	  curl_close($curlobj);
	}

###FTP下载文件

     /**
     * ftp下载文件
     */
    public function ftpDownFile(){
      $url = 'ftp://192.168.1.100/downloaddemo.txt';

      $curlobj = curl_init();
      curl_setopt($curlobj, CURLOPT_URL, $url); //设置访问的URL
      curl_setopt($curlobj, CURLOPT_HEADER, 0); //将头文件的信息作为数据流输出
	  //执行后不直接打印出来
      curl_setopt($curlobj, CURLOPT_RETURNTRANSFER, 1); 
      curl_setopt($curlobj, CURLOPT_TIMEOUT, 300);
	  //传递一个形如[username]:[password]风格的字符串,作用PHP去连接。
      curl_setopt($curlobj, CURLOPT_USERPWD, "silence:123456");

      $outfile = fopen('test.txt', 'wb');//保存到本地的文件名
      curl_setopt($curlobj, CURLOPT_FILE, $outfile);//这个文件将是你放置传送的输出文件
      $rtn = curl_exec($curlobj);

      if(!curl_errno($curlobj)){
          print_r($rtn);
      } else {
          echo 'cURL error: '.curl_error($curlobj);
      }
      curl_close($curlobj);
    }
###FTP上传文件

     /**
     * ftp上传文件
     */
    public function ftpUploadFile(){
      $url = 'ftp://192.168.1.100/ftp01_uploaded.php';
      $localfile = 'ftp01.php';
      $fp = fopen($localfile);

      $curlobj = curl_init();
      curl_setopt($curlobj, CURLOPT_URL, $url); //设置访问的URL
      curl_setopt($curlobj, CURLOPT_HEADER, 0); //将头文件的信息作为数据流输出
      curl_setopt($curlobj, CURLOPT_RETURNTRANSFER, 1); //执行后不直接打印出来
      curl_setopt($curlobj, CURLOPT_TIMEOUT, 300);
      //传递一个形如[username]:[password]风格的字符串,作用PHP去连接。
      curl_setopt($curlobj, CURLOPT_USERPWD, "silence:123456");

      curl_setopt($curlobj, CURLOPT_UPLOAD, 1);//启用时允许文件传输
      curl_setopt($curlobj, CURLOPT_INFILE, $fp);//这个文件是你传送过来的输入文件。
      curl_setopt($curlobj, CURLOPT_INFILESIZE, filesize($localfile));
      $rtn = curl_exec($curlobj);
      curl_close($curlobj);

      if(!curl_errno($curlobj)){
          print_r($rtn);
      } else {
          echo 'cURL error: '.curl_error($curlobj);
      }
    }

###下载HTTPS的资源

    /**
     * 下载网络上的HTTPS的资源
     */
    public function ftpDownResource(){
      $url = 'https://ajax.aspnetcdn.com/ajax/jquery.validate/1.12.0/jquery.validate.js';

      $curlobj = curl_init();
      curl_setopt($curlobj, CURLOPT_URL, $url); //设置访问的URL
      curl_setopt($curlobj, CURLOPT_RETURNTRANSFER, 1); //执行后不直接打印出来

      //设置HTTPS支持
      date_default_timezone_set('PRC');//因为HTTPS的SSL访问证书是有时效性的
      curl_setopt($curlobj, CURLOPT_SSL_VERIFYPEER, 0);//终止从服务器进行验证
      $output = curl_exec($curlobj);
      curl_close($curlobj);
      echo $output;
    }
###实现慕课网登陆，并跳转到个人主页

    /**
     * 登陆官网下载个人主页【POST】
     */
    public function mukeLogin(){  
      $data = 'username=548491578@qq.com&password=huangbin0236&remember=1';
      $header_date = array("application/x-www-form-urlencoded;charset=utf-8","Content-Length: ".strlen($data));

      $ch = curl_init( );  
      curl_setopt($ch, CURLOPT_URL, "http://www.imooc.com/user/login"); 
      curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 

      //设置cookie,session
      date_default_timezone_set('PRC');//使用cookie时,必须先设置时区
      curl_setopt($ch, CURLOPT_COOKIESESSION, true); //支持session
      curl_setopt($ch, CURLOPT_COOKIEJAR, "mucookiefile");//连接关闭以后,存放cookie信息的文件名称
      curl_setopt($ch, CURLOPT_COOKIEFILE, "mucookiefile");
      curl_setopt($ch, CURLOPT_COOKIE, session_name().'='.session_id());
      curl_setopt($ch, CURLOPT_HEADER, 0);//不能打印头部信息
      curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);//能让CURL支持页面跳转

      //POST提交设置
      curl_setopt($ch, CURLOPT_POST, true);  
      curl_setopt($ch, CURLOPT_POSTFIELDS, $data); 
      curl_setopt($ch, CURLOPT_HTTPHEADER, $header_date); //发送header头部信息
      $loginContent = curl_exec($ch);  

      //跳转urlhttp://www.imooc.com/u/3886915
      curl_setopt($ch, CURLOPT_URL, "http://www.imooc.com/space/index"); 
      curl_setopt($ch, CURLOPT_POST, 0); //下载页面不用POST
      curl_setopt($ch, CURLOPT_HTTPHEADER, array("Content-type: text/xml"));
      $output = curl_exec($ch);  
      curl_close($ch); //此时才关闭cURL
      echo $output;// echo $loginContent;
    } 
