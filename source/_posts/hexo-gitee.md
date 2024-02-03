---
title: hexo部署到gitee
date: 2022-03-27 15:37:35
tags: hexo
---
## hexo修改主题后部署问题
见：[butterfly官方文档](https://butterfly.js.org/posts/21cfbf15/#%E5%8D%87%E7%B4%9A%E5%BB%BA%E8%AD%B0)

hexo5之后可以使用npm安装主题
```bash
npm install hexo-theme-butterfly
```
安装之后主题不会像以往一样安装在themes文件夹下，而是安装在node_modules文件夹下，修改主题方法如下：
- 在_config.yml中修改theme为butterfly(同之前)
- _config.yml同级目录下新建一个config.butterfly.yml文件，将主题的配置项写入该文件中(可直接将之前的复制过来)
- 别忘了将node_modules文件夹下的butterfly的favicon.ico文件改为自己的

要在gitee上部署，还需要修改_config.yml文件
```yml
url: https://afly0321.gitee.io
root: /
#只有仓库名和gitee用户名一样时才不用写二级域名，
#否则需要写二级域名，如https://afly0321.gitee.io/blog，root也要改成/blog
```


