# mysql_01
#mysql_01
本项目旨在mysql下实现简易的图书管理系统
一共有三个文本文件【users category books】
分别表示用户表   图书类别表   现存图书表
用户表包含：用户编号/用户名/年龄/性别/购书编号
图书类别表包含：图书类别/书名
现存图书表包含：图书编号/图书编号/书名/价格

通过新建数据库/表、导入文本文件、用select方法筛选数据、到处文本文件等操作我们实现了对一个图书管理系统的数据操作

项目出现的常见问题及解决命令行如下：
启动MySQL服务：sudo service mysql start

首次进入MySQL：
-进入/etc/mysql，打开debian.cnf
-查看user和password【假设user=01 password=123】
-然后执行 mysql -u 01 -p
-接着输入密码123即可

添加root用户(mysql 5.5)：
-use mysql;
-update user set password=PASSWORD('新密码') where user='root'
-退出MySQL，重启MySQL服务，输入sudo -u root -p，即可以root身份进入MySQL

修改文件权限：
-在导入导出文件是我们用到目录/var/lib/mysql-files，
-需要对此目录进行权限更改 执行sudo chmod 777 /var/lib/mysql-files即可

项目业务相关命令如下：
CREATE DATABASE books_system;

USE books_system;

CREATE TABLE category(c_id int(10),name longtext);

CREATE TABLE books(b_id int(10),c_id int(10),b_name longtext,price int(10));

CREATE TABLE users(u_id int(10),u_name longtext,age int(10),gender longtext,b_id int(10));

LOAD DATA INFILE '/var/lib/mysql-files/category.txt' INTO TABLE category FIELDS TERMINATED BY ',';

LOAD DATA INFILE '/var/lib/mysql-files/books.txt' INTO TABLE books FIELDS TERMINATED BY ',';

LOAD DATA INFILE '/var/lib/mysql-files/users.txt' INTO TABLE users FIELDS TERMINATED BY ',';

# 图书价格降序排列，只显示书名和价格

SELECT b_name,price FROM books ORDER BY price DESC INTO OUTFILE '/var/lib/mysql-files/desc.txt';

# 把价格最高的书保存用户变量中

SELECT @max_price := MAX(price) FROM books;

# 查询哪些用户购买了价格最高的这本书

SELECT * FROM users WHERE b_id=(select b_id FROM books WHERE price=@max_price) INTO OUTFILE '/var/lib/mysql-files/user.txt';

# 按照书的类别分类，查询每种书，用户购买的数量

SELECT COUNT(u_id),c_id FROM books b,users u WHERE b.b_id = u.b_id GROUP BY c_id INTO OUTFILE '/var/lib/mysql-files/count.txt';

