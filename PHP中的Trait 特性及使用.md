#PHP中的Trait 特性及使用
****

>1.自 PHP 5.4.0 起，PHP 实现了代码复用的一个方法，称为Trait。

>2.Trait 是一种为类似 PHP 的单继承语言而准备的代码复用机制。**Trait 为了减少单继承编程语言的限制**，Traits 和类组合的语义是定义了一种方式来减少复杂性，避免传统多继承和混入类（Mixin）相关的典型问题。

>3.Trait 和一个类相似，但仅仅旨在用细粒度和一致的方式来组合功能。Trait 不能通过它自身来实例化。它为传统继承增加了水平特性的组合；**也就是说，应用类的成员不需要继承**。


###代码示例
>目录结构

	./trait.php
	./test.php


>trait.php

	<?php
	trait first_trait {
		function first_method() {
			echo 1;
		}
		function second_method() { 
			echo 2;
		}
	}

>test.php

	<?php
	include "./trait.php";
	class first_class {
		use first_trait;
	}
	
	$obj = new first_class();
	$obj->first_method(); // 1
	$obj->second_method(); // 2

