* 如何区分{} 和 [] 

	使用typeof得到的都是object

	最好的方法是使用object原型的toString方法

	```
	function isArray(o) {
	    return Object.prototype.toString.call(o) === ‘[object Array]‘;
	}
	```

* JavaScript有几种数据类型？

	栈：原始数据类型（Undefined, Null, Boolean, Number, String）

	堆：引用数据类型（对象，数组和函数）

	两种类型的区别是：存储位置不同
	
	原始数据类型直接存储在栈(Stack)中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储

	引用数据类型存储在堆(heap)中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能。引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获取实体。

* 数组随机排序新姿势get
	
	```
	var arr = [1, 2, 3, 4, 5, 6, 7, 8];
	arr.sort(function() {
		return Math.random() - 0.5;	
	});
	```

* eval是做什么的？

	它的功能是把对应的字符串解析成JS代码并运行；
	应该避免使用eval，不安全，非常耗性能（2次，一次解析为JS代码，一次执行）

* ['1', '2', '3'].map(parseInt)答案是多少?

	很多人第一眼看到这个问题肯定回答是[1, 2, 3]，但是尝试一下，发现不是这样，是不是觉得很奇怪，就很气，下面我们来分析一下。

	map()函数的参数是一个函数，这个函数的参数为(item, index, array), 在这个等式中，parseInt为map的参数，那么map就会将上书三个参数传给parseInt

	下面我们来分析一下parseInt函数，这个函数可以将2-36进制的字符串转为10进制，它接受2个参数，parseInt(num, scale), 因此map函数传给其的三个参数取前两个参数
	
	1传递过来的参数是 (1,0) 0会被parseInt默认按10进制来计算，因此得到的结果是1
	
	2传递过来的参数是 (2,1) 由于parseInt接受的参数为2-36，因此无法解析，得到的结果是NaN

	3传递过来的参数是 (3,2) 有的小伙伴就会说啦，你看，现在是2-36范围内的值了，为啥还不对，23333，因为3要按2进制来解析，要怎么样解析嘛，因此得到的结果也是NaN

	因此['1', '2', '3'].map(parseInt)得到的答案就是[1, NaN, NaN]，是不是很神奇

* 事件是什么？(给出官方解释)IE和FireFox的事件机制有什么区别?如何阻止冒泡

	我们在网页中的某个操作(有的操作对应多个事件)。例如：当我们点击一个按钮就会产生一个事件，这是可以被Javascript所侦测到的行为。

	IE的事件事件处理只支持冒泡，Firefox的事件处理支持冒泡和捕获

	如何组织冒泡，也需要兼容性的考虑, event.stopPropagation()(旧IE方法 event.cancelBubble = true;)

* new操作符具体干了些什么？

	1. 创建了一个新对象，并且this变量引用该对象，同时还继承了该函数的原型
	2. 属性和方法被加入到this引用的对象中
	3. 新创建的对象由this所引用，并且最后隐式的返回this对象

* Javascript中，有一个函数，执行对象查找时，永远不会去查找原型，这个函数是

	hasOwnProperty

	javascript中hasOwnProperty函数方法是返回一个布尔值，指出一个对象是否具有制定名称的属性，该方法不会去对象的原型链中查找
	
	object.hasOwnProperty(attribute)

	我这有个方法可以判断属性是否为其原型链中的属性
	
	```
	function isPropertyAttr(obj, proName) {
		return !obj.hasOwnProperty(proName) && proName in obj;
	}
	```

* [].forEach.call($$("\*"),function(a){a.style.outline="1px solid #"+(~~(Math.random()*(1<<24))).toString(16)}) 能解释一下这段代码的意思吗？

	说实话，第一眼看到这么长的代码，肯定是蒙蔽的，所以只能按部就班一步一步分析啦

	$$('\*')这是chrome的api，用于获取页面上所有元素，$$('\*')相当于document.querySelectorAll('*')，得到一个NodeLists，这不是真正的数组，因此需要采用[].forEach.call来改变this，也可以使用Array.property.forEach.call，前者少些一些代码

	后面代码可以很清晰的看到，是改动css的outline获得彩色轮廓，outline游离于盒子模型之外，因此不会影响元素之前的布局

	1<<24，css中，颜色值为6位16进制值，因此使用Math.random() * (1 << 24)可以得到范围内的一个小数，再采用~~两次取反得到整数部分，最后将10进制的数转为16进制，即大功告成啦2333

* 模块化开发怎么做？

	我一看到这个问题，想到的就是Nodejs的commonjs模块化，模块化将很多地方可能用到的代码块封装起来，隐藏具体的变量和实现方法，暴露出接口。

	然后联想到立即执行函数，reture一个对象变量，暴露出接口
	
	```
	var module = (function() {
		var _count = 0;
		var m1 = function() {
		}
		var m2 = function() {
		}
		return {
			m1 : m1,
			m2 : m2
		}
	})();
	```
	这样就暴露除了m1, m2两个接口，隐藏了模块的具体实现

* document.write 和 innerHTML的区别
	
	document.write能够重绘整个页面

    innerHTML能够重构部分页面

* 如何判断当前脚本运行在浏览器还是node环境中?

	`this === window` ? 'brower' : 'node'`
	node中this应该等于global

* 实现一个页面操作不会整页刷新的网站，并且能在浏览器前进、后退时正确响应。给出你的技术实现方案？

	我觉得这道题考的就是ajax干掉浏览器前进后退键的知识点，因为看到题目，首先想到的肯定是ajax优秀的部分刷新机制，然后再往后面看就会想到上面的问题，那么如何解决呢

	1. 加入页面操作改变了URL，可以在每个url后面加上hash，然后监听onHashChange，监听到变化后，重新执行数据拉取
	2. 使用Html5的Hisroty API 具体实现是使用pushState popState window.location.href这几个api
	3. 使用history.js

* 移动端最小触控的区域是多大?

	这个我也不知道，去百度了一下，好像ios是44pt * 44pt，android是48dp * 48dp，下次碰到了有机会再补充

* 把Script标签放在页面的最底部的body封闭之前和封闭之后有什么区别？浏览器会如何解析它们？

	==我以前接触的问题都是把script放在head中还是放在body标签结束之前(阻塞加载的问题)，从结果来看，放在body标签之内和body标签之外并没有什么区别，但是貌似有规则规定过html标签下只能有head和body，其中的script也会被错误修复机制放置到body内部去，有哪位老哥确切知道这个问题，可以告诉小弟一波~

* 移动端的点击事件有延迟，时间是多久，为什么会有？

	click有300ms延迟，为了实现safari的双击事件的设计，浏览器要知道你是不是要双击操作

	我想到最粗暴的方法就是user-scalable=no，禁用缩放美滋滋, 好吧，不扯淡了，正紧的解决方法是使用fastclick这个库，炒鸡好用

* 用js实现千位分隔符？

	这个题目有很多种解决的方法，可以使用三位循环，字符串数组分隔，也可以使用正则，这里我给出正则的实现方式，其他两种较为简单就不给出实现方法了

	```
	function changeFormat(num) {
		if (!num) {
			return ;
		}
		num = num.toString();
		return (num.indexOf('.') == -1) ?
			num.replace(/(\d)(?=(\d{3})+$)/g, function($0) {
				return $0 + ',';
			}) :
			num.replace(/(\d)(?=(\d{3})+\.)/g, function($0) {
				return $0 + ',';
			});
	}
	console.log(changeFormat(1234567.90));
	console.log(changeFormat(123123123));
	```
	可能有同学会问？=是什么，？=表示先行断言，可以作为匹配的条件，但是匹配的内容不获取，并且作为下一次循环的开始，下面以123456789来分析一下
	第一次(\d)匹配到了3, 因为后面要满足(\d{3})+，后面模式匹配到的是456789，依此类推，匹配到的就是3和6，得到的结果就是123,456,789，需要注意的就是整数匹配时候，由于不像小数一样最后总有.限制条件，需要使用$关闭懒惰匹配，不然得到的就是1,2,3,4,5,6,789, 同理?!为先行否定断言，只有匹配内容后面不存在否定内容才完成匹配，同样，也不计入返回结果

* 我们给一个DOM元素同时绑定两个点击事件，一个用捕获，一个用冒泡。会执行几次事件，会先执行冒泡还是捕获？

	http://www.cnblogs.com/greatluoluo/p/5882508.html
	
	这里需要注意的是所有事件的顺序是：其他元素捕获阶段事件->本元素代码顺序事件->其他元素冒泡阶段事件

	下面来解释一下上面这句话的意思，如果点击的元素是本元素，那么执行的顺序就是捕获事件和冒泡事件定义的先后顺序，那个先定义，哪个就先执行，假如点击的元素是本元素的子元素，那么先执行捕获事件再执行冒泡事件，因为在W3C下，都是先从根元素执行捕获到目标元素，再从目标元素向上执行。

* 列举IE与其他浏览器之间不一样的特性？
	
	1. 其他浏览器事件状态为event，IE中为window.event，为了方便下面统称为event 
	2. 其他浏览器触发事件的目标是event.target，而在IE中则是event.srcElement属性
	3. 获取字符代码，DOM需要使用charCode，而IE需要使用keyCode
	4. 阻止某个事件的默认行为，IE中将returnValue设为false，其他浏览器调用preventDefault()
	5. 停止事件冒泡，IE中设置cancelBubble为true，其他浏览器调用stopPropagation()

* 前端优化的大体思路？

	对普通的网站有一个统一的思路，就是尽量向前端优化、减少数据库操作、减少磁盘IO。
	
	1. 向前端优化指的是，在不影响功能和体验的情况下，能在浏览器执行的不要在服务端执行，能在缓存服务器上返回的不要到应用服务器，程序能直接取得的结果不要到外部取得，本机内能取得的数据不要到远程取，内存能取到的不要到磁盘去，缓存中有的不要去数据库查询。
	2. 减少数据库操作指的是，减少更新次数，缓存结果减少查询次数，将数据库执行的操作尽可能的让你的程序完成(例如join查询)。
	3. 减少磁盘IO指尽量不适用文件系统作为缓存、减少读写文件次数等。
	4. 程序优化永远要优化慢的部分，换语言是无法优化的。

* ajax处理跨域有哪几种方式

	1. 代理：通过后台获取其他域名下的内容，然后再把获取的内容返回到前端，这样在同一个域名下，就不会出现跨域的问题
	2. jsonp，该方法只支持GET请求，利用script标签没有跨域限制的特点，通过script标签的src属性发送GET请求
	
		```js
		// 随便找个浏览器发送跨域请求
		let script = document.createElement('script');
		script.type = 'text/javascript';
		script.src = 'http://127.0.0.1:8888?callback=cb';
		document.head.appendChild(script);
		function cb(res) {
			console.log(JSON.stringify(res));
			document.head.removeChild(script);
		}
		
		// 服务端
		const http = require('http');
		const url = require('url');
		
		var data = {'methods': 'jsonp', 'result': 'success'};

		http.createServer(function (request, response) {
		    var params = url.parse(request.url, true)
		    console.log(params)
		    response.writeHead(200, {
		      'Content-Type': 'text/plain'
		    });
		    if (params.query && params.query.callback) {
		
		      // callback(data)
		      var str = `${params.query.callback}(${JSON.stringify(data)})`
		    }
		
		    response.end(str);
		}).listen(8888);
		
		console.log('Server running at http://127.0.0.1:8888/');
		```
	
	3. 推荐使用的方法（XHR 2方法）在服务端头部加上允许跨域的标签(cors: cross-origin resource sharing)，和JSONP相比，支持更多方法，再发送非GET跨域请求的时候，会先发送一个OPTION请求来“预检”（preflight）该请求是否被允许

    	[阮一峰参考资料](http://www.ruanyifeng.com/blog/2016/04/cors.html)

		```js
		// 随便找个浏览器发送跨域请求
		let url = 'http://127.0.0.1:8888';
		let xhr = new XMLHttpRequest();
		xhr.open('GET', url, true);
		xhr.send();
		
		// 服务端代码
		const http = require('http');
		
		http.createServer(function(request, response) {
			response.writeHead(200, {
				'Content-Type': 'text/plain',
				'Access-Control-Allow-Origin': '*', // 允许跨域GET请求
 				'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS', // 允许多种方法
                'Access-Control-Allow-Credentials': true, // 允许携带cookie，不加这个标识位，cookie依旧是跨域的
			});
			
			response.end('response success');
		}).listen(8888);
		
		console.log('Server running at http://127.0.0.1:8888/');
		```

* js事件委托（事件代理）

	事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件

	http://www.cnblogs.com/liugang-vip/p/5616484.html

	这个博客讲解的非常清楚，事件委托确实和取快递差不多，一个一个dom节点去绑定事件就好比每个人都自己去取快递，而整体绑定到dom节点的父亲节点上，利用事件的冒泡属性去处理事件的话，好比委托前台小姐姐签收，然后小姐姐再去通知你去领取一样

	适合用事件委托的事件 : click, mousedown, mouseup, keyon, keydown, keypress

	mouseover和mouseout虽然也有冒泡属性，但是需要经常计算其位置，也不是很好用

	像focus，blur之类根本就没有冒泡属性的，就更不用说了

* js中的数据类型 typeof instanceof

	js的基本数据类型有String，Number，Boolean，Undefined，Null，Object
	
	typeof是用于判断一个变量或者表达式的类型，typeof一般只能返回如下几个结果

	number，boolean，string，undefined，function，object(array, 对象, null)

	这里有个容易忘记的知识点
	
	`var num = 1; num instanceof Number // false`
	`var num = new Number(1); num instanceof Number // true`
	
	String，Boolean和Number是一样的，Array无论是直接定义还是调用构造函数，都为Array的实例

* document.getElementsByClassName()的兼容问题

	又是IE==, IE6，7，8没有getElementsByClassName方法，因此需要兼容
	
	```
	function getName(obj, cName) {
		var obj = obj || document;
		if (obj.getElementsByClassName) {
			return obj.getElementsByClassName(cName);
		}	
		else {
			var arr = [];
			var all = obj.getElementsByTagName('*');
			for (var i in all) {
				if (all[i].className.indexOf('cName') != -1) {
					arr.push(all[i]);
				}			
			}
			return arr;
		}
	}
	```

* caller和callee的区别

	其实caller和callee是风马牛，不相及的两个东西，之所以把这个属性拿来比较记忆，是因为两个属性长的很像

	callee是arguments的一个属性，有两个作用
	
	arguments.callee.length 函数形参的个数
	arguments.callee() 可以用于函数递归调用自身

	arguments.length 函数实参的个数

	caller仅仅是当函数执行的时候才会有用，返回调用该函数的函数，如果是顶层函数调用的话，则返回null

    	var a = function() {
    		alert(a.caller);
    	}
    
    	var b = function() {
    		a();
    	}
    	
    	a() // 弹出null
    
    	b() // 弹出function() { a(); }

* 闭包的出现是基于什么机制的

	那必须是作用域机制，闭包的机制就是一个函数中返回另外一个函数，而返回的这个函数能够访问外围函数的属性，有一些oo的感觉

* window.onload和$(document).ready(function())的区别

	window.onload要等到页面上所有的元素加载完毕才会执行
	
	$(document).ready(function())在DOM树加载完毕的时候就会执行

	$(window).load() === window.onload

* 用setTimeout模拟setInterval

    	setTimeout(function() {
    		do something
    		setTimeout(arguments.callee, 1000);
    	}, 1000);
    	
    	===
    
    	setInterval(function() {
    		do something
    	}, 1000);

* 如何判断浏览器的种类和版本

	navigator.userAgent.indexOf('Chrome') //判断是哪种浏览器
	
	navigator.appVersion //浏览器的版本

	http://www.jb51.net/article/27876.htm

* 严格模式和非严格模式的一些区别

	http://blog.csdn.net/sunshinegirl_7/article/details/49510371
	
	1. 没有with语句，无法延长作用域链
	2. 使用未定义的变量，不再提示undefined，而是报错
	3. 没有caller，argument.callee
	4. 字面量属性重复报错
	5. eval，arguments变为关键字，不能作为变量名字
	6. 一般函数调用的时候，this指向undefined，不指向window

* javascript中的~运算符

    	var num = 25;
    	~num = -26; //也就是说非运算符其实就是原数的负数减去1

	这里小小复习一下，~~num可以对num取整数	

* DOM2的事件处理程序是addEventListener('click', function() {}, false)，这里想挖个坑的话，就问这样写如何清除这个事件处理程序，想当然脱口而出就是removeEventListener，那就完蛋了，**如果addEventListener的处理处理函数是匿名的话，是无法删除的**。

* setTimeout的第三个参数

	setTimeout可以通过第三个参数给其中的延时处理函数传递参数

    	for (var i = 0; i < 5; i++) {
    		setTimeout(function(j) {
    			console.log(j);
    		}, 1000, i);
    	}	
	
	这样的话，会在1s后同时输出0，1，2，3，4，也就证明将最后一个参数i成功传入了第一个函数中

	假如要延时输出0，1，2，3，4
	
    	for (var i = 0; i < 5; i++) {
    		setTimeout(function(j) {
    			console.log(j);
    		}, 1000 * i, i);
    	}

* 考察Javascript的运行机制的题目
	
	https://zhuanlan.zhihu.com/p/25407758

    	setTimeout(function() {
    	  console.log(1)
    	}, 0);
    	new Promise(function executor(resolve) {
    	  console.log(2);
    	  for( var i=0 ; i<10000 ; i++ ) {
    		i == 9999 && resolve();
    	  }
    	  console.log(3);
    	}).then(function() {
    	  console.log(4);
    	});
    	console.log(5);
	
	上面这个代码输出值是什么呢？

	首先碰到一个setTimeout，于是会先设置一个定时，在定时结束后(当前tick结束后)，将传递这个函数放到任务队列里面，因此开始肯定不会输出1。

	然后是一个Promise，里面的函数是直接执行的，因此应该直接输出2，3

	然后，Promise的then应当会放到当前tick的最后，但是还是在当期tick中

	因此，先输出5，再输出4

	最后，到下一个tick，就是输出1

	因此，输出的顺序是 2，3，5，4，1

* JS中变量名作为if条件的真/假
	
	以下情况会被认为返回false，""为空的字符串，为0的数字，为null的对象，为undefined的对象，布尔值false，其他情况下会被认为返回真

* Ajax的优点和缺点

	优点

	1. 页面不同刷新，局部刷新，用户体验好
	2. Ajax全名是异步javascript和xml，使用异步方式和服务器通信，具有更加迅速的响应能力
	3. 可以把以前一些服务端负担的工作转接到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和带宽成本。并且减轻服务器的负担，ajax的原则是"按需取数据"，可以最大程度的减少冗余请求，和响应对服务器造成的负担
	4. 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序。

	缺点
	
	1. ajax干掉了back按钮，即对浏览器后退机制的破坏。后退按钮是一个标准的web站点的重要功能，但是它没法和js进行很好的合作。这是ajax所带来的一个比较严重的问题，因为用户往往是希望能够通过后退来取消前一次操作的。那么对于这个问题有没有办法？答案是肯定的，用过Gmail的知道，Gmail下面采用的ajax技术解决了这个问题，在Gmail下面是可以后退的，但是，它也并不能改变ajax的机制，它只是采用的一个比较笨但是有效的办法，即用户单击后退按钮访问历史记录时，通过创建或使用一个隐藏的IFRAME来重现页面上的变更。（例如，当用户在Google Maps中单击后退时，它在一个隐藏的IFRAME中进行搜索，然后将搜索结果反映到Ajax元素上，以便将应用程序状态恢复到当时的状态。）
	但是，虽然说这个问题是可以解决的，但是它所带来的开发成本是非常高的，和ajax框架所要求的快速开发是相背离的。这是ajax所带来的一个非常严重的问题。
	2. 安全问题
	技术同时也对IT企业带来了新的安全威胁，ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。ajax的逻辑可以对客户端的安全扫描技术隐藏起来，允许黑客从远端服务器上建立新的攻击。还有ajax也难以避免一些已知的安全弱点，诸如跨站点脚步攻击、SQL注入攻击和基于credentials的安全漏洞等。
	3. 对搜索引擎的支持比较弱。
	4. 破坏了程序的异常机制。至少从目前看来，像ajax.dll，ajaxpro.dll这些ajax框架是会破坏程序的异常机制的。
	5. 另外，像其他方面的一些问题，比如说违背了url和资源定位的初衷。例如，有一个url地址，如果采用了ajax技术，也许在该url地址下面看到的和别人在这个url地址下看到的内容是不同的。这个和资源定位的初衷是相背离的。
	6. 一些手持设备（如手机、PDA等）现在还不能很好的支持ajax，如手机的浏览器上打开采用ajax技术的网站时，它目前是不支持的
	
* 什么时候你不能使用箭头函数？

	https://zhuanlan.zhihu.com/p/26540168

	逛知乎前端周刊看到的一个问题，记录一下。确实，使用箭头函数非常方便，但是乱用箭头函数可能会导致this指向window，然后就gg了。

	1. 定义字面量方法不能使用箭头函数
	2. 定义原型方法不能使用箭头函数
	3. DOM事件的回调函数不能使用箭头函数
	4. 定义构造函数不能使用箭头函数

	具体的例子见上面的URL

* Web 中的一些黑话（2333）

	写论文看到的Vanilla JavaScript，我一看，卧槽，这不是香草Javascript，一查才发现这就是原生Js的意思，然后Polyfill上面已经做过阐述了，就是兼容。

* 原生JS的Object上一些常用的方法（ES5/ES6）

	1. Object.defineProperty()              设置enumerabel等参数
	2. Object.getOwnPropertyDescriptor()    获取对象的一个属性的描述符
	3. Object.getOwnPropertyDescriptors()   获取对象全部属性的描述符
	3. Object.getOwnPropertyNames()         获取对象全部的属性（包括能用for-in遍历的和不能用for-in遍历的）
	4. Object.hasOwnProperty()              判断该属性是否是自己的，而不是原型上的
	5. Object.getOwnPropertySymbols()        获取对象中的symbol
	6. Object.create(proto[, 参数描述符]) 这里参数描述默认是不可枚举的，需要手动显示的将enumerable设为true                      
	7. Object.assign()   只拷贝对象本身的属性，不拷贝对象继承的属性，也不拷贝enumerable为false的属性，使用Object.assign({}, obj)可以拷贝自身对象，假如不想丢失原型上的属性，如下所示
    
    	var origin = Object.getPrototypeOf(obj);

    	var newObj = Object.assign(Object.create(origin), obj);

* js中的propertyIsEnumerable方法，用于判断对象的该属性是否是可枚举的，也即enumerate为true还是false

    如何获取对象的非枚举属性

    ```js
    Object.getOwnPrpertyNames(obj).filter((item) => !obj.propertyIsEnumerable(item));
    ```

* 原生JS中的prototype和__proto__的区别
	
	http://rockyuse.iteye.com/blog/1426510
	
	简单来说，这两个属性和原型链的关系都很大，prototype是构造函数的，而__proto__可以看作是对象的一个属性
	
    	function Person() {
    		Person.prototype.say = function() {
    			alert('name');
    		}
    	}
    
    	var person = new Person();
    
    	person.__proto__ === Person.prototype (true)

	其实prototype只是一个假象，他在实现原型链中只是起到了一个辅助作用，换句话说，他只是在new的时候有着一定的价值，而原型链的本质，其实在于__proto__。

* ES6模块和CommonJS模块之间的差异

	首先，他们有两个重大差异

	1. CommonJS模块输出的是一个值得拷贝，ES6模块输出的是值得引用，CommonJS会缓存原始值，内部的变化影响不到输出，除非写成一个函数，才能得到内部变动后的值，而ES6的import有点像Unix系统的“符号链接”，原始值变了，import加载的值也会跟着变化，因此，ES6模块是动态引用，并不会缓存值
	2. CommonJS模块是运行时加载，ES6模块是编译时输出接口，这是由于CommonJS加载的是一个对象，该对象只有在脚本运行完才会生成，而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成

* CommonJS模块的循环加载

	CommonJS模块发生循环加载的时候不会发送死循环，因为返回的是当前执行的部分的值，因此require()得出的就是一个{}

* 视图数据绑定的原理

	https://segmentfault.com/a/1190000006599500

	http://liuwanlin.info/jian-dan-de-shuang-xiang-shu-ju-bang-ding/

	简单的发布-订阅模式，Vue使用的是数据劫持，在我的github 2017ife的rep中有简单的实现
	
	https://github.com/mickey0524/2017ife/tree/master/vue-learning-5

* JavaScript中的继承

	在ES5，ES6中，继承其实都是基于原型链的操作，ES6中class的extends就是一个语法糖，但是ES5和ES6的this操作顺序是不同的，ES5的继承，其实是先创建子类的this，再通过Parent.apply()添加属性，而ES6的继承则是先创建父亲对象的this，然后再调用子类的构造函数修改this，因此在constructor函数中，必须先调用super()函数

    	class Child extends Parent {
    		constructor (x, y, z) {
    			super(x);
    			this.y = y;
    			this.z = z;
    		}
    	}

* http请求中get和post的区别

	1. get是幂等的操作，post不是幂等的操作
	2. get会将参数跟在url后面进行传递，而post请求则是作为http消息的实体内容发送给web服务器
	3. get请求对传输数据的大小有限制（通常不能大于2KB），而使用post方法传递数据量的上限要比get方法大得多（理论上不受限制）
	4. get方法默认会被浏览器缓存，容易被窃取

* JavaScript既然是单线程的，那么异步要怎么理解？

	所谓单线程，是指负责解释并执行JS代码的线程只有一个，其实还是有很多其他线程的，比如进行Ajax请求的线程、监控用户事件的线程、定时器线程、读写文件的线程（NodeJs中）等等。

	以Ajax来解释异步，主线程执行到发送Ajax的代码时，通知负责处理Ajax请求的线程(不妨叫做Ajax线程)：你给我发个请求吧，我把请求地址和参数都给你了。然后主线程就不管这事了，继续执行后面的代码。等Ajax线程收到HTTP响应的时候，就通知主线程：你的响应来了，快来取吧。主线程在合适的时候就会去取响应，进行适当的处理。

	多说一句，Ajax线程通知主线程的方式就是所谓的事件队列和事件循环机制。	

* JavaScript中可以使用！！来做强制布尔转型

* 请说出if([] == false) { console.log(1); }, if({} == false) { console.log(2); }, if([]) { console.log(3); }的最终结果

	嗯，今天下午，撇了一眼mentor桌上的面试题，看到了这道题，发现不会，深感基础薄弱gg，这道题的答案是1，3

	[] == false，这个比较首先对[]做了valueOf，然后调用toString变为“”，所以[] == false

	同理，String({}) == '[Object Object]' != false

	Boolean([])，当参数为object（排除null，undefined），返回true，详细可以看下方的url

	https://www.h5jun.com/post/why-false-why-true.html

* 函数节流的概念

	我相信很多童鞋，都用过函数节流的方法，但是听到这个名字的时候还是很陌生，嗯，函数节流就是避免响应短时间内发生的类似 resize，mouseover这种事件的技术，使用setTimeout就行啦

* mouseover和mouseenter两个事件的区别
    
     不论鼠标指针穿过被选元素或其子元素，都会触发 mouseover 事件。对应mouseout
    
     只有在鼠标指针穿过被选元素时，才会触发 mouseenter 事件。对应mouseleave

* 如何获取当前元素距离页面左上角的坐标

	1. offsetLeft, offsetTop累加
	2. scrollTop同理
	3. getBoundingClientRect().left, getBoundingClientRect().top (这里需要说明一下，getBoundingClientRect()用于得到DOM元素相对视口左上角的距离，这个在feed流之类的滚动页面中很有用，可以用于判断元素是否进入视口，用于记录曝光啥的)

	更多详细的解释见阮一峰大大的博客 http://www.ruanyifeng.com/blog/2009/09/find_element_s_position_using_javascript.html

* 一道前端新人OOP能力测试题

	实现一个前端标签管理器，M是标签容器，N是标签们
	
	要求：
	1. N始终是向左浮动布局，M的宽度可以任意
	2. 编码要求：纯粹的原生JS + CSS，JS要使用面向对象思想
	
		```
		var labels = new Labeler({
			container: '#container',
			data: [ '苹果', '香蕉', '黎' ...]
		});
		
		// 向后追加
		labeler.append('美国开心豆');
		
		labeler.prepend('日本豆腐');
		
		labeler.delete('香蕉');
		```
	
* 使用CDN的好处

	最近在做项目的时候，webpack打包出来的图片8192大小下的都会被转为base64编码的形式，而该大小之上的会被打包成单独的文件放在img文件下，这个时候就有两种选择方式了，设置publicPath，一种是让线上代码访问public目录，另外一种就是上传CDN，让线上代码访问CDN服务器，下面给出CDN的好处
	
	1. 访问速度有保障，稳定性也能提高
	2. 负载均衡
	3. cookie清除，避免访问静态文件的时候，也带上成堆的cookie
	4. 省下资源服务器的一部分资源负担，节省空间节省流量

* 实现一个flatten函数，将一个嵌套多层的数组 array（数组） (嵌套可以是任何层数)转换为只有一层的数组，数组中元素仅基本类型的元素或数组，不存在循环引用的情况。 Ex: flatten([1, [2], [3, [[4]]]]) => [1, 2, 3, 4]

	```js
	/*方法1*/
	function flatten(arr){
	  var newArr = []
	  function _flat(arr){
	    arr.forEach(val=>{
	      if(Array.isArray(val)){
	        _flat(val)
	      }else{
	        newArr.push(val)
	      }
	    })    
	  }
	  _flat(arr)
	  return newArr
	}
	
	/*方法2*/
	
	function flatten2(arr){
	  return arr.reduce(function(initArr, currentArr){
	    return initArr.concat(Array.isArray(currentArr)?flatten2(currentArr):currentArr)
	  }, [])
	}
	
	```

* 实现一个reduce函数，作用和原生的reduce类似，reduce(list, iteratee, [memo])，memo是reduce函数的初始值，会被每一次成功调用iteratee函数的返回值所取代 。这个迭代传递4个参数：memo,value 和 迭代的index和最后一个引用的整个 list。如果没有memo传递给reduce的初始调用，iteratee不会被列表中的第一个元素调用。第一个元素将取代memo参数传递给列表中下一个元素调用的iteratee函数。

	```js
	function reduce(list, f, initValue) {
	    let res = initValue ? initValue : list[0];
	    let index = initValue ? 0 : 1;
	    for (let i = index, len = list.length; i < len; i++) {
	        res = f(res, list[i]);
	    }
	    return res;
	}
	```

* 实现一个map函数，模拟原生的map函数，map(list, iteratee)，通过对list里的每个元素调用转换函数(iteratee迭代器)生成一个与之相对应的数组。iteratee传递三个参数：value，然后是迭代 index。
	
	```js
	function map(list, iteratee) {
	    let result = [];
	    for (let i = 0, len = list.length; i < len; i++) {
	        result.push(iteratee(list[i], i, list));
	    }
	    return result;
	}
	```

* getComputedStyle的用法

	不知道这个函数的用法之前，只用能过DOM.style获取定义在标签内部的样式，而window.getComputedStyle()方法能够给出活动样式表之后的元素的所有CSS属性的指，这就很舒服了，不过需要注意的是，解析这些值可能包含基本的计算，举个例子，移动端width和height的rem数值为小数的话，DOM.offsetWidth和getComputedStyle(el).width获得的数值是不一样的～

	getComputedStyle == document.defaultView.getComputedStyle // true

* initial-scale兼容性

	initial-scale在中低端android机并不完全支持，所以动态缩放之后布局视窗大于虚拟视窗，导致出现横向滚动条。移动端动态生成meta标签之后，判断window.innerWidth和document.documentElement.clientWidth，如果后者大于前者，则需要再次修改meta标签，制定width=window.innerWidth，伪代码如下:

	```
	docElem.getBoundingClientRect().width > win.innerWidth ? res.changeScale() : res.changeScale("initial");    
	 ...      
	var metaWidth = this.scale == '1.00' ? 'device-width' : win.innerWidth;
	       metaElem.setAttribute("content", "width="+metaWidth+",initial-scale=" + this.scale + ", maximum-scale=" + this.scale + ", minimum-scale=" + this.scale + ", user-scalable=no");
	```

* [].reduce(Math.pow)

	这个语句会抛出TypeError，原因是reduce on an empty array without an initial value throws TypeError

* 下面的函数会返回什么？

	```
	var name = 'World!';
	(function() {
		if (typeof name === 'undefined') {
			var name = 'Jack';
			console.log('Goodbye ' + name);
		} else {
			console.log('Hello ' + name);	
		}
	})();
	```

	第一眼看过去，惯性思维，name会按作用域链向上访问，于是name = 'world'，那就gg了，这里隐藏了一个变量提升的问题，上面的代码等同于
	
	```
	var name = 'World!';
	(function() {
		var name;
	        if (typeof name === 'undefined') {
	                name = 'Jack';
	                console.log('Goodbye ' + name);
	        } else 
	             console.log('Hello ' + name);
		}
	})();
	```

	这就一目了然了，因此，会输出Goodbye Jack

* 这个语句的结果是啥呢？

	```
	var END = Math.pow(2, 53);
	var START = END - 100;
	var count = 0;
	for (var i = START; i <= END; i++) {
	    count++;
	}
	console.log(count);
	``` 

	答案是这是一个无限循环，JavaScript能够安全的表达[-2^53 + 1, 2^53 - 1]范围内的数，2^53是能表达的最大的不安全的数值，超过这个数值都会转为2^53，因此上面是一个无限循环

* 下面语句的结果是啥？

	```
	var ary = [0,1,2];
	ary[10] = 10;
	ary.filter(function(x) { return x === undefined;});
	```

	不会为缺少的元素调用filter方法

* JavaScript精度一直存在问题，不光是小数存在问题，大数计算的时候也存在问题

	0.8 - 0.6 != 0.2，emmmmmm
	
	```
	var a = 111111111111111110000,
	
	    b = 1111;
	    
	a + b = 111111111111111110000;
	```

* Array.prototype是一个数组

	Array.isArray( Array.prototype ) = true

* 3.toString()报错，3..toString() == '3'

* 下面这个语句返回啥呢？

	`"1 2 3".replace(/\d/g, parseInt)`
	
	其实调用parseInt的是[1, 0], [2, 2], [3, 4]，分别为匹配项和index

因此这个语句得到的结果是[1, NaN, 3]

* 下面这个问题返回啥呢？

	```
	var lowerCaseOnly =  /^[a-z]+$/;
	[lowerCaseOnly.test(null), lowerCaseOnly.test()]
	```
	
	正则匹配的话，执行的事...test('null')和...test('undefined')

* 使用chrome devtool调试代码的办法

	http://www.css88.com/archives/8175

* 浏览器端的Object.assign()兼容性并不好，需要使用插件才能使用，这里用原生js实现Object.assign()的兼容写法
	
	```js
	var _extend = Object.assign || function(target) {
		for (var i = 1; i < arguments.length; i++) {
			var source = arguments[i];
			for (var key in source) {
				if (Object.prototype.hasOwnProperty.call(source, key)) {
					target[key] = source[key];
				}	
			}
		}
		return target;
	}
	``` 

* 当用户关闭了JavaScript，可以通过`<noscript>开启JavaScript，获得更好的体验</noscript>`来提示用户

* 简单的js响应式初始化代码
	
	```js
	var documentElement = document.documentElement;
	function callback() {
		var clientWidth = documentElement.clientWidth;
		// 屏幕宽度大于780，不再放大
		clientWidth = clientWidth < 780 ? clientWidth: 780;
		documentElement.style.fontSize = clientWidth / 10 + 'px';
	}
	
	document.addEventListener('DOMContentLoaded', callback);
	window.addEventListener('orientationchange' in window ? 'orientationchange' : 'resize', callback);
	```

* javaScript中的in操作符

	记录这个知识点是为了不和python混淆，python中的in操作符可以用来判断字串是否存在于字符串中，元素是否存在于数组中，key是否存在于dict中，然而在js中，in最好只用于判断key是否存在于对象中，当in用于数组的时候，其实判断的是索引是否存在，其实很好理解，arr是可遍历的(iterable)，可以理解为一个index: value的健值对，参照对象，用于判断的自然就是index了。

* javaScript中的document.documentElement.scrollTop，document.body.scrollTop以及window.pageYOffset之间的异同

	window.pageYOffset 在IE9版本之下不兼容，IE6，7，8支持document.documentElement.scrollTop，但是document.body.scrollTop与document.documentElement.scrollTop两者有个特点，就是同时只会有一个值生效，比如document.body.scrollTop能取到值的时候，document.documentElement.scrollTop就会始终为0，反之亦然，因此可以按照如下的写法

	window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop 

* javaScript中判断节点是否已经被插入DOM树

	今天看了一下vue-infinite-scroll的源码，记录一下如何判断节点是否被插入DOM树的代码，让我想记录的是他们的严谨性，他们判断了挂在指令的节点是否存在fragment，fragment的nodeType为11～

	```
	var isAttached = function isAttached(element) {
		var currentNode = element.parentNode;
		while (currentNode) {
	 		if (currentNode.tagName === 'HTML') {
	    		return true;
	 		}
	 		if (currentNode.nodeType === 11) {
	    		return false;
	  		}
	  		currentNode = currentNode.parentNode;
		}
		return false;
	};
	```

* 使用``和${}来拼接innerHTML的时候，要注意，html的自定义属性需要加上双引号，虽然有时候不加也能正常显示，不过这是由于浏览器自动纠错，当自定义属性的数值为JSON字符串的时候，记得不要加双引号，要用单引号，坑死了～

* 我们在使用new Date()创建一个特定时间对象的时候，最好采用new Date(timestamp)，这样不会由于时区问题导致得到错误的数据，在chrome中，我们使用new Date('2017-11-11 11:11:11')也能得到正确的时间实例，但是在safari中，这样会得到`Invalid Date`，需要加上时区，new Date('2017-11-15T06:06:14.330Z')这样，显得比较麻烦，所以最好使用timestamp～

* 写一个_new()函数，实现new关键字的功能

	分析一下，new运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一

	new的结果会是一个新的对象，所以在模拟实现的时候，我们也需要建立一个新的对象，这个对象需要去继承构造函数的原型，然后需要以该对象作为this作用域获取实例属性

	```
	function _new() {
		var obj = new Object();
		var Constructor = [].shift.call(arguments);
		obj.__proto__ = Constructor.prototype;
		var ret = Constructor.apply(obj, arguments);
		return typeof ret === 'object' ? ret || obj : obj; 
	}
	```

	最后使用typeof判断是否为object的原因是因为构造函数默认返回的是this实例，但是也可以显示的返回一个对象，这个时候就需要进行过滤判断	

* JS调试技巧
	* console.table()
		
		在chrome控制台可以用console.table()以表格的形式显示一个对象
	* 用console.time()和console.timeEnd()来计算循环耗时
		
		```js
		console.time('time1');
		var a = 1;
		for (let i = 0; i < 10; i++) {
			a += 1;
		}
		console.timeEnd('time1');
		```
	
* 在正则表达式里面使用变量只能用new RegExp()生成，用字面量方法没办法用变量

	```
	var name = 'mickey';
	var re = new RegExp(name, 'gi');
	re == /mickey/gi;
	```
* JS中将数字转为字符串的四种方式

	"" + 1, String(), toString(), new String()

* Babel默认只转换新的JS句法(syntax)，而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy等全局对象，以及一些定义在全局对象上的方法(比如Object.assign)都不会转码，这个时候需要引入babel-plugin-transform-runtime，默认transform-runtime会引入bable-polyfill，这样当识别出对应代码的时候，会进行转码，下面给出最基本的.babelrc

    ```js
    {
        "presets": ["es2015", "stage-2"],
        "plugins": ["transform-runtime"],
        "comments": false
    }
    ```

* js中的replace(re, (matched, capture1...))中的函数中的参数意义

    ```js
    '2018-02-06'.replace(re, (
        matched, // 整个匹配结果 2018-02-06
        capture1, // 第一个组匹配 2018
        capture2, // 第二个组匹配 02
        capture3, // 第三个组匹配 06
        position, // 匹配开始的位置 0
        S, // 原字符串 2018-02-06
        groups // 具名组构成的一个对象(ES2018新增的) 
    ))
    ```

* js中三种string转number的效率，易得，Number效率最高

    ```js
    console.time('time1')
    var b = +'1'; 
    console.timeEnd('time1');
    console.time('time2');
    var c = parseInt('1');
    console.timeEnd('time2');
    console.time('time3');
    var c = Number('1');
    console.timeEnd('time3');

    time1: 0.01708984375ms
    time2: 0.010009765625ms
    time3: 0.005859375ms
    ```

* js中Array的参数数目不同的时候，表现是不同的，可以用ES6的Array.of()代替，或者下面的自定义函数

    ```js
    Array() // []
    Array(3) // [empty * 3]
    Array(3, 11) // [3, 11]
    
    function ArrayOf(){
        return [].slice.call(arguments);
    }
    ```

* js中 == 操作的具体步骤

    ```js
    x == y
    1. 如果x不是正常值（比如抛出一个错误），中断执行
    2. 如果y不是正常值，中断执行
    3. 如果Type(x)与Type(y)相同，执行严格相等运算x === y
    4. 如果x是null，y是undefined，返回true
    5. 如果x是undefined，y是null，返回true
    6. 如果Type(x)是数值，Type(y)是字符串，返回x == ToNumber(y)的结果
    7. 如果Type(x)是字符串，Type(y)是数值，返回ToNumber(x) == y的结果
    8. 如果Type(x)是布尔值，返回ToNumber(x) == y的结果
    9. 如果Type(y)是布尔值，返回x == ToNumber(y)的结果
    10. 如果Type(x)是字符串或数值或Symbol值，Type(y)是对象，返回x == ToPrimitive(y)的结果
    11. 如果Type(x)是对象，Type(y)是字符串或数值或Symbol值，返回ToPrimitive(x) == y的结果
    12. 返回false
    ```

* js标准里是如何定义加法过程

    ```js
    AdditiveExpression: AdditiveExpression + MutiplicativeExpression
    1. 把AdditiveExpression的result赋值为lref
    2. 把GetValue(lref)的结果赋值给lval
    3. 把MultiplicativeExpression的result赋值给rref
    4. 把GetValue(rref)的结果赋值给rval
    5. 把ToPrimitive(lval)的结果赋值给lprim
    6. 把ToPrimitive(lval)的结果赋值给rprim
    7. 如果Type(lprim)和Type(rprim)中有一个是String，则
        a. 把ToString(lprim)的结果赋给lstr
        b. 把ToString(rprim)的结果赋给rstr
        c. 返回lstr和rstr拼接的字符串
    8. 把ToNumber(lprim)的结果赋给lnum
    9. 把ToNumber(rprim)的结果赋给rnum
    10. 返回lnum和rnum相加的数值
    ```

* js中 ToPrimitive(input[, PreferredType]) 操作的具体步骤

    ```js
    ToPrimitive(input[, PreferredType])

    1. 如果没有传入PreferredType参数，则让hint的值为'default'
    2. 否则，如果PreferredType值为String，则让hint的值为'string'
    3. 否则，如果PreferredType值为Number，则让hint的值为'number'
    4. 如果input对象有@@toPrimitive方法，则让exoticToPrim的值为这个方法，否则让exoticToPrim的值为undefined
    5. 如果exoticToPrim的值不为undefined，则
        a. 让result的值为调用exoticToPrim之后的值
        b. 如果Type(result)不是Object，则返回result
        c. throw一个TypeErrory异常
    6. 否则，如果hint的值为'default'，则把hint的值重新赋为'number'
    7. 返回 OrdinaryToPrimitive(input, hint)
    
    OrdinaryToPrimitive(input,hint)

    1. 如果hint的值为'string'，则
        a. 调用input对象的toString()方法，如果Type(result)不是Object，则返回
        b. 否则，调用input对象的valueOf()方法，如果Type(result)不是Object，则返回
        c. 否则，抛出TypeError错误
    2. 如果hint的值为'number'，则
        a. 调用input对象的valueOf()方法，如果Type(result)不是Object，则返回
        b. 否则，调用input对象的toString()方法，如果Type(result)不是Object，则返回
        c. 否则，抛出TypeError错误
    ```

* Swiper常用于移动端网站的内容触摸滑动，但是层叠的轮播例子却不多，可以用`onProgress(swiper, progress)`(切换视图的时候获取每一个slide的progress数值，用于设置style属性的改变)和`onSetTransition(swiper, transition)`(获取swiper实例的speed，可以用于设置slide的动画transitionDuration)，下面给出一个例子

	```js
	new Swiper('.swiper-container', {
        direction: 'horizontal',
        slidesPerView: 'auto',
        loop: true,
        centeredSlides: true,
        watchSlidesProgress: true,
        autoplayDisableOnInteraction: false,
        autoplay: 5000,
        onSlideChangeEnd: (swiper) => {
          const bullets = this.$pagination.children('.J_swiper-pagination-bullet');
          bullets.removeClass('active');
          bullets.eq(swiper.realIndex).addClass('active');
        },
        onProgress: (swiper) => {
          for (let i = 0, len = swiper.slides.length; i < len; i++) {
            const slide = swiper.slides[i];
            const progress = Math.round(Math.abs(slide.progress));
            const progresSymbol = slide.progress < 0 ? -1 : 1;
            const scale = 1 - progress * 0.2; // eslint-disable-line no-mixed-operators
            const opacity = 1 - Math.min(progress / 5, 1); // eslint-disable-line no-mixed-operators
            const slideStyle = slide.style;
            let zIndex;
            switch (progress) {
              case 0: zIndex = 4; break;
              case 1: zIndex = 3; break;
              case 2: zIndex = 2; break;
              case 3: zIndex = 1; break;
              default: zIndex = 0; break;
            }
            slideStyle.webkitTransform = `scale(${scale}, ${scale}) translateX(${progresSymbol * 50 * progress}%)`;
            slideStyle.transform = slideStyle.webkitTransform;
            slideStyle.opacity = opacity;
            slideStyle.zIndex = zIndex;
          }
        },
        onSetTransition: (swiper, transition) => {
          for (let i = 0, len = swiper.slides.length; i < len; i++) {
            const slideStyle = swiper.slides[i].style;
            slideStyle.webkitTransitionDuration = `${transition}ms`;
            slideStyle.transitionDuration = `${transition}ms`;
          }
        }
    });
	```

* 一道js的正则表达式相关的题目

	> 用js的正则表达式实现下面的功能：
	>
	> 2[ab] 输出 abab
	>
	> 2[a3[b2[c]]]   输出  abccbccbccabccbccbcc

	```js
	var str1 = '2[a3[b2[c]]]';
	var str2 = '2[ab]';

	var getDecryption = (str) => {
		var re = /^(.*)(\d+)\[(.*?)\](.*)$/g;
		while (re.test(str)) {
			str = str.replace(re, ($0, $1, $2, $3, $4) => {
				var tmp = '';
				for (let i = 0; i < $2; i++) {
					tmp += $3;
				}
				return $1 + tmp + $4;
			});  
		}
		return str;
	}

	console.log(getDecryption(str1));
	console.log(getDecryption(str2));
	```

* toString函数详解

	可以使用toString函数将值转换为字符串，这个功能在不同原型之间有所不同

	* String.prototype.toString 函数

		返回一个字符串的值

		```js
		const name = 'mickey';
		name.toString(); // 'mickey'
		String.prototype.toString.call('mickey') // 'mickey'
		String.prototype.toString.call({}) // Uncaught TypeError: String.prototype.toString requires that 'this' be a String
		```

	* Number.prototype.toString 函数

		返回转换为String的数字，可以输入进制数

		```js
		(15).toString(); // '15'
		(15).toString(2); // '1111'
		(15).toString(2); // '-1111'
		```
	
	* Symbol.prototype.toString 函数

		返回`Symbol(${description})`

		```js
		Symbol('mickey').toString(); // 'Symbol(mickey)'
		```

	* Boolean.prototype.toString 函数

		返回'true'和'false'

	* Object.prototype.toString 函数

		Object 调用内部 [[Class]] 。它是代表对象类型的标签。
		
		Object.prototype.toString 返回一个 `[object ${tag}]` 字符串。 要么它是内置标签之一 (例如 “Array”, “String”, “Object”, “Date” ), 或者它被明确设置

		```js
		const name = 'mickey';
		Object.prototype.toString.call(name); // '[object String]'
		```

		随着 ES6 的推出，设置自定义标签可以用`Symbol`来完成

		```js
		const stu = {
			name: 'mickey',
		};
		console.log(stu.toString()); // '[object object]'
		stu[Symbol.toStringTag] = 'student';
		console.log(stu.toString()); // '[object student]'
		
		const Student = function(name) {
			this.name = name;
		}
		Student.prototype[Symbol.toStringTag] = 'student';

		const stu = new Student('mickey');
		stu.toString(); // '[object student]'

		class Student {
			constructor(name) {
				this.name = name;
			}
			get [Symbol.toStringTag]() {
				return 'student';
			}
		}

		const stu = new Student('mickey');
		stu.toString(); // '[object student]'
		```

	* Array.prototype.toString 函数
	
		在每个元素上调用toString并返回一个字符串，所有的输出用逗号分隔。

		```js
		const arr = [
			{},
			2,
			3,
		];

		arr.toString(); // "[object object],2,3"
		```

* typeof实现的原理是什么

    不同的对象在底层都表现为二进制，在 javascript 的最初版本中，使用的32位系统，为了性能考虑使用低位存储了变量的类型信息

    * 000: 对象
    * 1: 整数
    * 010: 浮点数
    * 100: 字符串
    * 110: 布尔    

* instanceof实现的原理是什么

    instance instanceof constructor

    原理就是Object.getPrototypeOf(instance) === constructor.prototype

* 10 个 Ajax 同时发起请求，全部返回展示结果，并且至多允许三次失败，说出设计思路

    这个问题相信很多人会第一时间想到 Promise.all ，但是这个函数有一个局限在于如果失败一次就返回了，并不符合题意，所以我们需要自己实现下

    ```js
    // 以下是伪代码，着重于思路
    let successCount = 0;
    let errorCount = 0;
    let datas = [];
    ajax(url, (res) => {
        if (success) {
            success++;
            if (success + errorCount === 10) {
                 console.log(datas);
            } else {
                 datas.push(res.data);
            }
        } else {
            errorCount++;
            if (errorCount > 3) {
                // 失败次数大于3次就应该报错了
                 throw Error('失败三次');
            }
        }
    });
    ```

* js中的Event Loop

    总所周知，js是门非阻塞单线程语言，因为在最初 JS 就是为了和浏览器交互而诞生的。如果 JS 是门多线程的语言话，我们在多个线程中处理 DOM 就可能会发生问题（一个线程中新加节点，另一个线程中删除节点），当然可以引入读写锁解决这个问题

    JS 在执行的过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 Task（有多种 task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说 JS 中的异步还是同步行为

    不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（microtask） 和 宏任务（macrotask）。在 ES6 规范中，microtask 称为 jobs，macrotask 称为 task

    微任务包括 process.nextTick ，promise ，Object.observe ，[MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)
宏任务包括 script ， setTimeout ，setInterval ，setImmediate ，I/O ，UI rendering

    很多人有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了 script ，浏览器会先执行一个宏任务，接下来有异步代码的话就先执行微任务

    [从一道题浅说 JavaScript 的事件循环](https://github.com/dwqs/blog/issues/61)

    * 浏览器中正确的一次Event Loop顺序是这样的

        1. 执行同步代码，这属于宏任务
        2. 执行栈为空，查询是否有微任务执行
        3. 执行所有微任务
        4. 必要的时候渲染UI
        5. 执行宏代码中的异步代码，开始下一次Event Loop
        
        ![图解event loop](../images/4.png)    

    * Nodejs中的一次 Event Loop

        1. timers：会执行setTimeout和setInterval，一个 timer 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟，下限的时间有一个范围：[1, 2147483647] ，如果设定的时间不在这个范围，将被设置为1
        2. I/O阶段会执行除了close事件，定时器
        3. idle, prepare，内部实现
        4. poll
        	
        	1. 执行到点的定时器
        	2. 执行poll队列中的事件

        	并且当 poll 中没有定时器的情况下，会发现以下两件事情
        	
        	1. 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
        	2. 如果 poll 队列为空，会有两件事发生

        		* 如果有 setImmediate 需要执行，poll 阶段会停止并且进入到 check 阶段执行 setImmediate
        		* 如果没有 setImmediate 需要执行，会等待回调被加入到队列中并立即执行回调

        5. check 阶段执行 setImmediate
        6. close callbacks 阶段执行 close 事件
    
* js有趣的链式执行(在vue源码中，有很多类似写法)

    ```js
    let d;
    (d = []).push('asd')
    console.log(d) // ['asd']

    const event = {}
    (event[name] || (event[name] = [])).push('mickey0524')
    event // {name: ['mickey0524']}
    ```

* 使用Object.prototype.hasOwnProperty.call(obj, key)而不是obj.hasOwnProperty(key)的原因

    * 可以用使用Object.create(null)(丢弃Object原型，提高效率)创建的JavaScript对象，它将有一个null[[prototype]]链，因此在它上面没有hasOwnProperty()，无法使用obj.hasOwnProperty(key)
    * 为了安全考虑，可能在对象初始化的时候，赋予了一个hasOwnProperty的方法，这样的话，不会去原型链上查找

* js实现cache函数，缓存经常调用的函数的结果(vue源码)

    ```js
    function cached(fn) {
        const cache = Object.create(null);
        return function(str) {
            return cache[str] || (cache[str] = fn(str));
        };
    }

    const capitalize = cached((str) => {
        return str.charAt(0).toUpperCase() + str.slice(1);
    });
    ```

* js中实现once的思路(onceCall，vue指令中的$once)

    ```
    function once(fn) {
        let called = false;
        return function () {
            if (called) {
                called = true;
                fn.apply(this, arguments);
            }
        }
    }
    ```

* 特殊字符\u2028导致的Javascript脚本异常，在JSON解析的时候，\u2028会被理解为换行符，导致JSON解析错误，记录一下，另外，JS中的特殊字符一共有13个，建议都进行转义处理

	|    Unicode 字符值    | 转义序列 | 含义 | 类别 |
	| ------------------- | ------- | --- | --- |
	| \u0008 | \b | Backspace | |
	| \u0009 | \t	 | Tab	| 空白 |
	| \u000A | \n|换行符（换行）|	行结束符|
	|\u000B  |\v	|垂直制表符|	空白|
	|\u000C  |\f	|换页|	空白|
	|\u000D	|\r	|回车|	行结束符|
	|\u0022	|\"	|双引号| (")|	 
	|\u0027	|\'	|单引号| (')|	 
	|\u005C	|\\	|反斜杠 |(\)|	 
	|\u00A0	| 	|不间断空格	|空白|
	|\u2028	| 	|行分隔符|	行结束符|
	|\u2029	| 	|段落分隔符|	行结束符|
	|\uFEFF	| 	|字节顺序标记|	空白|
	
* String.raw的用法

    String.raw是一个模版字符串的标签函数，可以用于转义\m或\u2028这样的字符

    String.raw\`asd\masd\` // asd\\masd
    
    asd\\masd // asdasd

* JS中如何判断一个类是否是系统自带的

    ```js
    // Ctor === constructor
    function isNative(Ctor) {
        typeof Ctor === 'function' && /native code/.test(Ctor.toString());
    }
    ```

* JS数组快速clear的方法

    ```js
    var a = [1, 2, 3];
    a.length = 0; // []
    ```

* 记录一道很坑的js题

    ```js
    let x = 0;
    async function test() {
        x += await 2;
        console.log(x);
    }
    
    test();
    x += 1;
    console.log(x);
    ```

    结果是1，2

    ```js
    let x = 0;
    async function test() {
        x = await 2 + x;
        console.log(x);
    }
    
    test();
    x += 1;
    console.log(x);

    结果是1，3

    原因是前面的先取了x的数值，然后等待await 2，后面的直接等待await 2，取x的时候，x已经为1了
    ```

* js中实现python list的extend方法

    ```js
    let arr = [];
    arr.push.apply(arr, [1, 2, 3]);
    ```

* document.createComment()创建一个注释节点，得到的node的nodeType为8

* ele.hasChildNodes()判断当前dom节点是否含有子节点

* js中的基本数据类型，面试的时候容易混淆

    String, Number, Boolean, Null, Undefined

* js正则表达式容易混淆的带有?的操作符，js不支持?<=和?\<\!

    * (?:...) 只组合，把项组合到一个单元，但不记忆与该组相匹配的字符
    * (?=p) 零宽正向先行断言，要求接下来的字符都与p匹配，但不能记忆匹配p的那些字符，同时作为下一次匹配的开始位置
    * (?!p) 零宽负向先行断言，要求接下来的字符不与p匹配

* js中的正则表达式有字面量和构造函数两种方式，但是需要注意的是，传递给RegExp构造函数的两个参数都是字符串，所以在某些情况下要对字符进行双重转义，看下面的例子

	|字面量模式|等价的字符串|
	|:--------:|:------------:|
	|/\\[bc\\]at/|"\\\\[bc\\\\]at"|
	|/\w\\\\hello\\\\123/|"\\\w\\\\\\\\hello\\\\\\\\123"|
	
* 执行线程中的定时器执行

    首先，我们看一张图，来自于《js忍者秘籍》

    ![展示了单线程中主线代码和处理程序代码执行的时间图](../images/5.png)

    让我们看看这里发生了什么事情:

    1. 首先在0毫秒的时候有一个持续18ms的js代码块要执行
    2. 然后在0毫秒的时候设了两个10ms延迟的定时器，**setTimeout**以及**setInterval**，**setTimeout**先设定
    3. 在第6ms的时候有一个发生了鼠标单击事件

    同时发生了这么多事情，由于js的单线程特效，**当线程处于执行状态，有异步事件触发时，它就会排队，并且在线程空闲时才进行执行**，这里的异步事件包括：鼠标点击，定时器触发，ajax请求、promise等事件
    
    让我们回到栗子中：
    
    栗子中首先有一个18毫秒的代码块要执行，在这18毫秒中只能执行这段代码块，其他事件触发了之后只能排队等待执行。在代码块还在运行期间，第6毫秒的时候，发生了一个鼠标单击事件，以及第10毫秒时的setTimeout和setInterval两个处理程序，这三个事件不能立即执行，而是被添加到等待执行的队列中。
    
	<h3>先进先出(先排队的先执行)</h3>

	18毫秒的时候代码块结束执行，有三个任务在排队等待执行，根据先进先出的原则，此时会先执行click事件，setTimeout和setInterval将继续排队等待执行。
setInterval调用被废弃

	在click事件执行时，第20毫秒处，第二个setInterval也到期了，因为此时已经click事件占用了线程，所以setInterval还是不能被执行，并且因为此时队列中已经有一个setInterval正在排队等待执行，所以这一次的setInterval的调用将被废弃。浏览器不会对同一个setInterval处理程序多次添加到待执行队列。

	<h3>setTimeout/setInterval无法保证准时执行回调函数</h3>

	click事件在第28毫秒处结束执行，有两个任务(setTimeout和setInterval)正在等待执行，遵循先进先出的原则，setTimeout早于setInterval设定，所以先执行setTimeout。
	
	so:我们期望在第10毫秒处执行的setTimeout处理程序，最终会在第28毫秒处才开始执行，这就是上文提到的setTimeout/setInterval无法保证准时执行回调函数。

	在30毫秒处，setInterval又触发了，因为队列中已经有setInterval在排队，所以这次的触发又作废了。

	<h3>setInterval的连续执行</h3>

	setTimeout执行结束，在第36毫秒处，队列中的setInterval处理程序才开始执行，setInterval需要执行6毫秒。

	在第40毫秒的时候setInterval再次触发，因为此时上一个setInterval正在执行期间，队列中并没有setInterval在排队，这次触发的setInterval将进入队列等候。
	
	所以：setInterval的处理时长不能比设定的间隔长，否则setInterval将会没有间隔的重复执行
第42毫秒的时候，第一个setInterval结束，然后队列中的setInterval立即开始执行，在48毫秒的时候完成执行。然后50毫秒的时候再次触发setInterval，此时没有任务在排队，将会立即执行。

	<h3>setTimeout按照一定的间隔周期性的触发定时器</h3>

	上文说了，setInterval的处理时长不能比设定的间隔长，否则setInterval将会没有间隔的重复执行。
	
	但是对这个问题，很多情况下，我们并不能清晰的把控处理程序所消耗的时长，为了我们能按照一定的间隔周期性的触发定时器，忍者秘籍中提供了下面这种使用方法：
    
    ```
    setTimeout(function repeatMe(){
      // do something
      setTimeout(repeatMe,10); 
      // 执行完处理程序的内容后，在末尾再间隔10毫秒来调用该程序，这样就能保证一定是10毫秒的周期调用
    },10)
    ```

* 前端容灾可用手段

    * localStorage缓冲接口数据
    * 备份一份静态数据到CDN
    * 利用Service worker的cached API做页面接口缓存，Service worker提供了fetch事件可供监听，当页面发出请求的时候，会先过fetch方法，你可以在这里定义任何你需要的缓存策略，比如请求成功后，将结果存入caches。Service worker中，你甚至可以缓存当前页面的HTML，让网站离线运行

* addEventListener()的第三个参数

    印象中的addEventListener()是`target.addEventListener(type, listener[, useCapture])`

    新的语法：

    target.addEventListener(type, listener[, {capture: Boolean, once: Boolean, passive: Boolean});

    1. type表示监听事件类型的字符串
    2. listener当所监听的事件类型触发的时候，会接收到一个事件通知
    3. options(可选)
        1. capture 表示listener会在该类型的事件捕获阶段传播到该EventTarget时触发
        2. once 表示listener在添加之后最多调用一次，如果是true, listener会在其被调用之后自动删除
        3. passive表示listener永远不会调用preventDefault()。如果listener仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告

* js实例的constructor属性

    ```js
    function Person() {}
    let person = new Person();
    console.log(person.constructor === Person); // true
    ```

    当获取`person.constructor`时，其实person中并没有constructor属性，当不能读取到constructor属性时，会从person的原型也就是Person.prototype中读取，正好原型中有该属性，所以：

    `person.constructor === Person.prototype.constructor`

* 静态作用域与动态作用域

    js采用的是词法作用域，也就是静态作用域，函数的作用域在函数定义的时候就决定了

    而与词法作用域相对的是动态作用域，函数的作用域是在函数调用的时候才决定的

    ```js
    var value = 1;
    
    function foo() {
        console.log(value);
    }

    function bar() {
        var value = 2;
        foo();
    }
    
    bar(); // 1
    ```

    ```bash
    bash的栗子

    value = 1
    function foo() {
        echo $value;
    }
    function bar() {
        local value = 2;
        foo;
    }
    bar # 2
    ```
    
* 函数执行上下文

	### 函数上下文

	在函数上下文中，我们用活动对象（activation object，AO）来表示变量对象
	
	活动对象和变量对象其实是一个东西，只是变量对象是规范上的或者说是引擎实现上的，不可在 JavaScript 环境中访问，只有到当进入一个执行上下文中，这个执行上下文的变量对象才会被激活，所以才叫 activation object 呐，而只有被激活的变量对象，也就是活动对象上的各种属性才能被访问。

	活动对象是在进入函数上下文时刻被创建的，它通过函数的 arguments 属性初始化。arguments 属性值是 Arguments 对象。
	
	### 执行过程
	
	执行上下文的代码会分成两个阶段进行处理：分析和执行，我们也可以叫做：
	
	1. 进入执行上下文
	2. 代码执行

	#### 进入执行上下文
	
	当进入执行上下文的时候，这时候还没有执行代码
	
	变量对象会包括：
	
	1. 函数的所有形参（如果是函数上下文）
	
		1. 由名称和对应值组成的一个变量对象的属性被创建
		2. 没有实参，属性值设为 undefined

	2. 函数声明

		1. 由名称和对应值（函数对象(function-object)）组成一个变量对象的属性被创建
		2. 如果变量对象已经存在相同名称的属性，则完全替换这个属性

	3. 变量声明

		1. 由名称和对应值（undefined）组成一个变量对象的属性被创建
		2. 如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性
	
	举个栗子
	
	```js
	function foo(a) {
	  var b = 2;
	  function c() {}
	  var d = function() {};
	
	  b = 3;
	
	}
	
	foo(1);
	```
	
	在进入执行上下文后，这时候的AO是：
	
	```js
	AO = {
	    arguments: {
	        0: 1,
	        length: 1
	    },
	    a: 1,
	    b: undefined,
	    c: reference to function c(){},
	    d: undefined
	}
	```
	
	#### 代码执行
	
	在代码执行阶段，会顺序执行代码，根据代码，修改变量对象的值

	还是上面的例子，当代码执行完后，这时候的 AO 是：
	
	```js
	AO = {
	    arguments: {
	        0: 1,
	        length: 1
	    },
	    a: 1,
	    b: 3,
	    c: reference to function c(){},
	    d: reference to FunctionExpression "d"
	}
	```
	
	到这里变量对象的创建过程就介绍完了，让我们简洁的总结我们上述所说：

	1. 全局上下文的变量对象初始化是全局对象
	2. 函数上下文的变量对象初始化只包括 Arguments 对象
	3. 在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值
	4. 在代码执行阶段，会再次修改变量对象的属性值

	### 思考题
	
	现在让我们看几个栗子
	
	1. 第一题

		```js
		function foo() {
		    console.log(a);
		    a = 1;
		}
		
		foo(); // ???
		
		function bar() {
		    a = 1;
		    console.log(a);
		}
		bar(); // ???
		```
		
		第一段会报错：Uncaught ReferenceError: a is not defined。

		第二段会打印：1。
		
		这是因为函数中的 "a" 并没有通过 var 关键字声明，所有不会被存放在 AO 中。
		
		第一段执行 console 的时候， AO 的值是：
		
		```js
		AO = {
		    arguments: {
		        length: 0
		    }
		}
		```
		
		没有 a 的值，然后就会到全局去找，全局也没有，所以会报错。

		当第二段执行 console 的时候，全局对象已经被赋予了 a 属性，这时候就可以从全局找到 a 的值，所以会打印 1。
		
	2. 第二题

		```js
		console.log(foo);

		function foo(){
		    console.log("foo");
		}
		
		var foo = 1;
		```
		
		会打印函数，而不是undefined
		
		这是因为在进入执行上下文时，首先会处理函数声明，其次会处理变量声明，如果如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性

* 增加一道我司的前端面试题

    模拟实现loadash中的_.get()函数，实现如下传入参数的取值效果

    ```js
    function get() {
        // 请补全函数参数和实现逻辑
    }
    
    const obj = { selector: { to: { toutiao: 'FE coder' } }, target: [1, 2, { name: 'byted' }] };

    // 运行代码

    get(obj, 'selector.to.toutiao', 'target[0]', 'target[2].name') // ['FE coder', 1, 'byted'];
    ```
    
    ```js
    function get() {
        const argsLen = arguments.length;

        if (argsLen === 0) return undefined;

        if (argsLen === 1) return arguments[0];

        const res = [];
        const obj = arguments[0]; 
        const pattern = /^(.*)\[(\d*)\]/;

        for (let i = 1; i < argsLen; i++) {
            const arg = arguments[i];
            const levelArr = arg.split('.');
            let cntObj = obj;
            for (let j = 0, len = levelArr.length; j < len; j++) {
                if (levelArr[j] in cntObj) {
                    cntObj = cntObj[levelArr[j]];
                } else {
                    const re = levelArr[j].match(pattern);
                    if (re && re[1] in cntObj && re[2] < cntObj[re[1]].length) {
                        cntObj = cntObj[re[1]][Number(re[2])];
                    } else {
                        new Error('param error');
                    }
                }
            }
            res.push(cntObj);
        }
        return res;
    }

    const obj = { selector: { to: { toutiao: 'FE coder' }}, target: [1, 2, {name: 'byted'}]};

    const res = get(obj, 'selector.to.toutiao', 'target[0]', 'target[2].name');

    console.log(res);   
    ```

* 手动实现一个bind函数

	利用apply或bind函数实现
	
	```js
	Function.prototype.bind_1 = function(obj, ...args) {
		const context = this;
		const newBind = function(...newArgs) {
			args.concat(newArgs);
			context.apply(obj, args);
		}
		const F = function() {};
		F.prototype = context.prototype;
		newBind.prototype = new F();
		return newBind;
	}
	```
	
* 类的继承

	首先创建一个Animal父类
	
	```js
	// 定义一个动物类
	function Animal (name) {
	  // 属性
	  this.name = name || 'Animal';
	  // 实例方法
	  this.sleep = function(){
	    console.log(this.name + '正在睡觉！');
	  }
	}
	// 原型方法
	Animal.prototype.eat = function(food) {
	  console.log(this.name + '正在吃：' + food);
	};
	```

	* 原型链继承

		```js
		function Cat(){ }
		Cat.prototype = new Animal();
		Cat.prototype.name = 'cat';
		
		var cat = new Cat();
		console.log(cat.name);
		console.log(cat.eat('fish'));
		console.log(cat.sleep());
		console.log(cat instanceof Animal); //true 
		console.log(cat instanceof Cat); //true
		```
		
		* 介绍：在这里我们可以看到new了一个空对象,这个空对象指向Animal并且Cat.prototype指向了这个空对象，这种就是基于原型链的继承。
		* 特点：基于原型链，既是父类的实例，也是子类的实例
		* 缺点：无法实现多继承

	* 构造继承

		```js
		function Cat(name){
		  Animal.call(this);
		  this.name = name || 'Tom';
		}
		// Test Code
		var cat = new Cat();
		console.log(cat.name);
		console.log(cat.sleep());
		console.log(cat instanceof Animal); // false
		console.log(cat instanceof Cat); // true
		```
		
		* 特点：可以实现多继承
		* 缺点：只能继承父类实例的属性和方法，不能继承原型上的属性和方法。

	* 组合继承
	
		```js
		function Cat(name){
		  Animal.call(this);
		  this.name = name || 'Tom';
		}
		Cat.prototype = new Animal();
		Cat.prototype.constructor = Cat;
		// Test Code
		var cat = new Cat();
		console.log(cat.name);
		console.log(cat.sleep());
		console.log(cat instanceof Animal); // true
		console.log(cat instanceof Cat); // true
		```
		
		* 特点：可以继承实例属性/方法，也可以继承原型属性/方法
		* 缺点：调用了两次父类构造函数，生成了两份实例
	
	* 原型继承

		```js
		function inherits(Child, Parent) {
		  var F = function () {};
		  F.prototype = Parent.prototype;
		  Child.prototype = new F();
		  Child.prototype.constructor = Child;
		}
		```

	* 寄生组合继承

		```js
		function Cat(name){
		  Animal.call(this);
		  this.name = name || 'Tom';
		}
		(function(){
		  // 创建一个没有实例方法的类
		  var Super = function(){};
		  Super.prototype = Animal.prototype;
		  //将实例作为子类的原型
		  Cat.prototype = new Super();
		})();
		// Test Code
		var cat = new Cat();
		console.log(cat.name);
		console.log(cat.sleep());
		console.log(cat instanceof Animal); // true
		console.log(cat instanceof Cat); //true
		```

* 使用Boolean过滤数组中的所有假值

    我们知道JS中有一些假值：`false`, `null`, `0`, `""`, `undefined`, `NaN`, 可以用Boolean构造函数进行一次过滤

    ```js
    const compact = arr => arr.filter(Boolean);
    compact([0, 1, false, 2, '', 3, 'a', 'e' * 23, NaN, 's', 34])             // [ 1, 2, 3, 'a', 's', 34 ]
    ```

* `num | 0` 与 `num | 0`实现一样的操作，并且后者耗时更少

* 将数字四舍五入到指定位数的方法

    ```js
    const round = (n, decimals = 0) => Number(`${Math.round(`${n}e${decimals}`)}e-${decimals}`);
    round(1.345, 2); // 1.35
    round(1.345, 1); // 1.3
    ```
