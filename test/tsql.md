## 常量、变量、与用户自定义类型

### 用户自定义类型

6.1创建数据类型用于学号

```sql
USE pxscj

CREATE TYPE STUDENT_num
	FROM char(6) NOT NULL
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191127102955.png)

6.1+命令定义xsb表结构，使用STUDENT_num数据类型

```sql
CREATE TABLE xsbtest
(
	学号 STUDENT_num NOT NULL PRIMARY KEY,
	姓名 char(8) NOT NULL,
	性别 bit null DEFAULT 1,
	出生时间 datetime NULL,
	专业 char(12) NULL,
	总学分 int NULL,
	备注 varchar(500) NULL
)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191127104734.png)

6.2创建用户自定义表数据类型，包含cjb表中的所有列。

```sql
USE pxscj;
CREATE TABLE cjb_tabletype
(
	学号 	char(6)	NOT NULL,
	课程号		char(3)	NOT NULL,
	成绩		int NOT NULL,
	PRIMARY KEY(学号,课程号)
)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206102910.png)



6.2+其他使用测试

在创建**用户定义表类型**前先建立一个测试表TestTable

```sql
create table TestTable( 
ids int identity, 
name nvarchar(100), 
age int, 
zip nvarchar(100) 
) 
/*
identity从1开始每次自动增加1，如果删除还是从原来的序列开始，而不是删除后的新序列
比如插入三条记录，又删除三条记录，则再次添加一条记录，其增加为4而不是1
*/
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191127110029.png)

然后创建一个**用户定义表类型** AType 

suoyoulie

```sql
CREATE TYPE AType AS TABLE 
( 
name nvarchar(100) default(''), 
age INT 
)
```



![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191127110123.png)



```sql

--临时变量真的很临时，下面语句必须一起执行，否则会报错结果未声明@t

--声明一个变量@t,类型为AType 
declare @t AType

--往变量@t中插入2条数据 
insert into @t (name,age) 
VALUES ('b',33)

--显示变量@t中数据 
SELECT * FROM @t

--利用类型为AType的变量@t往TestTable表中插入数据 
insert into TestTable(age,zip) 
select age,'zip' 
from @t

insert into TestTable(name,age,zip) 
select name,age,'zip' 
from @t

--查询表TestTable 
select ids,name,age,'zip'
from TestTable


--select *
--from TestTable
--DELETE FROM TestTable WHERE ids = 1
--DELETE FROM TestTable WHERE ids = 2
--DELETE FROM TestTable WHERE ids = 3

/*
https://www.cnblogs.com/sunbingqiang/p/10334165.html
请注意,用户定义表类型使用有以下限制： 
1.在创建用户定义表类型定义后不能对其进行修改。
2.不能在用户定义表类型的计算列的定义中调用用户定义函数。 
3.无法对用户定义表类型创建非聚集索引，除非该索引是对用户定义表类型创建PRIMARY KEY 或UNIQUE约束的结果。 
4.用户定义表类型不能用作表中的列或结构化用户定义类型中的字段。
*/

```



### 变量：DECLARE/SET/SELECT

6.3创建局部变量@var1、@var2并赋值，然后输出变量的值

```sql
DECLARE @var1 char(10),@var2 char(30)
SET  @var1='中国'	/*一个SET语句只能为一个变量赋值*/
SET @var2=@var1+'是一个伟大的国家'
SELECT @var1,@var2

```



![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206103546.png)





6.3+创建一个名为sex的局部变量，并在SELECT语句中使用该局部变量查找表xsb中所有女同学的学号、姓名。

```sql
DECLARE @sex bit
SET @sex=0
SELECT 学号,姓名
FROM xsb
WHERE 性别=@sex;

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206104401.png)



6.4使用SELECT语句为局部变量赋值

```sql
DECLARE @var1 nvarchar(30)
SELECT @var1='刘丰'
SELECT @var1 AS 'NAME'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206112907.png)





6.4+为局部变量赋空值

```sql
DECLARE @var1 nvarchar(30)
SELECT @var1='刘丰'
SELECT @var1 =
(
	SELECT 姓名
	FROM xsb
	WHERE 学号 = '191399'
)
SELECT @var1 AS 'NAME'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206113144.png)





6.5使用游标

```sql
USE pxscj
DECLARE @CursorVar CURSOR
/*定义游标变量*/
SET @CursorVar=CURSOR SCROLL DYNAMIC
/*为游标变量赋值*/
FOR
	SELECT 学号,姓名
	FROM xsb
	WHERE 姓名 LIKE '王%'
OPEN @CursorVar
/*打开游标*/
FETCH NEXT FROM @CursorVar
FETCH NEXT FROM @CursorVar
/*通过游标读行记录*/
CLOSE @CursorVar
DEALLOCATE @CursorVar
/*删除对游标的引用*/
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206110433.png)



6.6声明一个表数据类型变量并向变量中插入数据

```sql
/*声明变量*/
DECLARE @var_table
AS TABLE
(
	num char(6) NOT NULL PRIMARY KEY,
	name char(8) NOT NULL,
	sex bit NULL
)
/*插入数据*/
INSERT INTO @var_table
SELECT 学号,姓名,性别 FROM xsb
/*查看内容*/
SELECT  TOP(4) * FROM @var_table
WHERE '备注' IS NOT NULL;
/*WHERE '备注' = NOT NULL;   错误*/

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206112338.png)



## 运算符与表达式



6.7在test1数据库中，建立表bitop，并插入一行，然后将a字段和b字段列上值进行安位与运算。

```sql
USE test1
GO
CREATE TABLE bitop
(
	a int NOT NULL,
	b int NOT NULL
)
INSERT bitop VALUES (168,73)
SELECT a&b, a|b, a^b
FROM bitop
GO

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206134235.png)



6.8查询成绩高于"林一帆"最高成绩的学生姓名、课程名及成绩。

```sql
USE pxscj
GO 
SELECT 姓名,课程名,成绩
FROM xsb,cjb,kcb
WHERE 成绩>ALL
(
	SELECT b.成绩
	FROM xsb a,cjb b
	WHERE a.学号 = b.学号 AND a.姓名='林一帆'
)
AND xsb.学号 = cjb.学号
AND kcb.课程号=cjb.课程号
AND 姓名<>'王一帆'

```



![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206135427.png)



6.9查询总学分在范围40~50的所有学生的学号和姓名。
```sql
SELECT 学号,姓名,总学分
FROM xsb 
WHERE  总学分 BETWEEN 40 AND 50
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206140410.png)



6.9+ 查询总学分在范围40~50之外的所有学生的学号和姓名。

```sql
SELECT 学号,姓名,总学分
FROM xsb 
WHERE  总学分 NOT BETWEEN 40 AND 50
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206135750.png)

6.10查询课程名以"计"或者C开头的情况

```sql
SELECT * 
FROM kcb 
WHERE 课程名 like '[计 C]%'

```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206140739.png)


6.11查询所有选课学生的姓名

```sql
SELECT DISTINCT 姓名
FROM xsb 
WHERE EXISTS
(
	SELECT *
	FROM cjb
	WHERE xsb.学号=cjb.学号
)
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206141114.png)



6.12多个字符串的连接

```sql
SELECT (学号+ ',' + 姓名) AS 学号及姓名
FROM xsb
WHERE 学号='191301'
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206141348.png)


## 流程控制语句

6.13查询总学分大于42的学生人数

```sql
USE pxscj
GO
DECLARE @num INT
SELECT @num = 
(
	SELECT COUNT(姓名) 
	FROM xsb
	WHERE 总学分>42
)
IF @num<>0
SELECT @num AS '总学分>42的人数'
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206143553.png)

6.14如果计算机基础课程的平均成绩高于75分，则显示”平均成绩高于75分“

```sql
IF
(
	SELECT AVG(成绩)
	FROM xsb,cjb,kcb
	WHERE xsb.学号=cjb.学号
	AND cjb.课程号=kcb.课程号
	AND kcb.课程名='计算机基础'
)<75
	SELECT '平均成绩低于75'
ELSE
	SELECT '平均成绩高于75'
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206144404.png)


6.14+IF...ELSE使用

```sql
IF
(
	SELECT AVG(成绩)
	FROM xsb,cjb,kcb
	WHERE xsb.学号=cjb.学号
	AND cjb.课程号=kcb.课程号
	AND kcb.课程名='计算机基础'
)<75
	SELECT '平均成绩低于75'
ELSE
	IF
	(
		SELECT AVG(成绩)
		FROM xsb,cjb,kcb
		WHERE xsb.学号=cjb.学号
		AND cjb.课程号=kcb.课程号
		AND kcb.课程名='计算机基础'
	)>75
		SELECT '平均成绩高于75'
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206144735.png)


6.15使用第一种格式的CASH语句，根据性别值输出”男“或者“女”

```sql
SELECT 学号,姓名,专业,SEX=
	CASE 性别
	WHEN 1 THEN '男'
	WHEN 0 THEN '女'
	ELSE '无'
	END
FROM xsb
WHERE 总学分>48
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206145929.png)

使用第二种格式CASE语句

```sql
SELECT 学号,姓名,专业,SEX=
	CASE
	WHEN 性别= 1 THEN '男'
	WHEN 性别= 0 THEN '女'
	ELSE '无'
	END
FROM xsb
WHERE 总学分>48
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191206150140.png)

6.16将学号为191301的学生的总学分使用循环修改到60，每次只加2并判断循环了多少次。

```sql
USE pxscj
GO
DECLARE @num INT
SET @num=0
WHILE (SELECT 总学分 FROM xsb WHERE 学号='191301')<60
BEGIN 
	UPDATE xsb SET 总学分 = 总学分+2 WHERE 学号='191301'
	SET @num=@num+1
END
SELECT @num AS 循环次数
	

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191208204204.png)



6.17判断是否存在学号为191328的学生，如果存在则返回，不存在则插入191328号学生的信息。

```sql
IF EXISTS(SELECT * FROM xsb  WHERE 学号='191328')
	RETURN
ELSE
	INSERT INTO xsb VALUES('191328','张可',1,'1990-08-12','计算机',52,NULL)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191208204736.png)

6.18如下语句设定在早上8点执行查询语句。

```sql
BEGIN
	WAITFOR TIME '8:00'
	SELECT * FROM xsb
END
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191208205031.png)

## 系统内置函数

6.19如下程序第一列为xsb中的姓，第二列为学生名。

```sql
SELECT TOP(4) SUBSTRING(姓名,1,1),SUBSTRING(姓名,2,LEN(姓名)-1)
FROM xsb
ORDER BY 姓名
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191208205757.png)



6.20显示字符串“China”中每个字符的ASCII值和字符

```sql
DECLARE @position int,@string char(8)
SET @position = 1
SET @string = 'China'
WHILE @position < DATALENGTH(@string)
	BEGIN
		SELECT ASCII(SUBSTRING(@string,@position,1)) AS ASCII码,
		CHAR(ASCII(SUBSTRING(@string,@position,1))) AS 字符
		SET @position=@position+1
	END
```
![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209091344.png)



6.21检索总学分在50-59分学生姓名，并将总学分转换为char(20)

```sql
USE pxscj
GO
/*使用cast实现*/
SELECT 姓名,总学分
FROM  xsb
WHERE  CAST(总学分 AS char(20)) LIKE '5_'
/*使用convert实现*/
SELECT 姓名,总学分
FROM  xsb
WHERE  CONVERT(char(20),总学分) LIKE '5_'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209092321.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209092341.png)





6.22声明一个游标，并用SELECT显示@@CURSOR_ROWS的值

```sql
USE pxscj
GO
SELECT @@CURSOR_ROWS
DECLARE student_cursor  CURSOR
FOR SELECT 姓名 FROM xsb
OPEN student_cursor
FETCH NEXT FROM student_cursor
SELECT @@CURSOR_ROWS
CLOSE student_cursor
DEALLOCATE student_cursor
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209093040.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209093058.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209093118.png)



6.23用@@FETCH_STATUS控制一个在WHILE循环中的游标活动

```sql
USE pxscj
GO
DECLARE @name char(20),@num char(6)
DECLARE student_cur  CURSOR
FOR SELECT 姓名,学号 FROM pxscj.dbo.xsb
OPEN student_cur
FETCH NEXT FROM student_cur INTO @name,@num
SELECT @name,@num
WHILE @@FETCH_STATUS=0
BEGIN
	FETCH NEXT FROM student_cur
END
CLOSE student_cur
DEALLOCATE student_cur
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209094110.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209094048.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209094138.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209094154.png)

## 用户自定义函数

6.24创建用户自定义函数，实现计算全体学生某门功课平均成绩

```sql
/*创建用户自定义函数*/
USE pxscj
GO
CREATE FUNCTION average(@num char(20)) RETURNS INT
AS
BEGIN
	DECLARE @aver int 
	SELECT @aver=
	(
		SELECT avg(成绩)
		FROM  cjb
		WHERE 课程号=@num
		GROUP BY 课程号
	)
	RETURN @aver
END

/*调用用户定义函数*/
DECLARE @course1 char(20)
DECLARE @aver1 INT
SELECT @course1 = '101'
SELECT @aver1=dbo.average(@course1)
SELECT @aver1 AS '101课程的平均成绩'

/*在pxscj中建立一个course表，并将一个字段定义为计算列*/
CREATE TABLE course
(
	cno int,
	cname nchar(20),
	credit int,
	aver AS 
	(
		dbo.average(cno)
	)
)

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209100004.png)





6.25对于pxscj函数，利用xsb、kcb、cjb三个表创建视图，让学生查询其各科成绩及学分。

```sql
/*创建用户自定义函数*/
USE pxscj
GO
CREATE VIEW xsv
AS 
	SELECT dbo.xsb.学号,dbo.xsb.姓名,dbo.kcb.课程名,dbo.cjb.成绩
	FROM dbo.kcb
	INNER  JOIN  dbo.cjb ON dbo.kcb.课程号=dbo.cjb.课程号
	INNER JOIN dbo.xsb ON dbo.cjb.学号 = dbo.xsb.学号

/*定义内嵌函数*/
CREATE FUNCTION student_score(@id char(6)) RETURNS table
AS RETURN
(
	SELECT *
	FROM pxscj.dbo.xsv
	WHERE dbo.xsv.学号 = @id
)


/*调用内嵌函数，查询学号为191301的学生的各科成绩及学分*/
SELECT *
FROM pxscj.dbo.student_score('191301')
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209105410.png)






6.26在pxscj数据库中创建返回表的函数，通过以学号作为实参调用该函数，可显示该学生各门功课的成绩和学分。

```sql
USE pxscj
GO
CREATE FUNCTION score_table(@id char(6)) 
RETURNS @score TABLE
(
	学号 char(6),
	姓名 char(8),
	课程 char(16),
	成绩 tinyint,
	学分 tinyint
)
AS 
BEGIN
	INSERT @score
		SELECT S.学号,S.姓名,P.课程名,O.成绩,P.学分
		FROM pxscj.dbo.xsb AS S 
			INNER JOIN pxscj.dbo.cjb AS O ON(S.学号=O.学号)
			INNER JOIN pxscj.dbo.kcb AS P ON(O.课程号=P.课程号)
		WHERE S.学号=@id
		RETURN
END

SELECT * 
FROM pxscj.dbo.score_table('191301')




```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209110724.png)



## myDB操作

（1）定义“部门”数据类型。

```sql
USE myDB
CREATE TYPE 部门
	FROM int
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209111520.png)

（2）在myDB数据库的ygb表中增加一个字段，字段类型为部门。

```sql
ALTER TABLE ygb
	ADD  bmh 部门 NULL
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209111638.png)



（3）在myDB数据库中创建表类型，结构与ygb相同，用该表类型创建ygb1表。

```sql
CREATE TABLE myDB.dbo.ygb1 (
	gh char(5)  NOT NULL,
	sfz char(18) NOT NULL,
	xm char(8)  NOT NULL,
	gzsj date NOT NULL,
	gz decimal(7,2) NOT NULL,
	hf bit NULL,
	bmh 部门 NULL,
	PRIMARY KEY (gh)
) 
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209111829.png)

（4）将ygb中的数据增加到ygb1。

```sql
/*DROP TABLE ygb1*/
CREATE TABLE myDB.dbo.ygb1 (
	gh char(5)  NOT NULL,
	sfz char(18) NOT NULL,
	xm char(8)  NOT NULL,
	gzsj date NOT NULL,
	gz decimal(7,2) NOT NULL,
	hf bit NULL,
	bmh 部门 NULL,
	PRIMARY KEY (gh)
) 

INSERT INTO ygb1
	SELECT * FROM ygb
	
SELECT * FROM ygb1
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191209112833.png)
















































































