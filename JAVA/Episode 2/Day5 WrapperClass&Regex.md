# 基本数据类型包装类
### int对应的是Integer
* ==Integer.parseInt(Sting str)==,Integer的静态方法,把数字字符串转换成对应的数,如Integer.parseInt("678");输出结果为678
### byte对应的是Byte
### short对应的是Short
### lnog对应的是Long
### float对应的是Float
### double对应的是Double
##### ==它们都可以把数字字符串转换成对应的数字的静态方法==
### char对应的是Character
### boolean对应的是Boolean

## ==包装类的默认值为null==

### Integer a1 = 100;
### Integer a2 = 100;
### Integer b1 = 200;
### Integer b2 = 200;
### a1==a2; b1!=b2;==如果Integer的值在-128到127之间,那么它们完全相等,否则它们的值相等,地址不相等==
# 第四个异常
## ==数字格式化异常 NumbersFormatException==

# 正则表达式
### 作用
* ==用来判断某个字符串是不是符合某个规则== 常用
* 在某个字符串中查找符合某个规则的子串
### 正则表达式的使用
String str = "lsjflsajfsilfosifjoij";
* +号:代表符合加号(+)前面的字符一个或多个
* 星号(*):代表匹配星号前面字符0个或多个
* \ 表示转义符号,在正则表达式中有意义的字符,如果需要匹配有意义的字符的话,需要用\转义,在字符串中的写法为两个\,比如 \ \ *
* [] 中括号代表匹配[]里的字符,匹配规则为[]里面的任意一个
* | 竖线代表或者的关系
* {num,num1} 大括号中的数代表根据大括号前面的字符组合num数量或num1拆分,如果num1不写,表示拆分num或num以上的数量
* (regex) 小括号表示根据括号里的内容拆除
### 常用写法
* \d 匹配所有数字
* \w 英文数字下划线(_)
* \s 匹配所有空白
  * 包括 空格/换行/制表符(tab) \n \t \r
## Parttern类 匹配正则表达式
* Parttern.matches(regex,String text) regex表示正则表达式,text表示文本,返回值会boolean类型
