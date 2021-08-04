# learn sql
## 查询数据
#### 基本查询
`select * from <table name>;`
#### 条件查询
`select * from <table name> where score = 80;`<br>
| 条件 |表达式举例1 |表达式举例2 |说明 |
| :-: | :-: | :-: | :-: |
| 使用=判断相等|score = 80 |name = 'abc' |字符串需要用单引号括起来 |
|使用>判断大于 |score > 80 |name >'abc' |字符串比较根据ASCII码，中文字符比较根据数据库设置 |
| 使用>=判断大于或相等|score >= 80 |name >='abc' | |
| 使用<判断小于|score < 80 |name <'abc' |
| 使用<=判断小于或等于| score<=80|name<='abc' |
| 使用<>判断不相等|score <> 80 | name <> 'abc' |
| 使用LIKE判断相似|name LIKE 'ab%' |name LIKE '%bc%' | %表示任意字符，例如'ab%'将匹配'ab','abc','abcd'|
`WHERE 60 <= score <= 90`，意思是`score >=60 or score <=90`
#### 投影查询
1. 如果我们只希望返回某些列的数据，而不是所有列的数据，我们可以用SELECT 列1, 列2, 列3 FROM ...，让结果集仅包含指定列。这种操作称为投影查询。<br>
    ```select id,score,name from student; ```
2. 使用SELECT 列1, 列2, 列3 FROM ...时，还可以给每一列起个别名，这样，结果集的列名就可以与原表的列名不同。它的语法是SELECT 列1 别名1, 列2 别名2, 列3 别名3 FROM ...。<br>
    ``` SELECT id, score points, name FROM students;``` --> 将score起别名为points
#### 排序
1. 由小到大排序可以加上ORDER BY子句 默认的排序规则是ASC：“升序”，即从小到大<br>
    ```select id,name,gender,score from students ORDER BY score;``` ```select id,name,gender,score from students ORDER BY score ASC;```
2. 由高到底降序,加上DESC<br>
    ```select id,name,gender,score from students ORDER BY score DESC;```
3. 有相同数据，继续排序 ORDER BY继续添加列名<br>
    ``` select id,name,gender,score from students ORDER BY score DESC,gender DESC;```
4. 如果有WHERE子句，那么ORDER BY子句要放到WHERE子句后面<br>
    ``` SELECT id,name,gender,score FROM students WHERE class_id = 1 ORDER BY score DESC;```
#### 分页查询
使用SELECT查询时，如果结果集数据量很大，比如几万行数据，放在一个页面显示的话数据量太大，不如分页显示，每次显示100条。<br>
分页查询的关键在于，首先要确定每页需要显示的结果数量pageSize（这里是3），然后根据当前页的索引pageIndex（从1开始），确定LIMIT和OFFSET应该设定的值：
- 
    - LIMIT总是设定为pageSize；
    - OFFSET计算公式为pageSize * (pageIndex - 1)。
    - OFFSET超过了查询的最大数量并不会报错，而是得到一个空的结果集。
- OFFSET是可选的，如果只写LIMIT 15，那么相当于LIMIT 15 OFFSET 0。

- 在MySQL中，LIMIT 15 OFFSET 30还可以简写成LIMIT 30, 15。

- 使用LIMIT <M> OFFSET <N>分页时，随着N越来越大，查询效率也会越来越低。<br>
``` SELECT id,name,gender,score FROM students ORDER BY score DESC LIMIT 3 OFFSET 0; ```
#### 聚合查询
1. 对于统计总数、平均数这类计算，SQL提供了专门的聚合函数，使用聚合函数进行查询，就是聚合查询，它可以快速获得结果。<br>
    | 函数|说明 |
    | :-: | :-: |
    | SUM|计算某一列的合计值，该列必须为数值类型 |
    | AVG|计算某一列平均值，该列必须为数值类型 |
    | MAX| 计算某一列最大值|
    | MIN| 计算某一列最小值|
    ```SELECT COUNT(*) FROM students;```<br>
    ```SELECT COUNT(*) num FROM students;```起别名<br>
2. 分组GROUP BY
    ```SELECT COUNT(*) num,class_id FROM students GROUP BY class_id;```<br>
    ```SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;```多个列进行分组
#### 多表查询
1. 查询多张表的语法是：SELECT * FROM <表1> <表2><br>
    ```select * from students,classes;```<br>
    这种多表查询又称笛卡尔查询，即students表的每一行与classes表的每一行都两两拼在一起返回。结果集的列数是students表和classes表的列数之和，行数是students表和classes表的行数之积。
2. 有重复列名，可以起别名<br>
    ``` 
    SELECT
        students.id sid,
        students.name,
        students.gender,
        students.score,
        classes.id cid,
        classes.name cname
    FROM students, classes;
    ```
3. 表名太长，表名别名<br>
    ```
    SELECT
        s.id sid,
        s.name,
        s.gender,
        s.score,
        c.id cid,
        c.name cname
    FROM students s, classes c;
    ```
4. 添加where条件<br>
    ```
    SELECT
        s.id sid,
        s.name,
        s.gender,
        s.score,
        c.id cid,
        c.name cname
    FROM students s, classes c
    WHERE s.gender = 'M' AND c.id = 1;
    ```
#### 连接查询
对多个表进行join运算
1. INNER JOIN 求取两表交集<br>
  - 先确定主表，仍然使用FROM <表1>的语法；
  - 再确定需要连接的表，使用INNER JOIN <表2>的语法；
  - 然后确定连接条件，使用ON <条件...>，这里的条件是s.class_id = c.id，表示students表的class_id列与classes表的id列相同的行需要连接；
  - 可选：加上WHERE子句、ORDER BY等子句。
  ```
  SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
  FROM students s
  INNER JOIN classes c
  ON s.class_id = c.id;
  ```
2. LEFT OUTER JOIN