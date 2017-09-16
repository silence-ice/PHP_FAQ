#PHP☞文件操作  
***

###fopen()：  
>fopen() 函数打开文件或者 URL。如果打开失败，本函数返回 FALSE。  
###fclose()：  
>fclose() 函数关闭一个打开文件。如果成功则返回 true，否则返回 false。  
###fgets()：  
>fgets() 函数从文件指针中读取一行。  
###fread()：  
>fread() 函数读取文件（可安全用于二进制文件）。
###fwrite()：
>fwrite() 函数写入文件（可安全用于二进制文件）
###flock():
>1.flock(file,lock,block)  
>>file:必需。规定要锁定或释放的已打开的文件。  
>>lock:必需。规定要使用哪种锁定类型。  
>>>1.要取得共享锁定（读取的程序），将 lock 设为 LOCK_SH（PHP 4.0.1 以前的版本设置为 1）。  
>>>2.要取得独占锁定（写入的程序），将 lock 设为 LOCK_EX（PHP 4.0.1 以前的版本中设置为 2）。  
>>>3.要释放锁定（无论共享或独占），将 lock 设为 LOCK_UN（PHP 4.0.1 以前的版本中设置为 3）。  
>>>4.如果不希望 flock() 在锁定时堵塞，则给 lock 加上 LOCK_NB  
>
>>block:可选。若设置为 1 或 true，则当进行锁定时阻挡其他进程。  

>2.flock() 函数锁定或释放文件。若成功，则返回 true。若失败，则返回 false。  

>3.注意：  
>>1.共享锁，不会阻塞其他用户对同一个表的读请求，但会阻塞其他用户对同一个表的写请求。  
>>2.独占锁，会阻塞其他用户对同一个表的读和写操作。  
>>3.注意fwrite之后，文件立即就被更新了，而不是等fwrite然后fclose之后文件才会更新。   

###正确使用锁：
>1.如果不想出现dirty数据，那么最好使用lock_sh共享锁。可以考虑以下三种情况：   
>>1.如果读的时候没有加共享锁，如果正好读了一半，然后被其他程序给写了，那么读的后一半就有可能跟前一半对不上。   
>
>>2.如果读的时候加上了共享锁，这个时候，其他程序开始写，这个写程序没有使用锁，那么写程序会直接修改这个文件，也会导致前面一样的问题.   
>
>>3.最理想的情况是，读的时候加锁(lock_sh),写的时候也进行加锁(lock_ex),这样写程序会等着读程序完成之后才进行操作，而不会出现贸然操作的情况 

###代码测试：
	【a.php】
		$file = "temp.txt";    
		$fp = fopen($file , 'w');    
		if(flock($fp , LOCK_EX)){    
		     fwrite($fp , "abc\n");    
		     sleep(10);    
		     fwrite($fp , "123\n");    
		    flock($fp , LOCK_UN);    
		}    
		fclose($fp);  
 
	【b.php】
		$file = "temp.txt";    
		$fp = fopen($file , 'r');    
		echo fread($fp , 100);    
		fclose($fp);   
		运行 a.php 后，马上运行 b.php ，可以看到输出：
		abc
		等 a.php 运行完后运行 b.php ，可以看到输出：
		abc
		123
		显然，当 a.php 写文件时数据太大，导致时间比较长时，这时 b.php 读取数据不完整
		
	【修改 b.php 为】：
		$file = "temp.txt";    
		$fp = fopen($file , 'r');    
		if(flock($fp , LOCK_EX)){    
		    echo fread($fp , 100);    
		    flock($fp , LOCK_UN);    
		} else{    
		    echo "Lock file failed...\n";    
		}    
		fclose($fp);   
		运行 a.php 后，马上运行 b.php ，可以发现 b.php 会等到 a.php 运行完成后(即 10 秒后)才显示：
		abc
		123
		读取数据完整，但时间过长，他要等待写锁释放。
		
	【修改 b.php 为】：
		$file = "temp.txt";    
		$fp = fopen($file , 'r');    
		if(flock($fp , LOCK_SH | LOCK_NB)){    
		    echo fread($fp , 100);    
		    flock($fp , LOCK_UN);    
		} else{    
		    echo "Lock file failed...\n";    
		}    
		fclose($fp);   
		运行 a.php 后，马上运行 b.php ，可以看到输出：
		Lock file failed…
		证明可以返回锁文件失败状态，而不是向上面一样要等很久。