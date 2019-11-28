## 选择查询结果输出列

### 1.选择所有列

 4.1查询pxscj数据库中xsb表的所有记录

```sql
USE pxscj
SELECT *
	FROM xsb
	/*使用*表示一个表或者视图中的所有列*/
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113103013.png)



### 2.选择一个表中指定列

4.2查询xsb中计算机专业学生的学号、姓名和总学分

```sql
SELECT 学号,姓名,总学分
	FROM xsb
	WHERE 专业 = '计算机'
	/*
	 * 注意
	 * 学号,姓名,总学分中间的逗号是英文半角逗号
	 * '计算机'的引号在sqlserver中是英文半角正引号，是enter边的，而非1边的
	 */
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124203555.png)


### 3.定义列别名

4.3查询xsb表中计算机系同学的学号、姓名和总学分。查询结果中各列的标题分别指定为`number` `name` 和`mark`

```sql
SELECT 学号 AS number,姓名 AS name,总学分 AS mark
	FROM xsb
	WHERE 专业 = '计算机'
	/*
	 * 原字段名 = 要显示字段名
	 */
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113104647.png)

```sql
SELECT number = 学号, name = 姓名,mark = 总学分
	FROM xsb
	WHERE 专业 = '计算机'
	/*
	 * 要显示字段名（列别名） = 原字段名（原字段名或者表达式） 
	 * 和AS区分 ： 原字段名 AS 列别名 是相反的
	 */
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124203609.png)




```sql
SELECT 'Student number' = 学号, 姓名 AS 'Student name',mark = 总学分
	FROM xsb
	WHERE 专业 = '计算机'
	/*
	 * 当自定义的标题中含有空格时，必须使用正的英文单引号括起来
	 */
/*
关于引号使用小总结
-----------------------------------
SQlserver中,
下列所有单引号，均是正的半角英文单引号，下列所有双引号，均是正的半角英文单引号
1）
创建表时
列名可以为【"姓名"【姓名
如果包含空格或者特殊字符只能是加双引号【"姓  名"【"姓&名"
如果数字开头只能是【"666姓名"
如果是纯数字只能是【"666"
2)
表别名时
列名可以为【"姓名"【姓名【'姓名'
如果包含空格或者特殊字符只能是加双引号或者单引号【"姓  名"【'姓  名'【"姓&名"【'姓&名'
如果数字开头只能是【"666姓名"【'666姓名'
如果是纯数字只能是【"666"【'666'
3）
字符串数据插入时
使用正的英文半角单引号
-------------------------------------
mysql中均为反的半角英文单引号（在1左边）
*/
```



![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113105453.png)

```sql
SELECT 性别 AS SEX
	FROM xsb
	WHERE SEX = 0
	/*
	 * 不允许在WHERE子句中使用列别名
	 * 这是因为WHERE代码中可能存在尚未确定的值
	 */
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113112327.png)









### 4.替换查询结果中的数据

4.4查询xsb中计算机系各同学的学号、姓名和总学分，并将总学分按照等级替换，标题改为等级

```sql
SELECT 学号,姓名,等级 = 
	CASE 
		WHEN 总学分 IS NULL THEN '尚未选课'
		WHEN 总学分 < 50 THEN '不及格'
		WHEN 总学分 >= 50 and 总学分 <= 52 THEN '合格'
		ELSE '优秀'
	END
	FROM xsb
	WHERE 专业 = '计算机'

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113162156.png)

### 5.计算列值

4.5查询计算机专业学生的年龄

```sql
USE pxscj
SELECT 学号,姓名,出生时间,YEAR(GETDATE())-YEAR(出生时间) AS 年龄
	FROM xsb
	WHERE 专业='计算机'

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113171632.png)

### 6.消除结果集中的重复行

```sql
INSERT INTO pxscj.dbo.xsb(学号,姓名,性别,出生时间,专业,总学分)
	VALUES('231201','王一平',1,'1990-02-10','管理工程',50)
INSERT INTO pxscj.dbo.xsb(学号,姓名,性别,出生时间,专业,总学分)
	VALUES('231301','王一平',1,'1990-02-10','管理工程',50)
```

```sql
USE pxscj
SELECT DISTINCT 专业 FROM xsb
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113172410.png)

```sql
USE pxscj
SELECT ALL 专业 FROM xsb
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113172449.png)



### 7.限制结果返回行数

4.7对pxscj数据库的xsb表选择姓名、专业和总学分，返回结果集的前6行

```sql
SELECT TOP 6 姓名,专业,总学分
	FROM xsb
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191113172807.png)



### 8.聚合函数

4.8求所有课程的总学分和选修101课程的学生的平均成绩

```sql
SELECT SUM(学分) AS '总学分'
	FROM kcb
SELECT AVG(成绩) AS '计算机基础平均成绩'
	FROM cjb
	WHERE 课程号 = '101'
```



![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124205751.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124205805.png)



4.9求选修101课程的学生的最高分和最低分

```sql
SELECT MAX(成绩) AS '计算机基础最高分', MIN(成绩)AS '计算机基础最低分'
	FROM cjb
	WHERE 课程号 = '101'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124205331.png)



4.10求学生的总数、专业个数和总学分在50分以上的人数




```sql
SELECT COUNT(*) AS '学生总数',COUNT(DISTINCT 专业) AS '专业个数'
	FROM xsb;

SELECT COUNT(总学分) AS '总学分>50分人数'
	FROM xsb
	WHERE 总学分>50;
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124213359.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124213418.png)

## 选择查询条件

### 1.表达式比较


4.11查询xsb表中计算机总学分大于等于42的同学的情况




```sql
SELECT *
	FROM xsb
	WHERE 专业='计算机' AND 总学分 >=42
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124214704.png)



### 2.模式匹配

4.12查询xsb表中姓“王”且单名的学生情况

```sql
SELECT *
	FROM xsb
	WHERE 姓名 LIKE '王_'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124215052.png)




4.13查询xsb表中学号倒数第5个数字为9，且倒数第1个在1~5之间的学生学号、姓名及专业。



```sql
SELECT 学号,姓名,专业
	FROM xsb
	WHERE 学号 LIKE '%9___[1-5]'

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124215229.png)

### 3.范围比较


4.14查询xsb表中不在1995年出生的学生情况



```sql

SELECT 学号,姓名,专业,出生时间
	FROM xsb
	WHERE 出生时间 NOT BETWEEN '1995-1-1' AND '1995-12-31'
       
 /*
 报错
 SELECT 学号,姓名,专业,出生时间
	FROM xsb
	WHERE WHERE year(出生时间)!=1995
*/
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124215907.png)




4.15查询xsb表中专业为“计算机”或“通信工程”学生情况。



```sql
SELECT *
	FROM xsb
	WHERE 专业 IN ('计算机','通信工程')

SELECT *
	FROM xsb
	WHERE 专业='计算机' OR 专业='通信工程'

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124220135.png)

### 3.空值比较


4.16查询总学分尚不定的学生情况

```sql
SELECT *
	FROM xsb
	WHERE 备注 IS NULL
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124220345.png)

### 4.子查询

#### 4.1IN子查询


4.17查询选修了课程号为206的课程的学生情况。

```sql
SELECT *
	FROM xsb
	WHERE 学号 IN 
		(
            SELECT 学号
			FROM cjb
			WHERE 课程号 = '206'
		)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124220611.png)




4.18查询为选修离散数学的学生学号和姓名



```sql
SELECT *
	FROM xsb
	WHERE 学号 NOT IN 
		(SELECT 学号
			FROM cjb
			WHERE 课程号 IN
				(
					SELECT 课程号
						FROM kcb
						WHERE 课程名 = '离散数学'
				)
		)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124220934.png)

#### 4.2比较子查询


4.19查询选修了离散数学的学生学号和姓名。



```sql
SELECT 学号
	FROM cjb
	WHERE 课程号 = 
		(
		    SELECT 课程号
			FROM kcb
			WHERE 课程名 = '离散数学'
		)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124221244.png)




4.20查询比所有计算机系的学生年龄都大的学生



```sql
SELECT *
	FROM xsb
	WHERE 出生时间 < ALL 
		(
		    SELECT 出生时间
				FROM xsb
				WHERE 专业 = '计算机'
		)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124221517.png)








4.21查询206课程号成绩不低于101课程号最低成绩的学生学号



```sql
SELECT 学号
	FROM cjb
	WHERE 课程号 = '206' AND 成绩 !< ANY
		(
		    SELECT 成绩
				FROM cjb
				WHERE 课程号 = '101'
		)

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124221825.png)

#### 4.3EXISTS


4.22查询选修206课程的学生姓名。



```sql
SELECT 姓名
	FROM xsb
	WHERE EXISTS
		(
		    SELECT 成绩
				FROM cjb
				WHERE 学号 = xsb.学号 AND 课程号 = '206'
		)

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124225450.png)




4.23查询选修了全部课程的同学的姓名。



```sql
SELECT 姓名
	FROM xsb
	WHERE NOT EXISTS
		(
		    SELECT *
				FROM kcb
				WHERE NOT EXISTS
					(
						SELECT *
							FROM cjb
							WHERE 学号=xsb.学号 AND 课程号 = kcb.课程号
					)
		)

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124225825.png)




4.24从xsb表中查询所有女学生的姓名、学号及其与“191301”号学生的年龄差距



```sql
SELECT 学号,姓名,
		YEAR(出生时间) - 
		YEAR(SELECT 出生时间
				FROM xsb
				WHERE 学号 = '191301'
		) AS 年龄差距
	FROM xsb
	WHERE 性别 = 0
//报错
```

## 指定查询对象：FROM子句

### 1.表或视图名


4.25查询“191301”号学生的计算机基础课的成绩。



```sql

SELECT 成绩
	FROM cjb,kcb
	WHERE cjb.课程号 = kcb.课程号
		AND 学号 = '191301'
		AND 课程名 = '计算机基础'
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124231031.png)




4.26查询选修了与学号为191302的同学所选修的全部课程的同学的学号。



```sql
SELECT DISTINCT 学号
	FROM cjb AS  CJ1
	WHERE NOT EXISTS 
		(
			SELECT *
			 FROM
			 	cjb AS CJ2
			 WHERE CJ2.学号 = '191302' AND NOT EXISTS
			 (
			 	SELECT *
			 		FROM cjb AS CJ3
			 		WHERE CJ3.学号 = CJ1.学号 AND CJ3.课程号 = CJ2.课程号
			 )
		)

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124232122.png)

### 2.导出表


4.27从xsb表中查询总学分大于50的男同学的姓名和学号。



```sql
SELECT 姓名, 学号, 总学分
	FROM(SELECT 姓名,学号,性别,总学分
		FROM xsb
		WHERE 总学分 >= 50
		)AS student
	WHERE 性别 = 1

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124232522.png)




4.28在xsb表中查询在1995年1月1日以前出生的学生的姓名和专业。



```sql

SELECT student.name,student.speciality
	FROM (SELECT * 
			FROM xsb
			where 出生时间<'19950101'
	)AS student(num,name,sex,birthday,speciality,score,mam)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124232941.png)

### 3.连接

#### 3.1连接谓词


4.29查询pxscj数据库每个学生的情况以及选修的课程情况。



```sql
SELECT xsb.*,cjb.*
	FROM xsb,cjb
	WHERE xsb.学号=cjb.学号

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124233219.png)




4.30自然连接查询。



```sql
SELECT xsb.*,
       cjb.课程号,
       cjb.成绩
FROM   xsb,
       cjb
WHERE  xsb.学号 = cjb.学号

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124235622.png)




4.31查询选修了206课程且成绩在80分以上的学生姓名及成绩。



```sql
SELECT 姓名,
       成绩
FROM   xsb,
       cjb
WHERE  xsb.学号 = cjb.学号
       AND 课程号 = '206'
       AND 成绩 >= 80

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124235648.png)




4.32查询选修了“计算机基础”课程且成绩在80以上的学生学号、姓名、课程名及其成绩。



```sql
SELECT xsb.学号,
       姓名,
       课程名,
       成绩
FROM   xsb,
       kcb,
       cjb
WHERE  xsb.学号 = cjb.学号
       AND kcb.课程号 = '计算机基础'
       AND 成绩 >= 80

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124235720.png)

#### 3.2JOIN关键字指定的连接


4.33查询每个学生的情况以及选修的课程情况。



```sql
SELECT *
FROM   xsb
       INNER JOIN cjb
               ON xsb.学号 = cjb.学号


```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191124235751.png)




4.34查询选修了206课程且成绩在80分以上的学生姓名及成绩。



```sql
SELECT 姓名,
       成绩
FROM   xsb
       JOIN cjb
         ON xsb.学号 = cjb.学号
WHERE  课程号 = '206'
       AND 成绩 >= 80

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000157.png)




4.35查询选修了“计算机基础”课程且成绩在80分以上的学生学号、姓名、课程名及其成绩。



```sql
SELECT xsb.学号,
       姓名,
       课程名,
       成绩
FROM   xsb
       JOIN cjb
            JOIN kcb
              ON cjb.课程号 = kcb.课程号
         ON xsb.学号 = cjb.学号
WHERE  课程名 = '计算机基础'
       AND 成绩 >= 80

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000328.png)




4.36查询不同课程成绩相同的学生的学号、课程号和成绩。



```sql
SELECT a.学号,
       a.课程号,
       b.课程号,
       a.成绩
FROM   cjb a
       JOIN cjb b
         ON a.成绩 = b.成绩
            AND a.学号 = b.学号
            AND a.课程号 != b.课程号


```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000540.png)




4.37查询所有学生情况，以及他们选修的课程号



```sql
SELECT xsb.*,
       课程号
FROM   xsb
       LEFT OUTER JOIN cjb
                    ON xsb.学号 = cjb.学号

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000455.png)




4.38查询被选修了的课程的选修情况和所有开设的课程名。



```sql
SELECT cjb.*,
       课程名
FROM   cjb
       RIGHT JOIN kcb
               ON cjb.课程号 = kcb.课程号


```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000635.png)




4.39列出学生所有可能的选课情况



```sql
SELECT 学号,
       姓名,
       课程号,
       课程名
FROM   xsb
       CROSS JOIN kcb

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000809.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125000839.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001006.png)

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001026.png)

## 指定查询结果分组方法


4.40在pxscj数据库xsb表中分别用GROUP BY ROLLUP、GROUP BY CUBE 和 GROUP BY GROUPING SETS对（专业，性别）产生一个结果集



```sql
SELECT 专业,
       性别,
       Count(*) AS '人数'
FROM   xsb
GROUP  BY rollup ( 专业, 性别 )

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001126.png)

​    


```sql
SELECT 专业,
       性别,
       Count(*) AS '人数'
FROM   xsb
GROUP  BY cube ( 专业, 性别 )

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001303.png)



​    


```sql

SELECT 专业,
       性别,
       Count(*) AS '人数'
FROM   xsb
GROUP  BY grouping sets ( 专业, 性别 )
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001344.png)

## 指定查询结果分组后筛选条件HAVING子句


4.41查询平均成绩在85分以上的学生的学号和平均成绩。



```sql
SELECT 学号,
       Avg(成绩) AS '平均成绩'
FROM   cjb
GROUP  BY 学号
HAVING Avg(成绩) >= 85

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001417.png)




4.42查询选修课程超过2门且成绩都在80分以上的学生的学号。



```sql
SELECT 学号
FROM   cjb
WHERE  成绩 >= 80
GROUP  BY 学号
HAVING Count(*) > 2

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001740.png)




4.43查询通信工程专业平均成绩在85分以上的学生的学号和平均成绩。



```sql
SELECT 学号,
       Avg(成绩) AS '平均成绩'
FROM   cjb
WHERE  学号 IN (SELECT 学号
                  FROM   xsb
                  WHERE  专业 = '通信工程')
GROUP  BY 学号
HAVING Avg(成绩) >= 85

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001814.png)

## 指定查询结果排序顺序：ORDER BY子句


4.44将计算机的学生按出生时间先后顺序排序。



```sql
SELECT *
FROM   xsb
WHERE  专业 = '计算机'
ORDER  BY 出生时间

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001916.png)




4.45讲计算机专业学生的“计算机基础”课程成绩按降序排列。



```sql
SELECT 姓名,
       课程名,
       成绩
FROM   xsb,
       kcb,
       cjb
WHERE  xsb.学号 = cjb.学号
       AND cjb.课程号 = kcb.课程号
       AND 课程名 = '计算机基础'
       AND 专业 = '计算机'
ORDER  BY 成绩 DESC

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125001958.png)

## SELECT子句其他用法


4.46由xsb表创建“计算机学生”表，包括学号和姓名。



```sql
SELECT 学号,
       姓名
INTO   计算机学生
FROM   xsb
WHERE  专业 = '计算机'

SELECT 学号,
       姓名
INTO   通信工程学生
FROM   xsb
WHERE  专业 = '通信工程'

```




4.47在“计算机学生”表和“通信工程学生”表中查询学号为191301和学号为221301的两位同学的姓名。



```sql
SELECT *
FROM   计算机学生
WHERE  学号 = '191301'
UNION ALL
SELECT *
FROM   通信工程学生
WHERE  学号 = '221301'

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125002118.png)




4.48查询计算机专业女学生的信息。



```sql
SELECT *
FROM   xsb
WHERE  专业 = '计算机'
EXCEPT
SELECT *
FROM   xsb
WHERE  性别 = 1

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125002137.png)




4.49查询总学分大于42的男学生信息



```sql
SELECT *
FROM   xsb
WHERE  总学分 > 42
INTERSECT
SELECT *
FROM   xsb
WHERE  性别 = 1

```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125002253.png)






4.50使用CTE从cjb表中查询姓名为“王林”的学生学号和成绩情况。



```sql
WITH cte_stu(number, point)
     AS (SELECT 学号,
                成绩
         FROM   cjb
         WHERE  课程号 = '101')
SELECT number,
       point
FROM   cte_stu,
       xsb
WHERE  xsb.姓名 = '王林'
       AND xsb.学号 = cte_stu.number


```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125002325.png)





自己做：
（1）显示年龄为40岁的女员工号、姓名、工作时间、工资、婚否，按照工作的时间排序，已婚显示“是”。

```sql
USE myDB
/*       
SELECT *
FROM   ygb
WHERE  sex = '0'
       AND age = 40
ORDER  BY gzsj

发现没这字节，原来信息隐藏在身份证里，倒数第二位是性别，男奇女偶，第七位开始是生日
用like匹配
2019-40=1979
*/

USE myDB

INSERT INTO myDB.dbo.ygb(gh,sfz,xm,gzsj,gz,hf)
VALUES('1103','320324197911030020','tomok','2010-11-11',90,0)
INSERT INTO myDB.dbo.ygb(gh,sfz,xm,gzsj,gz,hf)
VALUES('1104','320324197911020030','tom2no','2011-11-11',90,0)
INSERT INTO myDB.dbo.ygb(gh,sfz,xm,gzsj,gz,hf)
VALUES('1105','320324197911010040','tom3ok','2011-11-11',90,0)
	
SELECT *
FROM   ygb
WHERE  sfz LIKE '______1979%[02468]_'
ORDER  BY gzsj
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125094207.png)






（2）按照部门汇总工资总和，包括部门号、部门名、工资总和。

```sql
SELECT LEFT(ygb.gh, 2) AS bmh,
       Min(bmb.bmm)    AS bmm,
       Sum(gz)         AS gzsum
FROM   ygb,
       bmb
WHERE  LEFT(ygb.gh, 2) = bmb.bmh
GROUP  BY LEFT(ygb.gh, 2)
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125003249.png)



（3）查询工龄为20年、工资小于4000元的人员工号、姓名、工作时间和工资。

```sql
SELECT gh,
       xm,
       bmm,
       gzsj,
       gz
FROM   ygb,
       bmb
WHERE  LEFT(gh, 2) = bmh
       AND Year(Getdate()) - Year(gzsj) = 20
       AND gz < 4000 
```

![](https://raw.githubusercontent.com/ZanderZhao/images/master/img2019/20191125003357.png)





