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
35. 使用闭包存储私有数据
36. 只将实例对象存储在实例对象中
	
	>* 共享可变数据可能会出问题，因为原型是被其所有的实例共享的。
	>* 将可变的实例状态存储在实例对象中。
37. 认识到this变量的隐式绑定问题。

	例如在map、forEach参数调用中：
	
	>* this变量的作用域总是由其最近的封闭函数所确定。
	>* 使用一个局部变量（self,me,that）使得this绑定对于内部函数是可用的。
	>* map、forEach都提供第三个参数作为其回调函数的this绑定。
	>* 在回调函数中使用bind绑定。
	>* 使用局部变量缓存this。
	
		[].map(fn, o);
		[].forEach(fn,o);
		[].map(fn.bind(this));
		[].forEach(fn.bind(this));
38. 在子类的构造函数中调用父类的构造函数

	说白了，就是面向对象的继承和多态。
	>* 在子类构造函数中显示的传入this作为显示的接收者调用父类构造函数。
	>* 使用Object.create函数来构造子类的原型对象以避免调用父类的构造函数。
	
		function Actor(scene,x,y){
			this.scene = scene;
			this.x = x;
			this.y = y;
			scene.register(this);
		}
		Actor.prototype.moveTo = function(x,y){
			this.x = x;
			this.y = y;
			this.scene.draw();
		}
		function SpaceShip(scene,x,y){
			Actor.call(this,scene,x,y);
			this.points = 0;
		}
		SpaceShip.prototype = Object.create(Actor.prototype);
39. 不要重用父类的属性名
> 如果在继承体系中的两个类指向相同的属性名，那么它们指向的是同一个属性。

40. 避免继承标准类
>* 继承标准类往往会由于一些特殊的内部属性（如[[Class]]）而被破坏。
>* 使用属性委托优于继承标准类。
41. 将原型视为实现细节
> js提供了便利的内省机制(introspection mechanisms)来检查对象的细节。Object.prototype.hasOwnProperty/Object.getPrototypeOf 
42. 避免使用轻率的猴子补丁

由于对象共享原型，因为每一个对象都可以增加、删除或修改原型的属性。
>* 避免使用轻率的猴子补丁。
>* 记录程序库所执行的所有猴子补丁。
>* 考虑通过将修改置于一个导出函数中，使猴子补丁成为可选的。
>* 使用猴子补丁为缺失的标准API提供polyfills。

一个较为靠谱的做法是
		
	if(typeof Array.prototype.map !=='function'){
		Array.prototype.map = function(){} 
	}

###数组和字典
43. 使用Object的直接实例构造轻量级的字典

	JavaScript对象的核心是一个字符串属性名称与属性值的映射表。这使得使用对象实现字典易如反掌，因为字典就是可变长的字符串与值的映射集合。配合对象枚举利器：for...in循环。
	
44. 使用null原型以防止原型污染
> 在ES5环境中，使用Object.create(null)创建的自由原型的空对象是不太容易被污染的。
> 在一些较老的环境中，考虑使用{__proto__:null}。
> 但要注意__proto__既不标准也不完全可移植，将来可能会去除。
45. 使用hasOwnProperty方法以避免原型污染。
> 使用词法作用域和call方法避免覆盖hasOwnProperty方法。
> 使用字典类避免将'__proto__'作为key来使用。

	终极词典代码实现：
		
		function Dict(elements){
			this.elements = elements;
			this.hasSpecialProto = false;
			this.specialProto = undefined;
		}
		Dict.prototype.has = function(key){
			if(key === '__proto__'){
				return this.hasSpecialProto;
			}
			return {}.hasOwnProperty.call(this.elements,key);
		};
		Dict.prototype.get = function(key){
			if(key === '__proto__'){
				return this.specialProto;
			}
			return this.has(key)?this.elements[key]:undefined;
		};
		Dict.prototype.set = function(key, val){
			if(key === '__proto__'){
				this.hasSpecialProto = true;
				this.specialProto = val;
			}else{
				this.elements[key] = val;
			}
		};
		Dict.prototype.remove = function(key){
			if(key === '__proto__'){
				this.hasSpecialProto = false;
				this.specialProto = undefined;
			}else{
				delete this.elements[key];
			}
		};
		var dict = new Dict();
		dict.set('__proto__',1);
		console.log(dict.has('__proto__'));

46. 使用数组而不要使用字典来存储有序集合

		var ratings = {
			'haha':0.8,
			'xixi':0.7,
			'21':0.6,
			'hehe':0.9
		};
		var total = 0,count=0;
		for(var key in ratings){
			total += ratings[key];
			console.log(key);
			count++;
		}
		total /= count;
		console.log(total);
	>* 使用for...in循环来枚举对象属性应当与顺序无关。
	>* 如果聚集运算字典中的数据，确保聚集操作与顺序无关。
	>* 使用数组而不是字典来存储有序集合。
47. 绝对不要在Object.prototype中增加可枚举的属性

	>* 使用Object.defineProperty方法定义不可枚举属性。
	>* 使用{}.hasOwnProperty判断是否是原型上的方法。
48. 避免在枚举期间修改对象
49. 数组迭代要优先使用for循环而不是for...in循环
50. 迭代方法优于循环
	>* 使用迭代方法（forEach,map,filter）替代for循环使得代码更可读，并且避免了重复循环控制逻辑。
	>* 使用自定义的迭代函数来抽象未被标准库支持的常见循环模式。
	>* 在需要提前终止循环的情况下，仍然推荐使用传统的循环。some和every方法也可用于提前退出。
51. 在类数组对象上复用通用的数组方法
	>* 对于类数组对象，通过提取方法对象并使用其call方法来复用通用的Array方法。
	>* 任意一个具有索引属性和恰当length属性的对象都可以使用通用的Array方法。
52. 数组字面量优于数组构造函数

###库和API设计
53. 保持一致的约定
54. 将undefined看做"没有值"
>避免使用undefined表示任何非特定值
>使用描述性的字符串值或命名布尔属性的对象，而不要使用undefined或null来表示特定应用标志
>提供参数默认值应当采用测试undefined的方式，而不是检查arguments.length
>在允许0、NaN或空字符串为有效参数的地方，绝不要通过真值测试来实现参数默认值

55. 接收关键字参数的选项对象
>使用选项对象使得API更具可读性、更容易记忆
>所有通过选项对象提供的参数应当被视为可选的
>使用extend函数抽象出从选项对象中提取值的逻辑
56. 避免不必要的状态
>尽可能地使用无状态的API
>如果API是由状态的，标示出每个操作与哪些状态由关联
57. 使用结构类型设计灵活的接口
58. 区分数据对象和类数组对象
59. 避免过度的强制转换
60. 支持方法链
>使用方法链来连接无状态的操作
>通过在无状态的方法中返回新对象来支持方法链
>通过在有状态的方法中返回this来支持方法链

###兵法
61. 不要阻塞I/O事件队列
62. 在异步序列中使用嵌套或命名的回调函数
>使用嵌套或命名的回调函数按顺序地执行多个异步操作
>尝试在过多的嵌套的回调函数和尴尬的命名的非嵌套回调函数之间取得平衡
>避免将可被并行执行的操作顺序化
63. 当心丢弃错误
64. 对异步循环使用递归
>循环不能是异步的
>使用递归函数在事件循环的单独轮次中执行迭代
>在事件循环的单独轮次中执行递归，并不会导致调用栈溢出
65. 不要在计算时阻塞事件队列
>避免在主事件队列中执行代价高昂的算法
>在支持Worker API的平台，该API可以用来在一个独立的事件队列中运行长计算程序
66. 使用计数器来执行并行操作
		
		function downloadAllAsync(urls, onsuccess){
			var pending = urls.length;
			var result = [];
			if(pending === 0){
				setTimeout(onsuccess.bind(null, result),0);
			}
			//for(var i=0;i<pending;i++){
			//	console.log(urls[i],i);
			//	downloadAsync(url,function(urls[i]){
			//		result[i] = text;
			//		console.log(i,text);
			//		pending--;
			//		if(pending === 0){
			//			onsuccess(result);
			//		}
			//	});
			//}
			urls.forEach(function(url, i){
				console.log(url,i);
				downloadAsync(url, function(text){
					result[i] = text;
					console.log(i,text);
					pending--;
					if(pending === 0){
						onsuccess(result);
					}
				});
			});
		}
		function downloadAsync(url, fnSuc){
			setTimeout(function(){
				fnSuc(url);
			},Math.random()*10000);
		}
		downloadAllAsync([1,3,5],function(x){
			console.log(x);
		});
		
67. 绝不要同步地调用异步的回调函数

		var cache = new Dict();
		function downloadCachingAsync(url, onsuccess, onerror){
			if(cache.has(url){
				var cached = cache.get(url);
				setTimeout(onsuccess.bind(null,cached),0);
				return;
			}
			return downloadAsync(url, function(file){
				cache.set(url, file);
				onsuccess(file);
			},onerror);
		}
		
	>即使可以立即得到数据，也绝不要同步地调用异步回调函数。
	>同步地调用异步的回调函数扰乱了预期的操作序列，并可能导致意想不到的交错代码。
	>同步地调用异步的回调函数可能导致栈溢出或错误地处理异常。
	>使用异步的API，比如setTimeout函数来调度异步回调函数，使其运行于另一个回合。

68. 使用promise模式清洁异步逻辑

