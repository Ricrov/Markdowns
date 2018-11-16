# TCP协议编程
## Socket 套接字
- java中的Socket类可以直接操作TCP协议
### 创建服务端Socket步骤
- 创建ServerSocket对象,设置要监听的窗口
```
ServerSocket sever = new ServerSocket(7890)
```
- 通过accept()方法获取Socket对象
```
Socket socket = sever.accept();
```
- accept()方法的作用:如果监听到有客户端连接到端口了,就可以获取到连接过来的Socket对象
- accept()方法是一个阻塞方法
### 服务端数据的传输
- 先获取客户端发送过来的数据
- 通过socket.getInputStream()获取
```
InputStream in = socket.getInputStream();

```
- 然后可以对数据进行处理
- 如果想向客户端发送数据那么需要先获取到输出流
- 通过socket.getOutputStream()
```
 OutputStream out = socket.getOutputStream();

```
- 然后通过获取的输出流向客户端发送数据
## ==PrintStream和PrintWriter输出流==
- 通过它们的println()方法可以按行写入数据
- 它们的构造方法中填写字节输出流和字符输出流
- 如果用它们写入数据的话,写完之后需要flush()一下,才能正确的写入
### 创建客户端Socket步骤
- 先创建Socket对象,构造方法中填IP地址和端口号
```
Socket socket = new Socket("127.0.0.1", 7890);

```
### 客户端数据传输步骤
- 先获取输出流然后对流进行写操作
```
OutputStream out = socket.getOutputStream();

```
- 如果想获取服务端传输过来的数据,那么可以获取输入流
```
 InputStream in = socket.getInputStream();

```
- 然后可以对输入流进行操作
# UDP协议编程
## UDP服务端
- 先创建DatagramSocket对象,它的构造方法中填端口号
```
DatagramSocket ds = new DatagramSocket(6789);

```
- 然后再建立数据包对象(DatagramPacket)
- 数据包对象的构造方法中填写字节数组
```
 byte[] bytes = new byte[1024];
 DatagramPacket dp = new DatagramPacket(bytes,bytes.length);

```
- UDP编程是把数据存到数据包中,然后读取数据包中的内容
- UDP编程通过recieve方法把数据存储在数据包中的
- 这个recieve()方法有阻塞功能
```
 ds.receive(dp);

```
- 通过数据包对象读取字节数组数据
- 然后把字节数组转换成字符串输出
```
byte[] data = dp.getData();
String str = new String(data);

```
## UDP客户端
- 先创建DatagramSocket对象
```
DatagramSocket ds = new DatagramSocket();

```
- 客户端的ip地址和端口号是填在数据包对象的构造方法中的
- 在创建数据包对象之前, 需要创建一个用来发送数据的字节数组,然后再创建一个IP地址,最后再创建数据包
```
 byte[] bytes = "udp,你好啊".getBytes();
 // 获取ip地址
 InetAddress addr = InetAddress.getByName("172.16.31.255");
 // 构建数据包
 DatagramPacket dp = new DatagramPacket(bytes,bytes.length,addr,6789);

```
- 最后通过DatagramSocket对象的send()方法把数据包中的内容发送出去
```
 ds.send(dp);

```
