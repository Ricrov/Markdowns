# 存储过程
- 为了完成特定功能的sql语句集,一组可编程的函数
- 可以在mysql进行编程
- 它会保持在数据库中,可以永久保存
- 在调用的时候直接调用即可
## 存储过程的创建和调用
- delimiter:自定义执行符号,系统默认的是分号,可以通过delimiter改为其他符号
- 它与存储过程 语句无关
- 创建存储过程: creat procedure 储存过程名字() begin 需要执行的语句 end;
- 运行存储过程: call 储存过程名字();
- 查看所有的存储过程: show procedure status
- 查看某个数据库中的存储过程: show procedure status where db='数据库名';
- 查看存储过程详细信息: show creat procudure 存储过程名字;
- 删除存储过程: drop procedure 存储过程名字;
## 存储过程变量
- 声明变量: declare语句声明变量
- declare 变量名 数据类型 default(默认值) '';
- 给变量赋值:set 变量名 = 变量值;
- 给变量赋值: select ... into...: select avg(salary) into 变量名 from 表名;
## 存储过程参数:
- in
- creat procedure getName(in name varchar(255)) begin  select*from emp where ename = name;  end;
- out
- inout
# 索引
- 创建索引: creat index 索引名字 on 表名(字段名)
- 查看索引: show index from 表名
