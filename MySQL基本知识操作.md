```mysql

# 创建数据库
create database exercise;

# 选择数据库
use exercise;

# 创建表
CREATE TABLE IF NOT EXISTS `runoob_tbl`(
                                           `runoob_id` INT UNSIGNED AUTO_INCREMENT,
                                           `runoob_title` VARCHAR(100) NOT NULL,
                                           `runoob_author` VARCHAR(40) NOT NULL,
                                           `submission_date` DATE,
                                           PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

#表2
create table websites(
    id int primary key auto_increment,
    name varchar(10) not null ,
    url varchar(100) not null,
    alexa int not null ,
    country varchar(10) not null
)engine=InnoDB DEFAULT  CHARSET =utf8;
insert into websites values (1,'Google','https://www.google.cm/',1,'USA');
insert into websites values (2,'菜鸟网站','https://www.runoob.com/',12,'CN');
insert into websites values (3,'微博','https://www.weibo.com/',2121,'CN');

#表3
CREATE TABLE `employee_tbl` (
                                `id` int(11) NOT NULL,
                                `name` char(10) NOT NULL DEFAULT '',
                                `date` datetime NOT NULL,
                                `singin` tinyint(4) NOT NULL DEFAULT '0' COMMENT '登录次数',
                                PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

#开启事务
BEGIN;
INSERT INTO `employee_tbl` VALUES ('1', '小明', '2016-04-22 15:25:33', '1'), ('2', '小王', '2016-04-20 15:25:47', '3'), ('3', '小丽', '2016-04-19 15:26:02', '2'), ('4', '小王', '2016-04-07 15:26:14', '4'), ('5', '小明', '2016-04-11 15:26:40', '4'), ('6', '小明', '2016-04-04 15:26:54', '2');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;

# 查看表的信息
desc runoob_tbl;

# 插入数据
INSERT INTO runoob_tbl(runoob_title, runoob_author, submission_date)VALUES('学习 MySQL', '菜鸟教程', NOW());
INSERT INTO runoob_tbl (runoob_title, runoob_author, submission_date)VALUES('学习 Git', '菜鸟教程', NOW()),('学习 Java', '菜鸟教程', NOW());

# 查询数据
select * from runoob_tbl as r ;#分页查询！

# 更新数据
update runoob_tbl as r set r.runoob_title = '学习 Linux',r.runoob_author='www.runoob.com',r.submission_date = NOW() where runoob_id = 1;
update runoob_tbl as r set r.runoob_author = '鸟菜' where r.runoob_id = 2;

# 复杂查询
select runoob_author from runoob_tbl where (select * from runoob_tbl where runoob_author = 'www.runoob.com');

# 模糊查询(注意逻辑的运用！)
select * from runoob_tbl as r where r.runoob_author like '_菜';
select * from runoob_tbl as r where r.runoob_author like '鸟菜';
select * from runoob_tbl as r where r.runoob_author like '%菜';
select * from runoob_tbl as r where r.runoob_author like '菜%';
select * from runoob_tbl as r where r.runoob_author like '%菜%';
select * from runoob_tbl as r where r.runoob_author like '%q%' or r.runoob_title like '%x%';
select * from runoob_tbl as r where r.runoob_author like '%q%' and r.runoob_title like '%x%';

# MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。
select runoob_title from runoob_tbl union select country from websites;
select runoob_author from runoob_tbl union select url from websites;
select runoob_author from runoob_tbl where runoob_id = 1 union select name from websites;

#查询排序
select * from runoob_tbl as r order by  r.runoob_title desc ;#小到大
select * from runoob_tbl as r order by  r.runoob_title asc ;#大到小

#分组
select * from employee_tbl;
select name,singin from employee_tbl group by name;
select name,count(*) from employee_tbl group by name;
select name as '姓名',sum(singin) as '登录次数' ,date as '登陆时间' from employee_tbl group by name with rollup;

##重点
#INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
#LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
#RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。
select * from websites inner join employee_tbl;
select runoob_id,runoob_title,runoob_author,submission_date from runoob_tbl left join employee_tbl on id;
select url from websites right join runoob_tbl on runoob_title;

#事务
begin;
insert into websites values (4,'QQ','https://www.qq.com/',2455,'CN');
select * from websites;
rollback;#回滚
select * from  websites;

#修改表字段的命令
alter table websites add place varchar(10);
desc websites;
alter table websites drop place;
begin ;
alter table websites rename web;
rollback ;
show columns from websites;

#索引
# ALTER TABLE tbl_name ADD PRIMARY KEY (column_list): 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
# ALTER TABLE tbl_name ADD UNIQUE index_name (column_list): 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。
# ALTER TABLE tbl_name ADD INDEX index_name (column_list): 添加普通索引，索引值可出现多次。
# ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list):该语句指定了索引为 FULLTEXT ，用于全文索引。
show index from websites;#查看索引
create index name on websites(name);#创建索引
alter table websites drop index name;#删除索引

#临时表
#MySQL 临时表在我们需要保存一些临时数据时是非常有用的。临时表只在当前连接可见，当关闭连接时，Mysql会自动删除表并释放所有空间
CREATE TEMPORARY TABLE SalesSummary (
     product_name VARCHAR(50) NOT NULL
     , total_sales DECIMAL(12,2) NOT NULL DEFAULT 0.00
     , avg_unit_price DECIMAL(7,2) NOT NULL DEFAULT 0.00
     , total_units_sold INT UNSIGNED NOT NULL DEFAULT 0
    );
desc SalesSummary;
insert into SalesSummary values ('a',3,4,1);
select * from SalesSummary;

#MySQL 导出数据
select * from websites into outfile 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/websites.txt';
show variables like '%secure%';

SELECT * FROM websites INTO OUTFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/websites.txt'
     FIELDS TERMINATED BY ',' ENCLOSED BY '"'
     LINES TERMINATED BY '\r\n';

#导入数据
LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE websites;

LOAD DATA LOCAL INFILE 'dump.txt' INTO TABLE websites FIELDS TERMINATED BY ':' LINES TERMINATED BY '\r\n';
```