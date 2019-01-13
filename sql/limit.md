limit

	1、limit 用来获取一张表中的某部分数据
	
	2、limit 只有在MYSQL数据库中存在，不通用，是MYSQL数据库管理系统的特色
	
	3、案例：找出员工表中前5条记录
	mysql>select ename from emp limit 5;

	以上sql语句的"limit 5"中的5表示从表中记录下标0开始，取5条
	等同于下面的sql语句：
		
		select ename from emp limit 0,5;

	limit的使用语法格式：
		limit  起始下标，长度
		起始下标没有指定，默认从0开始，0表示第一条记录。

	4、案例：找出公司中工资排名在前5名的员工<limit出现在sql语句的最后的位置上>

	思路：按照工资降序排列取前5个
	select ename,sal from emp group by sal desc limit 5;


	5、案例：找出工资排名在【3-9】名的员工

	select ename,sal from emp group bu sal desc limit 2 7;

	6、mysql中通用的分页sql语句：

	案例：每页显示3条记录
	第1页：0,3
	第2页：3,3
	第3页：6,3
	第4页：9,3
	……………………………………

	每页显示pagesize条记录
	第pageno页：（pageNo-1）*pagesize,pagesize


	通用的分页SQL
	select
			t.*
	from 
			t
	order by
			t.x desc/asc
	limit 
		  （pageNo-1）*pagesize,pagesize


##############################################################3

1、使用mysql front工具插入数据成功，在DOS窗口中使用select语句查询的时候出现乱码，怎么解决？？
  
 	修改查询结果集的显示编码方式，这里修改的不是DOS窗口:
 	mysql>set character_set_results = 'GBK';  【只对当前会话有有效】

  查看MYSQL的相关字符编码方式：show variables like '%char%';


2.表的复制【快速创建表，并插入数据】

mysql> create table emp1 as select * from emp;
mysql> select * from emp1;

mysql> create tables emp2 as select empno,ename,sal from emp;
mysql> select * from emp2;

语法结构：
	create table tablename as select columnname,………… from tablename;
	将查询结果当做一张表创建


3.将查询结果插入到某张表中
 mysql> select * from emp2;

 mysql> insert into emp2 select * from emp2 where sal=3000

 mysql>select * from emp2;


 4.增/删/改 表结构 【DDL】
 drop table if exists t_student;
 create table t_stuendt(
		no int(10),
		name varchar(32)
 )

 mysql>desc t_stuent

 给t_student表添加一个联系电话字段【增】
 ALTER TABLE t_student add tel varchar(10);


 将t_student表格中的tel字段长度扩展到20个长度【改】
 ALTER TABLE t_student modify tel varchar(20);


 将t_student表格中的tel字段删除【删】

 ALTER TABLE t_student drop tel;


 5、增/删/改 表中的数据【insert update delete 】----DML语句

  5.1 update
  		UPDATE语句的语法格式：
  			UPDATE tablename set 字段名=字段值，字段名=字段值，字段名=字段值 where 条件;

  		注意：update语句没有条件，会将一张表中所有的数据全部更新	

  	  将no=3的记录name修改为zhangsan,email修改为zhangsan@itcast.com
	  update t_student set name ='zhangsan',email='zhangsan@itcast.com' where no =3;


	  将emp_bak表中的所有名字中含有O的员工名修改为zhangsan
	  update emp_bak set ename='zhangsan' where ename like '%O%';

	  将emp_bak表中所有工作岗位是MANGER和SALESMAN的员工工资上调10%；
	  update emp_bak set sal=sal*1.1 where job='MANAGER' or job='SALESMAN';

  5.2  delete
  	delete语句的语法格式:
  		delete from tablename where 条件

  	注意：若没有条件限制，会将这张表中所有的记录全部删除

	删除学号为3的学生
  	delete from t_student where no=3;

	将20部分中的MANAGER删除
	delete from emp_bak where deptno=20 and job='MANAGER';