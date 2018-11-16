## 什么是反射
#### 反射允许java程序在运行中可以动态的获取一个类的信息
#### 这些信息包括
* 类名
* 继承的父类
* 实现的接口
* 所有的属性
* 构造方法和普通方法等等
- 并且可以在运行中动态的调用这些属性和方法

## Class类
- 什么是 Class 类
- Class 实际上是类的类, Class 的对象保存了一个类的组成信息

### 获取Class对象的方式
- `类名.class`
- `对象.getClass()`
- `Class.forName("类的全名")` <类的全名为包名.类名>

### Class对象的常用方法
- String(返回值类型) `getName()` 获取类的全名
- String(返回值类型) `getSimpleName()` 获取类名
- Class(返回值类型) `getSuperClass()` 获取直接继承的父类
- Interface[](返回值类型) `getInterfaces()` 获取实现的所有接口
- Fields[](返回值类型) `getFields()` 获取自己及所有父类的 public 属性
- Fields[](返回值类型) `getDeclaredFields()` 获取自己的所有属性
- Field(返回值类型) `getField(String name)` 获取自己及父类的某个名为 `name` 的 `public` 属性
- Field(返回值类型) `getDeclaredField(String name)` 获取自己的某个名为 `name` 的属性
- Method[](返回值类型) `getMethods()` 获取自己及父类的 `public` 方法
- Method[](返回值类型) `getDeclaredMethods()` 获取自己的所有方法
- Method(返回值类型) `getMethod("方法名称",参数类型(根据需求填写数量))` 根据方法名和参数类型获取自己及父类的某个 `public` 方法
- Method(返回值类型) getDeclaredMethod("方法名",参数类型...):根据方法名及参数类型获取自己的某个方法
- int(返回值类型)getModifiers():获取修饰符
- Object.newInstance():创建一个对象
- 构造方法相关的方法:自己总结
## Modifier 修饰符类
- 常用方法:静态方法
  * static String toString(int mod):可以把通过反射获取到的修饰符转换成能看懂的修饰符
## 属性类
- 保存了一个属性的所有信息
  * 比如修饰符
  * 数据类型
  * 属性名
  * 属性值
- 常用方法
- int getModifiers():获取修饰符
- Class getType():获取数据类型
- String getName():获取属性名
- Object get(Objet obj):获取该属性在Object中的值
- set(Object obj,Object value):设置该属性在obj对象中的值为value
- setAccessible(boolean flag):设置该属性是否可访问
## 方法类
- 保存了一个方法的所有信息
  * 包括修饰符
  * 返回值类型
  * 方法名
- 常用方法:
- invoke(Object obj,Object ... params):调用obj对象的该方法,填入的参数为params
```
invoke第一个参数为哪个对象调用的该方法,第二个为方法的参数,o1为返回值
Method eat = class3.getDeclaredMethod("eat", String.class);
Object o1 = eat.invoke(o, "小鸡炖蘑菇");
```
- int getModifiers():获取修饰符
- setAccessible(boolean flag):设置该方法是否可访问
### 如果方法的参数是基本数据类型,那么通过反射获取它的方法方式如下
```
class3.getDeclaredMethod("eat",String.class,int.class);
class3.getDeclaredMethod("eat",String.class,Integer.TYPE);
```
## ==反射可以破坏类的私有属性以及泛型==
## 获取类的加载器
```
Class<Person> aClass = Person.class;
获取类的加载器
aClass.getClassLoader()
```
## 通过反射创建对象的几种方式
### ==通过class对象创建==
- ==前提必须有默认的构造方法==
```
Object o = class3.newInstance();
```
### ==通过反射获取到的构造方法创建==
```
Constructor<Person> declaredConstructor = aClass.getDeclaredConstructor()
Person person = declaredConstructor.newInstance("小鱼人")
```
