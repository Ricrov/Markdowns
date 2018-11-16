## Apache工具类使用步骤
1. 加载驱动
2. 获取数据库连接
3. 创建QueryRunner对象
```
QueryRunner runner = new QueryRunner()
"通过QueryRunner对象的方法操作数据库";
```
### QueryRunner中的常用方法
1. update 方法
```
String sql = "delete from t_score where stu_id = ? and course_id = ?";
Object[] o = {1,1};
int num = runner.update(conn, sql, o)
"第一个参数是Connection对象";
"第二个参数是SQL语句";
"第三个参数是SQL语句中的条件参数";
```
2. query 查询方法
```
String s = "select * from t_students where stu_gender = ?";
Object[] p = {"男"};
List<StudentBean> beanList = qr.query(conn,
        s,
        new BeanListHandler<>(StudentBean.class, new UnderLineProcessor()),
        p);
"第一个参数为Connection对象";
"第二个参数为SQL语句";
"第三个参数为查询所返回的类型";
"第四个参数为SQL语句中的条件参数";
```
==query 查询一条语句==
```
String sq = "select * from t_students where stu_id = ?";
QueryRunner qrs = new QueryRunner();
StudentBean stu = qrs.query(conn,
        sq,
        new BeanHandler<>(StudentBean.class, new UnderLineProcessor()),
        11
        );
```
### 解决实体类的属性和结果集中的列名不匹配的方法
1. 让实体类中的变量名和结果集列名一样
2. 把结果集中的列名设置别名和实体类中的一样
3. 通过转换器转换
### 通过工具类关闭连接
```
DbUtils.close(conn);
```
