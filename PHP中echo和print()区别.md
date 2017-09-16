##PHP中echo和print()区别  
***

##echo,print(),print_r()的区别：
>返回值：  
>1.echo是PHP语句，没有返回值  
>2.print()和print_r()是函数，有返回值  
>输出方式：  
>1.echo可以输出多个变量  
>2.print()只能输出一个变量  
>3.print_r()只能输出一个变量，输出array时会输出具体结构。  
>执行速度：  
>echo>print()>print_r()

##求echo '1'.print(2)+3;的值
>1.首先先执行print()函数，再执行加法运算，得出结果"5"输出至缓冲区。  
>2.又因为print()是个函数有返回值，执行成功返回"1"，echo'1'.1  
>3.最终结果为511    