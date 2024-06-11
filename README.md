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

1.  xxxx
2.  xxxx
3.  xxxx

#### 使用说明

1.  xxxx
2.  xxxx
3.  xxxx

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
