# MyPersonalBlog

#### 介绍
基于Springboot+Vue的前后端分离式的个人博客云架构

#### 软件架构
软件架构说明

##  1.技术介绍

**前端**：vue + vuex + vue-router + axios + vuetify + element + echarts

**后端**：SpringBoot + nginx + docker + SpringSecurity + Swagger2 + MyBatisPlus + Mysql + Redis + elasticsearch + rabbitMQ + MaxWell

**其他**：接入QQ，微博第三方登录

## 2.运行环境

**开发工具**：IDEA

**服务器**：阿里云2核4G CentOS7.2

**CDN**：阿里云全站加速

**对象存储**：阿里云OSS

## 3.项目截图



## 4.项目总结

此次对博客进行了全方位的升级，前端技术点和后端技术点相比第一版提升了不少。

前端方面：UI模仿的是hexo的butterfly(原谅我不会UI设计)，加入了许多动画过渡效果，并考虑到了一些用户细节（深色模式，图片预览，代码复制等），评论加入了表情和分页，提升了用户体验。新增了留言弹幕墙，支持匿名发送弹幕。后台文章编辑器换为MarkDown，语法更加简洁，新增了草稿箱和自动保存文章等功能。

后端方面：使用Docker部署环境，更加方便快捷。采用elasticsearch做为搜索引擎，支持搜索结果分词和高亮。使用MaxWell监听mysql数据，绑定RabbitMQ消息队列同步es文章数据。接入了QQ，微博第三方登录，无需邮箱注册即可登录。

总的来说，这次升级后主流技术基本全用到了，对于学习来说是一个很不错的项目。门槛对于初学者来说稍微高了一点😂，不过我会尽量帮助大家解决问题，写文章避免大家踩坑。服务器由于使用了elasticsearch这个内存大户所以升级到了2核4G，如果是使用学生机的小伙伴可以先不用elasticsearch，用Mysql模糊查询代替。

[项目源码地址](https://gitee.com/kkhead/my-personal-blog)，感谢大家支持，项目配置部署运行另开文章介绍。


#### 安装教程
## 1.打包后端项目jar包

打开pom.xml文件，修改packaging方式为jar

![QQ截图20210813231637 1.png](https://static.talkxj.com/articles/25de8986f7af569e7586936b847f596a.png)

点击右侧maven插件 -> package

![QQ截图20210813231652 1.png](https://static.talkxj.com/articles/1f03c29f1da200e8bc7eab611697475b.png)

打包成功后会在target目录下生成jar包

![QQ截图20210813231721 1.png](https://static.talkxj.com/articles/a8c50ac983abf425dfccda924b4d0ab3.png)


## 2.编写Dockerfile文件

```sh
FROM java:8
VOLUME /tmp
ADD blog-springboot-0.0.1.jar blog.jar       
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/blog.jar"] 
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```

**ps：Dockerfile文件不需要后缀，直接为文件格式**

## 3.编写blog-start.sh脚本

```sh
#源jar路径  
SOURCE_PATH=/usr/local/docker
#docker 镜像/容器名字或者jar名字 这里都命名为这个
SERVER_NAME=blog-springboot-0.0.1.jar
TAG=latest
SERVER_PORT=8080
#容器id
CID=$(docker ps | grep "$SERVER_NAME" | awk '{print $1}')
#镜像id
IID=$(docker images | grep "$SERVER_NAME:$TAG" | awk '{print $3}')
if [ -n "$CID" ]; then
  echo "存在容器$SERVER_NAME, CID-$CID"
  docker stop $CID
  docker rm $CID
fi
# 构建docker镜像
if [ -n "$IID" ]; then
  echo "存在$SERVER_NAME:$TAG镜像，IID=$IID"
  docker rmi $IID
else
  echo "不存在$SERVER_NAME:$TAG镜像，开始构建镜像"
  cd $SOURCE_PATH
  docker build -t $SERVER_NAME:$TAG .
fi
# 运行docker容器
docker run --name $SERVER_NAME -v /usr/local/upload:/usr/local/upload -d -p $SERVER_PORT:$SERVER_PORT $SERVER_NAME:$TAG
echo "$SERVER_NAME容器创建完成"
```

**ps：sh文件需要用notepad++转为Unix格式**

![QQ截图20210402213326.png](https://static.talkxj.com/articles/1617370849316.png)

## 4.将文件传输到服务器

![QQ截图20210916104127.png](https://static.talkxj.com/articles/7bf9fc73a83b29737f02ee0f9db547b7.png)

将上述三个文件传输到/usr/local/docker下（手动创建文件夹）

![QQ截图20210402212725.png](https://static.talkxj.com/articles/1617370924245.png)

## 5.docker运行后端项目

进入服务器/usr/local/docker下，构建后端镜像

```sh
sh ./blog-start.sh 
```

![QQ截图20210402213552.png](https://static.talkxj.com/articles/1617370970473.png)

**ps：第一次时间可能比较长，耐心等待即可**

查看是否构建成功

![QQ截图20210402214341.png](https://static.talkxj.com/articles/1617371042321.png)

可以去测试下接口是否运行成功

![QQ截图20200706094027.png](https://static.talkxj.com/articles/1593999635017.png)

**ps：需要重新部署只需重新传jar包，执行sh脚本即可**

## 6.打包前端项目

打开cmd，进入Vue项目路径 -> npm run build

![QQ截图20200706091427.png](https://static.talkxj.com/articles/1593998091709.png)

打包成功后会在目录下生成dist文件

![QQ截图20200706091817.png](https://static.talkxj.com/articles/1593998311729.png)

将Vue打包项目传输到/usr/local/vue下（由于我前台和后台分为两个项目，所以改名dist文件）

![QQ截图20200706092538.png](https://static.talkxj.com/articles/1593998758469.png)

## 7.nginx配置(有域名选这个)

**参考域名解析**

![QQ截图20220114151133.png](https://static.talkxj.com/articles/108e6eb7baa26d42017d6b50466a1174.png)

在/usr/local/nginx下创建nginx.conf文件，格式如下

```sh
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    client_max_body_size     50m;
    client_body_buffer_size  10m; 
    client_header_timeout    1m;
    client_body_timeout      1m;

    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_comp_level  4;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;

server {
        listen       80;
        server_name  前台域名;
     
        location / {		
            root   /usr/local/vue/blog;
            index  index.html index.htm; 
            try_files $uri $uri/ /index.html;	
        }
			
	location ^~ /api/ {		
            proxy_pass http://你的ip:8080/;
	    proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;						
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
		
    }
	
server {
        listen       80;
        server_name  后台子域名;
     
        location / {		
            root   /usr/local/vue/admin;
            index  index.html index.htm; 
            try_files $uri $uri/ /index.html;	
        }
			
	location ^~ /api/ {		
            proxy_pass http://你的ip:8080/;
	    proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;						
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
		
    }

server {
        listen       80;
        server_name  websocket子域名;
     
        location / {
          proxy_pass http://你的ip:8080/websocket;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "Upgrade";
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
       }
	
    }

server {
        listen       80;
        server_name  上传文件子域名;
     
        location / {		
          root /usr/local/upload/; 
        }		
		
    }
 
 }
```

**ps：我前台和后台时分为两个域名，所以写了两个server，前端项目路径为之前传输的路径，其他两个为文件上传域名和websocket转发域名。**

docker启动nginx服务

```sh
docker run --name nginx --restart=always -p 80:80 -d -v /usr/local/nginx/nginx.conf:/etc/nginx/nginx.conf -v /usr/local/vue:/usr/local/vue -v /usr/local/upload:/usr/local/upload nginx 
```

## 8.nginx配置(无域名选这个)

```sh
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    client_max_body_size     50m;
    client_body_buffer_size  10m; 
    client_header_timeout    1m;
    client_body_timeout      1m;

    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_comp_level  4;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;

server {
        listen       80;
        server_name  你的ip;
     
        location / {		
            root   /usr/local/vue/blog;
            index  index.html index.htm; 
            try_files $uri $uri/ /index.html;	
        }
			
	location ^~ /api/ {		
            proxy_pass http://你的ip:8080/;
	    proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;						
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
		
    }
	
server {
        listen       81;
        server_name  你的ip;
     
        location / {		
            root   /usr/local/vue/admin;
            index  index.html index.htm; 
            try_files $uri $uri/ /index.html;	
        }
			
	location ^~ /api/ {		
            proxy_pass http://你的ip:8080/;
	    proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;						
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
		
    }

server {
        listen       82;
        server_name  你的ip;
     
        location / {
          proxy_pass http://你的ip:8080/websocket;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "Upgrade";
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
       }
	
    }

server {
        listen       83;
        server_name  你的ip;
     
        location / {		
          root /usr/local/upload/; 
        }		
		
    }
 
 }
```

docker启动nginx服务

```sh
docker run --name nginx --restart=always -p 80:80 -p 81:81 -p 82:82 -p 83:83 -d -v /usr/local/nginx/nginx.conf:/etc/nginx/nginx.conf -v /usr/local/vue:/usr/local/vue -v /usr/local/upload:/usr/local/upload nginx 
```

**ps：需要通过ip + 端口号访问项目**

## 9.运行测试

去浏览器测试下是否运行成功

![QQ截图20200706094824.png](https://static.talkxj.com/articles/1594000142563.png)

## 10.其他设置

进入后台管理 -> 系统管理 -> 网站管理 -> 其他设置，配置websocket地址，有域名则填ws://websocket域名，无域名则填ws://ip:82

![QQ截图20210910204242.png](https://static.talkxj.com/articles/7ba0cb206bb6a4973d1d9fe0c0bbb69d.png)

## 11.总结

整个前后端分离的部署教程到这里就结束啦，第一次部署可能会比较麻烦，不过后面就轻车熟路了，有问题的可以私聊问我或者在评论区留言。



#### 使用说明

> 项目需要配置的地方不多，主要就两个地方需要注意：邮箱授权和第三方授权需要提前申请

## 1.基本设置

### 1.1 打开application.yml，修改数据库ip等基本信息

![QQ截图20210818144550.png](https://static.talkxj.com/articles/b984c1f473e8903be92408bfa698afe9.png)

这些基本的配置就不多说了，基本就是改下服务器ip和账号密码什么的

### 1.2 获取QQ邮箱授权码（邮箱验证码用）

打开QQ邮箱官网，点击设置 -> 账户 -> 往下翻

![QQ截图20200630213835.png](https://static.talkxj.com/articles/1593524350827.png)

点击生成授权码

![QQ截图20200630220934.png](https://static.talkxj.com/articles/1593526185438.png)

更改application中的邮箱配置

![QQ截图20200630221617.png](https://static.talkxj.com/articles/1593526696245.png)

**username填你的QQ号邮箱，password填刚才的授权码**

## 2.自定义设置

### 2.1 搜索和上传模式

搜索模式可选elasticsearch或mysql，上传模式可选oss或local，按个人情况选择。

![QQ截图20220114153605.png](https://static.talkxj.com/articles/addd7f792d8c1d74a03fed7a58e08846.png)

### 2.2 修改application中的阿里云OSS配置信息（若选OSS上传模式）

点击OSS管理 -> 点击Access Key

![QQ截图20220114152927.png](https://static.talkxj.com/articles/ae892bb6da40d2465b19c5de795c4d49.png)

查看AccessKey ID和AccessKey Secret

![QQ截图20200701173256.png](https://static.talkxj.com/articles/1593596274580.png)

查看bucket信息，没有则先创建

![QQ截图20220114152421.png](https://static.talkxj.com/articles/a0e2cda751a081d03749956109c01f1b.png)

修改配置信息

![QQ截图20220127143857.png](https://static.talkxj.com/articles/697a321696570fdff14999e06ff6283f.png)


**url：** 填你的bucke域名地址

**endpoint：** 填endpoint信息

**accessKeyId，accessKeySecret：** 填刚刚获取的信息

**bucketName：** 填创建的bucketName

最后OSS访问权限记得设置为公共读

![QQ截图20220114152757.png](https://static.talkxj.com/articles/8fec9bf7e6c716f4970cd0223beaf1a9.png)


**ps：如果选local上传模式请看部署文章nginx配置部分！！！**


## 3.第三方登录设置（可选）

### 3.1 打开QQ互联官网申请授权

登录后点击右上角头像 -> 个人接入 

![QQ截图20200701162420.png](https://static.talkxj.com/articles/1593592258970.png)

申请成功后点击应用管理创建，就能得到appId，appSecret和回调地址（一般一天左右审核成功）

![QQ截图20200701163357.png](https://static.talkxj.com/articles/1593592484456.png)

![QQ截图20220127143306.png](https://static.talkxj.com/articles/70e16886695767b466a04be848490ba6.png)

### 3.2 打开微博开发者平台申请授权

申请步骤大同小异，这里直接展示结果，同样的appId，appSecret和回调地址

![QQ截图20200701164214.png](https://static.talkxj.com/articles/1593593080715.png)

![QQ截图20220127143418.png](https://static.talkxj.com/articles/c38d781adfd2db62c9e435c6eecee7de.png)

**ps：第三方登录回调域名最好保持一样写/oauth/login/qq和/oauth/login/weibo，如若修改需到router.js里配置OauthLogin组件路由，回调域名根据你网站的情况选择http或https。**

### 3.3 修改vue中的第三方授权信息

> 打开vue项目中的public的index.html文件，修改引入的js

```html
<script src="http://tjs.sjs.sinajs.cn/open/api/js/wb.js?appkey=你的微博appId" type="text/javascript" charset="utf-8"></script>
<script type="text/javascript" src="http://connect.qq.com/qc_jssdk.js" data-callback="true" data-appid="你的QQAppId" data-redirecturi="QQ回调地址" charset="utf-8"></script>
```

打开前台项目assets下的/js/config修改配置信息，填写APPID和回调地址

![QQ截图20210818152204.png](https://static.talkxj.com/articles/624a3496cd0809fbb788ee2ba5a9c727.png)


### 3.4 修改application中的第三方授权信息

![QQ截图20200701170018.png](https://static.talkxj.com/articles/1593594734916.png)

修改appId，appSecret和回调地址即可


## 4.总结

只需要修改yml提示的地方即可，不用删除和修改其他任何地方。


#### 参与贡献

1.  Fork 本仓库
2.  新建 Feat_xxx 分支
3.  提交代码
4.  新建 Pull Request


#### 特技

1.  使用 Readme\_XXX.md 来支持不同的语言，例如 Readme\_en.md, Readme\_zh.md
2.  Gitee 官方博客 [blog.gitee.com](https://blog.gitee.com)
3.  你可以 [https://gitee.com/explore](https://gitee.com/explore) 这个地址来了解 Gitee 上的优秀开源项目
4.  [GVP](https://gitee.com/gvp) 全称是 Gitee 最有价值开源项目，是综合评定出的优秀开源项目
5.  Gitee 官方提供的使用手册 [https://gitee.com/help](https://gitee.com/help)
6.  Gitee 封面人物是一档用来展示 Gitee 会员风采的栏目 [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
