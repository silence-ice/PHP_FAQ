#CGI、FastCGI、php-fpm 之间的关系？  
***

##什么是CGI？
> 1.CGI，又叫通用网关接口；主要是HTTP服务器和其他机器数据通信的工具。  
> 2.一般Web Server接受浏览器的请求后，会判断请求资源属静态资源(CSS，JS，JPG)还是动态资源(PHP)；一般静态资源直接返回给浏览器渲染即可，而PHP文件之类的动态资源就需要CGI进行处理了。  
>>  具体处理过程如下：    
	1.nginx收到index.html，index.php文件。  
	2.nginx进行内容分发，如果是index.html，nginx就在系统中找到这个文件，并发送给浏览器。如果是index.php则进行下面操作。   
	3.nginx发现是index.php，找到相关CGI程序，并提交相关参数(Http Header，GET/POST，COOKIE...)，而此处的CGI就是PHP解析器，简明来说就是CGI规定nginx以特定格式来访问自身。  
	4.PHP解析器会解析php.ini文件并初始化php环境，然后处理请求，返回结果至nginx，nginx再返回给浏览器。

##CGI的通用性
> 1.既然CGI是一种程序，自然需要用编程语言来写。你可以用任何一种编程语言编写CGI。    
  2.其中Perl语言是UNIX特属语言，有强大的字符串处理能力，特别适合CGI的编写。  

##什么是FastCGI？
> 1.简单来说FastCGI就是为了提高CGI的性能的，每当有PHP文件请求CGI，CGI每次都会进行环境的初始化，非常消耗性能，响应速度也非常慢；  
> 2.DuangDuangDuang，FastCGI出现了，FastCGI会启动一个master进程，msater主要负责环境的初始化，之后都不用进行环境初始化了，这样每当有php文件请求的时候，只需要将请求传递给worker进程就行了。  

##FastCGI和PHP-FPM关系？
> 1.PHP-FPM是PHP特有的，而FastCGI是一种通用实现，可以实现JAVA的，PHP的,.NET的。    
> 2.在PHP中，PHP-FPM只是FastCGI的管理者；代替FastCGI负责处理web请求。 
