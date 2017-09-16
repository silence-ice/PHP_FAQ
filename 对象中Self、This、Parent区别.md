#对象中Self、This、Parent区别  
***

##This
>this是指向当前对象的指针
	class UserName{
	private $name;

	function __construct($name){
		$this->name = $name;
	}

	function printName(){
		print($this->name);
	}
	}
	
	//第一次实例化
	$nameObject = new UserName("silence");
	$nameObject->printName();//输出silence
	
	//第二次实例化
	$nameObject = new UserName("huangbin");
	$nameObject->printName();//输出huangbin

##Self
>self是指向当前类的指针  

	class Counter{
		private $lastCount;
		private static $firstCount = 0;
	
		function __construct(){
			//必须使用self来调用静态变量,self必须使用::(域运算符)
			$this->lastCount = ++self::$firstCount;
		}
	
		function printLastCount(){
			print($this->lastCount);
		}
	}
	
	//第一次实例化
	$nameObject = new Counter();
	$nameObject->printLastCount();//输出1
	//因为实例化一次只会调用一次构造函数
	$nameObject->printLastCount();//输出1

##Praent☞类构造函数继承
>1.使用parent::__construct()调用父类的构造函数进行对父类的初始化  
>2.因为父类的成员都是public的，可以在继承类中直接使用this来调用。**而self是指向当前类的指针。**  


	class Animal{
		public $name;
	
		function __construct($name){
			$this->name = $name;
		}
	}
	
	class Person extends Animal{
		public $name;
	
		function __construct($sex, $age){
			parent::__construct("huangbin");
			$this->sex = $sex;
			$this->age = $age;
		}
	
		function printPerson(){
			print($this->name." is ".$this->sex."this year".$this->age);
		}
	}
	// //第一次实例化
	$nameObject = new Person("male", "21");
	$nameObject->printPerson();//输出huangbin is male this year 21


##总结
>1.this是指向当前对象的指针  
>2.self是指向当前类的指针  
>3.parent是指向父类的指针  
>>1.self能在静态函数里使用,而this则不能。  
>>2.self访问的类变量和方法方式用self::fun()，而this则用$this->fun()。  
>>3.self不需要一个实例对象，而this需要。

	class exampleClass{
		public static $foo;
		public $bar;
	
		public function regularFunction(){
			echo $this->$bar;
		}
	
		public static function staticFunction(){
			echo self::$bar;
		}
	
		public static function anotherStatFn(){
			echo self::$staticFunction();
		}
	
		public function regularStaticFun(){
			echo self::$bar;
		}	
	}
	
	exampleClass::$foo = "Hello";//静态变量调用不需要实例化对象
	exampleClass::staticFunction();//打印Hello，静态方法调用不需要实例化对象
	
	
	$obj = new exampleClass();
	$obj->bar = "World!";
	
	$obj->foo = "World111"//静态属性不可以由对象实例化设置
	$obj->staticFunction();//打印Hello