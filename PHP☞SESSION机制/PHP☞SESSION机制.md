#PHP☞SESSION机制 
***

##SESSION存储机制
>1.PHP默认是以文件的形式存储SESSION数据信息。  
>2.存储机制可以通过php.ini进行修改成数据库存储/Redis存储等。  

##SESSION在WebServer和Client存储形式
>1.SESSION在Client浏览器就存储了一个SESSION_ID。每当打开一个浏览器在其内存中会生成一个全局唯一的SESSION_ID；关闭浏览器SESSION_ID销毁。  
>2.SESSION在WebServer端默认是以文件形式存储，文件名形如:sess_01aab840166fd1dc253e3b4a3f0b8381，每一个文件对应一个会话。  
>3.Client与Server端进行交互时，默认是在请求头携带COOKIE，COOKIE包含SESSION_ID。当然也可以通过URL形式和SERVER交互，形如:index.php?PHPSESID=01aab840166fd1dc253e3b4a3f0b8381

##session_start()的内部操作不简单！！
> 1.执行session_start()后，session机制就开始了，它有一定概率开启垃圾回收。开始如下操作：  
>> 1.告诉服务器赶快把对应的SESSION文件给反序列化了，老子准备读数据！别偷懒~！  
>> 2.服务器找到浏览器传过来的SESSION_ID，然后去session.save_path指定的文件夹里去找名字为'SESS_'.session_id()的文件.读取文件的内容反序列化，然后放到$_SESSION中。    

> 2.【注意】：只有一个session相关函数可以在session_start()之前执行，就是获取SESSION_ID的session_name()

##SESSION赋值
>1.比如进行如$_SESSION['silence'] ='Hello World'; 那么这个SESSION只会维护在内存中，当脚本执行结束的时候，会把对应的SESSION的值写入到session_id指定的文件夹中，然后关闭相关资源。  
>2.在脚本结束的时候会执行SESSION写入操作，把$_SESSION中值写入到session_id命名的文件中，如果不存在则创建之，最后将数据序列化之后写入文件。

##SESSION回收机制:
>回收机制原理：  
>>当php每发出一次请求时,会有一定的概率触发"session回收",此时会检查SESSION存储的文件，若修改时间超过了gc_maxlifetime的值,就将其删除。  

>回收概率如何计算：  
>>回收概率是gc_probability/gc_divisor；在php.ini文件可以看到session.gc_probability和session.gc_divisor的值。  

##SESSION机制实操案例
>1.在第一次用浏览器登录代理商的时候，因为没session_id，所以在/tmp下产生了该文件：  
1  
>2.进行页面代理商登录操作之后呢，该文件里面会产生对应的session内容：  
1  
>3.对该火狐浏览器再进行eflydns网站登录的时候呢，该文件会增加eflydns的session内容：  
1  
>4.当使用**另外一个浏览器**的时候打开代理商呢，会产生另外一个session文件：  
1  
>5.因为浏览器的session_id是保存在内存中的，当关闭浏览器的时候session_id会丢失。当再次打开该浏览器进入代理商网页的时候，会产生新的session文件:  
1  