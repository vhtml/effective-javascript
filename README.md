###Effective Javascript
####一、让自己习惯javascript
1.	在代码中使用严格模式

		(function(){
			'use strict';
			//your code
		})();
2. 小心js中的浮点数
	
	`0.1+0.2+0.3 != 0.1+(0.2+0.3)`
	
	转换为整数计算
	
	`1+2+3 == 1+(2+3)`
	
	浮点数有舍入误差，整数不会
	
3.	 当心隐式的强制转换

	`1+true //2`
	`1+2 //3`
	`1+'2' //12`
	`1+2+'3' //33`
	`1+'2'+3 //123`
	
	判断NaN
	
	`isNaN(NaN) //true`
	
	`isNaN('foo') //true`
	
	看来标准库函数isNaN也不是很可靠，其会进行隐式类型转换，对于确定是数字的可以使用该方法。
	
		//使用原理：NaN是js中唯一一个不等于其自身的值
		function isReallyNaN(x){
			return x !== x;
		}
		
4. 原始类型优于封装对象

	对原始值设置属性，毫无作用，因为每次隐式封装，都会产生一个新的对象。
	
	* 获取或设置原始类型值的属性会隐式的创建封装对象。
	* 当做相等比较时，原始类型的封装对象与其原始值行为不一样。

			'xx'.someProperty = '11';
			'xx'.someProperty //undefined
		
			var x = '111';
			x.ss = '222';
			x.ss; //undefined
		
			var s = new String('dd');
			s.n = 1;
			s.n; //1
			
5. 避免对混合类型使用`==`运算符

	显示地定义转换的逻辑能确保你不会混淆`==`运算符的强制转换规则
	
6. 了解分号插入的局限
7. 视字符串为16位的代码单元系列

####二、变量作用域
8. 尽量少用全局对象
9. 始终声明局部变量
10. 避免使用with
11. 熟练掌握闭包

	掌握闭包的三个基本事实：
	1. js允许你引用在函数以外定义的变量。
	2. 即使外部函数已经返回，当前函数仍然可以引用在外部函数所定义的变量。
	3. 闭包可以更新外部变量的值。
	
	> * 函数可以引用定义在其外部作用域的变量。
	> * 闭包比创建他的函数有更长的生命周期。
	> * 闭包在内部存储其外部变量的引用，并能读写这些变量。
12. 理解变量声明提升
13. 使用立即调用的函数表达式创建局部作用域

	闭包通过引用而不是值捕获它们的外部变量。
14. 当心命名函数表达式笨拙的作用域
15. 当心局部块函数声明笨拙的作用域
16. 避免使用eval创建局部变量

	容易造成变量污染
17. 间接调用eval函数优于直接调用

		var x = 'global';
		function test(){
			var x = 'local';
			var f = eval;
			return f('x'); //非直接调用
		}
		test(); // "global"
		
	>编写简介调用eval函数的一种简洁方式是使用表达式序列运算符(,)和一个明显毫无意义的数字字面量。`(0,eval)(src)`
	
####三、使用函数
18. 理解函数调用、方法调用和构造函数调用之间的区别
19. 熟练掌握高阶函数

	>* 高阶函数是那些将函数作为参数或返回值的函数
	>* 熟悉掌握现有库中的高阶函数
	>* 学会发现可以被高阶函数所取代的常见的编码模式
20. 使用call方法自定义接收者来调用方法
21. 使用apply方法通过不同数量的参数调用函数
22. 使用arguments创建可变参数的函数
23. 永远不要修改arguments对象

	>可使用[].slice.call(arguments)将arguments对象复制到一个真正的数组中再进行修改。
24. 使用变量保存arguments的引用
25. 使用bind方法提取具有确定接收者的方法

	bind创建一个新函数而不是修改了原函数
	
		var x=function(){};
		x.bind(document) === x;//false
26. 使用bind方法实现函数柯里化
		
		function simpleURL(protocal, domain, path){
			return protocal+'//'+domain+'/'+path;
		}
		var urls = paths.map(function(path){
			simple('http',siteDomain,path);
		});
		var urls = paths.map(simpleURL.bind(null,'http',siteDomain));
27. 使用闭包而不是字符串来封装代码
28. 不要信赖函数对象的toString方法

	> 1. 当调用函数的toString方法时，并没有要求js引擎输出函数的源码，且不同引擎输出结果可能不同。
	> 2. toString方法的输出结果并不会暴露存储在闭包中的局部变量值。
29. 避免使用非标准的栈检查属性。

	> * 避免使用非标准的arguments.callee和arguments.caller属性，因为他们不具有良好的移植性。
	> * 避免使用非标准的函数对象caller属性，因为在包含全栈信息方面，它是不可靠的，例如函数自身循环引用。
	
####四、对象和原型
30. 理解prototype、getPrototypeOf、__proto__之间的不同

	>* C.prototype属性的new C()创建的对象的原型。
	>* Object.getPrototypeOf(obj)是ES5中检索对象原型的标准方法。
	>* obj.__proto__是检索对象原型的非标准方法。
	>* 类是由一个构造函数和一个关联的原型组成的一种设计模式。
31. 使用Object.getPrototypeOf函数而不要使用__proto__属性

	>对于不支持ES5 API的可以使用该属性
32. 使用不要修改__proto__属性
33. 使构造函数与new操作符无关

	使用Object.create函数，该函数需要一个原型对象作为参数，并返回一个继承自该原型对象的新对象。
	
		if(typeof Object.create === 'undefined'){
			Object.create = function(prototype){
				function C(){}
				C.prototype = prototype;
				return new C();
			}
		}
		
		function User(name){
			var self = this instanceof User
					? this
					: Object.create(User.prototype);
			self.name = name;
			return self;
		}
34. 在原型中存储方法
35. 