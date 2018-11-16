## html基本结构
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
```
- 第一行为DOCTYPE,不区分大小写,表示文档类型,用来规定该html文件可以使用哪些标签
- lang属性用来标识语言类型,cn代表中文,en代表英文
- 根标签为html
- head表示头部内容,一般用来设置html文档的一些配置
- body标签用来显示html文档的内容
## html常用标签
1. h1~h6: 标题标签
2. p: 段落标签
3. em: 斜体标签
4. strong: 加粗
5. br: 换行
6. hr: 分割线
7. span: 区域标签,一般和css一起使用,给span区域内的标签加样式
8. img: 图片标签
    * alt属性: 表示图片加载失败时,显示的内容
    * title属性: 表示鼠标移到图片上时显示的信息
    * src: 表示图片的地址
9. a标签: 链接标签
    * href属性: 表示链接名字
    * tatget属性: 表示是否在新的页面打开链接内容
    * a标签常用功能:
      * 打开某个网站
      * 设置锚点:根据某个标签的id属性,跳转到那个标签的位置
      * 打开某个应用:根据固定的协议打开应用,如发送邮件等等
      * 也可以在a标签中放入图片标签,通过点击图片来访问a标签的地址
## 列表标签
### 有序列表 ol + li
```
<ol>
    <li>吉林</li>
    <!--列表中套列表,列表中type属性表示列表按什么排序,i表示按罗马数字排-->
        <ol type="A">
            <li>吉林</li>
            <li>长春</li>
        </ol>
    <li>辽宁</li>
        <ol>
            <li>鞍山</li>
            <li>鞍山</li>
            <li>鞍山</li>
        </ol>
    <li>沈阳</li>
```
### 无序列表 ul + li
```
<ul>
    <li>水果</li>
    <li>零食</li>
    <li>蔬菜</li>
</ul>
```
### 自定义列表 dl
```
 <!--dl表示自定义列表-->
    <!--dt相当于有序列表中的type属性的值什么排序-->
    <!--dt相当于有序列表中的值-->
    <dl>
        <dt>城市</dt>
        <dd>大连</dd>
    </dl>
```
## 表格 table
```
<!--border表格数边框属性,50%表示当前浏览器的一般-->
<table border="1" width="50%">
    <!--caption标签表示表格的名字-->
    <caption>成绩表</caption>
    <!--thead表示表头-->
    <thead>
    <!--tr括起来的部分表示一行-->
    <!--th表示每一列,一个th表示一列-->
    <!--表头中的列用th,效果好看-->
    <tr>
        <th rowspan="2">学号</th><th rowspan="2">姓名</th><th colspan="3">分数</th><th rowspan="2">总分</th>
    </tr>
    <tr>
       <th>语文</th><th>数学</th><th>英语</th>
    </tr>
    </thead>
    <!--tbody表示表格的具体内容-->
    <tbody>
    <tr>
        <!--在td标签中单元格跨列colspan-->
        <!--在td标签中单元格夸行rowspan-->
        <td>1</td>
        <td>赵雷</td>
        <td>98</td>
        <td>88</td>
        <td>78</td>
        <td>266</td>
    </tr>
    </tbody>
```
## 表单 form
- 表单用来向服务器提交数据的
- 实际上就是用来发送http请求的
- action属性: 表示标签发送的数据的地址
- method属性: 表示请求的方法
- get请求方法:只能发送简单的键值对,可以在网址中看见请求的内容
- post方法:可以发送文件,在网址中看不见请求内容
### 表单常用标签 input
#### ==如何实现单选框和多选的功能==
1. 单选type属性值为radio
2. 多选type属性值为checkbox
3. ==单选或多选的提交内容是通过value属性值提交的==
4. ==想要实现单选或多选的功能, 它们的name属性值必须一样, 这样才能保证在同一组中==
- type属性: 用来标记输入框的类型
  * text: 用来输入内容的
  * password: 表示输入的是密码
  * submit: input中的提交按钮,value表示按钮的值
  * radio: 单选框
  * checked: 用来表示默认选中的内容
  * checkbox: 多选框
  * reset: 重置按钮
### label标签
- 它可以把单选框包起来,然后可以通过点击内容直接选中这个单选框的内容
```
<label>抽烟 <input type="checkbox" name="hobbies" value="1001"></label>
```
- 也可以通过label中的for属性,定位到单选框的id属性,功能和上面那种一样
```
<label for="input">密码:</label>
<input id="input" type="password" name="password">
```
### fieldset标签表示用一个框把单选框括起来
```
<fieldset>

        <legend>选择性别</legend>
        <br>
        <label ><input type="radio" name="gender" value="1" checked>男</label>
        <label><input type="radio" name="gender" value="2">女</label>
    </fieldset>
```
### 下拉列表select
#### 下拉列表中的内容使用option标签表示
- option中的selected属性表示默认选中该标签
### 文本域 textarea
- 就是一个大的文本框
```
"cols 和rows表示文本域的大小";
<textarea name="info" cols="30" rows="10"></textarea>
```
