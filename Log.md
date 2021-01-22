## 目录

- [6. 异常日志规范](#exception-log)
  - [6.1. 异常处理](#exception)
  - [6.2. 日志规约](#log)

<a name="exception-log"></a>
# 6. 异常日志规范

<a name="exception"></a>
## 6.1. 异常处理

1. <font color=bc0008>【强制】</font>异常不要用来做流程控制，条件控制。

	<font color=977919>说明:</font>异常设计的初衷是解决程序运行中的各种意外情况，且异常的处理效率比条件判断方式要低很多。

1. <font color=bc0008>【强制】</font>catch时请分清稳定代码和非稳定代码，稳定代码指的是无论如何不会出错的代码。 对于非稳定代码的 catch 尽可能进行区分异常类型，再做对应的异常处理。 

	<font color=977919>说明:</font>对大段代码进行 try-catch，使程序无法根据不同的异常做出正确的应激反应，也不利于定位问 题，这是一种不负责任的表现。 

	<font color=29965b>正例:</font>用户注册的场景中，如果用户输入非法字符，或用户名称已存在，或用户输入密码过于简单，在程序上作出分门别类的判断，并提示给用户。

1. <font color=bc0008>【强制】</font>捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它， 请将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理 解的内容。
1. <font color=bc0008>【强制】</font>有try块放到了事务代码中，catch异常后，如果需要回滚事务，一定要注意手动回 滚事务。
1. <font color=bc0008>【强制】</font>finally块必须对资源对象、流对象进行关闭，有异常也要做try-catch。 
1. <font color=bc0008>【强制】</font>不要在finally块中使用return。
		
	<font color=977919>说明:</font>try 块中的 return 语句执行成功后，并不马上返回，而是继续执行 finally 块中的语句，如果此处存在 return 语句，则在此直接返回，无情丢弃掉 try 块中的返回点。

	<font color=fa4113>反例:</font>
			private $x = 0;
			public function checkReturn() {
				try {
					// x 等于 1，此处不返回
					return ++$x; 
				} finally {
					// 返回的结果是 2
					return ++$x; 
				}
			}
1. <font color=bc0008>【强制】</font>捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类。

	<font color=977919>说明:</font>如果预期对方抛的是绣球，实际接到的是铅球，就会产生意外情况。 

1. <font color=febd2c>【推荐】</font>方法的返回值可以为 null，不强制返回空集合，或者空对象等，必须添加注释充分 说明什么情况下会返回 null 值。

	<font color=977919>说明:</font>本手册明确防止 NPE (Null Pointer Exception)是调用者的责任。即使被调用方法返回空集合或者空对象，对调用者来说，也 并非高枕无忧，必须考虑到远程调用失败、序列化失败、运行时异常等场景返回 null 的情况。

1. <font color=febd2c>【推荐】</font>防止 NPE，是程序员的基本修养，注意 NPE 产生的场景:

		1) 数据库的查询结果可能为 null。
		2) 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。
		3) 远程调用返回对象时，一律要求进行空指针判断，防止 NPE。
		4) 对于 Session 中获取的数据，建议进行 NPE 检查，避免空指针。 
		5) 级联调用 obj->getA()->getB()->getC();一连串调用，易产生 NPE。

1. <font color=febd2c>【推荐】</font>定义时区分 unchecked / checked 异常，避免直接抛出 new RuntimeException()， 更不允许抛出 Exception 或者 Throwable，应使用有业务含义的自定义异常。推荐业界已定 义过的自定义异常，如:DAOException / ServiceException 等。

1. <font color=799042>【参考】</font>对于公司外的 http/api 开放接口必须使用“错误码”;而应用内部推荐异常抛出; 跨应用间 RPC 调用优先考虑使用 Result 方式，封装 isSuccess()方法、“错误码”、“错误 简短信息”。

	<font color=977919>说明:</font>跨应用间 RPC 调用使用抛异常返回方式，调用方如果没有捕获到就会产生运行时错误

1. <font color=799042>【参考】</font>避免出现重复的代码(Don't Repeat Yourself)，即 DRY 原则。 

	<font color=977919>说明:</font>随意复制和粘贴代码，必然会导致代码的重复，在以后需要修改时，需要修改所有的副本，容易遗漏。必要时抽取共性方法，或者抽象公共类，甚至是组件化。

	<font color=29965b>正例:</font>一个类中有多个 public 方法，都需要进行数行相同的参数校验操作，这个时候请抽取:
			private boolean checkParam($param) {...}

<a name="log"></a>
## 6.2. 日志规约

1. <font color=bc0008>【强制】</font>应用中不可直接使用日志系统中的API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一。
1. <font color=bc0008>【强制】</font>所有日志文件至少保存15天，因为有些异常具备以“周”为频次发生的特点。网络 运行状态、安全相关信息、系统监测、管理后台操作、用户敏感操作需要留存相关的网络日 志不少于 6 个月。
1. <font color=bc0008>【强制】</font>应用中的扩展日志(如打点、临时监控、访问日志等)命名方式: appName_logType_logName.log。logType:日志类型，如 stats/monitor/access 等;logName:日志 描述。这种命名的好处:通过文件名就可知道日志文件属于什么应用，什么类型，什么目的，也有利于归 类查找。 

	<font color=977919>说明:</font>推荐对日志进行分类，如将错误日志和业务日志分开存放，便于开发人员查看，也便于通过日志对 系统进行及时监控。

	<font color=29965b>正例:</font>force-web 应用中单独监控时区转换异常，如:force_web_timeZoneConvert.log

1. <font color=febd2c>【推荐】</font>谨慎地记录日志。生产环境禁止输出debug日志;有选择地输出info日志;如果使 用 warn 来记录刚上线时的业务行为信息，一定要注意日志输出量的问题，避免把服务器磁盘 撑爆，并记得及时删除这些观察日志。 

    <font color=977919>说明:</font>大量地输出无效日志，不利于系统性能提升，也不利于快速定位错误点。记录日志时请思考:这些 日志真的有人看吗?看到这条日志你能做什么?能不能给问题排查带来好处?

1. <font color=febd2c>【推荐】</font>可以使用 warn 日志级别来记录用户输入参数错误的情况，避免用户投诉时，无所适从。如非必要，请不要在此场景打出 error 级别，避免频繁报警。

	<font color=977919>说明:</font>注意日志输出的级别，error 级别只记录系统逻辑出错、异常或者重要的错误信息。

1. <font color=febd2c>【推荐】</font>尽量用英文来描述日志错误信息，如果日志中的错误信息用英文描述不清楚的话使用中文描述即可，否则容易产生歧义。<font color=bc0008>【强制】</font>国际化团队或海外部署的服务器由于字符集问题，使用全英文来注释和描述日志错误信息。
