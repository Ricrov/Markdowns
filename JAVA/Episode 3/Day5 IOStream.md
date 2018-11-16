# 字符输出流
# Writer
- 所有字符输出流的抽象父类
### 它的实现类
#### FileWriter
- 它是字符输出流的其中一个子类,文件输出流
- 可以进行文件的写操作
- 把声明数据流的部分声明再try-catch外面,赋值部分声明在里面
```
    try {
            // 第二个参数表示拼接,代表在原来的基础上继续写入数据,并不会覆盖以前的数据,如果为false就会覆盖以前的数据
            writer = new FileWriter("Text.txt",true);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```
- 数据流使用完之后必须得关闭
- new FileWriter(参数1,参数2)
- 参数1:表示要写入的文件路径
- 参数2:为true时,表示每次写入的时候,是在上次的基础上继续写,false时表示会覆盖上次的内容


# 字符输入流
# Reader
- 所有字符输入流的父类
## 它的一些实现类
### FileReader
- 文件字符输入流
- 用来读取文本文件中的内容
- 它的声明方式和字符输入流相同
#### ==它里面的read()方法使用方式==
- int read = reader.read():读取文件中的一个字符,返回值为int类型,int的值是根据ASCII码表把读到的内容转换为int值
- 读取文件的方式
```
    while (read != -1) {
               System.out.println((char) read);
                read = reader.read();
        }
```
- char[] buf = new char[8];
- int read = reader.read(buf):
  * 将输入流中的字符读取到数组中
  * 一次读取char[]数组长度的字符,放入char[]数组中,写一次,读一次
  * ==返回值代表读出字符的数量==
- 读取文件的方式一:
```
    int length = reader.read(buf)
    while(length != -1) {
                String str = new String(buf,0,length);
                System.out.println(str);
                length = reader.read(buf);
            }
```
- 读取文件的方式二:
```
    int length = -1;
    while((length = reader.read(buf))!=-1) {
                String str = new String(buf,0,length);
               System.out.println(str);
        }
```
#### 把字符数组转换成字符串
- String(char[],int offset, count)
  * offset:表示从数组的第几位开始转换成字符串
  * count:表示转换的数量

# 字节输入流
### InputStream是所有字节输入流的抽象父类
- 它以字节的形式读取文件中的内容
- 它在读文件的时候,读的是字节数组
- 它的声明方式同字符输入流
- 这种写法,不用写关闭数据流,它会在运行完之后自动关闭数据流
- 在数据流中一个汉字等于三个字符
```
     try (FileInputStream input = new FileInputStream("Music.txt")) {
            // 在数据流里一个汉字等于三个字节
            byte[] bytes = new byte[4];
//            int read = input.read(bytes);
//            String str = new String(bytes);
//            System.out.println(str);

            int length = -1;
            while ((length = input.read(bytes))!=-1) {
                String  str = new String(bytes,0,length);
                System.out.println(str);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
```
- 遍历方式:
```
    byte[] bytes = new byte[4];
    int length = -1;
            while ((length = input.read(bytes))!=-1) {
                String  str = new String(bytes,0,length);
                System.out.println(str);
    }
```

# 字节输出流
### OutputStream是所有字节输出流的抽象父类
- 它的声明方式和字符输出流一样
- 它的写入方法写的是字节数组
```
    FileOutputStream os = null;
        try {
            os = new FileOutputStream("Music.txt");
            String str = "锄禾日当午";
            byte[] bytes = str.getBytes();
            // write(byte[])参数为字节数组
            os.write(bytes);

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if(os!=null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```

# 带缓冲的流
### BufferedReader
- 带缓冲的字符输入流
### BufferedWriter
- 带缓冲的字符输出流
### BufferedInputStream
- 带缓冲的字节输入流
### BufferedOutputStream
- 带缓冲的字节输出流
#### ==BufferedReader==
- 带缓冲的字符输入流
- 特点:==在读文本文件的时候可以一次读一行==
#### ==BufferedWriter==
- 优点:==往硬盘写东西的时候,可以先存到一个缓冲区,存满之后一起写进去,效率高,对硬盘损伤小==
#### ==缓冲流使用方式==
- 使用方式:==把字符/字节流放入对应的带缓冲流的构造方法中==
```
  FileReader reader = new FileReader("Goods.txt");
  BufferedReader br = new BufferedReader(reader);
```
#### ==在使用缓冲写入数据时可以手动刷新==
- 手动冲一下,手动刷新,刷新完之后可以继续写,如果刷新了,那么缓冲区,就会被刷新了
- ==brt.flush()==
# ==数据流的转换==
### ==把字节流转换成字符流==
- 把字节流对象放入InputStreamReader的构造方法中就可以把字节流转换成字符流了
```
  InputStream inputStream = new FileInputStream("Goods.txt");
  InputStreamReader inputStreamReader = new InputStreamReader(inputStream)
```
# 使用字节流实现文件的复制
```
   // 源文件的路径
        String strPath = "/Users/dllo/Downloads/timg.jpeg";
        // 目的地文件
        String desPath = "/Users/dllo/Desktop/copy.jpeg";
        FileInputStream input = null;
        FileOutputStream output = null;
        try {
            input = new FileInputStream(strPath);
            output = new FileOutputStream(desPath);
            byte[] bytes = new byte[1024 * 1024];
            int length = -1;
            // 把输入流中的数据放入数组,并判断是否放完
            while((length = input.read(bytes))!=-1) {
                // 把输入流中的数据放入输出流中
                output.write(bytes,0,length);

            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (input != null) {
                try {
                    input.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (output != null) {
                try {
                    output.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```

# 其他的流
## 对象输入/输出流
- 对象输入/输出流的构造方法中填字节输入/输出流对象
- 它的作用是把对象写入到文件中
- 优点:可以完成对象的持久化
- ==如果想把对象写入到数据流中,必须对该对象序列化,让这个对象实现Serializable接口==
- 反序列化:就是从文件中读取该对象
- ==在把对象写入数据流时,如果不想把对象的某个属性写入到数据流,那么可以在该属性加上transient关键字,那么该属性就不会被写入数据流中==
- ==private transient double price==
```
  FileOutputStream fos = new FileOutputStream("Object.txt")
  ObjectOutputStream oos = new ObjectOutputStream(fos)
```
# 字节数组输入/输出流
```
  // 字节数组字节输出流
        ByteArrayOutputStream by = new ByteArrayOutputStream();
        // 把数据以字节数组的形式储存
        by.write("safsdfs".getBytes());
        byte[] bytes = by.toByteArray()
```
