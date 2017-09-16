#session引起的阻塞
****

###问题发生
>当一个脚本在执行脚本的时间过长同时需要获取session信息时，另一个脚本的执行也需要使用session的信息，这时候第二个脚本就会阻塞，直到第一个脚本执行结束。

###原理
>由于PHP的Session信息是写入文件的，1个客户端占有1个session文件。因此，当 session_start被调用的时候，该文件是被锁住的，而且是以**读写模式锁住**的（因为程序中可能要修改session的值），这样，第2次调用 session_start的时候就被阻塞了。

###最简单粗暴的方法
>在session操作完成后调用session_write_close()即可避免此问题;

>这个方法就是写session的数据，同时关闭这个session。因此，我们可以在用完session之后，调用这个函数关闭session 文件即可解除锁定。
>

###注意
>可以直接使用`$_SESSION['user_name'] = 'huangbin';`是因为php.ini里面设置了`session_auto_start =on;`的参数。
>
>`$_SESSION['user_name'] = 'huangbin';`赋值后是保存在缓存中，直到脚本结束才写入文件。