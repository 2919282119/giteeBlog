---
title: linux命令
tags: 
- linux
categories: 
---
### mysql修改密码
如果是用了docker：
```bash
docker exec -it mysql bash
```
```bash
set password for 'root'@'%'='qazwsxedc123';
# @前面是用户名，后面是host,可以是localhost,表示本机,也可以是%，代表所有host
flush privileges;
# 刷新状态
```