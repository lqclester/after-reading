---
title: sql优化
date: 2017-10-31 16:19:53
tags:
  - mysql
  - 语句优化
categories:
  - code
---

mysql在开发和设计中，相关的优化建议。主要从schema设计,索引的设计,查询语句编写，3方面。
<!-- more -->

### schema
#### 1. 主键建议用 自增主键：bigint unsinged
- 不建议与业务数据处理有关联关系
- 方便dba运维，比如归档，批量更新数据，可以根据id来做范围归档，更方便，如 id >= $i and id < $i+3000 and id < $max and 其他条件，做到更精准的遍历数据
- 按load到内存考虑，varchar 占用的内存空间，是它定义的字符长度的至少3倍


#### 2. 禁止default NULL
- NULL值会有额外的空间来存储，并不是Null标志位只固定占用1个字节==，而是以8为单位，满8个null字段就多1个字节，不满8个也占用1个字节，高位用0补齐。对于相同数据的表，字段中有NULL值的表比NOT NULL的大，为了空间容量和性能，[参考](http://mysql.taobao.org/monthly/2016/08/07/)
- NULL值是不相等的 ,对业务表述可能会有影响,不能=‘NULL’ 或者 = ‘null’，只能 is null，这个最终不利于开发的编写，随着代码迭代，人员的流动容易产生（实际也出现过），产生不必要的麻烦，如代码要写成 `col =‘NULL’ or col is null`
- `count(),max(),min()`是忽略NULL的，而`count(*)`是包含NULL值，那么`count(col)和count(*)`结果是不一样的。
- 禁止default NULL，数字类型not null default 0，字符类型not null default ''，时间not null default '1970-01-01 00:00:00'或者 ‘0000-00-00 00:00:00’；


#### 3. 尽量用单表查询，避免多表JOIN，禁止多于3表join，join的字段数据类型必须绝对一致。

### 建立索引原则
#### 1. 最左前缀匹配原则
 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

#### 2. 和查询条件顺序无关，mysql查询器自动优化
=和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

#### 3. 尽量选择区分度高的列作为索引
 尽量选择区分度高的列作为索引,区分度的公式是`count(distinct col)/count(*)`,表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录

#### 4. 索引字段不参与运算
索引列不能参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);

#### 5. 尽量拓展索引
尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，(a,b,c) 相当于 (a) 、(a,b) 、(a,b,c)。

#### explain
Column|Meaning
--|--
id|本次 select 的标识符。在查询中每个 select都有一个顺序的数值。
select_type|select 的类型，SIMPLE，PRIMARY，UNION等
table|表
type|表连接类型 const> eq_ref > ref
possible_keys| 可以走的索引  
key| 索引
key_len| 索引长度(大小，int->4,允许NULL，加1byte =5)
ref| ref 字段显示了哪些字段或者常量被用来和 key配合从表中查询记录出来。
rows| 查询的总行数
extra|额外信息

##### select_type
select 的类型，可能会有以下几种：
1. simple: 简单的 select （没有使用 union或子查询）
2. primary: 最外层的 select。
3. union: 第二层，在select 之后使用了 union。
4. dependent union: union 语句中的第二个select，依赖于外部子查询
5. subquery: 子查询中的第一个 select
6. dependent subquery: 子查询中的第一个 subquery依赖于外部的子查询
7. derived: 派生表 select（from子句中的子查询）
##### 参考资料
1. [mysql explain官方文档解释](https://dev.mysql.com/doc/refman/5.5/en/explain-output.html)
2. [explain字段解释](http://blog.jobbole.com/103058/)


### 查询语句编写
 1. 先运行看看是否真的很慢，注意设置SQL_NO_CACHE
例如：
```
select sql_no_cache id from table
```
2. where条件单表查，锁定最小返回记录表。
这句话的意思是把查询语句的where都应用到表中返回的记录数最小的表开始查起，单表每个字段分别查询，看哪个字段的区分度最高
2.explain查看执行计划，是否与1预期一致（从锁定记录较少的表开始查询）
3. order by limit 使用
order by limit使用形式的sql语句让排序的表优先查
4. 了解业务方使用场景
5. 加索引时参照建索引的几大原则
6. 观察结果，不符合预期继续从0分析

### 其他相关
#### 常用的类型大小
类型	|大小	|范围（有符号）	|范围（无符号）	|用途 |宽度
--|--|--|--|--|--|--|--|--
TINYINT	|1 字节|	(-128, 127)	|(0, 255=2^8-1)|	小整数|3
SMALLINT	|2 字节|	(-32 768, 32 767)	(0, 65 535)	|	大整数值|6
INT或INTEGER	|4 字节	|(-2 147 483 648, 2 147 483 647)|	(0, 4 294 967 295)	|	大整数值 |10
BIGINT	|8 字节|	(-9 233 372 036 854 775 808, 9 223 372 036 854 775 807)|		(0, 2^8-1)	|	极大整数值|20
DOUBLE	|8 字节|	(1.797 E+308, 2.225 E-308) , 0, (2.225 E-308, 1.797E+308)	|0, (2.225E-308, 1.797E+308)|双精度|根据情况

### 资料
[美团](https://tech.meituan.com/mysql-index.html)
[strace 跟踪进程中的系统调用](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/strace.html)
