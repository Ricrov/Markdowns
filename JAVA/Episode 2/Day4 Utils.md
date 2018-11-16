# 可变字符串 (也是一个字符串)
## ==StringBuilder== sb = new StringBuilder();
### StringBuilder 常用方法
* 字符串拼接
  * ==sb.append(可填参数)== 常用
* 把StringBuilder对象转换成String,如果需要String类型的时候可以把StringBuilder转换成String类型
  * sb.toString();
* 截取字符串是有返回值的也不会对原来的字符串造成更改
  * sb.substring();
* 替换字符串中的内容
  * sb.replace(int start,int end,String str);
* 把字符串翻转
  * sb.reverse();
* 在字符串中插入
  * sb.insert(int index,String str);在index位置插入str
### ==返回值不是StringBuilder的时候,是不会对StringBuilder字符串更改的,否则会对它字符串更改==
# StringBuffer
* ==用法与StringBuilder一样==
* 区别:==在多线程开发中,优先使用StringBuffer,因为StringBuffer是线程安全的==
* ==单线程情况下优先使用StringBuilder,因为它的效率比StringBuffer高==


# Math (常用类)
#### ==提供了一些常用的数学计算方法,是一个工具类(静态的)==
### 常用方法
* 求绝对值
  * Math.abs()
* 求三角函数
  * Math.sin(double a);参数填写弧度
* 求数的次幂
 * Math.pow(2,3) 表示2的3次方
* 随机数
  * Math.random(),常用方法,返回一个[0,1)之间的随机小数,返回值为double类型
* 常量:Math.PI;表示常量π
## 常量的命名
* 每个字母都大写,并且不同单词之间用下划线分割

# 日期时间相关 (long型数字)
## 起始时间规定
* 1970.1.1零点到现在为止所经历的毫秒数
## 基本用法
* System.currentTimeMillis() 获取从起始时间到现在为止的毫秒数
## DATE
## 基本用法
* DATE的构造方法里可以填时间,如果填了就表示填的时间,否则表示当前时间
* 判断两个时间前后顺序
  * date.after(date1) before(date1),判断date是否在date1之后或之前
* date.getTime() 获取当前时间毫秒数

## 时间格式化类 SimpleDateFormat
* SimpleDateFormat("yyyy-MM-dd hh:mm:ss") 在创建对象的时候填写要格式化的时间格式 括号里填的是日期被格式化的标准
* y代表年,M代表月,d代表日,h代表小时,m代表分钟,s代表秒
* format()方法,把毫秒格式化具体时间
* parse();把具体日期转换成毫秒数
  * Date date = simple.parse();
  * date.getTime() 先转换成时间然后获取毫秒数(getTime());


# 随机数 Random
### Random ran = new Random(long seed);
### 计算机中的随机数都是伪随机数
* seed种子数,用来生产随机数的,不填的话是根据当前时间产生随机数
* ran.nextInt(num) 产生[0,num)之间的随机整数(int)
* ran.nextFloat() 产生[0,1)之间的随机小数(float)
# Calendar 日期
### Calendar cal = Calendar.getInstance();用此方式创建Calendar对象
#### Calendar中的方法
##### 获取日期相关的数据
* int year = cal.get(Calendar.YEAR); 获取年
* int month = cal.get(Calendar.MONTH); 获取月份
##### 日期计算 (加日期 , 设置日期)
* cal.add(int field, int amount) 表示在field表示的时间加上amount值, 如cal.add(Calendar.Year, 5), 表示加5年
* cal.set(int field, int value) 设置时间
* cal.set(int year, int month, int day) 设置时间
* 计算出来的月份加一才是实际月份

# ==单例模式(静态工厂)== java中23中设计模式之一
### 基本概念:保证在java程序中,某个类只有一个实例存在
