# XML解析

## XML文件

1. 什么是XML文件

    - 可扩展标记语言(Extensible Markup Language);
    - ==设计宗旨是传输数据, 而非显示数据;==
    - ==没有预定义标签, 需要自定义;==
    - ==纯文本文件;==

2. 为什么需要使用XML

    - 可以用来传输数据;
    - 以后学习的Spring框架/maven, 配置文件多数都是XML格式;
    - 为以后学习的HTML打下基础;
    - 学习解析, 方便学习以后的JS;

3. 还有哪些数据格式的作用类似于XML

    - JSON, 目前绝大多数都用来传输数据. 少量的框架使用JSON格式的数据作为配置文件, 例如前端框架Angular2;
    - properties, 基本上都用作配置文件,里面一般存放键值对
    - yaml或yml, 比较新的一种文件格式, 简化properties, 用作配置文件.
    - 要不你就自定义

4. ==XML格式文件的要点==

    - ==第一行必须为文档声明==
    - ==必须有根元素==
    - ==元素的标签是成对出现的, 必须有关闭标签==
    - 元素的标签对大小写敏感
    - 元素之间必须正确的嵌套
    - ==文档中的空格会被保留==
    - ==元素的属性值必须加`""`==
    - 在标签里写的叫属性
    - 文档的注释使用`<!-- 注释 -->`形式, ==注释不能写在属性中==

5. 特殊符号的引用,转义

    | XML代码 | 需替换的符号 |
    | :-: | :-: |
    | `&lt;` | < |
    | `&gt;` | > |
    | `&amp;` | & |
    | `&apos;` | ' |
    | `&quot;` | " |

5. XML文件示例

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <albumList>
        <album name="Jay">
            <music>
                <name>斗牛</name>
                <duration>100</duration>
            </music>
            <music>
                <name>娘子</name>
                <duration>188</duration>
            </music>
        </album>
        <album name="七里香">
            <music>
                <name>搁浅</name>
                <duration>200</duration>
            </music>
            <music>
                <name>借口</name>
                <duration>268</duration>
            </music>
        </album>
    </albumList>
    ```
## 解析
- ==在特定格式的字符串中提取需要的数据==

## dom4j常用API
- dom for java

1. ==获取Document对象的方式==

    - 读取某个XML文件, 或读取某个数据流

        ```java
            SAXReader reader = new SAXReader();
            Document doc = reader.read(new File("file-name.xml"));
        ```

    - 将字符串解析读取为Document对象

        ```java
            Document doc = DocumentHelper.parseText(String xmlString);
        ```

    - 创建一个空的Document对象

        > 该方式适用于一点一点生成一个XML文档

        ```java
            Document doc = DocumentHelper.createDocument();
        ```

2. 常用类

    > 神奇的`略`

3. 常用方法

    > 你猜

4. 将Document对象内容输出到XML文件中

    > 太简单了
