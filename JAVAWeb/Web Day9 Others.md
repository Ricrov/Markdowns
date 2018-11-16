# Session & Cookie
![image](https://ws3.sinaimg.cn/large/0069RVTdly1fuqmt166b5j31kw0smjw1.jpg)

![image](https://ws2.sinaimg.cn/large/0069RVTdly1fuqmtb8dalj30xw0t0whc.jpg)

## HTTP
### 特点:
1. 简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。

2. 灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。

3. 无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。

4. 无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。
5. 支持B/S及C/S模式。
### URL
- 统一资源定位符
### HTTP值请求消息Request
#### 它包括
1. 请求行: 请求行，用来说明请求类型,要访问的资源以及所使用的HTTP版本.
2. 请求头部: 请求头部，紧接着请求行（即第一行）之后的部分，用来说明服务器要使用的附加信息
3. : 空行，请求头部后面的空行是必须的
4. 请求数据: 请求数据也叫主体，可以添加任意的其他数据。
### HTTP值响应消息Response
### 它包括
1. 状态行: 状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成
2. 消息报头: 消息报头，用来说明客户端要使用的一些附加信息
3. 空行: 空行，消息报头后面的空行是必须的
4. 响应正文: 响应正文，服务器返回给客户端的文本信息

## HTTP请求
### 发送HTTP请求的步骤
1. 请求地址
2. 指定请求方法
    * 常用的方法有
    * GET: 一般用来向服务器请求数据
    * POST: 一般用来向服务器发送数据
    * HEAD: 用来向服务器获取数据信息
3. 设置请求头
4. 设置请求体
5. 发送请求
6. 处理响应
### 使用OkHTTP请求框架
```java
OkHttpClient client = new OkHttpClient.Builder().build();
        "获取请求";
        Request request = new Request.Builder()
                .url(url) // 1.
                .get() // 2.
                //.headers() 3
                .build();
        "发送请求, 获取相应";
        Response resp = client.newCall(request).execute();

        "处理相应";
        InputStream in = resp.body().byteStream();
        "获取文件大小"
        long size = resp.body().contentLength();

        FileOutputStream out = new FileOutputStream("lufei.jpg");
        byte[] bytes = new byte[1024];
        double currentSize = 0;
        int length = -1;
        while ((length = in.read(bytes)) != -1) {
            out.write(bytes,0,length);
            currentSize+=length;
            System.out.println("进度: " + (currentSize/size * 100) + "%");
        }
        out.close();
        in.close();
```
