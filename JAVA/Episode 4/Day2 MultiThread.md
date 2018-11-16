# 进程和线程
- 进程中包含线程,一个进程至少包含一个线程
- 进程就是一个应用程序
- 进程之间内存空间是独立的
- 多个线程之间内存是共享的
## 线程的生命周期
- 新建(new)
- 就绪(runnable)
- 运行(running)
- 阻塞(block)
- 死亡(dead)
## 如何创建新的线程
- 在java中线程的类就是Thread
- 建立一个类继承Thread,那么新建的类就是一个线程
```
    public class MyThread extends Thread {
    // name是Thread的一个属性,表示该线程的名字
     public MyThread(String name) {
         super(name);
     }
     // 重写父类的run方法
    // 该方法中的代码就是运行在该子线程中的
     public void run() {
         super.run();
         try {
             // 线程休息时间
             Thread.sleep(1000);
         } catch (InterruptedException e) {
             e.printStackTrace();
         }
         System.out.println(super.getName());
     }
}
```
- ==多个线程之间是同时运行的==
- ==新建的线程必须冲写Thread中的run()方法==
- ==新建的线程可以通过构造方法给线程命名==
### 如何开启线程
- 调用线程对象的start()方法
- 开启线程之后系统会自动执行线程中的run()方法
- 线程开始之后会同时执行
## 第二种创建线程的方式
- 让一个类实现Runnable接口,并重写它的run()方法,然后通过Thread的构造方法,把新建的类传进去,那么就创建了一个线程
```
    public class MyRunnable implements Runnable{
    @Override
    public void run() {
        // 在这里写的代码运行在子线程里的
        // 获取当前的线程对象
        // 哪个线程调用了run()方法,那么thread就是该线程
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName());
    }
}
通过Thread的构造方法创建线程
public class RunnableDemo {
    public static void main(String[] args) {
        // 第二种开启线程的方式
        for (int i = 0; i < 5; i++) {
            MyRunnable myRunnable = new MyRunnable();
            Thread t = new Thread(myRunnable,"Thread-" + i);
            第二个参数表示新建的线程的名字
            t.start();
        }

        // 简写的方式1
        Thread t1 = new Thread(new MyRunnable(),"t");
        t1.start();
        // 简写方式2
        // 匿名对象开启线程
        new Thread(new MyRunnable(),"2").start();
        // 随便开个线程
        new Thread(() -> System.out.println(Thread.currentThread().getName())).start();

    }
}

```
## 线程之间的同步 synchronized
### 让线程安全的几种方式
- ==通过Collections工具类里的方法让线程变安全==
- data = Collections.synchronizedList(data)
- 直接使用线程安全的集合
  * Vector()是List集合的线程安全的实现类
  * Hashtable是Map集合的线程安全实现类
  * Map 也叫做字典
- ==使用同步代码块 synchronized==: 哪个地方需要使用同步就在哪使用
### 同步代码块的作用
- ==每次只能有一个线程访问同步代码块中的代码==
- 同步代码块有阻塞功能,它保证在一个线程访问它内容的时候,其他线程不能访问
- 同步代码块在线程中使用
- synchronized 有两种用法:
  * 当做同步代码块
  * 当做方法的修饰符
### synchronized 在方法中使用时
- synchronized()方法参数可以填任意参数,但必须保证是同一个对象,这样才能保证线程是安全的,这个参数被称为==同步锁==,==一般为静态常量==
- 如果用在成员方法中,那么它的参数就表示当前对象
```
    public synchronized void doSth() {
        synchronized (this) {
        index += 10;
        }
    }
```
- 如果用在静态方法中,那么它的参数就表示当前类
```
    public static synchronized void toDo() {
        synchronized (Waiter.class) {

        }
    }
```
## wait()和notify()的使用
- ==wait()等待:只能在同步代码块中使用,并且只能用锁对象调用==
```
    private void test() {
        System.out.println(getName() + "子线程开始干一些事");
        System.out.println(getName() + "干了一半");
        synchronized (lock) {
            try {
                System.out.println(getName() + "子线程开始等待");
                lock.wait();// 参数填0也表示一直等
                System.out.println(getName() + "子线程继续开始工作");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
```
- notify()通知:只能在其他同步代码块中使用,并且只有使用同一个锁对象调用的时候,等待的线程才可以继续工作
```
    synchronized (Waiter.lock) {
            Waiter.lock.notify();
        }
```
- nofity()的作用:它通知等待的线程可以继续工作
- notifyAll():通知所有线程继续工作
### 获取当前线程
- ==Thread t = Thread.currentThread();==
## 线程池
### ==线程池核心类 ThreadPoolExecutor==
#### ==线程池在创建的时候是没有线程的,只有当有任务放进去的时候才会创建线程==
### ==线程池常用方法==
- execute: 执行任务
- submit: 实际上也是执行任务,但可以返回结果
- shutdown: 关线程池
- shutdownNow: 让线程处于stop状态
- 存放线程的一个容器
- ==队列:是一种数据结构(容器):它里面的元素是先进先出的:有进口和出口==
- ==栈也是一种数据结构,它里面的元素是先进后出的:它的进出口只有一个==
### 阻塞队列
- 当队列中的元素全被取出之后,如果再取,那么就会发生阻塞现象,程序会在当前位置等待,直到有新元素加到队列中
- ==阻塞队列的总接口:BlockingQueue==
### ==在匿名内部类中,如果想使用局部变量,那么该变量必须是final的==
## 系统提供的线程池
- Executor:是线程池的最顶层接口
- Executors:是线程池相关的工具类
- 线程池中包括:
  * ==核心区==
  * ==工作区==
  * ==阻塞队列==
### ==线程池的工作顺序==
- 线程(任务)先存放在核心区
- 如果核心区满了,线程(任务)就被放在阻塞队列中
- 如果核心区和阻塞队列都满了,那么任务会被放在工作区
- 核心区的大小不会变,工作区会在存活时间结束之后消失
- 如果线程池全满了,那么会抛异常
- 放在核心区和工作区里的任务会先执行,阻塞队列中的元素会后执行
### 创建系统的线程池对象
- 1
```
    ExecutorService pool = new ThreadPoolExecutor(3,
                5,
                1,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<Runnable>(10));
```
- 第一个参数(3): int corePoolSize:表示核心线程数量
- 第二个参数(5): int maximumPoolSize表示最大工作线程数(核心区的线程和工作区线程之和)
- 第三个参数(1): long keepAliveTime表示工作区的线程存活时间
- 第四个参数:TimeUint表示存活时间单位
- 第五个参数:BlookingQueue<Runnable> :表示阻塞队列
- 2
- 通过Executors的方法创建系统线程池对象
```
    ExecutorService pool1 = Executors.newCachedThreadPool()
```
### volatile关键字
- 它常用来修饰多线程编程里的变量
- 那么对于volatile修饰的变量（共享变量）来说，在工作内存发生了变化后，必须要马上写到主内存中
- 而线程读取到是volatile修饰的变量时，必须去主内存中去获取最新的值，而不是读工作内存中主内存的副本，这就有效的保证了线程之间变量的可见性
### volatile特性
- 内存可见性，即线程A对volatile变量的修改，其他线程获取的volatile变量都是最新的
- 可以禁止指令重排序


## java中开启线程的几种方式
1. 最核心的就是调用Thread对象的start方法
2. 实现Runnable接口,但是实现Runnable也需要使用Thread类开启
3. 线程池也可以开启线程
4. FutureTask也可以开启线程
5. ==线程有join方法,当调用该方法时,其他线程只能等待,直到该线程完成任务,其他线程才可以继续执行任务==
## Future模式 它的泛型填的是子线程返回的结果类型
- ==future模式可以保证多线程同时工作的时候,各个线程之间是独立的,并且可以获取另一个线程的结果==
```
"通过单线程获取结果";
FutureTask<Integer> task = new FutureTask<>(new IceCallable(10));
System.out.println("二弟拿着10块钱去买雪糕了");
        new Thread(task).start();
       "看看剩了多少钱";
        Integer integer = task.get();
        System.out.println(integer);
```
```
ExecutorService pool = Executors.newCachedThreadPool();
    "通过线程池获取Future的结果";
    ExecutorService pool = Executors.newFixedThreadPool(3);
    "通过线程池的submit方法,也可以实现futrue模式";
    "它的构造方法中填Callable对象,";
    Future<Integer> submit = pool.submit(new IceCallable(10));
    "通过get方法获取到另一个线程的值";
    Integer integer1 = submit.get();
```
## ==线程中的join方法==
- 该方法有阻塞线程的功能
