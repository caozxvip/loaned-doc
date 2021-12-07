
文档名称 | LOANED项目文档 
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
    |-- loaned-admin -- 子模块（内容管理及系统模块）
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
    |   |   |   |                   |-- rabbitmq -- 消息队列（交换机、队列、绑定关系维护，消息监听、消费）
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
    |-- loaned-common -- 公共模块：公共配置、feign、jackson、mybatisplus、redis、资源服务器、knife4j接口文档、统一异常处理等...
    |-- loaned-getway -- 网关模块：网关路由配置
    |-- loaned-iaas -- 基础服务模块：oauth2 + SpringSecurity + JWT多端用户授权、短信服务等...
    |-- loaned-order -- 订单管理模块：会员订单，微店订单，投诉单...等管理
    |-- loaned-rabbit -- 消息模块：封装了rabbitmq消息中间件（多种消息类型，多种发送方式，可靠性投递,异步发送，Template池化关管理）
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
```text
Asserts.fail("错误信息");
Asserts.fail(SysErrorEnums.EMPTY_PARAME);
Asserts.fail("订单{0}预支付失败，当前状态：{1}", "012312312", "已支付");

//param check
Asserts.isNull(param, SysErrorEnums.ERROR_PARAME);
Asserts.notNull(param, SysErrorEnums.ERROR_PARAME);
Asserts.equals(param1, param1, SysErrorEnums.ERROR_PARAME);
Asserts.isTrue(param1, SysErrorEnums.ERROR_PARAME);
Asserts.notEmpty(param, SysErrorEnums.ERROR_PARAME);
Asserts.notEquals(param1, param2, SysErrorEnums.ERROR_PARAME);
```
2. 直接new一个BusinessException异常
```text
throw new BusinessException("错误信息");
throw new BusinessException(SysErrorEnums.EMPTY_PARAME);
throw new BusinessException(SysErrorEnums.ORDER_FAIL, "012312312", "已支付");
```
> 最好使用Asserts.fail方式，使用维护的错误码

### 2.3. 返回结果封装
#### 2.3.1. 返回通用结果对象
```text
返回R对象（com.zsk.loaned.common.model.R）
```
方法示例：
```java
@RestController
public class DemoController {
    @GetMapping("/test")
    public R test(){
        JSONObject d = new JSONObject();
        d.put("test", "test");
        //返回结果直接放到data里面
        return R.ok().setData(d);
    }
}
```
返回示例：
```json
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
```text
返回PageResult对象（com.zsk.loaned.common.model.PageResult）
```
方法示例：
```java
@RestController
public class DemoController {
    @GetMapping("/list")
    public R list(Integer page, Integer limit){
        PageResult pageData = service.list(page, limit);
        return R.ok().setPageData(pageData);
    }
}
```
返回示例：
```json
{
    "msg": "success",
    "code": 200,
    "data": {
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
    },
    "success": true
}  
```
### 2.4. 获取用户信息
#### 2.4.1 从CurrentUser中获取用户信息

```java
@RestController
public class DemoController {
    public void test(){
        CurrentUser user = UserContext.getUser();
        log.info("用户ID：{}", user.getUserId());
        //admin 后端用户，member APP用户，用户服务获取用户详细信息时使用
        log.info("用户类型：{}", user.getUserType());
    }
}
```
#### 2.4.2 从安全上下文获取用户信息
```text
String userId = SecurityContextHolder.getContext().getAuthentication().getPrincipal().toString();
```
#### 2.4.3 业务中获取用户的详细信息进行业务处理（`业务使用`）

```java
@RestController
public class DemoController {
    
    @Autowired
    private UmsUserService umsUserService;
    
    // 1.参数直接传递（controller接口直接接收用户信息，需配合@CurrentUser注解使用）（推荐）
    @GetMapping("/userInfo")
    public R userInfo(@CurrentUser UmsUserInfo userInfo){
        return R.ok().setData(userInfo);
    }

    // 2.注入umsUserService服务，使用提供的getCurrentUser方法
    UmsUserInfo userInfo = umsUserService.getCurrentUser();
}
```
> 推荐使用注解参数的方式注入用户信息
>

### 2.5. Redis使用
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
    
    /** 根据用户名缓存用户信息 **/
    public static UserKey userByUserNameAndAppID = new UserKey(60 * 10, "una");
    /** 根据用户ID缓存用户信息 **/
    public static UserKey userById = new UserKey(60 * 10, "uid");
}
```
#### 2.5.2. 使用redis存取数据
```java
@RestController
public class DemoController {
    //注入redis服务
    @Autowired
    private RedisService redisService;

    public void test(){
        //从redis里面获取缓存数据
        UmsUser umsUser = redisService.get(UserKey.userById, userId, UmsUser.class);

        //将数据存入redis
        redisService.set(UserKey.userById, userId, umsUser);
    }
}
```

### 2.6 分布式锁（redission）
#### 2.6.1 直接redisson原生代码实现（不推荐）
```java
@RestController
public class DemoController {
    
    @UserLogin(userType = JwtConstant.LOGIN_USER_TYPE.MEMBER)
    @ApiOperation(value = "渠道注册")
    @PostMapping("/channelRegisterH5")
    public R channelRegisterH5(@Validated @RequestBody UmsChannelRegisterReq channelRegisterReq, HttpServletRequest request) {
        try {
            //直接使用redission代码加锁
            if (redissionService.tryLock(LockKey.channelRegLockH5, channelRegisterReq.getPhone(), 5)) {
                return umsUserService.channelRegisterH5(channelRegisterReq, request);
            } else {
                return R.error(SysErrorEnums.TIME_OUT);
            }
        } finally {
            //最终解锁
            redissionService.unlock(LockKey.channelRegLockH5, channelRegisterReq.getPhone());
        }
    }
}
```

#### 2.6.2 使用封装的分布式锁注解@BusinessLock（推荐）

| 属性      | 必填 | 示例                                                         | 描述                                                         |
| --------- | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| spelKey   | 是   | 例1："'xxxController.xxxMethod' + #param"<br />例2："#param" | 加锁的key，支持spel表达式，如果计算失败默认不加锁（必须是可变的key，避免对所有用户加锁） |
| waitTime  | 否   | 0                                                            | 获取锁的最大等待时间，默认0，大于0使用tryLock加锁            |
| leaseTime | 否   | 10                                                           | 加锁的时间， 默认10                                          |
| unit      | 否   | TimeUnit.SECONDS                                             | waitTime，leaseTime参数的单位，默认秒                        |
| isFair    | 否   | false                                                        | 是否公平锁，默认否                                           |

```java
@RestController
public class DemoController {
    
    //计算后的spelKey例：com.xxx.UmsUserController.loginSms131xxxx8888，lock加锁5秒，获取失败阻塞等待
    @BusinessLock(spelKey = "#umsLoginSmsReq.phone", leaseTime = 5)
    @PostMapping(value = "/loginSms")
    @ApiOperation(value = "登录短信接口")
    public R loginSms(@Validated @RequestBody UmsLoginSmsReq umsLoginSmsReq, HttpServletRequest request) {
        return umsUserService.loginSms(umsLoginSmsReq, request);
    }

    //计算后的spelKey例：com.xxx.UmsUserController.closed12，tryLock尝试获取公平锁800毫秒，获取成功加锁500毫秒，获取失败返回数据处理中，请稍后再试...
    @BusinessLock(spelKey = "#user.id", waitTime = 800, leaseTime = 500, unit = TimeUnit.MILLISECONDS, isFair = true)
    @UserLogin(userType = JwtConstant.LOGIN_USER_TYPE.MEMBER)
    @ApiOperation(value = "注销接口")
    @PostMapping(value = "/closed")
    public R closed(@Validated @RequestBody UserClosedReq closedReq, @CurrentUser UmsUserInfo user, HttpServletRequest request) 	{
        return umsUserService.closed(closedReq, user, request);
    }
}
```

### 2.7 消息中间件（rabbitmq）
#### 2.7.1引入配置和相关依赖

```yaml
//在nacos中加入rabbit-config.yaml扩展配置
extension-configs:
    - refresh: true
    dataId: rabbit-config.yaml
```
```text
//在maven中引入相关依赖
<!-- 消息模块 -->
<dependency>
    <groupId>com.zsk.loaned</groupId>
    <artifactId>rabbit-core-producer</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

#### 2.7.2 配置交换机，队列，绑定关系

```java
//实例化交换机，如果rabbitmq已经创建可不用代码维护
@Component
public class RabbitmqExchange {

    /** 通用topic交换机 */
    @Bean
    public TopicExchange userEventExchange() {
        //String name, boolean durable, boolean autoDelete, Map<String, Object> arguments
        return new TopicExchange(RabbitmqUserConstant.EXCHANGE.EVENT, true, false);
    }

    /** 延迟交换机<安装了rabbitmq_delayed_message_exchange插件> */
    @Bean
    public CustomExchange userDelayedExchange() {
        Map<String, Object> args = Maps.newHashMap();
        args.put("x-delayed-type", "topic");
        return new CustomExchange(RabbitmqUserConstant.EXCHANGE.EVENT_DELAYED, "x-delayed-message", true, false, args);
    }
}

@Component
public class RabbitmqTaskQueue {

    /** 任务队列声明 <声明队列、队列属性> */
    @Bean
    public Queue taskQueue() {
        //String name, boolean durable, boolean exclusive, boolean autoDelete, @Nullable Map<String, Object> arguments
        return new Queue(RabbitmqUserConstant.QUEUE.TASK, true, false, false);
    }
	
    /** 任务队列通过路由键和交换机绑定关系 */
    @Bean
    public Binding taskBinding() {
        //String destination, Binding.DestinationType destinationType, String exchange, String routingKey, @Nullable Map<String, Object> arguments
        return new Binding(RabbitmqUserConstant.QUEUE.TASK, Binding.DestinationType.QUEUE, RabbitmqUserConstant.EXCHANGE.EVENT, RabbitmqUserConstant.ROUTINGKEY.TASK, null);
    }
}
```

#### 2.7.3 业务消息发送和消费
##### 2.7.3.1 发送消息
```java
@RestController
public class DemoController {
    /** rabbitmq客户端 */
    @Autowired
    private RabbitProducerClient producerClient;

    /** 发送rabbitmq消息 */
    @GetMapping("/sendMq")
    public R sendMq(String msg) {
        JSONObject data = new JSONObject();
        data.put("message", msg);
        data.put("time", System.currentTimeMillis());
        /**
         * 业务消息建造者<MessageBuilder>构建消息
         * messageId: 消息唯一ID，可不设置，默认IdUtil.simpleUUID()方法创建
         * messageType消息类型：
         *              MessageType.RAPID      迅速消息：不保证消息的可靠性，也不做confirm确认
         *              MessageType.CONFIRM    确认消息：不保证消息的可靠性，但是会做消息的confim确认
         *              MessageType.RELIANT    可靠消息：保证消息的100%的可靠性投递，定时自动补偿
         * topic：消息要发送的交换机
         * routingKey：消息要发送的路由键（根据不同的路由规则，将交换机上的消息投递到不同的队列）
         * delayMills: 消息延迟发送的时间（单位/ms），队列必须是延迟队列才会生效
         * attributes：消息内容，Map<String, Object>类型用于存放业务消息
         */
        Message message = MessageBuilder.create()
                .messageId(IdUtil.simpleUUID())
                .messageType(MessageType.RELIANT)
                .topic(RabbitmqUserConstant.EXCHANGE.EVENT)
                .routingKey(RabbitmqUserConstant.ROUTINGKEY.TASK)
                .delayMills(5000)
                .attributes(data).build();
        //使用rabbit封装的api客户端发送消息
        producerClient.send(message);


        List<Message> messageList = Lists.newArrayList();
        messageList.add(message);
        messageList.add(message);
        //使用rabbit封装的api客户端发送批量消息，注：批量消息默认迅速消息类型，不保证可靠性
        producerClient.send(messageList);


        //使用rabbit封装的api客户端发送带回调方法的消息，注：带回调消息默认confirm消息类型，不保证可靠性，会做confirm确认
        producerClient.send(message, new SendCallback() {
            @Override
            public void onSuccess() {
                log.info("消息发送成功，messgeId：{}", message.getMessageId());
                //do something
            }
            @Override
            public void onFailure(String reason) {
                log.error("消息发送失败，messgeId：{}，reason：{}", message.getMessageId(), reason);
                //do something
            }
        });
        return R.ok();
    }
}
```

##### 2.7.3.2 监听消息并消费

```java
@Component
public class RabbitMqListener {

    //queues：要监听的消息，数组，可监听多个队列
    @RabbitListener(queues = RabbitmqUserConstant.QUEUE.TASK)
    public void sms(com.zsk.loaned.base.rabbit.api.Message msg, Message message, Channel channel) {
        Map<String, Object> data = msg.getAttributes();
        log.info("业务消息：", data);
        //do something
        
        //deliveryTag：该消息的标识，通道内递增，用于签收消息
        //multiple：是否批量， true：将一次性ack所有小于deliveryTag的消息。
        //requeue：被拒绝的是否重新入队列。
        long deliveryTag = message.getMessageProperties().getDeliveryTag();
        try {
            channel.basicAck(deliveryTag, false);			//手动签收消息
//          channel.basicNack(deliveryTag, false, false); 	//表示签收失败
//          channel.basicReject(deliveryTag, false);		//拒签，第二个参数表示是否重新入队
        } catch (IOException e) {
            //一般是网络原因，确认失败
        }
    }
}
```

