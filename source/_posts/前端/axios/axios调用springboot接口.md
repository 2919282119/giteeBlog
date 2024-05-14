---
title: axios调用springboot接口
tags: 
- 前端
- axios
categories: 
---
### 查询使用axios.get
这里需要自定义一个函数用于将参数对象转换为查询字符串
```js
const objToQueryString = (obj) => {
    return obj ? Object.keys(obj).map(key => encodeURIComponent(key) + '=' + encodeURIComponent(obj[key])).join('&') : '';
}
```
然后和接口拼接起来即可
```js
const p={
    pageno:1,
    pagesize:5
}
axios.get("http://localhost:8888/user/getpage?"+objToQueryString(p)).then(res=>{
    console.log(res.data);
})
```


### 增或改使用axios.post
这里使用mp的saveorupdate，将新增和修改的接口进行合并
```js
const p2={
    name:"张三",//这里没有主键id,于是进行新增操作（id自动生成）
    password:"123456",
    address:"北京王府井",
    phone:"13812345678",
    email:"123456@qq.com"
}
axios.post("http://localhost:8888/user/saveorupdate",p2).then(res=>{
    console.log(res.data);
})
//如果有主键（且该主键在数据库中存在），则进行修改操作，否则进行新增操作

```

### 删除使用axios.delete
```js
axios.delete("http://localhost:8888/user/deletebyid?id=1").then(res=>{
    
})
//如果需要删除，考虑新增接口，调用mp的removebatchbyids,传入一个js数组
```
