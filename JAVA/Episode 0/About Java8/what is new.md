## java8 函数式接口
### lambda表达式的结果类型是函数式接口
1. 接口中只能存在一个必须实现的抽象方法
### 接口中可以有
1. 接口中只有一个必须被实现的抽象方法
2. @FunctionalInterface 它是用来检测某个接口是不是函数式接口
3. 接口中也可由有一些抽象的Objiect类的方法
4. ==接口中也可以有default修饰的方法,此方法可以有方法体==
```
@FunctionalInterface
public interface AddCalculate {
    int add(int x, int y);

    boolean equals(Object object);
    // default 不是访问权限
    default void fun() {
        System.out.println("我是计算加法的接口");
    }
}
```
## java8流式编程 stream
1. 集合可以使用流式编程
2. 数组也可以使用流式编程
### 把数组转成流式编程
#### 流式编程对原来的数据不会产生影响
```
int[] array = {8,2,6,7,4,3,1,5,6,7};
"把数组转成流 stream(流式编程)
 流式编程对原来的数据不会产生影响
 使用Arrays工具类把数组转成流式编程形式
Arrays.stream(array)
            .parallel() // 把流转成并发流(多线程)
            .filter(value -> value % 2 == 1)
            .average()
            .getAsDouble();

```
## 在不同的线程中,保证对象的独立性,使用 ThreadLocal类
```
"它的作用可以保证在某个线程中,独立的使用某个对象";
    private ThreadLocal<Random> local = new ThreadLocal<Random>(){
"该方法不能保证在哪个线程中被调用,使用ThreadLocal进行优化";
        @Override
        public Random initialValue() {
            return new Random();
        }
    };

```


## 如何跳出多重循环
### 在循环外面定义一个标签,然后break改标签即可
```
"跳出多重层循环";
"跳出switch和循环嵌套结构"
    PPP:
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 10; j++) {
            if(i + j ==15){
                break PPP;
                }
            }

        System.out.println("XXX");
        }

```
