#PHP开发APP接口
***


###开发注意事项
>数据格式：数据格式最好使用JSON格式数据，因为JSON有较好的跨平台性。

>返回值：
>>不管是成功，还是失败。接口必须提供明确的数据状态信息，并且不能返回NULL，如果返回NULL的话，在IOS端会崩掉。

>尽量使用单文件多接口
>>单文件实现多接口的形式有很多种，例如：if..elseif.. 或 switch 或 动态方法 (也就是TP的这种访问函数体的形式)

>尽量使用原生PHP开发
>
>>客户端一般对服务端的响应速度有极高要求，因此，使用最原生态的 PHP 完成接口开发，是最高效的，假如用到了框架，还需要加载各种不需要多余的文件
>
>>框架对于WEB开发而准备的，而不是为了开发API和出现的，但对于 API 而言，你实在不敢想象它会给你出什么岔子！


###APP接口实现方案
>读取数据库方式
>
>读取缓存方式

###APP接口的安全性问题
>方案一
>>与APP端开发人员约定特定的md5组合算法，然后两端比对一下，如果相同就allow，不相同就deny；
>
>>但是，这也是不安全的，如果APP程序被反编译，这些约定的算法就会暴露，特别是在安卓APP中，有了算法，完全就可以模拟接口请求通过验证；

>方案二
>>数据库会员表的password是带上了随机密窜并经过双重加密的md5值;在用户登录的时候,我返回会员相应的uid和加密过的password,然后每次请求接口的时候user_id=333&token=aa37e10c7137ac849eab8a2d5020568f,通过主键uid可以很快的找到当前uid对应的token,然后再来比对;
>
>>但是这样想法是too yang too simple的,抓包的人虽然不能通过密文密码来登录该会员,然而一旦知道了这个token,也可以一直通过这个token来操作该会员的相关接口;(抓包的情况下只能在客户端和服务端，除非终端被hacker给黑了，当然不排除中间人攻击进行抓取)。

>方案三
>
>>通过对称加密算法，该加密算法对uid+网站公钥进行时效加密，在一定时效内可用。在会员登录成功时，服务器端对该ID加密后返回给客户端，客户端每次请求接口的时候带上该参数，服务器端通过解密认证；
>
>>但是这样做，也是不安全的。因为，防外不防内，听说这次的携程宕机就是因为内部离职人员的恶意操作。内部不怀好意的人员如果知道相应的算法规则后，就算没有数据库权限，也可以通过接口来操作相关会员；

>方案四
>
>>会员登录的时候请求登录接口，然后服务器端返回给客户端一个token(同方案三)，该token生成的规则是 网站公钥 + 当前uid + 当前时间戳 + 一段随机数双重加密，根据需求决定是把
>>>1)该token放进cache等一段时间自动失效
>
>>>2)还是放进数据库（如果要放进数据库的话，单独拎出一张表来，顺便记录用户的登录，登出时间），在用户登出登录的时候改变一下，确保该token只能在用户人为登出登录之间有用。为保安全，应保证让用户在一段时间内自动退出；此方案配合Linux和数据库的权限管理可以防外又防内；