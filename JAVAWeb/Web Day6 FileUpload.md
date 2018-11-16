### 需要用到的jar包
1. commons-fileupload
2. commons-io
### 文件上传步骤
1. 需要在表单中添加enctype="multipart/form-data" 属性
```xml
"enctype用来设置请求头, 表示上传的是文件类型";
<form action="file-upload" method="post" enctype="multipart/form-data">
    <label>用户名: <input type="text" name="username"></label><br>
    <label>密码: <input type="password" name="password"></label><br>
    <label>头像: <input type="file" name="headerImg"></label><br>
    <label>身份证: <input type="file" name="cardId"></label><br>
    <input type="submit" value="注册">
</form>
```
2. 后台接收数据并处理
### 后台如何接收数据
1. 创建ServletFileUpload 对象
```java
ServletFileUpload upload = new ServletFileUpload();
```
2. ==设置文件(FileItem)项工厂==
```java
upload.setFileItemFactory(new DiskFileItemFactory());
```
3. 为文件可能有中文的属性设置编码
```java
upload.setHeaderEncoding("utf-8");
```
4. 设置单个文件的大小
```java
upload.setFileSizeMax(2 * 1024 * 1024);
```
5. 设置一次表单提交的最大值
```java
upload.setSizeMax(5 * 1024 * 1024);
```
6. ==解析请求 upload.parseRequest(req)==
```java
"如何把获取到的文件存起来";
"1. 先获取真实路径, 然后通过该路径创建一个文件夹";
"2. 最后把接收到的文件保存到该文件夹下";
"如何创建该文件夹";
PrintWriter out = resp.getWriter();
    "获取文件的真实路径";
    String path = req.getServletContext().getRealPath("files");
    "创建真实路径创建一个文件夹, 用来保存接收到的数据";
    File dir = new File(path);
        if (!dir.exists()) {
            dir.mkdirs();
        }
try {
    List<FileItem> fileItems = upload.parseRequest(req);
    for (FileItem item : fileItems) {
    "item.isFormField用来判断是不是表单属性, 也就是该文件项不是文件";
     if (item.isFormField()) {
     "如何获取到非文件数据";
     "1. 获取非文件数据的name属性值";
     String key = item.getFieldName();
     "获取该key所对应的表单的值";
     String value = item.getString("utf-8");
     continue;
                }
    "在dir文件夹下创建一个名为item.getName()的文件";
    File file = new File(dir, item.getName());
    "获取文件的绝对路径";
    String absolutePath = file.getAbsolutePath();
    "写入文件";
    item.write(file);
    }
```

### FileItem文件项常用方法
1. item.getContentType
```
"该方法返回文件项的ContentType";
"如果这个文件项是formField, 那么没有ContentType";
```
2. item.getFiledName()
```
"返回该文件项上传时候的key";
"也就是html中input的name属性的值";
```
3. item.getName()
```
"该方法返回文件项的文件名";
```
4. item.getSize()
```
"返回文件项的文件大小";
```
### java中的UUID类中的常用方法
```
"该方法可以获取到永不重复的随机数";
"UUID uuid = UUID.randomUUID()";
```
### ==使用Ajax上传multipart/form-data数据==
1. 需要阻止表单的默认提交方式
2. 将表单中的数据加载成FormData对象
3. ==必须使用$.ajax(setting).done(function(resp){处理请求的结果}): 这种形式来提交文件类型的数据==
4. setting中的配置如下
```js
$(function () {
    $('form').submit(function (e) {
        "阻止表单默认的提交方式";
        e.preventDefault();
        "将表单中的数据加载成FormData对象";
        var data = new FormData(this);
        var settings = {
            url: 'file-upload',
            method: 'post',
            "processData": false,"必须";
            "contentType": false,"必须";
            "mimeType": "multipart/form-data",
            "data": data
            }
        $.ajax(settings).done(function (resp) {
                console.log(resp);
            })
        })
    })
```
