## 目录

- [4. Mysql数据库设计规范](#mysql)
  - [4.1. 建表规约](#buildtable)
  - [4.2. 索引规约](#index)
  - [4.3. SQL语句](#sql)
  - [4.4. ORM映射](#orm)

<a name="mysql"></a>
# 4. Mysql数据库设计规范

<a name="buildtable"></a>
## 4.1. 建表规约

1. <font color=bc0008>【强制】</font>表达是与否概念的字段，必须使用 is_xxx 的方式命名，数据类型是 unsigned tinyint(1 表示是，0 表示否)。

	<font color=977919>说明:</font>任何字段如果为非负数，必须是 unsigned。

	<font color=29965b>正例:</font>表达逻辑删除的字段名 is_deleted，1 表示删除，0 表示未删除。

1. <font color=bc0008>【强制】</font>表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只出现数字。数据库字段名的修改代价很大，因为无法进行预发布，所以字段名称需要慎重考虑。

	<font color=977919>说明:</font>MySQL 在 Windows下不区分大小写，但在 Linux 下默认是区分大小写。因此，数据库名、表名、字段名，都不允许出现任何大写字母，避免节外生枝

	<font color=29965b>正例:</font>aliyun_admin，rdc_config，level3_name

	<font color=fa4113>反例:</font>AliyunAdmin，rdcConfig，level_3_name

1. <font color=bc0008>【强制】</font>表名不使用复数名词。

	<font color=977919>说明:</font>表名应该仅仅表示表里面的实体内容，不应该表示实体数量，对应于 Model 类名也是单数形式，符合表达习惯。

1. <font color=bc0008>【强制】</font>禁用保留字，如desc、range、match、delayed等，请参考MySQL官方保留字。
1. <font color=bc0008>【强制】</font>主键索引名为pk_字段名;唯一索引名为uk_字段名;普通索引名则为idx_字段名。

	<font color=977919>说明:</font>pk_ 即 primary key;uk_ 即 unique key;idx_ 即 index 的简称。

1. <font color=bc0008>【强制】</font>小数类型为decimal，禁止使用float和double。

	<font color=977919>说明:</font>在存储的时候，float 和 double 都存在精度损失的问题，很可能在比较值的时候，得到不正确的结果。如果存储的数据范围超过 decimal 的范围，建议将数据拆成整数和小数并分开存储。

1. <font color=bc0008>【强制】</font>如果存储的字符串长度几乎相等，使用char定长字符串类型。
1. <font color=bc0008>【强制】</font>varchar是可变长字符串，不预先分配存储空间，长度不要超过5000，如果存储长度大于此值，定义字段类型为 text，独立出来一张表，用主键来对应，避免影响其它字段索 引效率。
1. <font color=bc0008>【强制】</font>表必备三字段:id,created_at,updated_at。

	<font color=977919>说明:</font>其中id必为主键，类型为bigint unsigned、单表时自增、步长为 1。created_at,updated_at的类型均为 datetime 类型。

1. <font color=febd2c>【推荐】</font>表的命名最好是遵循“业务名称_表的作用”。

	<font color=29965b>正例:</font>alipay_task / force_project / trade_config
        
1. <font color=febd2c>【推荐】</font>库名与应用名称尽量一致。
1. <font color=febd2c>【推荐】</font>如果修改字段含义或对字段表示的状态追加时，需要及时更新字段注释。
1. <font color=febd2c>【推荐】</font>字段允许适当冗余，以提高查询性能，但必须考虑数据一致。冗余字段应遵循: 

		1) 不是频繁修改的字段。
		2) 不是 varchar 超长字段，更不能是 text 字段。
		3) 不是唯一索引的字段。 正例:商品类目名称使用频率高，字段长度短，名称基本一不变，可在相关联的表中冗余存储类目名称，避免关联查询。

1. <font color=febd2c>【推荐】</font>单表行数超过 500 万行或者单表容量超过 2GB，才推荐进行分库分表。 说明:如果预计三年后的数据量根本达不到这个级别，请不要在创建表时就分库分表。
1. <font color=799042>【参考】</font>合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是提升检索速度。
  
    <font color=29965b>正例:</font>如下表，其中无符号值可以避免误存负数，且扩大了表示范围。
		
| 对象 | 年龄区间 | 类型 | 字节 | 表示范围 |
| :-----| :---- | :---- | :----: | :---- |
| 人 | 150 岁之内 | tinyint unsigned | 1 | 无符号值:0 到 255 |
| 龟 | 数百岁 | smallint unsigned | 2 | 无符号值:0 到 65535 |
| 恐龙化石 | 数千万年 | int unsigned | 4 | 无符号值:0 到约 42.9 亿 |
| 太阳 | 约 50 亿年 | bigint unsigned | 8 | 无符号值:0 到约 10 的 19 次方 |

<a name="index"></a>
## 4.2. 索引规约

1. <font color=bc0008>【强制】</font>业务上具有唯一特性的字段，即使是多个字段的组合，也必须建成唯一索引。 

	<font color=977919>说明:</font>不要以为唯一索引影响了 insert 速度，这个速度损耗可以忽略，但提高查找速度是明显的;另外，即使在应用层做了非常完善的校验控制，只要没有唯一索引，根据墨菲定律，必然有脏数据产生。

1. <font color=bc0008>【强制】</font>超过三个表禁止join。需要join的字段，数据类型必须绝对一致;多表关联查询时，保证被关联的字段需要有索引。

	<font color=977919>说明:</font>即使双表 join 也要注意表索引、SQL 性能。

1. <font color=bc0008>【强制】</font>在varchar字段上建立索引时，必须指定索引长度，没必要对全字段建立索引，根据实际文本区分度决定索引长度即可。

	<font color=977919>说明:</font>索引的长度与区分度是一对矛盾体，一般对字符串类型数据，长度为 20 的索引，区分度会高达90%以上，可以使用 count(distinct left(列名, 索引长度))/count(*)的区分度来确定。

1. <font color=bc0008>【强制】</font>页面搜索严禁左模糊或者全模糊，如果需要请走搜索引擎来解决。

	<font color=977919>说明:</font>索引文件具有 B-Tree 的最左前缀匹配特性，如果左边的值未确定，那么无法使用此索引。

1. <font color=febd2c>【推荐】</font>如果有 order by 的场景，请注意利用索引的有序性。order by 最后的字段是组合索引的一部分，并且放在索引组合顺序的最后，避免出现 file_sort 的情况，影响查询性能。 

	<font color=29965b>正例:</font>where a=? and b=? order by c; 索引:a_b_c 

	<font color=fa4113>反例:</font>索引如果存在范围查询，那么索引有序性无法利用，如:WHERE a>10 ORDER BY b; 索引 a_b 无法排序。

1. <font color=febd2c>【推荐】</font>利用覆盖索引来进行查询操作，避免回表。

    <font color=977919>说明:</font>如果一本书需要知道第 11 章是什么标题，会翻开第 11 章对应的那一页吗?目录浏览一下就好，这个目录就是起到覆盖索引的作用。 
    正例:能够建立索引的种类分为主键索引、唯一索引、普通索引三种，而覆盖索引只是一种查询的一种效 果，用 explain 的结果，extra 列会出现:using index。

1. <font color=febd2c>【推荐】</font>利用延迟关联或者子查询优化超多分页场景。

	<font color=977919>说明:</font>MySQL 并不是跳过 offset 行，而是取 offset+N 行，然后返回放弃前 offset 行，返回 N 行，那当 offset 特别大的时候，效率就非常的低下，要么控制返回的总页数，要么对超过特定阈值的页数进行 SQL 改写。

	<font color=29965b>正例:</font>先快速定位需要获取的 id 段，然后再关联:
	``` php
	SELECT a.* FROM 表 1 a, (select id from 表 1 where 条件 LIMIT 100000,20 ) b where a.id=b.id
	```

1. <font color=febd2c>【推荐】</font>SQL 性能优化的目标:至少要达到 range 级别，要求是 ref 级别，如果可以是 consts 最好。

	<font color=977919>说明:</font>
		1) consts 单表中最多只有一个匹配行(主键或者唯一索引)，在优化阶段即可读取到数据。 
		2) ref 指的是使用普通的索引(normal index)。
		3) range 对索引进行范围检索。

	<font color=fa4113>反例:</font>explain 表的结果，type=index，索引物理文件全扫描，速度非常慢，这个 index 级别比较 range 还低，与全表扫描是小巫见大巫。

1. <font color=febd2c>【推荐】</font>建组合索引的时候，区分度最高的在最左边。

	<font color=29965b>正例:</font>如果 where a=? and b=? ，如果 a 列的几乎接近于唯一值，那么只需要单建 idx_a 索引即可。 说明:存在非等号和等号混合时，在建索引时，请把等号条件的列前置。如:where c>? and d=? 那么 即使 c 的区分度更高，也必须把 d 放在索引的最前列，即索引 idx_d_c。

1. <font color=febd2c>【推荐】</font>防止因字段类型不同造成的隐式转换，导致索引失效。
1. <font color=799042>【参考】</font>创建索引时避免有如下极端误解:

		1) 宁滥勿缺。认为一个查询就需要建一个索引。
		2) 宁缺勿滥。认为索引会消耗空间、严重拖慢记录的更新以及行的新增速度。
		3) 抵制惟一索引。认为业务的惟一性一律需要在应用层通过“先查后插”方式解决。

<a name="sql"></a>
## 4.3. SQL语句

1. <font color=bc0008>【强制】</font>不要使用count(列名)或count(常量)来替代count(*)，count(*)是SQL92定义的 标准统计行数的语法，跟数据库无关，跟 NULL 和非 NULL 无关。

	<font color=977919>说明:</font>count(*)会统计值为 NULL 的行，而 count(列名)不会统计此列为 NULL 值的行。

1. <font color=bc0008>【强制】</font>count(distinct col) 计算该列除 NULL 之外的不重复行数，注意 count(distinct col1, col2) 如果其中一列全为 NULL，那么即使另一列有不同的值，也返回为 0。
1. <font color=bc0008>【强制】</font>当某一列的值全是NULL时，count(col)的返回结果为0，但sum(col)的返回结果 为 NULL，因此使用 sum()时需注意 NPE 问题。

	<font color=29965b>正例:</font>使用如下方式来避免 sum 的 NPE 问题:SELECT IFNULL(SUM(column), 0) FROM table;

1. <font color=bc0008>【强制】</font>使用ISNULL()来判断是否为NULL值。

	<font color=977919>说明:</font>NULL 与任何值的直接比较都为 NULL。
		1) NULL<>NULL 的返回结果是 NULL，而不是 false。 
		2) NULL=NULL 的返回结果是 NULL，而不是 true。 
		3) NULL<>1 的返回结果是 NULL，而不是 true。

1. <font color=bc0008>【强制】</font>代码中写分页查询逻辑时，若count为0应直接返回，避免执行后面的分页语句。
1. <font color=bc0008>【强制】</font>不得使用外键与级联，一切外键概念必须在应用层解决。 

    <font color=977919>说明:</font>以学生和成绩的关系为例，学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外 键。如果更新学生表中的 student_id，同时触发成绩表中的 student_id 更新，即为级联更新。外键与级联更新适用于单机低并发，不适合分布式、高并发集群;级联更新是强阻塞，存在数据库更新风暴的风险;外键影响数据库的插入速度。

1. <font color=bc0008>【强制】</font>禁止使用存储过程，存储过程难以调试和扩展，更没有移植性。
1. <font color=bc0008>【强制】</font>数据订正(特别是删除、修改记录操作)时，要先select，避免出现误删除，确认无误才能执行更新语句。
1. <font color=febd2c>【推荐】</font>in操作能避免则避免，若实在避免不了，需要仔细评估in后边的集合元素数量，控制在 1000 个之内。
1. <font color=799042>【参考】</font>如果有国际化需要，所有的字符存储与表示，均以 utf-8 编码，注意字符统计函数的区别。

	<font color=977919>说明:</font>
		SELECT LENGTH("轻松工作"); 返回为 12
		SELECT CHARACTER_LENGTH("轻松工作"); 返回为 4 如果需要存储表情，那么选择 utf8mb4 来进行存储，注意它与 utf-8 编码的区别。

1. <font color=799042>【参考】</font>TRUNCATE TABLE 比 DELETE 速度快，且使用的系统和事务日志资源少，但 TRUNCATE 无事务且不触发 trigger，有可能造成事故，故不建议在开发代码中使用此语句。 

    <font color=977919>说明:</font>TRUNCATE TABLE 在功能上与不带 WHERE 子句的 DELETE 语句相同。

<a name="orm"></a>
## 4.4. ORM映射

1. <font color=bc0008>【强制】</font>在表查询中，一律不要使用 * 作为查询的字段列表，需要哪些字段必须明确写明。 

	<font color=977919>说明:</font>

		1)增加查询分析器解析成本。
		2)增减字段容易与 resultMap 配置不一致。
		3)无用字段增加网络消耗，尤其是 text 类型的字段。

1. <font color=bc0008>【强制】</font>更新数据表记录时，必须同时更新记录对应的 updated_at 字段值为当前时间。
1. <font color=febd2c>【推荐】</font>不要写一个大而全的数据更新接口。不管是不是自己的目标更新字段，都进行 update table set c1=value1,c2=value2,c3=value3; 这是不对的。执行 SQL 时，不要更新无改动的字段，一是易出错;二是效率低;三是增加 binlog 存储。
1. <font color=799042>【参考】</font>@Transactional事务不要滥用。事务会影响数据库的QPS，另外使用事务的地方需要考虑各方面的回滚方案，包括缓存回滚、搜索引擎回滚、消息补偿、统计修正等。
1. <font color=799042>【参考】</font><isEqual>中的 compareValue 是与属性值对比的常量，一般是数字，表示相等时 带上此条件;<isNotEmpty>表示不为空且不为 null 时执行;<isNotNull>表示不为 null 值 时执行。
