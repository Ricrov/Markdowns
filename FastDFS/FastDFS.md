# FastDFS

## 环境搭建
https://github.com/happyfish100/fastdfs/wiki
> 实际开发中文件服务器是不能暴露在公网上的. <br>
> Spring服务器和文件服务器处在同一局域网. 其他用户只能访问到 tomcat 而不能直接访问到文件服务器. <br>

- 下载 centOS
  - `docker pull centos:7`
  - `docker run -it --name centos -p 8888:8888 -p 22122:22122 -p 23000:23000 centos:7`

### CentOS
- CentOS 安装软件
  > `yum`

- 配置镜像
  - 备份 CentOS-Base.repo
    ```
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
    ```
  - 创建 CentOS-Base.repo
    ```
    touch /etc/yum.repos.d/CentOS-Base.repo
    ```
  - 将此网站的 CentOS7 文件代码加入此文件
    ```
    http://mirrors.ustc.edu.cn/help/centos.html
    ```
  - 运行 `yum makecache` 生成缓存更新软件列表


- 更新 `yum`

- 编译环境 `-y: 自动选'是'`
  ```
  yum install git gcc gcc-c++ make automake autoconf libtool pcre pcre-devel zlib zlib-devel openssl-devel wget vim -y
  ```

  `mkdir /home/dfs` #创建数据存储目录
  `cd /usr/local/src` #切换到安装目录准备下载安装包

#### 安装libfatscommon
  - `git clone https://github.com/happyfish100/libfastcommon.git --depth 1`
  - `cd libfastcommon/` 进入目录
  - `./make.sh && ./make.sh install` 编译安装

#### 安装FastDFS
  ```
  cd ../ #返回上一级目录
  git clone https://github.com/happyfish100/fastdfs.git --depth 1
  cd fastdfs/
  ./make.sh && ./make.sh install #编译安装
  #配置文件准备
  cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
  cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
  cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf #客户端文件，测试用
  cp /usr/local/src/fastdfs/conf/http.conf /etc/fdfs/ #供nginx访问使用
  cp /usr/local/src/fastdfs/conf/mime.types /etc/fdfs/ #供nginx访问使用
  ```

#### 安装fastdfs-nginx-module
  ```
  cd ../ #返回上一级目录
  git clone https://github.com/happyfish100/fastdfs-nginx-module.git --depth 1
  cp /usr/local/src/fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs
  ```

#### 安装nginx ( web 服务器 )
  ```
  wget http://nginx.org/download/nginx-1.15.4.tar.gz #下载nginx压缩包
  tar -zxvf nginx-1.15.4.tar.gz #解压
  cd nginx-1.15.4/
  #添加fastdfs-nginx-module模块
  ./configure --add-module=/usr/local/src/fastdfs-nginx-module/src/
  make && make install #编译安装
  ```

## 单机部署
>  storage 存储上传文件, 一个系统可能有多个 storage, 上传文件的前提是连接 tracker.
>  tracker 会筛出1个 storage 告知 ip 地址

  `yum install net-tools` 安装网络工具
  `ifconfig` 查看 ip 地址

  - `/` 搜索

  - tracker 配置
  ```
  vim /etc/fdfs/tracker.conf
  #需要修改的内容如下
  port=22122  # tracker服务器端口（默认22122,一般不修改）
  base_path=/home/dfs  # 存储日志和数据的根目录
  ```

  - storage 配置
  ```
  vim /etc/fdfs/storage.conf
  #需要修改的内容如下
  port=23000  # storage服务端口（默认23000,一般不修改）
  base_path=/home/dfs  # 数据和日志文件存储根目录
  store_path0=/home/dfs  # 第一个存储目录
  tracker_server=192.168.52.1:22122  # tracker服务器IP和端口
  http.server_port=8888  # http访问文件的端口(默认8888,看情况修改,和nginx中保持一致)
  ```

  - client 测试
  ```
  vim /etc/fdfs/client.conf
  #需要修改的内容如下
  base_path=/home/dfs
  tracker_server=192.168.52.1:22122    #tracker服务器IP和端口
  #保存后测试,返回ID表示成功 如：group1/M00/00/00/xx.tar.gz
  fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/nginx-1.15.4.tar.gz
  ```

  - 配置nginx访问
  ```
  vim /etc/fdfs/mod_fastdfs.conf
  #需要修改的内容如下
  tracker_server=192.168.52.1:22122  #tracker服务器IP和端口
  url_have_group_name=true
  store_path0=/home/dfs
  #配置nginx.config
  vim /usr/local/nginx/conf/nginx.conf
  #添加如下配置
  server {
      listen       8888;    ## 该端口为storage.conf中的http.server_port相同
      server_name  localhost;
      location ~/group[0-9]/ {
          ngx_fastdfs_module;
      }
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
      root   html;
      }
  }
  #测试下载，用外部浏览器访问刚才已传过的nginx安装包,引用返回的ID
  http://192.168.52.1:8888/group1/M00/00/00/wKgAQ1pysxmAaqhAAA76tz-dVgg.tar.gz
  #弹出下载单机部署全部跑通
  ```

## 启动
`ps -ef` 查看是否已启动

tracker
```
/etc/init.d/fdfs_trackerd start #启动tracker服务
/etc/init.d/fdfs_trackerd restart #重启动tracker服务
/etc/init.d/fdfs_trackerd stop #停止tracker服务
chkconfig fdfs_trackerd on #自启动tracker服务
```

storage
```
/etc/init.d/fdfs_storaged start #启动storage服务
/etc/init.d/fdfs_storaged restart #重动storage服务
/etc/init.d/fdfs_storaged stop #停止动storage服务
chkconfig fdfs_storaged on #自启动storage服务
```

nginx
```
/usr/local/nginx/sbin/nginx #启动nginx
/usr/local/nginx/sbin/nginx -s reload #重启nginx
/usr/local/nginx/sbin/nginx -s stop #停止nginx
```

## 测试
client测试
```
#测试,返回ID表示成功 如：group1/M00/00/00/xx.tar.gz
fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/nginx-1.15.4.tar.gz
```
上传的地址: /home/dfs/data/00/00
此时通过浏览器访问此地址即可下载 http://localhost:8888/group1/M00/00/00/rBEAAlvqQD6AKdigAA-itrfn0m4.tar.gz

## 配置好的镜像
docker pull morunchang/fastdfs

- 运行 tracker
`run -d --name tracker morunchang/fastdfs`
- 运行 storage
