# sqlbasis
# SQL

## SELECT
### 查询数据
     SELECT * FROM crawer                                 | 从crawer表中搜索
     SELECT url FROM crawer LINMIT 5 OFFSET 4             | 查找前5条从第4条开始
-------

     SELECT DISTINCT source FROM                          | crawer表中source只返回不同值
    
-------

     SELECT source FROM crawer ORDER BY source,url        | 按列字母顺序排序
     SELECT source，url，title FROM crawer ORDER BY 2,3    | 2,3表示先按url再按title排序
     SELECT source，url，title FROM crawer ORDER BY source DESC,title
-------
### 过滤数据
    SELECT * FROM crawer WHERE source = 'bbs.neotv.cn'    |
    SELECT * FROM crawer WHERE source = 'bbs.neotv.cn' ORDER BY source

#### 不匹配检测 <> 或者 != 或者 NOT
    SELECT * FROM crawer WHERE url <> 'bbs.neotv.cn' 
    
#### 范围检测
    SELECT * FROM crawer WHERE price BETWEEN 5 AND 10
    查询5-10之间的数据
    SELECT * FROM crawer WHERE price IS NULL
    查询空值，不是为0
    
#### and or in
    SELECT source,url FROM crawer WHERE source = '' or source =''
    SELECT source,url FROM crawer WHERE source = '' or (source ='' and url = '')
    SELECT source, url FROM crawer WHERE source IN ('bbs.neotv','bbs.xxx') ORDER BY source
    搜索source为括号里面的值得数据 列为source，url
    
###用通配符进行过滤
####LIKE操作符
    SELECT prod_iud,prod_name FROM  Products WHERE prod_name LIKE 'Fish%' 
    搜索以Fish起头的词
    SELECT prod_iud,prod_name FROM  Products WHERE prod_name LIKE '%bean bag%'  或者 'F%Y'
    搜索前后为任何字，或者中间为任何字
####下划线(_)通配符  方括号([])通配符
    _与%用途一样，但只匹配单个字符
    SELECT cust_contact FROM Customers WHERE cust_contact LIKE '[JM]%' ORDER BY cust_contact
    搜索以J或者MKAITOU DE   如果[^JM]则取非J或M开头的数据
    
####计算字段
    SELECT CONCAT_WS('#',source,title) FROM crawler ORDER BY source
    输出source,title拼接的
    
####使用别名 AS
    SELECT CONCAT_WS('#',source,title) AS title_source FROM crawler ORDER BY source
    输出source,title拼接 以title_source为列名
    
####数据处理函数
    SUBSTRING()  提取字符串的组成部分
    CONVERT()   数据类型转换
    CURDATE（）   取当前日期   ---》select now();
####文本处理函数    
    LEFT()  返回字符串左边的字符
    LENGTH()    返回字符串长度
    UPPER() 将字符串转换为大写
    LOWER（） 将字符串转换为小写
    LTRIM() 去掉字符串左边的空格
    RIGHT（） 返回字符串右边的字符
####日期和时间处理函数
    DATEDIFF
    DATE_FORMAT
    DATE_ADD
    DATE_SUB
    
    select DATE_FORMAT('1997-10-04 22:23:00', '%W %M %Y');
    'Saturday October 1997'
    
    SELECT order_num FROM Orders WHERE YEAR(ORDER_DATA) = 2012;
    提取年份
    
####数值处理函数
    ABS()   返回一个数的绝对值
    COS()   返回角度的余弦
    EXP()   返回一个数的指数值
    PI()    返回圆周率
    SQRT()  返回一个数的平方根
    
    
###汇总数据
    AVG()   返回某列的平均值
    COUNT() 返回某列的行数
    MAX()   返回牟烈的最大值
    MIN()   
    SUM()   返回某列值之和
    
    SELECT AVG(prod_price) AS avg_price FROM Products
    AVG只能用来确定特定数值列的平均值，而且名必须作为函数参数给出
    AVG胡烈值为NULL的行
    
    SELECT COUNT（*） AS num_cust FROM Customers;
    
    文本数据中MAX返回按该列排序后的最后一行 MIN返回该列排序后最前面的行
    
    SELECT AVG(DISTINCT prod_pric) AS avg_price FROM Products WHERE VEND_ID = 'DLL01'
    使用AVG函数返回特定供应商提供的产品的平均价格
    DISTINCT不能用于COUNT(*)
    
    SELECT COUNT(*) AS num_items, MIN(prod_price) AS price_min, MAX(prod_price) AS price_max,AVG(prod_price) AS price_AVG FROM Products;
    在制定别名以包含某个聚集函数的结果时，不应该使用表中实际的列名。
    
###分组数据
    SELECT source,COUNT(*) AS num_source FROM crawler GROUP BY source
    ![](media/15574052876129/15574721176763.jpg)
    返回分组数据， 按不同来源 显示数量
    GROUP BY 子句可以包含任意数目的列，可以对分组进行嵌套
    1、如果在GROUP BY 子句中嵌套了分组，数据将在最后指定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）
    2、GROUP BY 子句中列出的每一列都必须是检索列活有效列的表达式。 如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式，不能使用别名
    3、不允许是GROUP BY 列带有长度可变的数据类型，如文本或者备注型字段
    4、如果分组有NULL 那么NULL为一组
    5、必须出现在WHERE子句之后， ORDER BY子句之前
    
    WHERE不能过滤行 使用HAVING
    SELECT cust_id ,COUNT(*) AS orders FROM Orders GROUP BY cust_id HAVING COUNT(*) >=2
    过滤大于2的组
    
    SELECT source,COUNT(*) AS num_source FROM crawler GROUP BY source HAVING COUNT(*) >= 510 ORDER BY num_source
    分组排序并且过滤
    
####句子顺序
    SELECT  要返回的列或者表达式  必须使用
    FROM    从中检索数据的表        仅在从表选择数据时使用
    WHERE   行级过滤        不必须使用
    GROUP BY    分组说明        仅在按组计算聚集时使用
    HAVING  组级过滤        不必须使用
    ORDER BY    输出排序顺序  不必须使用
    
    
##使用子查询
    SELECT cust_id FROM Orders WHERE order_num IN (SELECT order_num FROM OrderItems WHERE prod_id = 'RGAN01')
    查询一张表中的包含此物品的订单，  查询此订单的顾客id
    
    SELECT cust_name,sust_state,(SELECT COUNT(*) FROM Orders WHERE Orders.cust_id = Customers.cust_id) AS orders From Customers ORDER BY cust_name;
    订单数量通过顾客名 在 订单表中查询每个人有多少订单。
    
###联结表  等值联结=内联结  联结多个表
    SELECT vend_name, prod_name, prod_price FROM Vendors,Products WHERE Vendors.vend_id = Products.vend_id;
    vend_name在一个表中。另外两个字段在另一张表中。 用WHERE子句正确的联结

    SELECT prod_name, vend_name, prod_price,quantity FROM OrderItems,Products,Vendors WHERE Products.vend_id = Vendors.vend_id AND OrderItems.prod_id = Products.prod_id AND order_num = 20007;
    
    
###自联结 自然联结 外联结
    包含两段sql的为自联结， 自然联结一段sql包含多张表
    外联结包括没有关联行的行，  使用OUTER JOIN来指定联结类型。 必须使用RIGHT或者LEFT 关键字指定包括其所有行的表 LEFT指出的是OUTER JOIN左边的表
    外联结
    SELECT Customers.custid, Orders.order_num FROM Customers LEFT OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id
    检索没有订单顾客在内的所有顾客
    
####使用带聚集函数的联结
    SELECT Customers.cust_id COUNT(Orders.order_num) AS num_ord FROM Customers INNER JOIN Orders ON Customers.cust_id = Orders.cust_id GROUP BY Customers.cust_id;
    检索所有顾客机每个顾客下的订单数。
    
###组合查询 UNION
    使用UNION查询多条sql语句 返回查询结果集
    必须有两条或者两条以上的SELECT语句组成，语句之间用。
    列数据必须兼容，类型不必完全相同
    
    如果使用 UNION ALL 不去重
    如果需要排序 则在语句后面加ORDER BY 
    
 
   
   
#insert数据
    INSERT INTO Customers Values('123123','234234')
    必须给每一列提供一个值
    
    INSERT INTO Customers(cust_id,cust_contact) SELECT cust_id,cust_contact FROM CustNes;
    把灵异表中的顾客列合并到Customers中。
    
    <!--SELECT * INTO CustCopy FROM Customers;-->
    CREATE TABLE Custcopy AS SELECT * FROM Customers;
    创建一个名为CustCopy的新表，并把内容复制过去
    
    INSET SELECT 与 SELECT INTO 前者到处数据。后者导入数据
    
#update delete
    UPDATE Customers SET cust_contact = 'lc', cust_email = 'liuchaomuyu@163.com' WHERE cust_id = '100000'
    
    DELETE FROM Customers WHERE cust_id = '10000';
    要删除指定的列，情书用update。
    TRUNCATE TABLE速度更快，并且不记录数据的变动
    
    
#创建和操纵表
    CREATE TABLE Products
    (
        prod_id   CHAR(10)  NOT NULL
        quantity  INTEGER   NTO NULL  DEFAULT 1
    )
    非空 默认值为1
    
    CURRENT_DATE() 获取系统日期
    
####更新表
    ALTER TABLE Vendors ADD vend_phone CHAR(20)
    给表添加一个vend_phone的列  
    
    复杂的表结构更改一般需要手动删除过程
    
####删除表
    DROP TABLE CustCopy
    
#存储过程
####
   

#管理实务处理
###事物-回退-提交-保留点
    START TRANSACTION  
###使用ROLLBACK   COMMIT   保留点
    DELETE FROM Orders;
    ROLLBACK;
    删除之后撤销
    
    BEGIN TRANSACTION
    DELETE OrderItems WHERE order_num = 12345
    DELETE Orders WHERE order_num = 12345
    COMMIT TRANSACTION
    删除定单12345 。涉及两个表使用事务处理块，最后的COMMIT语句 在不出错时才更改，如果第二条失败，则delete不会提交。
    
    ROLLBACK TO delete1;
    
#游标
    在查询的行中前进或者后退一行或多行， 使用游标。
    能够标记游标为制度，是数据能读取，、但不能更新删除
    能控制可以执行的定向操作（向前，向后，第一，最后，绝对位置，相对位置）
    能标记某些列为可标记的，某些列为不可编辑的
    规定范围，使游标对创建它的特定请求（如存储过程）或对所有请求可访问。
    
    
    使用前 必须声明，这个过程实际上没有检索数据，它只是定义要使用的SELECT语句和游标选项
    声明之后必须打开游标以供使用，这个过程用前面定义的select语句吧数据实际检索出来。
    对于填有数据的游标，根据需要取出各行
    结束必须关闭。可能的话释放。
    
    DECLARE CustCursor CURSOR 
    FOR
    SELECT * FROM Customers
    WHERE cust_email IS NULL
    DECLARE语句用来定义和命名游标。这里为CustCursor SELECT语句定义一个包含没有电子邮件地址的所有顾客的游标。
    
    使用游标
    OPEN CURSOR CustCursor
    使用FETCH语句访问游标数据，FETCH支出要检索那些航，从何处检索他们以及将他们防御何处（如变量名），
    DECLARE TYPE CustCursor IS REF CURSOR
        RETURN Customers%ROWTYPE;
        DECLARE CustRecord Customers%ROWTYPE
    BEGIN
        OPEN CustCursor;
        FETCH CustCursor INTO CustRecord
        CLOSE CustCursor;
    END;
    使用FETCH用来检索当前行，放到声明的变量CustRecord中
    
        DECLARE TYPE CustCursor IS REF CURSOR
        RETURN Customers%ROWTYPE;
        DECLARE CustRecord Customers%ROWTYPE
    BEGIN
        OPEN CustCursor;
        LOOP
        FETCH CustCursor INTO CustRecord
        EXIT WHEN CustCurso%NOTFOUND;
        ...
        CLOSE CustCursor;
    END;
    使用FETCH用来检索当前行，放到声明的变量CustRecord中,EXIT使在取不出更多行时终止处理，
    
###添加外键
    ALTER TABLE Orders
    ADD CONSTRAINT
    FOREIGN KEY (cust_id) REFERENCES Customers (cust_id)
    
###唯一约束 检查约束
    唯一约束保证一列中数据是唯一的
    检查约束用来保证数据满足一组指定条件
    
    CREATE TABLE OrderItems
    (
        quantity    INTEGER check(QUANTITY > 0)
    )

###索引
    索引改善检索操作性能
    可能占用大量储存空间
    。。。
    
    CREATE INDEX prod_name_ind
    ON PRODUCTS (prod_name);
    索引必须唯一命名，这里的索引名prod_name_ind在关键字CREATE INDEX之后定义，ON用来指定被索引的表，尔索引中包含的列在表明后的括号中给出
    
###触发器
触发器内的代码居右以下数据的访问权
insert才做中的所有新数据
UPDATE操作中所有新数据和旧数据
DELETE操作中删除的数据
用途：
保证数据一致， 例如在INSERT或UPDATE操作中讲所有洲名转换为大写
基于某个表的变动在其他表上执行活动。 例如，每当更新或删除一行时将审计跟踪记录写入某个日志表
进行额外的验证并根据需要回退数据
计算计算列的值或者更新时间戳




    
    
