# 反射

#### 反射方法1
根据对象推出类型, 调用方法

```Java
public void test1(Object obj) {
	System.out.println(obj);
	// 通过对象获取类
	// 使用 obj.getClass(), obj 的数据类型为 Object
	// 即 Object 类拥有方法 getClass, 方法的功能是通过对象获取到类
	Class clazz = obj.getClass();
	System.out.println(clazz);
	// 判断对象是否属于某个类
	if (obj instanceof Dog) {
		((Dog) obj).bark();
	}
}
```
#### 反射方法2
实现由传入的字符串, 创建相应的对象, 并调用方法

```Java
public void test2(String str) throws Exception {
	// Class 类的方法: forName, 功能是根据传入的字符串得到相应的对象, 并调用方法
	// 传入的字符串必须是完整的类名 + 包名的格式, 才能查找到相应的类
	Class clazz = Class.forName(str);
	System.out.println(clazz);

	// Class 类方法, 根据类型查找构造方法
	Constructor con = clazz.getConstructor();
	System.out.println(con);

	// Constructor 类的方法 newInstance, 根据构造方法实例化对象
	Object obj = con.newInstance();
	System.out.println(obj);
	if (obj instanceof Dog) {
		((Dog) obj).bark();
	}
}
```

#### @Test 注解
表示使用 junit 测试单元, 作为测试的入口
※ @Test 标注的函数不能有参数

```Java
@Test
public void shouldAnswerWithTrue() {
	Dog dog = new Dog();
	this.test1(dog);

	String str = "com.ric.spring.jav.Dog";
	try {
		this.test2(str);
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```
