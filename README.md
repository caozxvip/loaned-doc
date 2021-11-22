
文档名称 | 技术一部-LOANED项目文档 
--- | ---  
更新时间 | 2021-11-22
基于框架 | SpringCloudAlibaba
作者 | Mickey
说明 | 共库项目文档（`内部使用，禁止外泄`）

## 1. 项目介绍
### 1.1. 项目结构
```
|-- loaned
    |-- .gitignore
    |-- pom.xml -- 父pom文件
    |-- README.md -- 项目介绍
    |-- loaned-admin -- 子模块（后端管理系统模块）
    |   |-- pom.xml -- 子模块pom
    |   |-- src
    |   |   |-- main
    |   |   |   |-- java
    |   |   |   |   |-- com
    |   |   |   |       |-- zsk
    |   |   |   |           |-- loaned
    |   |   |   |               |-- admin
    |   |   |   |                   |-- LoanAdminApplication.java -- 子模块启动类
    |   |   |   |                   |-- config -- 子模块配置类
    |   |   |   |                   |-- aspect -- 切面类
    |   |   |   |                   |-- filter -- 过滤器
    |   |   |   |                   |-- interceptor -- 拦截器
    |   |   |   |                   |-- handler -- 自定义处理类，如mybatisplus自动填充，认证服务自定义异常
    |   |   |   |                   |-- annotation -- 注解类
    |   |   |   |                   |-- constant -- 常量类
    |   |   |   |                   |-- enums -- 枚举类，业务枚举或异常枚举等
    |   |   |   |                   |-- utils -- 工具类
    |   |   |   |                   |-- feign -- feign接口类，所有远程调用服务
    |   |   |   |                   |-- modules -- 子模块下的全部业务逻辑代码
    |   |   |   |                   |   |-- sys -- 子模块下子业务块逻辑（后台管理系统的系统管理模块）
    |   |   |   |                   |       |-- controller -- 控制层
    |   |   |   |                   |       |-- dao -- 持久化接口层
    |   |   |   |                   |       |-- dto -- 业务dto层
    |   |   |   |                   |       |-- model -- 数据实体层
    |   |   |   |                   |       |-- service -- 业务service层
    |   |   |   |                   |       |   |-- impl -- 业务service实现层
    |   |   |   |                   |       |-- vo -- 页面对象
    |   |   |   |                   |-- rediskeys -- 业务redis前缀配置
    |   |   |   |-- resources -- 资源目录
    |   |   |       |-- bootstrap-dev.yml -- 开发环境配置
    |   |   |       |-- bootstrap-prod.yml -- 生产环境配置
    |   |   |       |-- bootstrap-test.yml -- 测试环境配置
    |   |   |       |-- bootstrap.yml -- 主配置，端口、服务名等
    |   |   |       |-- logback-spring.xml -- logback日志配置
    |   |   |       |-- mappers -- 持久化层sql
    |   |   |           |-- sys -- 子模块子业务的sql
    |   |   |-- test
    |   |       |-- java
    |-- loaned-cms -- 内容管理模块：产品、渠道、应用...等管理
    |-- loaned-common -- 公共模块：公共配置、feign、jackson、mybatisplus、redis、资源服务器、knife4j接口文档、统一异常处理等...
    |-- loaned-getway -- 网关模块：网关路由配置
    |-- loaned-iaas -- 基础服务模块：oauth2 + SpringSecurity + JWT多端用户授权、短信服务等...
    |-- loaned-report -- 报表模块：业务报表统计
    |-- loaned-user -- 用户模块：会员用户登录、用户数据操作等...
    |-- ... -- 其他业务模块
```

> 以上为模块划分示例

## 2. 开始使用
### 2.1. 代码生成器
1. IDEA安装插件，MyBatisCodeHelperPro [下载地址](https://zhile.io/2019/04/23/mybatis-code-helper-pro-crack.html)<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616836193248886755174.png)</br>
2. IDEA连接数据库<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616836872200765681441.png)</br>
![](https://huabeifile.corntoast.cn/data/20210327/1616836898284762176685.png)<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616836940038247754946.png)<br>
3. 生成代码<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616838210950386711454.png)<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616838239849484252142.png)<br>
`检查代码是否生成`<br>
![](https://huabeifile.corntoast.cn/data/20210327/1616838272158358202264.png)<br>


> 请先安装插件和连接数据库，按规范生成到对应目录，以上仅做参考


### 2.2. 统一异常处理
#### 2.2.1. 业务错误码枚举
```java
/**
 * 系统错误枚举，实现IMessageEnum接口
 * @Author Mickey
 * @Date 2021/2/10 17:41
 **/
public enum SysErrorEnums implements IMessageEnum {

    /**
     *
     * 系统错误1000~1999
     *  不同业务模块用不同错误码区间
     *  系统模块：1000~1999
     *  基础模块：2000~2999
     *  用户模块：3000~3999
     *  订单模块：4000~4999
     *  报表模块：4000~4999
     *  ...
     *  错误信息可加参数，例如：订单{0}状态异常，当前状态为{1} ，使用时 new BusinessException(OrderErrorEnums.ERROR_ORDER_STATE, "订单号", "已失效");
     **/

    /**操作成功*/
    SUCCESS(200, "操作成功"),
    /**用户未认证*/
    UNAUTHORIZED(401, "未登录或token已过期"),
    /**操作失败*/
    FAIL(500, "操作失败"),

    /**服务器未知异常*/
    UNKNOW(1000,"Sorry，服务器开小差啦~"),
    /**参数为空*/
    EMPTY_PARAME(1001,"参数为空"),
    /**参数错误*/
    ERROR_PARAME(1002,"参数错误"),

    ;
    private int errorCode;
    private String errorMessage;

    SysErrorEnums(int errorCode, String errorMessage) {
        this.errorCode = errorCode;
        this.errorMessage = errorMessage;
    }

    @Override
    public int getErrorCode() {
        return errorCode;
    }

    @Override
    public String getErrorMessage() {
        return errorMessage;
    }
}
```


#### 2.2.2. 抛异常的方式

1. 调用Asserts类方法（`推荐`）
```java
Asserts.fail("错误信息");
Asserts.fail(SysErrorEnums.EMPTY_PARAME);
Asserts.fail(SysErrorEnums.ORDER_FAIL, "012312312", "已支付");
```
2. 直接new一个BusinessException异常
```java
throw new BusinessException("错误信息");
throw new BusinessException(SysErrorEnums.EMPTY_PARAME);
throw new BusinessException(SysErrorEnums.ORDER_FAIL, "012312312", "已支付");
```
> 最好使用Asserts.fail方式，使用维护的错误码

### 2.3. 返回结果封装
#### 2.3.1. 返回通用结果对象
```java
返回R对象（com.zsk.loaned.common.model.R）

方法示例：
@GetMapping("/test")
public R test(){
    JSONObject d = new JSONObject();
    d.put("test", "test");
    //返回结果直接放到data里面
    return R.ok().setData(d);
}

返回示例：
{
  "msg": "success",
  "code": 200,
  "data": {
    "test": "test"
  },
  "success": true
}
```
#### 2.3.2. 返回通用分页对象
```java
返回PageResult对象（com.zsk.loaned.common.model.PageResult）

方法示例：
@GetMapping("/list")
public PageResult list(Integer page, Integer limit, String username, String nickname){
    return sysUserService.list(page, limit, username, nickname);
}

返回示例：
{
  "totalCount": 3,
  "pageSize": 10,
  "totalPage": 1,
  "currPage": 1,
  "hasPrevious": false,
  "hasNext": false,
  "list": [
    {
      "userId": "1",
      "username": "admin",
      "nickname": "管理员",
      "state": 1,
      "createTime": "2021-02-28 17:56:25",
      "updateTime": "2021-03-23 23:43:11"
    }
  ]
}
```

### 2.4. 获取用户信息
1. 从CurrentUser中获取用户信息
```java
CurrentUser user = UserContext.getUser();
log.info("用户ID：{}", user.getUserId());
//admin 后端用户，member APP用户，用户服务获取用户详细信息时使用
log.info("用户类型：{}", user.getUserType());
```
2. 从安全上下文获取用户信息
```java
String userId = SecurityContextHolder.getContext().getAuthentication().getPrincipal().toString();
```
3. 业务中获取用户的详细信息进行业务处理（`业务使用`）
```java
// 1.参数直接传递（controller接口直接接收用户信息，需配合@CurrentUser注解使用）（推荐）
@GetMapping("/userInfo")
public R userInfo(@CurrentUser UmsUserInfo userInfo){
    return R.ok().setData(userInfo);
}
        
// 2.注入umsUserService服务，使用提供的getCurrentUser方法
UmsUserInfo userInfo = umsUserService.getCurrentUser();
```

### 2.5. redis使用
#### 2.5.1. 维护业务redis的key
维护在com.zsk.loaned.*.rediskeys下，以下已用户缓存为例：
```java
/**
 * 用户key
 * @Author Mickey
 * @Date 2021/2/13 0:25
 **/
public class UserKey extends BasePrefix {

    public UserKey(int expireSeconds, String prefix) {
        super(expireSeconds, prefix);
    }

    public static UserKey userlist = new UserKey(60 * 10, "userlist");

    /**
     * 根据用户名缓存用户信息
     **/
    public static UserKey userByUserNameAndAppID = new UserKey(60 * 10, "una");
    /**
     * 根据用户ID缓存用户信息
     **/
    public static UserKey userById = new UserKey(60 * 10, "uid");
}
```
#### 2.5.2. 使用redis存取数据
```java
//注入redis服务
@Autowired
private RedisService redisService;

//从redis里面获取缓存数据
UmsUser umsUser = redisService.get(UserKey.userById, userId.toString(), UmsUser.class);

//将数据存入redis
redisService.set(UserKey.userById, userId.toString(), umsUser);
```
