# XML
### 解析步骤
- 获取Document对象
```java
    SAXReader reader = new SAXReader();
    Document doc = reader.read(new File("album-list.xml"));
```
- 获取根元素
```java
    Element root = doc.getRootElement();
```
- 获取某个根元素的子元素的迭代器
```java
    Iterator<Element> iterator = root.elementIterator();
```
- 遍历该迭代器获取数据内容
### 将Document对象的xml数据写入到文件中的步骤
- 先创建document对象
- 通过DocumentHelper类中的createDocument()方法创建document对象
```java
    Document doc = DocumentHelper.createDocument();
```

- 添加根元素
```java
    Element persons = doc.addElement("persons");
```

- 在根元素下添加子元素
```java
    Element p1 = persons.addElement("person");
```

- 给某个元素添加属性
```java
    p1.addAttribute("id", "1001");
    第一个字符串表示属性的名字
    第二个字符串表示属性的值
```

- 给某个元素设置文本信息
```java
    p1.setText("小鱼");
```

- 创建一个输出流
```java
    Writer writer = new FileWriter("output.xml");
```

- 向输出流中写xml文本
```java
    doc.write(writer);
    writer.close();
```

# JSON解析
### JSON格式
- JSON格式的数据由两种数据类型组成:
- 第一种:{}括起来的部分叫做对象
- 第二种:[]括起来的部分叫做数组或集合
- 对象中的数据都是以key-value的形式保存的
- key:一定是字符串
- value:可以是任意类型,比如数组/集合/字符串/boolean等等

### 主流 JSON
`jackson`, `gson`, `fastJson`

### gson解析步骤
- 按照JSON文本内容把对应的类建立出来
- 创建gson对象
```java
    Gson gson = new Gson();
```

- 判断gson数据的最外层的数据类型,确定解析结果的数据类型
- 有两种情况,最外层是[]表示返回的是集合,{}表示返回的是对象
- 确定要返回的结果类型,使用java.lang.reflect包下的type
```java
    Type t = new TypeToken<List<Album>>(){}.getType();
```

- `TypeToken<List<Album>>(){}` 相当于创建了一个匿名子类对象

#### 解析返回的数据类型是集合的情况
```java
Reader r = new FileReader("album-list.json");
List<Album> albumList = gson.fromJson(r,t);
for(Album album : albumList) {
    System.out.println(album.getAlbumName());
    List<Music> musics = album.getMusicList();
    for(Music music : musics) {
        System.out.println(music.getName());
        System.out.println(music.getDuration());
    }
}
```

#### 解析最外层的是{}的情况
```java
Reader reader = new FileReader("album.json");
Album album = gson.fromJson(reader,Album.class);
System.out.println("--------------");
System.out.println(album.getAlbumName());

List<Music> musicList = album.getMusicList();

for(Music music : musicList) {
    System.out.println(music.getName());
    System.out.println(music.getDuration());
}
```

#### 将对象序列化成字符串,把对象转换成gson对象的数据
```java
String json = gson.toJson(album);
System.out.println(json);
```
