# Set集合框架
- Set里面的元素是不允许重复的
- Set里面的元素是无序的
- ==Set里面没有get方法,取元素是根据遍历取==
- Set的实现类:
- HashSet:常用
- TreeSet: 可以按顺序排列
- LinkedHashSet:它的遍历和输入顺序一致
- 它的遍历方式和list集合一样
# Map
- Map(key,value)
- Map<String,String> map = new HashMap<>();
- ==它里面的元素是无序的==
- 它有实现类:
  * HashMap(key,value) : 存储的数据是无序的,效率最高
  * TreeMap(key,value) : 存储的数据是根据key排序的,不是存放数据的顺序
  * LinkedHashMap(key,value) : 按照存放数据的顺序排序
- 它以键(key)-值(value)对的方式存储数据
- ==键唯一值不唯一==
- ==如果同一个key,对应多个value,那么会覆盖掉之前的value,存入的是最后一个value==
- ==一般情况下会把key声明为全局常量==
- 主要向Map中存储的数据是value,但是在存储的时候需要给value指定一个标签,这个标签就时key
- ==声明Map对象时,需要指定两个泛型,第一个泛型是key的类型,第二个泛型是value的类型==
## Map常用方法
- map.put(key,value):向集合中存储数据
- map.get(key):根据key取出对应的value
- map.remove(key):返回值是key所对应的value
- map.remove(key,value):返回值是boolean类型
# Map的遍历
- ==map.keySet(),方法的返回值为Set集合==
- ==map.keySet():取到Map所有key的集合,然后遍历该集合,根据集合中的元素,从Map中获取数据==
- map.values():直接取出所有值得集合,返回的是Collection<value的泛型> 集合,直接遍历该集合即可
- ==map.values()缺点:取不到key==
- ==map.entrySet()方法:是最常用的遍历Map的的方式==
```
Set<Map.Entry<String, String>> entries = fruit.entrySet();
for (Map.Entry<String, String> entry : entries) {
        String key = entry.getKey();
        String value = entry.getValue();
        System.out.println(key + " --> " + value);
        }
```
## ==在一个集合中放Map对象==
- List<Map<String, Object>> persons = new ArrayList<>()
- ==Object表示值可以是任意类型==
- ==在存放数据的时候,一层一层存放==
- ==取出集合中的元素时,也是一层层往外取==

```java
// 创建集合保存所有专辑
List<Map<String, Object>> albumList = new ArrayList<>();
// 创建第一个专辑
Map<String, Object> jayAlbum = new HashMap<>();
// 把专辑放到集合中
albumList.add(jayAlbum);
// 为专辑设置名字
jayAlbum.put("name", "Jay");
// 为专辑放入歌曲(歌曲的集合)
List<Map<String, String>> jMusics = new ArrayList<>();

jayAlbum.put("musics",jMusics);
Map<String,String> douNiu = new HashMap<>();
douNiu.put("musicname","斗牛");
douNiu.put("duration","100");
jMusics.add(douNiu);
Map<String,String> ffxdz = new HashMap<>();
ffxdz.put("musicname","反方向的钟");
ffxdz.put("duration","200");
jMusics.add(ffxdz);
Map<String,String> hsym = new HashMap<>();
hsym.put("musicname","黑色幽默");
hsym.put("duration","150");
jMusics.add(hsym);

// 第二张专辑
// 创建第二张专辑
Map<String,Object> bdkjAlbum = new HashMap<>();
albumList.add(bdkjAlbum);
bdkjAlbum.put("name","八度空间");
List<Map<String, String>> bdkjMusics = new ArrayList<>();
bdkjAlbum.put("musics",bdkjMusics);
Map<String, String> ah = new HashMap<>();
ah.put("musicname", "暗号");
ah.put("duration","234");
bdkjMusics.add(ah);
System.out.println(jayAlbum);
Map<String, Object> album0 = albumList.get(0);
List<Map<String,String>> musics = (List<Map<String, String>>) album0.get("musics");
Map<String, String> musics1 = musics.get(1);
String duration = musics1.get("duration");
System.out.println(duration);
```
