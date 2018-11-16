- 配置 Maven
- 克隆 FastDFS `git clone https://github.com/happyfish100/fastdfs-client-java.git`
- 在其根目录下使用 `mvn clean install` 命令
- 此时将在源码文件夹中自动生成 target 文件夹, 并生成 jar 包

使用 IO 流写入文件上传
```java
public class FastDFSTest {

    @Test
    public void test() throws IOException, MyException {

        ClientGlobal.initByProperties("fastdfs.properties");
        System.out.println(ClientGlobal.configInfo());
        TrackerClient trackerClient = new TrackerClient();
        TrackerServer trackerServer = trackerClient.getConnection();
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);
//        StorageServer storageServer = null;
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);
        String srcPath = "C:\\Users\\Administrator\\Desktop\\dameinv.jpg";
        FileInputStream fis = new FileInputStream(srcPath);
        ByteArrayOutputStream out = new ByteArrayOutputStream();

        byte[] buff = new byte[1024];
        int length = -1;

        while ((length = fis.read(buff)) != -1) {
            out.write(buff, 0, length);
        }
        byte[] bytes = out.toByteArray();
        String[] strings = storageClient.upload_file(bytes, 0, bytes.length, "jpg", null);
        System.out.println(Arrays.toString(strings));

    }

}
```
