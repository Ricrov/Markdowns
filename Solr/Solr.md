# Solr 搜索应用服务器
#### 下载 Solr
- `https://mirrors.tuna.tsinghua.edu.cn/apache/lucene/solr/7.5.0/solr-7.5.0.zip`

#### 配置环境变量
- `path/bin`

#### 运行 Solr
- `solr.cmd start`

#### 默认 url
- `localhost:8983`

#### 创建一个核心
`solr.cmd create -c 核心名`

#### 重启 solr
`solr.cmd restart -p 8983`

### 实现分词搜索
#### 使用内置的中文分词器

- 依赖包
   - 将 Solr 内置的依赖包 `C:\Program Files\web\solr-7.5.0\contrib\analysis-extras\lucene-libs\lucene-analyzers-smartcn-7.5.0.jar` 复制到 `C:\Program Files\web\solr-7.5.0\server\solr-webapp\webapp\WEB-INF\lib` 下
- `C:\Program Files\web\solr-7.5.0\server\solr\search\conf\managed-schema` 添加以下 xml 配置
 ```xml
<fieldType name="text_cn" class="solr.TextField">
  <analyzer class="org.apache.lucene.analysis.cn.smart.SmartChineseAnalyzer">
  </analyzer>
</fieldType>
 ```

- `managed-schema` 创建 field: `type` 即为上方配置的 `name`
 ```xml
<field name="title" type="text_cn" indexed="true" stored="true" required="true" multiValued="false" />
 ```

#### 使用第三方分词器
`ik-analyzer-solr7-7.x.jar`

##### 导入数据
- Step 1: 依赖包放入 `WEB-INF/lib` 下
  - mysql-connector-java-6.0.6.jar
  - solr-dataimporthandler-7.5.0.jar
  - solr-dataimporthandler-extras-7.5.0.jar
- Step 2: 将 `"C:\Program Files\web\solr-7.5.0\example\example-DIH\solr\db\conf\solrconfig.xml"` 配置文件中的以下代码↓↓↓↓
```xml
<requestHandler name="/dataimport" class="solr.DataImportHandler">
  <lst name="defaults">
    <str name="config">db-data-config.xml</str>
  </lst>
</requestHandler>
```
复制粘贴到`C:\Program Files\web\solr-7.5.0\server\solr\search\conf\solrconfig.xml` 中

- Step 3: 复制`db-data-config.xml` 到 `C:\Program Files\web\solr-7.5.0\server\solr\search\conf` 下并进行配置 (注意时区必须填)
  - 配置此 xml 文件
    ```xml
    <dataConfig>
        <dataSource driver="com.mysql.cj.jdbc.Driver" url="jdbc:mysql://localhost:3306/shoppingstore?serverTimezone=UTC" user="root" password="root" />
        <document>
            <entity name="item" query="select * from tb_item">
                <field column="item_id" name="itemId" />
                <field column="title" name="title" />
            </entity>
        </document>
    </dataConfig>
    ```

#### 测试
- search -> Dataimport -> Entity -> item -> Execute -> Refresh Status

- Query -> ExecuteQuery -> 可查出所有数据 (默认1-10)
- Query -> ExecuteQuery -> q -> `*:*` 可根据需要替换键值对进行查询 (例如 title:苹果手机)
- Documents -> Document Type -> XML -> `<delete><query>*:*</query></delete><commit/>
` -> Submit Document 可清除已经 index 的数据

## SpringBoot 整合 Solr
- SpringBoot 加入 solr 依赖 (隶属 NoSQL)

- application.properties
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/shoppingstore?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
# 加入核心 /search
spring.data.solr.host=http://127.0.0.1:8983/solr/search
```

- 实体类
  - @Field 注解: 需要加入 solr 的列
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Table(name = "tb_item")
@Entity
public class ProductEntity implements Serializable {
    @Id
    // solr 注解
    @Field
    private Long itemId;
    @Field
    private String title;
    @Field
    private String sellPoint;
    private Long price;
    private Integer num;
    private String barcode;
    private String image;
    private Long cid;
    private Integer status;
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
    private Date created;
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
    private Date updated;
}
```
- repository
```java
public interface ProductRepository extends JpaRepository<ProductEntity, Long> {}
```

- 测试类
```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    @Resource
    private SolrClient solrClient;

    @Resource
    private ProductRepository productRepository;

    @Test
    public void contextLoads() throws IOException, SolrServerException {

        List<ProductEntity> items = productRepository.findAll();
        // 相当于 DataImport
        solrClient.addBeans(items);
        // 必须 commit
        solrClient.commit();
    }
}
```
- 此时在 `http://localhost:8983/solr/#/search/query` Query 中已可查出数据

#### 若查出的数据类型不符
更改 `managed-schema` 中
搜索关键字 (比如"sellPoint"), 将 type="text_general" 更改为 "string"
