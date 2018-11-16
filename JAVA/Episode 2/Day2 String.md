# 字符串
* String str = "让教育回归本质";
* ==字符串是通过封装char而得到的==
## 字符串方法:
* 1 获取字符串的长度
  * int length = str.length();
* 2 获取字符串中对应位置的==字符==
  * str.charAt(int index) 获取字符串中第index位置的字符
* 3 ==获取某个字符在字符串中第一次出现的位置==
  * str.indexOf('育');括号里填字符
* 4 第二个indexOf()方法
  * str.indexOf('本',fromIndex); 表示从fromIndex 开始,第一次出现'本'字符的位置
* 5 第三个indexOf();
  * str.indexOf("教育");查找括号里的==连续的子串==在原字符串出现的位置,如果没有返回 -1
* 6 第4个indexOf()
  * str.indexOf("教育",fromIndex) 表示从fromIndex位置开始,"教育"在原字符串中出现的位置
* 7 查找字符串中是否包含某个字符串
* str.matches(String regex),判断字符串是否符合regex正则表达式的规则
  * str.contains("本质"),返回值为boolean类型
* 8 判断字符串是否以某个字符串开始的
  * str.startsWith("让");返回值为boolean类型
* 9 判断字符串是否以某个字符串结束的
  * str.endsWith("质");
* 10 查找某个字符在字符串中从后往前出现的位置;语法同indexOf()一样
  * str.lastIndexOf();
* 11 ==替换字符串中某个字符==
  * str.replace(old,new),把old的字符或字符串,全部替换为new的字符或字符串,==该方法会返回一个新的字符串对象==; ==注意:原字符串的内容不变==
* 12 str.replaceAll(String regex,String replacement), 把满足正则表达式的子串,替换成新的字符串
* 判断字符串是否为空
  * str.isEmpty() 返回值为boolean类型;
  * String str1;  不能用
  * String str2 = null;   空指针异常
  * String str3 = ""; 返回值为空
  * String str4 = "  "; 不为空
* 截取字符串
  * str.substring(beginindex,endindex) 表示从字符串的beginindex位置开始到endindex-1为止的字符串,也可以只填beginindex
* 大小写转换
  * str.toLowerCase() 把字符串中的英文都转换为小写字母
  * str.toUpperCase() 把字符串中所有的英文都转换为小写字母
* ==去掉字符串的首尾所有空格==
  * str.trim(); ==注意:中间的不能去掉==
* ==str.getBytes(); 把字符串转换为字节数组==
* ==str.toCharArray(); 把字符串转换为字符数组==
* ==str.split(String regex) 根据正则表达式(可以理解为根据某个字符或字符串)把字符串分割为字符串的数组==
* ==重点:str.equals(str1) 判断两个字符串是否一样,返回boolean类型的值==
* ==str.equalsIgnoreCase(str1) 忽略大小写判断两个字符串是否相等,比如判断验证码==
## ==正则表达式==
* ==判断字符串是否满足某个规则==
* 点(.)在正则表达式中代表任意字符
## 方法的重载 Overload
* 在同一个类中存在多个方法,这几个方法满足以下规则
* 1 方法名相同
* 2 参数列表不一样
  * 参数类型 个数 顺序不一样
* 3 再调用的时候可以明确区分调用的是哪一个方法即可
