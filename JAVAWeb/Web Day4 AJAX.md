### 什么是Ajax
- Asynchronous JavaScript And XML
- 异步的JS和XML
### Ajax工作原理

"ajax是在HTML中使用JS异步来处理网络请求的";
"当用户在前端发送一个请求时";
"如果该请求使用了Ajax技术";
"那么本次请求就会额外开一个线程进行处理"
"就是让ajax和服务器进行数据的处理";
"然后再通过dom操作把处理的结果返回给用户";
"实现了在不刷新页面的情况下,对数据进行更新";
"也就是异步的进行数据请求和响应";

### JQuery如何使用GET请求向服务器发送表单数据(以键值对形式显示的数据)

  "done中的回调函数会在请求成功后执行";
  "done中的回调函数的第一个参数表示服务器发送过来的数据";
  "done中的回调函数的第二个参数表示请求是否成功";
  "如果success返回的是success那么就表示该请求发送成功了"
  ```js
  $.ajax(setting).done(function(resp, success){

  });
  "setting中的数据是一个对象, 里面以键值对储存数据";
  "setting保存的是请求相关的配置";
  "包括请求方法, URL, 请求头等等";
  "GET请求没有请求体";
  "如何定义setting对象";
  var setting = {
      url: 'http://localhost:8080/ajax',
      "method": 'get',
      "headers": {
      'Content-Type': ''},
  };
```

### JQuery如何使用POST请求向服务器发送表单数据(以键值对形式显示的数据)
```js
"post请求需要有请求体";
"发送的数据放在请求体中";
"data属性就表示请求体, 它的值就表示发送的数据";
$('#btn2').click(function () {
    var setting = {
        "url": 'http://localhost:8080/ajax',
        "method": 'post',
        "headers": {
        "该请求头表示以表单的形式发送数据";
        'Content-Type': 'application/x-www-form-urlencoded'
                },
        "data":{
            username: "小鱼",
            password: "130420"
        }
  $.ajax(setting).done(function(resp, success){
      "这里对返回的结果进行处理";
  });
});
```

### JQuery如何使用POST请求向服务器发送Json数据

```js
$('#btn2').click(function () {
    "定义一个数据, 使用JSON.stringify()转成json格式";
    var data = {
        username: 'xiaoyu',
        password: 'my130420'
        }
    var setting = {
        url: 'http://localhost:8080/ajax',
        method: 'post',
        headers: {
        "该请求头表示以json的形式发送数据";
        'Content-Type': 'application/json'
                },
        "使用JSON.stringify()把数据转成Json形式发送给服务器";
        data:JSON.strigify(data);

$.ajax(setting).done(function(resp, success){
    "这里对返回的结果进行处理";

});
});
```

### 使用JQuery发送简单的get请求
```js
$('#btn3').click(function () {
    "只需要定义一个url即可";
    "然后通过$.get()方法发送get请求";
    let url = "http://localhost:8080/ajax";
    $.get(url,function(resp, success){
        "在这里对结果进行处理";
    });
});
```
### 使用JQuery发送简单的post请求
```js
$('#btn4').click(function () {
    "它和简单的get请求的区别就是需要额外定义一个请求体";
    "然后通过$.post()方法发送post请求";
    let url = "http://localhost:8080/ajax";
    let data = {
        id: 1,
        username: '李四',
        password: '男'
            };
    $.post(url,data,function(){
        "在这里对获取到的结果进行处理";
    });
})
```

### Java开发分层思想
- controller层: 一般用来处理前端传递过来的数据, 对传过来的参数进行合理化的校验
- service层: 是用来处理业务逻辑的
- dao层: 只用来查询数据的
- common层: 用来放通用类的
- bean层: 用来放实体类的

### 使用Ajax提交表单数据
- 默认情况下提交表单时, 页面会发生跳转, 并且在新的页面来接收请求的结果
```js
"文档就绪事件";
$(function () {
  "表单中的submit方法";
    $('form').submit(function (e) {
        "submit中的匿名函数的参数表示表单";
        "参数的preventDefault()方法在阻止表单的默认提交方式";
        e.preventDefault();
        "使用ajax发送表单数据"
        "serialize() 该函数就会把表单中的数据转换为page=xxx&count=xxx 的形式";
        $.get('game',$(this).serialize(), function (resp) {
        "对服务端返回的数据进行处理";
        });
    });
});
```

### 使用Ajax提交a标签的请求
```js
$('a').click(function (e) {
        "e表示当前a标签";
        "其他用法和ajax提交表单数据一样";
        e.preventDefault();
        currentPage = Number($(this).text());
        params = {
            page: currentPage,
            count:count
        }
        $.get('game',params, showResult);
    });
```
