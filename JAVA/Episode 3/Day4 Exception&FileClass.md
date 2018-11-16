# 异常
- ==异常会导致程序的中断==
- ==异常也是类==
### 异常类型种类
- 算数异常
- 下标越界异常
  * 数组下标越界
  * 字符串越界
- 数字格式化异常
- 空指针异常: NullPointerException
- 类型转换异常
# java中的所有出错分为两类
### ==Error:错误, 它继承自Throwable==
- 通过一般的代码修改无法处理的
### ==Exception:异常, 它继承自Throwable==
- 异常时可以进行处理的
## Exception下面分为两类
### RuntimeException: 运行时异常
- 算数异常
- 下标越界异常
  * 数组下标越界
  * 字符串越界
- 数字格式化异常
- 空指针异常: NullPointerException
- 类型转换异常
### 其他异常:强制检查型异常
- ==必须用try-catch处理的异常==
- ==在编译期必须进行异常捕获==
- ==除RuntimeEeception异常之外的异常==
# ==Throwable异常最顶层父类==
NullPointerException nu = new NullPointerException("去你妹的空指针")
- nu.printStackTrace():在控制台打印该异常的日志
- nu.getMessage():获取异常信息
- ==异常的构造方法参数为异常信息==
 ```
  public Throwable(String message) {
        fillInStackTrace();
        detailMessage = message;
    }==异常中的构造方法,参数为日志信息==

```
## 异常的处理
- 基本语法
  ```
  try{
      可能抛出异常的代码
  }catch(){
      解决异常的代码
      可以有多个
  }finally{
      无论是否发生异常都会执行
      一般会在在finally代码块中执行一些释放资源的操
  }


  ```
- ==try-catch不能捕获error错误==
- ==catch块是对出现的异常进行弥补工作==
- 如果try代码块中没有异常,所有的catch都不会执行
- 如果try中的某一行代码出现异常,try中的该行以后的代码都不会执行
- 检查catch有没有捕获对应的异常,如果有则执行对应的catch代码块,try-catch代码块执行结束,如果没有,程序直接中断
- catch捕获异常的时候,按照先后顺序依次判断,符合多态规则
- 书写catch的时候要把子类写在前面(由小到大顺序书写)
- ==一般会在在finally代码块中执行一些释放资源的操作==
- ==在try-catch-finally结构中,try必须写,catch和finally至少需要有一个==
```
  // 异常的捕获
        // 异常处理
        try{
            // 可能会抛出异常的代码
            // 当捕获到异常时会直接跳转到catch块,不会执行出现异常之后的语句,当处理完异常之后try-catch直接结束,执行finally和之后的语句
            System.out.println("Start");
            System.out.println(5/0);
            System.out.println(o.toString());
            System.out.println("end");
        } catch (NullPointerException e) {
            // catch可以写多个
            // 解决该异常的代码
            //e.getMessage();
            // e.printStackTrace():打印异常日志
            System.out.println("出现了空指针");
        }finally {
            // 无论是否有异常都会执行该代码块中的代码
            System.out.println("我永远会被执行");

        }
```
## 抛出异常
### throw关键字
- 语法:throw后面跟上异常的对象
- ==常用在方法体中,手动抛出==
- ==常用于方法中对调用者传递的参数进行规则性的校验==
```
  private static int div(int x, int y) {
        // 该方法只能计算两个正整数
        if(x <= 0 || y <= 0){
            throw new RuntimeException("传入的参数必须为正数,您传入的x = " + x + ", y = " + y + "不合法");
        }
        return x / y;
    }
```
### throws关键字
- 强制检查型异常,在编译期必须进行异常捕获
- throws用在方法声明中抛出异常
- 如果在一个方法中不需要处理该异常的话,可以在方法声明时使用该关键字==将异常向上抛出==(向调用该方法的地方抛出),在哪里调用的该方法,就在哪里再进行捕获
- ==throws也遵循多态原则==
- ==抛出什么类型的异常,在捕获的时候必须捕获该异常的类型或其父类,不能捕获其子类==
```
    public void test(int age) throws IOException,RuntimeException,NullPointerException{
        if(age < 0){
            throw new IOException();
        }

    }
```
```
     // 方法中可能出现异常,但自己不想处理,可以使用throws抛出去,让调用方法的对象处理
    public void test(int age) throws IOException{
        if(age < 0){
            throw new IOException();
        }

    }
```
## ==自定义异常==
- 让自己想要定义的异常类型继承自系统的异常(RuntimeException等),然后通过构造方法,调用父类的构造方法实现
```
    public class NumberNotBeNegativeException extends RuntimeException {
    // 调用父类的构造方法
    // 自定义异常
    public NumberNotBeNegativeException(String message) {
        super(message);
    }
}
```
## 常见的异常
### RuntimeException
- IllegalArgumentException:非法参数异常


# 文件类
## 路径
- ==定位到某个文件的地址叫路径==
- 绝对路径:从硬盘根目录开始一直到某个文件的路径,在哪里用都好使
- /Users/dllo/Desktop/Myfistjava.java
- 相对路径:相对于当前路径的路径
- 相对路径:相对于某个文件夹定位文件的路径
## File文件类
### ==它是对文件进行各种操作的==
### 常用方法:
- file.getName():获取文件的名称
- file.getParent():获取文件的上层路径
- file.getPath():获取文件的路径
- file.exists():判断文件是否存在
- file.isFile():判断是否是文件
- file.isDirectory():判断是否为文件夹
- file.listFiles():列出该目录下的所有文件
- file.createNewFile():创建新文件,但需要捕获异常,如
```
    try {
            // 新建文件
            file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
```
- file.length():获取文件的长度,单位是字节
- file.mkdir():创建最后一级文件夹,但最后一级的前面级别必须存在,否则创建失败
- file.mkdirs():创建多级文件夹
```java
  String path = "/Users/dllo/Desktop/";
        // 可以定位到一个文件,也可以定位到一个文件夹
        File file = new File(path);
        // 获取文件的名称
        System.out.println(file.getName());
        // 获取该文件的上层目录路径
        System.out.println(file.getParent());
        // 获取该文件路径
        System.out.println(file.getPath());
        // 判断文件是否存在
        System.out.println(file.exists());
        // 判断是否是文件
        System.out.println(file.isFile());
        // 判断是否是文件件
        System.out.println(file.isDirectory());
        // 列出该目录下的所有文件
        System.out.println(file.listFiles());
        try {
            // 新建文件
            file.createNewFile();
        } catch (IOException e) {
            e.printStackTrace();
        }
        // 获取文件的长度(单位是字节)
        System.out.println(file.length());
        // 创建最后一级的文件夹
        System.out.println(file.mkdir());
        // 创建多级文件夹
        System.out.println(file.mkdirs());
        File desktop = new File("/Users/dllo/Desktop");
        // 获取桌面上的所有文件
        File[] files = desktop.listFiles();
        for(File f : files) {
            // 获取文件名称
            String name = f.getName();
            // 判断是不是文件
            boolean b = f.isFile();
            // 获取文件大小
            long length = f.length();
            System.out.println("文件名:" + name);
            System.out.println("是否是文件:" + b);
            System.out.println("文件大小:" + length / 1024 + "kb" );
        }

```
