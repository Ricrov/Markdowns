## Collection 集合超类
- 它规定了Collection集合中常用的方法
- ==使用最多的情况是集合中放的是同一类型的对象==
- ==使用泛型来实现==
- 试用于存储对象
- 长度可变
- size() 方法:获取集合元素个数

## ArrayList
- 内部使用数组形式实现
- 优点:查找效率高(因为内存是连续的)
- 缺点:插入和删除速度慢(因为每插入或删除一个元素,它内部大部分元素都会发生移动,所以效率比较低)
- add():添加方法
- get():获取方法
- 当元素被取出之后,元素还在集合里面
- remove():移除方法,用来移除集合中的元素,移除之后,被移除的元素就没有了
- 如果数组中有相同对象,那么如果删除元素的话会从第一个开始删除
- 集合中元素被移除之后,被移除的空位会被后面的元素补位

## LinkedList
- 内部使用链表形式实现
- 用法同ArrayList
- 优点:插入和删除效率高(因为当插入和删除元素的时候,其他元素不会移动位置,所以效率比较高)
- 缺点:查找慢(因为查找某个元素的时候,只能从开始位置开始,所以效率比较低)

### List集合中的泛型类型是基本数据类型
- 那么移除方法里面的数字代表集合中所对应的下标

## 泛型
- 参数化类型
- 相当于类的参数
- 常用来规定集合中能存放的类型信息
- 比如List<String> list = new ArrayList<String>();
- 使用泛型的优点:
- ==便于存放与取出==

## 重载的特点
- 它会根据实际情况匹配最接近的那个方法

## 集合中常用方法
- contains():判断集合中是否包含某个对象
- add():向集合中添加方法
- get():获取集合中的元素
- remove():移除集合中的某个元素
- clear():清空集合
- indexOf(Object o):获取某个元素在集合中的位置
- isEmpty():判断集合是否为空
- toArray():把集合转换成数组
- removeAll(Collection v):移除集合中的另一个集合
- subList(start,end):==截取从start到end-1的元素的子集,子集的变化会影响原集合的结果==
- list.sort(new Comparator,但需要重写compare方法):集合排序方法

# 集合的遍历
- for循环
- 增强for循环
- for(集合中的泛型类型 g : 集合名称) {}
- 在增强for循环中不能更该集合的长度(如果更改了会抛出异常)
- ==使用迭代器== Iterator
- 使用集合的iterotor()方法

```java
Iterator <Integer> i = list.iterator();
while(i.hasNext()){
    Integer int = i.next();
}

list.forEach(new java.util.function.Consumer<Goods>() {
    @Override
    public void accept(Goods goods) {}
});
```

### 工具类Collections
- Collections.sort():对集合进行排序,但集合中的元素必须是可以比较大小的
- 如果没有可比性,那么需要让比较的元素实现Comparable接口,并重写compareTo方法,自定义比较规则

#### Comparator随时随地自定义比较规则
- 如果需要比较集合中的元素大小需要重写写Comparator中的compare()方法
  ```java
  Collections.sort(list, new Comparator<Goods>() {
      @Override
      public int compare(Goods o1, Goods o2) {
          return o1.getName().compareTo(o2.getName());
      }
  });
  ```

### java 8中的集合处理stream()
- 函数式编程
- list.stream()
- list.stream().filter():过滤集合中的元素
  ```java
  list.stream().filter(new Predicate<Goods>() {
      @Override
      public boolean test(Goods goods) {
          return false;
      }
  });
  ```
- 如果return false
- 如果return ture 把符合条件的过滤出来
- list.stream().sorted():对集合的排序
- list.stream().map():可以进行类型转换,把传进来的类型以别的类型转换出去
- list.stream().collect():转成集合


# 集合复习

- 可以把集合看做一个长度可变的数组
- Collection 集合的总接口, 里面规定了集合的常用方法
- Collections 是一个工具类, 里面封装了操作集合的常用方法
- List 里面的元素是有序的, 可以重复, 通过下标获取数据
- Set 里面的元素是无序的, 不可重复, 通过标签获取数据
- ArrayList 内部使用数组实现, 查找的效率较高
- LinkedList 内部使用链表实现, 增删的效率较高
- 常用方法


    ```
    1. 获取集合中元素的数量: size()
    2. 添加元素: add(E element), add(int index, E element)
    3. 获取元素: E get(int index) -- 根据下标获取, 有返回值
    4. 移除元素: E remove(int index) -- 根据下标移除有返回值, 能获取移除的元素
       移除元素: Boolean remove(Object o) -- 移除某个元素, 返回布尔类型
    5. 添加一个集合: addAll(Collection c)
    6. 把集合转换为数组: Object[] toArray()
       重载: T[] toArray(T[] array)
    7. 排序: sort() -- 适合集合元素已实现 compare 接口的集合, 即元素是可以比较大小的
       sort(Comparator comparator) -- 若集合中元素不能比较大小则需要自己填写规则

    ```

- 集合的排序


    ```
    前提: 集合中的元素必须有比较规则
    集合中元素的类需要实现 Comparable 接口或者额外指定比较规则(Comparator 比较器)
    排序方式: 可以使用 Collections.sort()方法排序, 也可以直接调用集合的 sort 方法排序
    ```

- 迭代器

    
    ```
    作用: 可以以一个统一的方式遍历不同的容器
    Iterable<T>: 可迭代的, 方法: Iterator<T> iterator(), 作用: 获取迭代器对象
    Iterator<T>: 迭代器, 两个方法: boolean hasNext(), T next().
    ```
