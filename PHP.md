
## 目录

- [3. PHP编码规范](#php)
  - [3.1. 命名风格](#name)
  - [3.2. 常量定义](#constant)
  - [3.3. 代码格式](#format)
  - [3.4. OOP规约](#oop)
  - [3.5. 并发处理](#concurrent)
  - [3.6. 控制语句](#control)
  - [3.7. 其它](#other)

<a name="php"></a>
# 3. PHP编码规范

<a name="name"></a>
## 3.1. 命名风格

1. <font color=bc0008>【强制】</font>代码中的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。 
        
    <font color=977919>说明:</font>正确的英文拼写和语法可以让阅读者易于理解，避免歧义。注意，纯拼音命名方式更要避免采用。 

    <font color=29965b>正例:</font>renminbi / taobao / youku / chongqing 等国际通用的名称，可视同英文。 

    <font color=fa4113>反例:</font>DaZhePromotion [打折] / getPingfenByName() [评分] / (int)某变量 = 3

1. <font color=bc0008>【强制】</font>类名使用大驼峰(UpperCamelCase)风格。

    <font color=29965b>正例:</font>XmlService / TcpUdpDeal 

    <font color=fa4113>反例:</font>XMLService / TCPUDPDeal

1. <font color=bc0008>【强制】</font>方法名、参数名、成员变量、局部变量都统一使用小驼峰(lowerCamelCase)风格。
        
    <font color=29965b>正例:</font> localValue / getHttpMessage() / inputUserId

1. <font color=bc0008>【强制】</font>常量命名全部大写，单词间用下划线隔开，力求语义表达完整清楚，不要嫌名字长。

    <font color=29965b>正例:</font>MAX_STOCK_COUNT / CACHE_EXPIRED_TIME 

    <font color=fa4113>反例:</font>MAX_COUNT / EXPIRED_TIME

1. <font color=bc0008>【强制】</font>抽象类命名使用Abstract或Base开头;异常类命名使用Exception结尾;测试类命名以它要测试的类的名称开始，以 Test 结尾。

1. <font color=bc0008>【强制】</font>避免在子父类的成员变量之间、或者不同代码块的局部变量之间采用完全相同的命名，使可读性降低。

	<font color=977919>说明:</font>子类、父类成员变量名相同，即使是 public 类型的变量也是能够通过编译，而局部变量在同一方法内的不同代码块中同名也是合法的，但是要避免使用。对于非 setter/getter 的参数名称也要避免与成员变量名称相同。

    <font color=fa4113>反例:</font>
        
    ``` php
    class ConfusingName
    {
        public $age;
        // 非 setter/getter 的参数名称，不允许与本类成员变量同名
        public function getData(String $arg) { 
            if($arg) {
                $money = 531;
                // ...
            }
            for (i = 0; i < 10; i++) {
                // 在同一方法体中，不允许与其它代码块中的 money 命名相同 
                $money = 615;
                // ...
            }
        }
    }
    class Son extends ConfusingName
    {
        // 不允许与父类的成员变量名称相同 
        public $age;
    }
    ```
 
1. <font color=bc0008>【强制】</font>杜绝完全不规范的缩写，避免望文不知义。

    <font color=fa4113>反例:</font>AbstractClass“缩写”命名成 AbsClass;condition“缩写”命名成 condi，此类随意缩写严重降低了代码的可阅读性。

1. <font color=febd2c>【推荐】</font>为了达到代码自解释的目标，任何自定义编程元素在命名时，使用尽量完整的单词组合来表达其意。
        
    <font color=29965b>正例:</font>表达原子更新的类名为:AtomicReferenceFieldUpdater。

    <font color=fa4113>反例:</font>int a 的随意命名方式。

1. <font color=febd2c>【推荐】</font>在常量与变量的命名时，表示类型的名词放在词尾，以提升辨识度。

    <font color=29965b>正例:</font>startTime / workQueue / nameList / TERMINATED_THREAD_COUNT

    <font color=fa4113>反例:</font>startedAt / QueueOfWork / listName / COUNT_TERMINATED_THREAD

1. <font color=febd2c>【推荐】</font>如果模块、接口、类、方法使用了设计模式，在命名时需体现出具体模式。 

	<font color=977919>说明:</font>将设计模式体现在名字中，有利于阅读者快速理解架构设计理念。     

    <font color=29965b>正例:</font> 
	``` php
	class OrderFactory{}; 
	class LoginProxy{};
	class ResourceObserver{};
	```

1. <font color=799042>【参考】</font>枚举类名带上 Enum 后缀，枚举成员名称需要全大写，单词间用下划线隔开。 

    <font color=977919>说明:</font>枚举其实就是特殊的类，域成员均为常量，且构造方法被默认强制是私有。 

    <font color=29965b>正例:</font>枚举名字为 ProcessStatusEnum 的成员名称:SUCCESS / UNKNOWN_REASON。

1. <font color=799042>【参考】</font>Service 层方法命名规约:

		1) 获取单个对象的方法用 get 做前缀。
		2) 获取多个对象的方法用 list 做前缀，复数形式结尾如:listObjects。 
		3) 获取统计值的方法用 count 做前缀。
		4) 插入的方法用 save/insert 做前缀。
		5) 删除的方法用 delete 做前缀。
		6) 修改的方法用 update 做前缀。

<a name="constant"></a>
## 3.2. 常量定义

1. <font color=febd2c>【推荐】</font>不要使用一个常量类维护所有常量，要按常量功能进行归类，分开维护。 
        
    <font color=977919>说明:</font>大而全的常量类，杂乱无章，使用查找功能才能定位到修改的常量，不利于理解和维护。 

    <font color=29965b>正例:</font>缓存相关常量放在类 CacheConsts 下;系统配置相关常量放在类 ConfigConsts 下。

1. <font color=febd2c>【推荐】</font>如果变量值仅在一个固定范围内变化用enum类型来定义。

    <font color=29965b>正例:</font>
	``` php
	final class AccountEnum
	{
		const PHONE_NUMBER = 1;
		const EMAIL = 2;
		const USERNAME = 3;
		static public $__names = array(
			1 => 'phone_number',
			2 => 'email',
			3 => 'username',
		);
	}
	```

<a name="format"></a>
## 3.3. 代码格式

1. <font color=bc0008>【强制】</font>如果是大括号内为空，则简洁地写成{}即可，大括号中间无需换行和空格;如果是非空代码块则:

        1) 左大括号前不换行。
        2) 左大括号后换行。
        3) 右大括号前换行。
        4) 右大括号后还有 else 等代码则不换行;表示终止的右大括号后必须换行。

1. <font color=bc0008>【强制】</font>左小括号和字符之间不出现空格;同样，右小括号和字符之间也不出现空格;而左 大括号前需要空格。详见第 5 条下方正例提示。

    <font color=fa4113>反例:</font>if (空格 a == b 空格)

1. <font color=bc0008>【强制】</font>if/for/while/switch/do等保留字与括号之间都必须加空格。
1. <font color=bc0008>【强制】</font>任何二目、三目运算符的左右两边都需要加一个空格。

    <font color=977919>说明:</font>运算符包括赋值运算符=、逻辑运算符&&、加减乘除符号等。

1. <font color=bc0008>【强制】</font>采用4个空格缩进，禁止使用tab字符。

    <font color=977919>说明:</font>如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。

    <font color=29965b>正例:</font> (涉及 1-5 点)
	``` php
	 public function main(String $args) {
		// 缩进 4 个空格
		$say = "hello";
		// 运算符的左右必须有一个空格
		$flag = 0;
		// 关键词 if 与括号之间必须有一个空格，括号内的 f 与左括号，0 与右括号不需要空格
		if ($flag == 0) { 
		    echo $say;
		}
		// 左大括号前加空格且不换行;左大括号后换行
		if ($flag == 1) { 
		    echo "world";
		// 右大括号前换行，右大括号后有 else，不用换行
		} else { 
		    echo "world";
		// 在右大括号后直接结束，则必须换行
		} 
	}
	```

1. <font color=bc0008>【强制】</font>注释的双斜线与注释内容之间有且仅有一个空格。 

    <font color=29965b>正例:</font>// 这是示例注释，请注意在双斜线之后有一个空格

1. <font color=bc0008>【强制】</font>在进行类型强制转换时，右括号与强制转换值之间不需要任何空格隔开。

    <font color=29965b>正例:</font>
	``` php
	$second = (int)$first + 2;
	```

1. <font color=bc0008>【强制】</font>单行字符数限制不超过120个，超出需要换行，换行时遵循如下原则:

        1)第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。 
        2)运算符与下文一起换行。
        3)方法调用的点符号与下文一起换行。 
        4)方法调用中的多个参数需要换行时，在逗号后进行。
        5)在括号前不要换行，见反例。 
        
    <font color=29965b>正例:</font>
	``` php
	$sb = new StringBuilder();
	// 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行
	$sb->append("Jack")->append("Ma")... 
		->append("alibaba")... 
		->append("alibaba")... 
		->append("alibaba");
	```
			
    <font color=fa4113>反例:</font>
	``` php
	$sb = new StringBuilder();
	// 超过 120 个字符的情况下，不要在括号前换行 
	$sb->append("Jack")->append("Ma")...append
	("alibaba");
	// 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行 
	method(args1, args2, args3, ...
	, argsX);
	```
			
1. <font color=bc0008>【强制】</font>方法参数在定义和传入时，多个参数逗号后边必须加空格。

    <font color=29965b>正例:</font>下例中实参的 args1，后边必须要有一个空格。 
	``` php
	method(args1, args2, args3);
	```

1. <font color=febd2c>【推荐】</font>单个方法的总行数不超过 80 行。

	<font color=977919>说明:</font>除注释之外的方法签名、左右大括号、方法内代码、空行、回车及任何不可见字符的总行数不超过80 行。

	<font color=29965b>正例:</font>代码逻辑分清红花和绿叶，个性和共性，绿叶逻辑单独出来成为额外方法，使主干代码更加清晰;共性逻辑抽取成为共性方法，便于复用和维护。

1. <font color=febd2c>【推荐】</font>没有必要增加若干空格来使变量的赋值等号与上一行对应位置的等号对齐。 

    <font color=29965b>正例:</font>
	``` php
	$first = 1;
	$one = 2;
	$sb = new StringBuilder();
	```

	<font color=977919>说明:</font>增加 sb 这个变量，如果需要对齐，则给 one、first 都要增加几个空格，在变量比较多的情况下，是非常累赘的事情。

1. <font color=febd2c>【推荐】</font>不同逻辑、不同语义、不同业务的代码之间插入一个空行分隔开来以提升可读性。

	<font color=977919>说明:</font>任何情形，没有必要插入多个空行进行隔开。

<a name="oop"></a>
## 3.4. OOP规约

1. <font color=bc0008>【强制】</font>避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可。
1. <font color=bc0008>【强制】</font>相同参数类型，相同业务含义，才可以使用可变参数，避免使用Object。 

	<font color=977919>说明:</font>可变参数必须放置在参数列表的最后。(提倡尽量不用可变参数编程)

	<font color=29965b>正例:</font>
	``` php
	public function listUsers(String type, int... ids) {...}
	```

1. <font color=bc0008>【强制】</font>不能使用过时的类或方法。

	<font color=977919>说明:</font>PHP 7.4.x中在一个对象中使用 array_key_exists() 已被废弃。请使用 isset() 或 property_exists() 来替代。接口提供方既然明确是过时接口，那么有义务同时提供新的接口;作为调用方来说，有义务去考证过时方法的新实现是什么。

1. <font color=bc0008>【强制】</font>构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。

1. <font color=febd2c>【推荐】</font>当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放置在一起， 便于阅读，此条规则优先于下一条。

1. <font color=febd2c>【推荐】</font> 类内方法定义的顺序依次是:公有方法或保护方法 > 私有方法 > getter / setter 方法。

	<font color=977919>说明:</font>公有方法是类的调用者和维护者最关心的方法，首屏展示最好;保护方法虽然只是子类关心，也可能是“模板设计模式”下的核心方法;而私有方法外部一般不需要特别关心，是一个黑盒实现;因为承载的信息价值较低，所有 Service 和 Model 的 getter/setter 方法放在类体最后。

1. <font color=febd2c>【推荐】</font>final 可以声明类、成员变量、方法、以及本地变量，下列情况使用 final 关键字: 

		1) 不允许被继承的类，如:String 类。
		2) 不允许修改引用的域对象。
		3) 不允许被覆写的方法。
		4) 不允许运行过程中重新赋值的局部变量。
		5) 避免上下文重复使用一个变量，使用 final 可以强制重新定义一个变量，方便更好地进行重构。

1. <font color=febd2c>【推荐】</font>类成员与方法访问控制从严:

		1) 如果不允许外部直接通过 new 来创建对象，那么构造方法必须是 private。 
		2) 工具类不允许有 public 构造方法。
		3) 类非 static 成员变量并且与子类共享，必须是 protected。
		4) 类非 static 成员变量并且仅在本类使用，必须是 private。
		5) 类 static 成员变量如果仅在本类使用，必须是 private。
		6) 若是 static 成员变量，考虑是否为 final。
		7) 类成员方法只供类内部调用，必须是 private。
		8) 类成员方法只对继承类公开，那么限制为 protected。

	<font color=977919>说明:</font>任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于模块解耦。

<a name="concurrent"></a>
## 3.4. 并发处理

1. <font color=bc0008>【强制】</font>获取单例对象需要保证线程安全，其中的方法也要保证线程安全。 

	<font color=977919>说明:</font>资源驱动类、工具类、单例工厂类都需要注意。

1. <font color=bc0008>【强制】</font>高并发时，同步调用应该去考量锁的性能损耗。能用无锁数据结构，就不要用锁; 能锁区块，就不要锁整个方法体;能用对象锁，就不要用类锁。 说明:尽可能使加锁的代码块工作量尽可能的小，避免在锁代码块中调用 RPC 方法。

1. <font color=bc0008>【强制】</font>对多个资源、数据库表、对象同时加锁时，需要保持一致的加锁顺序，否则可能会造成死锁。

	<font color=977919>说明:</font>线程一需要对表 A、B、C 依次全部加锁后才可以进行更新操作，那么线程二的加锁顺序也必须是 A、B、C，否则可能出现死锁。

1. <font color=bc0008>【强制】</font>在使用阻塞等待获取锁的方式中，必须在try代码块之外，并且在加锁方法与try代码块之间没有任何可能抛出异常的方法调用，避免加锁成功后，在 finally 中无法解锁。 

	<font color=977919>说明一:</font>如果在 lock 方法与 try 代码块之间的方法调用抛出异常，那么无法解锁，造成其它线程无法成功获取锁。

	<font color=977919>说明二:</font>如果 lock 方法在 try 代码块之内，可能由于其它方法抛出异常，导致在 finally 代码块中，unlock 对未加锁的对象解锁，它会调用 AQS 的 tryRelease 方法(取决于具体实现类)，抛出 IllegalMonitorStateException 异常。

	<font color=977919>说明三:</font>在 Lock 对象的 lock 方法实现中可能抛出 unchecked 异常，产生的后果与说明二相同。 

	<font color=29965b>正例:</font>
	``` php
	$lock = new XxxLock();
	// ...
	$lock->lock(); 
	try {
		doSomething();
		doOthers(); 
	} finally {
		$lock->unlock(); 
	}
	```
	<font color=fa4113>反例:</font>
	``` php
	$lock = new XxxLock();
	// ...
	try {
		// 如果此处抛出异常，则直接执行 finally 代码块
		doSomething();
		// 无论加锁是否成功，finally 代码块都会执行
		$lock->lock();
		doOthers(); 
	} finally {
		$lock->unlock(); 
	}
	```

1. <font color=bc0008>【强制】</font>在使用尝试机制来获取锁的方式中，进入业务代码块之前，必须先判断当前线程是否持有锁。锁的释放规则与锁的阻塞等待方式相同。

	<font color=29965b>正例:</font>
	``` php
	$lock = new XxxLock();
	// ...
	$isLocked = $lock->tryLock(); 
	if ($isLocked) {
		try { 
			doSomething();
			doOthers(); 
		} finally {
			$lock->unlock(); 
		}
	}
	```

1. <font color=bc0008>【强制】</font>并发修改同一记录时，避免更新丢失，需要加锁。要么在应用层加锁，要么在缓存加锁，要么在数据库层使用乐观锁，使用 version 作为更新依据。 

	<font color=977919>说明:</font>如果每次访问冲突概率小于 20%，推荐使用乐观锁，否则使用悲观锁。乐观锁的重试次数不得小于 3 次。

1. <font color=febd2c>【推荐】</font>资金相关的金融敏感信息，使用悲观锁策略。

	<font color=977919>说明:</font>乐观锁在获得锁的同时已经完成了更新操作，校验逻辑容易出现漏洞，另外，乐观锁对冲突的解决策略有较复杂的要求，处理不当容易造成系统压力或数据异常，所以资金相关的金融敏感信息不建议使用乐观锁更新。

<a name="control"></a>
## 3.5. 控制语句

1. <font color=bc0008>【强制】</font>在一个switch块内，每个case要么通过continue/break/return等来终止，要么注释说明程序将继续执行到哪一个 case 为止;在一个 switch 块内，都必须包含一个 default 语句并且放在最后，即使它什么代码也没有。

	<font color=977919>说明:</font>注意 break 是退出 switch 语句块，而 return 是退出方法体。

1. <font color=bc0008>【强制】</font>当switch括号内的变量类型为String并且此变量为外部参数时，必须先进行null 判断。

1. <font color=bc0008>【强制】</font>在if/else/for/while/do语句中必须使用大括号。

	<font color=977919>说明:</font>即使只有一行代码，避免采用单行的编码方式:if ($condition) statements; 
	
1. <font color=bc0008>【强制】</font>在高并发场景中，避免使用”等于”判断作为中断或退出的条件。

	<font color=977919>说明:</font>如果并发控制没有处理好，容易产生等值判断被“击穿”的情况，使用大于或小于的区间判断条件来代替。

	<font color=fa4113>反例:</font>判断剩余奖品数量等于 0 时，终止发放奖品，但因为并发处理错误导致奖品数量瞬间变成了负数，这样的话，活动无法终止。

1. <font color=febd2c>【推荐】</font>表达异常的分支时，少用if-else方式，这种方式可以改写成:

	``` php
	if ($condition) { 
	    ...
	    return $obj; 
	}
	// 接着写 else 的业务逻辑代码;
	```
	<font color=977919>说明:</font>如果非使用 if()...else if()...else...方式表达逻辑，避免后续代码维护困难，<font color=bc0008>【强制】</font>请勿超过 3 层。 

	<font color=29965b>正例:</font>超过 3 层的 if-else 的逻辑判断代码可以使用卫语句、策略模式、状态模式等来实现，其中卫语句即代码逻辑先考虑失败、异常、中断、退出等直接返回的情况，以方法多个出口的方式，解决代码中判断分支嵌套的问题，这是逆向思维的体现。示例如下:
	``` php
	public function findBoyfriend($man) {
		if ($man->isUgly()) { 
			var_dump("本姑娘是外貌协会的资深会员");
			return; 
		}
		if ($man->isPoor()) { 
			var_dump("贫贱夫妻百事哀");
			return; 
		}
		if ($man->isBadTemper()) { 
			var_dump("银河有多远，你就给我滚多远");
			return; 
		}
		var_dump("可以先交往一段时间看看"); 
	}
	```
1. <font color=febd2c>【推荐】</font>除常用方法(如getXxx/isXxx)等外，不要在条件判断中执行其它复杂的语句，将复杂逻辑判断的结果赋值给一个有意义的布尔变量名，以提高可读性。

	<font color=977919>说明:</font>很多 if 语句内的逻辑表达式相当复杂，与、或、取反混合运算，甚至各种方法纵深调用，理解成本非常高。如果赋值一个非常好理解的布尔变量名字，则是件令人爽心悦目的事情。

	<font color=29965b>正例:</font>// 伪代码如下
	``` php
	$existed = (fopen(fileName, "w") != null) && (...) || (...);
	if ($existed) { 
		...
	}
	```
	<font color=fa4113>反例:</font>
	``` php
	public final function acquire($arg) { 
		if (!tryAcquire($arg) &&
			acquireQueued(addWaiter(Node.EXCLUSIVE), $arg)) { 
				selfInterrupt();
		}
	}
	```

1. <font color=febd2c>【推荐】</font>不要在其它表达式(尤其是条件表达式)中，插入赋值语句。

	<font color=977919>说明:</font>赋值点类似于人体的穴位，对于代码的理解至关重要，所以赋值语句需要清晰地单独成为一行。

	<font color=fa4113>反例:</font>
	``` php
	public function getLock(boolean $fair) {
		// 算术表达式中出现赋值操作，容易忽略 $count 值已经被改变
		$threshold = ($count = MAX_VALUE) - 1;
		// 条件表达式中出现赋值操作，容易误认为是 $sync==$fair
		return ($sync = $fair) ? new FairSync() : new NonfairSync(); 
	}
	```

1. <font color=febd2c>【推荐】</font>循环体中的语句要考量性能，以下操作尽量移至循环体外处理，如定义对象、变 量、获取数据库连接，进行不必要的 try-catch 操作(这个 try-catch 是否可以移至循环体外)。
1. <font color=febd2c>【推荐】</font>避免采用取反逻辑运算符。

	<font color=977919>说明:</font>取反逻辑不利于快速理解，并且取反逻辑写法必然存在对应的正向逻辑写法。

	<font color=29965b>正例:</font>使用 if (x < 628) 来表达 x 小于 628。

	<font color=fa4113>反例:</font>使用 if (!(x >= 628)) 来表达 x 小于 628。

1. <font color=799042>【参考】</font>下列情形，需要进行参数校验:

		1) 调用频次低的方法。
		2) 执行时间开销很大的方法。此情形中，参数校验时间几乎可以忽略不计，但如果因为参数错误导致中间执行回退，或者错误，那得不偿失。
		3) 需要极高稳定性和可用性的方法。
		4) 对外提供的开放接口，不管是 RPC/API/HTTP 接口。 
		5) 敏感权限入口。

1. <font color=799042>【参考】</font>下列情形，不需要进行参数校验:

		1) 极有可能被循环调用的方法。但在方法说明里必须注明外部参数检查要求。
		2) 底层调用频度比较高的方法。毕竟是像纯净水过滤的最后一道，参数错误不太可能到底层才会暴露问题。一般 Model 层与 Service 层都在同一个应用中，部署在同一台服务器中，所以 Model 的参数校验，可以省略。
		3) 被声明成 private 只会被自己代码所调用的方法，如果能够确定调用方法的代码传入参数已经做过检查或者肯定不会有问题，此时可以不校验参数。

<a name="other"></a>
## 3.7. 其它

1. <font color=bc0008>【强制】</font>在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度。 

	<font color=977919>说明:</font>不要在方法体内定义:preg_match(“规则”, $str);

1. <font color=bc0008>【强制】</font>日期格式化时，传入pattern中表示年份统一使用小写的y。 说明:日期格式化时，yyyy 表示当天所在的年，而大写的 YYYY 代表是 week in which year，意思是当天所在的周属于的年份，一周从周日开始，周六结束， 只要本周跨年，返回的 YYYY 就是下一年。另外需要注意:
	- 表示月份是大写的 M
	- 表示分钟则是小写的 m 
	- 4 小时制的是大写的 H
	- 12 小时制的则是小写的 h

	<font color=29965b>正例:</font>表示日期和时间的格式如下所示:
	``` php
	SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	```

1. <font color=febd2c>【推荐】</font>不要在视图模板中加入任何复杂的逻辑。

	<font color=977919>说明:</font>根据 MVC 理论，视图的职责是展示，不要抢模型和控制器的活。

1. <font color=febd2c>【推荐】</font>及时清理不再使用的代码段或配置信息。 

	<font color=977919>说明:</font>对于垃圾代码或过时配置，坚决清理干净，避免程序过度臃肿，代码冗余。 

	<font color=29965b>正例:</font>对于暂时被注释掉，后续可能恢复使用的代码片断，在注释代码上方，统一规定使用三个斜杠(///) 来说明注释掉代码的理由。
