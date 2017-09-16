#PHP☞unset()  
***

##unset()的实质
> 

	<?php
	#PHP允许两个变量来指向同一个内容
	$arg1 = "huangbin";
	$arg2 = &$arg1;
	echo $arg1,$arg2;#都输出huangbin
	
	#其中一个变量更变值另一个变量也会改变，因为指向的数据内容已经改变
	// $arg2 = "silence";
	// echo $arg1,$arg2;#都输出huangbin
	
	#当你删除一个变量时，另外一个变量值也不会删除，因为指向的数据内容还在
	#除非你把所有指向该数据内容的变量删除掉
	unset($arr1);
	echo $arg2;#输出huangbin
