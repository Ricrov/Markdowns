## 一. JQuery简介

1. 简介
    jQuery是一个JavaScript函数库, 它简化了原生的JavaScript对HTML DOM(文档对象模型)操作. 并且在里面封装了很多常用的函数

    它包含以下功能:

    - HTML 元素选取
    - HTML 元素操作
    - HTML DOM 遍历和修改
    - HTML 事件函数
    - CSS 操作
    - 特效和动画
    - 异步网络请求

2. 版本介绍

    - 1.x 兼容ie678, 使用最为广泛的, 功能不再新增。因此一般项目来说, 使用1.x版本就可以了, 最终版本：1.12.4
    - 2.x 不兼容ie678, 官方只做BUG维护, 功能不再新增. 如果不考虑兼容低版本的浏览器可以使用2.x, 最终版本：2.2.4
    - 3.x 不兼容ie678，只支持最新的浏览器。除非特殊要求，一般不会使用3.x版本的，很多老的jQuery插件不支持这个版本。目前该版本是官方主要更新维护的版本。目前最新版本为3.3.1

## 二. JQuery安装

1. 下载JQuery

    可以在[JQuery官网](https://code.jquery.com/)下载不同的版本. 之后使用外部引用JS的方式将JQuery引入到html中.
```
"使用JS方式将JQuery引入到HTML中";
<script src="../js/jquery-2.2.4.min.js"></script>
```

2. 使用CDN服务器

    CND(Content Delivery Network) 内容分发网络, 简单来说是一个存储数据的服务器. 我们可以在html中直接引入CND服务器的JQuery代码, 有如下好处:

    - 减少自己服务器的压力
    - 用户在加载JQuery时, 会优先选择离自己最近的服务器, 可以增加加载的速度.
    - 用户在访问其他的网站如果已经加载过JQuery, 那么加载我们的JQuery时可以直接使用浏览器的缓存, 不必二次加载.

    CDN服务器在全球有很多, 例如[谷歌](https://developers.google.com/speed/libraries/devguide#jquery)/[微软](https://docs.microsoft.com/en-us/aspnet/ajax/cdn/overview#jQuery_Releases_on_the_CDN_0)/[CDNJS](https://cdnjs.com/libraries/jquery/)/[jsDelivr](https://www.jsdelivr.com/package/npm/jquery). 但是由于众所周知的原因, 访问国外的这些CDN服务器总不是那么稳定, 所以我们可以选择国内的CDN服务器.

    下面列出常用的国内的CDN服务器:

    - **百度**: [http://cdn.code.baidu.com/](http://cdn.code.baidu.com/) 进去之后点击JQuery图片即可选择不同版本.
    - **BootCDN**: [https://www.bootcdn.cn/](https://www.bootcdn.cn/jquery/)
    - **又拍云**: [http://jscdn.upai.com/](http://jscdn.upai.com/)

## 三. JQuery功能介绍

### 1. 语法

JQuery是封装的JavaScript, 所以同样可以执行JS对html的一些常用操作. 整体思路与JS一样, 先根据某个规则获取到某些标签, 之后对标签可以进行一些操作.

使用JQuery, 基本语法如下:

**$(选择器).操作()**

- **$:** 美元符表示调用JQuery库
- **选择器:** 学过的css选择器, 根据选择器即可找到对应的标签.
- **操作:** 表示一些常用方法

例如:

```JQuery
    $(this).hide(); // 表示隐藏当前元素
    $('span').hide(); // 表示隐藏所有的 span 元素
    $('span.error').hide(); // 隐藏所有 class="error" 的 span 元素
    $('#acctError').show(); // 显示 id="acctError" 的元素
```

### 2. 事件

#### 2.1 文档就绪事件

为了防止文档在完全加载(就绪)之前运行jQuery代码, 即应该在 DOM 加载完成后才可以对 DOM 进行操作, 所以JQuery提供了一个文档就绪事件. 也就是说, 在今后的`script`标签中, 尽量不要直接写JS代码, 应该将直接运行的代码写入到`文档就绪事件`中.

事件语法如下:

1. 第1种写法:

    ```
    <script>
        $(document).ready(function(){
            // 开始写 JS 代码...
        });
    </script>
    ```

2. 第2种写法:

    ```
    <script>
        $(function(){
            // 开始写 JS 代码...
            // 当然这种写法也可以使用 箭头函数 完成
        });
    </script>
    ```

3. 第3种写法, 使用默认的JavaScript实现:

    ```
    window.onload = function () {
        // 开始写 JS 代码...
    }
    ```

#### 2.2 点击事件

例如, 在 html 中有 id 为 btn1 和 btn2 的两个 button, 我们需要给它们分别添加单击和双击事件, 那么代码如下:

```
$('#btn1').click(function() {
    // 单击事件函数
});

$('#btn2').dblclick(function(){
    // 双击事件函数
});

// 如果代码像下面这么写, 就代表使用代码直接调用这个按钮的点击事件函数.
// 实际上这也是JQuery设计的一个思想: 一个同名的函数, 如果不填参数那么就代表获取值, 填参数就代表设置值.
// 这个技巧在后面也会接触到.
$('#btn1').click();
```

#### 2.3 焦点事件

写法与上面的`点击事件`类似, 只不过调用的函数变成了`focus()`和`blur()`. 名字与 JavaScript 中的一致.

#### 2.4 鼠标事件
```
"切换某个属性的显示和隐藏状态:";
"1. 使用toggle()函数";
"2. 使用boolean类型变量加判断";
$('#btn').click(function () {
    // toggle()方法: 切换点击状态, 就是显示和消失来回切换
    $('#content').toggle();
        });
```

鼠标事件分为几种, 写法都与上面一样, 我们只需要知道不同的事件对应不同的函数名字即可.

细则如下:

- 鼠标进入某个元素: mouseenter()
- 鼠标移出某个元素: mouseleave()
- 鼠标按下事件: mousedown()
- 鼠标抬起事件: mouseup()
- 鼠标悬浮事件: hover(enter, leave),需要填两个匿名函数, 第一个表示鼠标进入事件, 第二个表示鼠标移出事件;相当于集成了 mouseenter 和 mouseleave 两个事件.

### 3. DOM 操作

#### 3.1 获取和更改内容

获取一个元素中的内容, 常用的函数有三个, 例子如下:

假如存在一段 html 代码

    ```
    <body>
    <input id="input" value="root123"><br>
    <p id="test">易透 <b>达春绿</b>.</p>
    </body>
    ```

分别执行下面的操作后获取的数据是不同的

- `$('#input').val();`: 获取 id 为 input 元素的 value 属性的值. 一般用此种方式获取输入框中的文本内容.
- `$('#test').text();`: 会获取 p 标签中的纯文本信息, 也就是会去掉 p 元素里面的所有标签. 结果为 `易透 达春绿.`.
- `$('#test').html();`: 会获取 p 标签内的所有文本, 会保留 p 元素里面的各个标签. 结果为 `易透 <b>达春绿</b>.`.

**注意**, 这三个函数是直接调用的, 参数都没有填写, 表示获取对应的值. 如果在函数中都填入字符串参数, 那么就代表给对应的位置进行赋值操作.

#### 3.2 获取和更改属性

常见的用法有如下三种:

- $(选择器).attr(属性名), 表示获取某个元素的某个属性的值.
- $(选择器).attr(属性名, 属性值), 表示为某个元素的某个属性赋值.
- $(选择器).attr({属性名1: 属性值1, 属性名2: 属性值2}), 同时为某个元素的多个属性赋值, js中的key可以是一个单词或者字符串都可以, 最好都用字符串表示
- 设置多个属性时,需要使用对象, 在对象中添加属性

#### 3.3 添加元素

想要添加元素我们需要知道两点: 第一点是使用JQuery如何创建一个元素; 第二点是有哪些添加元素的方法. 这两点都知道之后我们就可以根据不同的需求调用不同的方法了.

##### 3.3.1 如何创建一个元素

    ```
    // 1. 使用原生的JavaScript, 也就是通过 document 对象来创建元素
    var p1 = document.createElement('p');
    p1.innerText = '创建的第一个 p 标签';
    // 2. 使用JQuery创建一个标签, 并设置内容
    var p2 = $('<p></p>').text('使用JQ创建第二个 p 标签');
    // 3. 纯字符串创建
    var p3 = '<p>使用字符串生写一个标签</p>';
    ```

##### 3.3.2 JQ添加元素的函数

下面介绍的四个函数都可以添加元素, 当然添加元素的位置是不一样的.

这几个函数有一个共同点: 调用函数时都可以填入多个参数, 会按照顺序依次添加.

- append(元素), 在被选元素的内部结尾处添加元素
- prepend(元素), 在被选元素的内部开头插入元素
- after(元素), 在被选元素的后面添加元素, 与原来的被选元素是同一个级别
- before(元素), 在被选元素的前面添加元素, 与原来的被选元素是同一个级别

#### 3.4 删除元素

- remove(), 删除被选元素及其子元素.
- empty(), 删除被选元素的子元素.

### 4. CSS操作

#### 4.1 对class操作

我们可以使用JQuery为所选元素添加或删除某个或某些class.

常用方法如下:

- addClass(), 为所选元素添加class
- removeClass(), 为所选元素移除class
- toggleClass(), 为所选元素来回的切换添加或删除某个class

#### 4.2 对css属性操作

我们可以使用 css() 函数对所选元素进行css的设置.

语法比较简单:

- css('background-color', '#ff0000'), 设置所选元素的背景色为红色
- css({'background-color': '#ff0000', 'color': '#ffffff'}), 同时设置多条style
- 添加多个css样式时, 需要使用对象, 在对象中添加属性

### 5. 遍历

常用方法:

- parent() 爹
- children() 所有孩子
- siblings() 所有同胞
- next() 同胞的下一个

## 四. JQuery特殊的选择器

- tr:odd, 选择所有index为奇数的tr
- tr:even, 选择所有index为偶数位的tr

## 五. 动画

略
