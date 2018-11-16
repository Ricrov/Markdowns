### 什么是JavaScript
1. 它是一种弱类型的语言, 声明变量的时候不用指定类型
2. 它是解释型语言, 不需要编译, 给浏览器就能运行
3. 它是动态类型的语言: 就是变量的数据类型可以随意变化
4. 在html文档中script标签中的内容就是JavaScript代码
5. JavaScript中的主角: 万物皆函数
6. 每条语句可以不用分号结尾
7. 字符串可以使用单引号表示
### JS中的数据类型
1. number: 所有的数字
2. string: 字符串类型
3. boolean: 布尔类型
4. object: null属于object类型
### JS中常用关键字
1. var: 用来声明变量的
2. undefined: 用来定义某个变量没有值或没有类型
3. NaN: not a number, 不是一个数字
### ==typeof 用来判断某个对象的类型==
### JS中判断两个值是否相等
1. 两个等号: 判断时,会忽略数据类型
2. 三个等号: 判断时,也会判断数据类型
### JS中的分支
#### if分支
1. 条件为number类型时: 只要不是0或NaN都是true
2. 条件为string类型时: 只要不是空串都是true
3. 条件为null时: 表示false
4. 只要条件不正常都是false
#### switch分支
1. 是根据三个等号判断case条件的
### 向浏览器中写入数据
1. document.write('内容')
### JS中的数组
1. var arr = [1,2,a,true]
2. arr.pop(): 栈的结构, 弹出一个末尾的元素
3. arr.push(): 向栈中压入一个元素,添加在数组的末尾
4. arr.join('-'):把数组中的相邻的元素使用-连接成一个字符串
### JS中的函数
1. 第一种声明方式
```
funtion fun() {}
```
2. 第二种声明方式
```
"匿名函数";
 var add = funtion() {}
```
3. 第三种声明方式
```
"箭头函数,类似于java中的lambda表达式";
var add = ()=>{}
```
#### 函数的调用
1. 在调用函数的时候, 参数可以填任意数量,但可能没有意义
2. 可以把函数名字赋值给一个变量,那么该变量就称为了一个该函数
```
"变量g就相当于add函数了";
var g = add;
```
### JS中的对象
1. var p = {};
2. 可以动态的给对象赋值属性和方法
```
p.name = "小鱼";
p.age = 27;
p.say = ()=>{console.log('我叫MT')}
```
### JS中的点击事件
1. 第一种实现方式
```
"在标签中添加onclick属性";
<button id="btn" onclick="onBtn1Click()">点我1号</button>
```
2. 第二种实现方式
```
"使用js获取到某个需要添加事件的元素";
var btn = document.getElementById('id');
"给元素添加事件";
btn.onclick = function() {}
```
### JS中的焦点事件
1. 获取要添加焦点事件的元素
```
"获得焦点事件元素";
var focus = document.getElementById('id');
"给元素添加焦点事件";
focus.onfocus = function() {}
"移除焦点事件";
focus.onblur = function() {}
"on开头的方法名一般表示回调的方法";
"这样的方法不是由自己调用的,是触发该方法的调用";
"实际上这个方法是由系统调用的";
```
### 获取某个标签下的所有子元素
```
document.getElementById('id').children
```
### 获取某个标签中的值
```
var p = document.getElementById('id');
"获取标签中的文本";
p.innerText;
"在某个标签内部设置标签";
p.innerHTML;
"获取标签中的属性值";
p.value;
```
### 添加删除元素
1. 创建一个标签
```
"创建li标签";
var li = document.createElement('li');
"给ul标签添加li标签";
ul.appendChild(li);
"移除某个标签中的子标签";
ul.removeChild(li);
```
### JS中声明变量的三种形式
1. var
2. let: 使用方式和var一样
3. const: 声明时必须先给定初始值, 相当于声明了一个常量, 不可以更改
