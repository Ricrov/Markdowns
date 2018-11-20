## 负载均衡
#### Nginx

`docker pull nginx` <br>
`docker run -d --name lbserver -p 80:80 nginx` -d 后台启动

配置两个相同的 SpringBoot 项目以供测试
pic01.png

### 配置负载均衡

#### 进入 nginx 系统
`docker exec -it lbserver /bin/bash`
#### 更改配置文件
- `nginx -t` 查看配置文件是否正确(也可以显示配置文件的路径) => `/etc/nginx/nginx.conf`
- 配置国内镜像 `sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list`
- 更新软件列表 `apt-get update`
- 安装文本编辑器 `apt-get install vim -y`
- 编辑配置文件 `vim /etc/nginx/nginx.conf`
- `:set nu` 显示行号
- `#include /etc/nginx/nginx.conf` 注释掉此行代码因为此配置中有配置80端口会发生冲突
- 在上行代码下添加以下代码 (`springboot` 为自定义名, 代表此组有两台主机)
```
upstream springboot {
    server 192.168.1.46:8080 weight=1 fail_timeout=20s;
    server 192.168.1.46:9090 weight=2 fail_timeout=20s;
}
server {
    listen 80;
    location / {
        proxy_pass http://springboot;
    }
}
```
- 重启容器 `docker restart lbserver`
- 此时访问 localhost/test 即会显示 8080 或 9090 端口的内容

#### 验证
打印日志: 在 controller 中打印语句, 即可判断是进入了哪个服务器

## SpringBoot 集成
