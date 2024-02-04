---
title: 通过docker部署前端项目(nginx)
tags: 
- docker
- 运维
- nginx
---
## 通过以下docker命令部署前端项目
```bash
docker run -d \
  --name nginx \
  -p 80:80 \
  -p 8094:8094 \ 
  #可设置多个端口映射(多个项目),根据nginx.conf文件中的配置来设置
  -v /root/nginx/html:/usr/share/nginx/html \
  -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
  #一定要挂载这两个文件，注意要先用mobaxterm将这两个文件传到根目录~下面的nginx文件夹下
  nginx

```
