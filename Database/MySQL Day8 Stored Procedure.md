## 存储过程 procedure
- 它相当于java中的方法
### 创建存储过程
```
"创建存储过程";
create procedure query_student()
"定义存储过程起始位置";
begin
"存储过程需要做的事";
select * from t_students;
"标记存储过程结束的位置";
end
```
```
"如果存储过程中只有一条SQL语句,
那么begin和end可以省略";
create procedure query_stu()
select * from t_students;
```
### 存储过程不能修改
#### 删除存储过程
```
drop procedure 存储过程名字
```
### 创建存储过程之前需要先自定义分隔符
```
delimiter $$
"创建完存储过程之后需要把分隔符修改回来";
```
### 调用存储过程
```
call 存储过程名字
```
### 创建带参数的存储过程
```
delimiter $$
create procedure insert_stu(
	-- 格式 in 变量名 数据类型
	-- 可以传递具体的值,也可以传递变量
	-- in 表示该变量是调用存储过程时需要传入的变量
	in name varchar(100),
	in birthday date,
	in gender varchar(20),
	-- out 表示存储过程执行完毕之后,用来获取处理的结果
	-- 在调用该过程的时候,该参数只能填写变量
	-- 进入存储过程中会把该变量置空
	-- 如果没有在存储过程中给该变量重写赋值,那么该变量最终的结果为null
	out qty int
	-- inout
	-- 相当于结合了in和out
	-- 可以向过程中传递值
	-- 过程执行完毕后也可以修改该变量的值
	-- 只能传递变量不能传递具体的值
)
begin
-- select qty;
insert into t_students(
	stu_name, birthday, stu_gender
) values (
	name, birthday, gender
);
-- 局部变量不需要加@
set qty=(select row_count());
end
$$
delimiter ;
```
### ==select row_count()==
- 获取到它上面的SQL语句影响的条数

### 使用JDBC操作存储过程
1. 加载驱动
2. 获取数据库连接
3. 获取CallableStatemet对象,用它来调用存储过程
```
CallableStatement call = conn.prepareCall("{call query_stu}")
```
4. 执行过程一般会用executeQuery方法
```
ResultSet query = call.executeQuery();
"处理结果集的方式和普通查询一样";
```
5. 如果一个存储过程中有out参数,那么如何获取到该参数
```
"注册输出参数";
"设置存储过程中的out参数的数据类型";
out.registerOutParameter(4,Types.INTEGER);
```
6. 调用过程
```
out.executeQuery()
"调用完存储过程之后,取出out参数";
int qty = out.getInt(4)
"4指的是out参数的位置";
```
