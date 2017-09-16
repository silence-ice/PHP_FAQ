#Xdebug使用
****

###Xdebug安装
>sublime text3安装xdebug插件
>>ctrl+shift+p，输入install packge，回车，再输入xdebug client，回车。xdebug插件安装完成。

>firefox安装xdebug插件
>>进入附加组件，点击扩展，输入xdebug查找，安装`theeasiestxdebug`组件。

>php安装xdebug扩展
>>1.安装完成；phpstudy已经包含该扩展，开启xdebug扩展就可以了
>
>>2.在php.ini添加如下配置
>>>端口设置为9999，保证不和fastcgi的9000端口冲突。同时sublime text3默认也是9000，选中"工具"->"xdebug"->"Setting User"->添加`{"port":9999}`自定义端口
>>
	xdebug.remote_enable = on  
	xdebug.remote_handler = "dbgp"  
	xdebug.remote_host = "127.0.0.1"  
	xdebug.remote_port = 9999  


###Xdebug断点常用快捷键：

	Shift+f8: 打开调试面板；
	f8:打开调试面板快速连接；
	Ctrl+F8: 切换断点；
	Ctrl+Shift+F5: 运行到下一个断点；
	Ctrl+Shift+F6: 单步；
	Ctrl+Shift+F7: 步入；
	Ctrl+Shift+F8: 步出 ；


###Xdebug 【函数触发】生成报告文件：
>1.在测试前请先确认配置xdebug.auto_trace=off(关闭自动回溯)
>
>2.然后找到你要开始追踪回溯的代码位置调用xdebug_start_trace函数,再在要停止追踪回溯的位置调用xdebug_stop_trace函数,这样就会生成回溯信息

>3.记录了函数参数还不足够,想要记录每一次函数的返回值也可以,设置xdebug.collect_return=on
>
	xdebug.auto_trace=off
	xdebug.trace_enable_trigger=on

>4.示例代码
>
	xdebug_start_trace(); //开始记录回溯
	$user_name = "eflytest";
	$num = 1;
	echo 1;
	// var_dump($_GET);
	$access_token =12;
	echo 2;
	xdebug_stop_trace(); //结束记录回溯

>执行脚本，会在F:/phpStudy/tmp/xdebug/处生成文件trace.3220740752.xt，


###Xdebug 生成图形文件：
>php.ini设置
>
	xdebug.profiler_output_dir="F:\phpStudy\tmp\xdebug"
	xdebug.trace_output_dir="F:\phpStudy\tmp\xdebug"
	xdebug.profiler_enable=on

>修改生成文件名
>>默认是以`xdebug.profiler_output_name = cachegrind.out.%p`的文件名，可修改为`xdebug.profiler_output_name = callgrind.out.%s`

>参数含义
>>Self是代表此Funcion自己花费的时间，不包含此Function调用的其他Function。

>>Cum则是此Funcion整体花费的时间，包含此Function调用的其他Function。