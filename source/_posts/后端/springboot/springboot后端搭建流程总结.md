---
title: springboot后端搭建流程总结
tags: 
- springboot
categories: 
---
### pom.xml引入依赖
```xml
<dependencies>
<!--
    最主要的就是第一个springboot-starter-web
-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.2.6</version>
    </dependency>
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.5</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>3.0.3</version>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>5.8.16</version>
    </dependency>
    <dependency>
        <groupId>com.auth0</groupId>
        <artifactId>java-jwt</artifactId>
        <version>4.3.0</version>
    </dependency>
</dependencies>

```
### 配置镜像仓库
```xml
<repositories>
    <repository>
        <id>aliyunmaven</id>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </repository>
    <repository>
        <id>aliyunspring</id>
        <url>https://maven.aliyun.com/repository/spring/</url>
    </repository>
</repositories>

### application.yml配置
```yml
server:
  port: 8888
  servlet:
    context-path: /
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://47.113.145.36:3306/test
    username: root
    password: xxxxxxxx

```


### 安装mp插件和restfulTool插件
- MyBatisPlus(初音封面)
- RestfulTool(作者：ZhangYuanSheng)

### 创建工作目录
在springboot启动类所在目录下新建如下目录
- config
  - WebMVCConfig
  - MybatisConfig
- controller
- pojo
  - entity
  - vo
  - query
    - Result
    - pageQuery
- dao(记得在生成mapper之后，要手动给每一个加上@Mapper注解)
- service
  - impl
- utils
- exception
  - GlobalExceptionHandler (全局异常处理器)   
- interceptor
  - LoginInterceptor

### controller接口示例（以UserController为例）
记得给User实体类的password字段加上@JsonIgnore注解，这样在响应给前端时就不会有password字段
```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private IUserService userService;

    //获取所有用户
    @GetMapping("getall")
    public Result getall(){
        List<User> list = userService.list();
        return  Result.success(list);
    }
    //根据id获取用户
    @GetMapping("getbyid")
    public Result getbyid(String id){
        User user = userService.getById(id);
        if(user!=null){
            return Result.success(user);
        }else{
            return Result.error("该用户id不存在");
        }
    }
    //根据用户名获取用户
    @GetMapping("getbyname")
    public Result getbyname(String username){
        LambdaQueryWrapper<User> qr = new LambdaQueryWrapper();
        qr.eq(User::getUsername,username);
        User user = userService.getOne(qr);
        if(user!=null){
            return Result.success(user);
        }else{
            return  Result.error("该用户名不存在");
        }
    }
    //根据id删除用户
    @DeleteMapping("deletebyid")
    public Result deletebyid(String id){
        boolean flag = userService.removeById(id);
        if(flag){
            return Result.success();
        }else{
            return Result.error("该用户id不存在");
        }
    }
    //新增或修改用户
    @PostMapping("saveorupdate")
    public Result saveorupdate(@RequestBody User user){
         userService.saveOrUpdate(user);
         //如果user有主键（且数据库包含该主键），就修改，否则就是新增
         return Result.success();
    }
    //分页查询
    @GetMapping("getpage")
    public Result getpage(int pageno,int pagesize){
        Page<User> page = Page.of(pageno, pagesize);
        Page<User> tarpage = userService.page(page);
        long pages = tarpage.getPages();
        long total = tarpage.getTotal();
        List<User> records = tarpage.getRecords();
        return Result.success(new PageQuery<User>(pageno,pagesize,pages,total,records));
    }

    //根据id批量删除
    @PostMapping("deletebatchbyids")
    public Result deletebatchbyids(@RequestBody List<String> ids){
        boolean flag = userService.removeBatchByIds(ids);
        if(flag){
            return Result.success();
        }else{
            return Result.error("用户id不存在！");
        }
    }
    //根据条件分页查询过滤
    @GetMapping("filterpage")
    public Result filterpage(int pageno,int pagesize,@RequestBody User user){
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.like(user.getUsername()!=null,User::getUsername,user.getUsername())
                .like(user.getPassword()!=null,User::getPassword,user.getPassword())
                .like(user.getEmail()!=null,User::getEmail,user.getEmail())
                .like(user.getPhoneno()!=null,User::getPhoneno,user.getPhoneno())
                .like(user.getAddress()!=null,User::getAddress,user.getAddress());
        Page<User> page = Page.of(pageno, pagesize);
        Page<User> tarpage = userService.page(page,queryWrapper);
        long pages = tarpage.getPages();
        long total = tarpage.getTotal();
        List<User> records = tarpage.getRecords();
        return Result.success(new PageQuery<User>(pageno,pagesize,pages,total,records));
    }
    //用户注册
    @PostMapping("register")
    public Result register(@RequestBody User user){
        //拿到所有用户，判断用户名是否已经存在
        List<User> users = userService.list();
        User user1 = userService.getOne(new LambdaQueryWrapper<User>().eq(User::getUsername, user.getUsername()));
        if(user1!=null){
            return Result.error("用户名已存在!");
        }else{
            userService.save(user);
            return Result.success("注册成功!");
        }
    }
    //用户登录，登录后，会得到一个token，前端保存这个token，以后每次请求都携带这个token，后端验证这个token是否正确
    @RequestMapping("login")
    public Result login(@RequestBody User user){
        User user1 = userService.getOne(new LambdaQueryWrapper<User>().eq(User::getUsername, user.getUsername()).eq(User::getPassword, user.getPassword()));
        if(user1!=null){
            HashMap<String, Object> claims = new HashMap<>();
            claims.put("username",user.getUsername());
            String token = MyJwtUtil.geneToken(claims);
            return Result.success(token);
        }else{
            return Result.error("用户名或密码错误!");
        }
    }
    //根据token获取用户详细信息
    @GetMapping("getinfo")
    public Result getinfo(@RequestHeader("Authorization") String token){
        Map<String, Object> claims = MyJwtUtil.parseToken(token);
        String username = (String) claims.get("username");
        User user = userService.getOne(new LambdaQueryWrapper<User>().eq(User::getUsername, username));
        return Result.success(user);
    }
}

```
