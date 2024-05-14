---
title: nginx部署Vue3项目注意的问题
tags: 
- 运维
categories: 
---
### 本地开发与服务器部署区别
众所周知可以在`vite.config.js`中配置代理，在本地开发时，vite会自动将请求转发到服务器，vite.config.js中的代理配置如下：
```js
server{
    proxy:{
        '/api':{
            target:'http://host:port',
            changeOrigin:true,
            rewrite:(path)=>path.replace(/^\/api/,'')
        }
    }
}
```

而在服务器部署时，vite不会自动转发请求，所以需要手动配置代理。
nginx.conf中配置如下(可能还需要其他配置，这里只展示了基础配置)
```nginx
location /api/ {
    proxy_pass http://host:port/;
}
```

如果是用docker部署的nginx,在修改了nginx.conf之后，需要reload
```bash
docker exec -it nginx bash
nginx -s reload
```
