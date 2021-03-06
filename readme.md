# 微信公众号配置服务器的时候，一直报 token 验证失败原因

- [我的坑] 在验证的 signature 相等的时候，返回了的字符串带了引号，要返回不带引号的字符串
  >         ctx.body = "hh" + echostr  但是  hh放在后面却可以 echostr + 'hh'

# wechat-lib 目录

存放和微信 API 相关常用的一些中间件函数，模板或者认证文件

# config 目录

配置文件 包括端口号，微信相关配置，图床的配置

# 发送过来的数据处理

（解析+数据的拼接 / 消息的加解密）

> POST 路由请求处理 -> XML 数据包解析 -> 判断消息类型和内容
> XML 数据 -> JSON 数据 -> 拼装为数据片段 -> 交给 KOA

# 回复的数据处理

> 把数据包装成 XML 的格式 -> 返回给微信服务器
> 注意：回复数据的格式非常严谨，空格要格外注意

- 问题

1. 回复的方式都是写死，而且不能全部东西都在中间件中维护，这样子这个中间件太庞大了，而且杂糅了业务的代码
   > 回复的逻辑不应该和中间件杂糅在一起，而是抽象出来
2. 而且回复之后，这个动作也是在中间件里面去做的，而且也是用 xml 格式进行拼接的，不合理
   > 回复的内容应该是拼接，应该是一个模板，而不是固定的格式

封装了中间件 => 把业务代码和逻辑代码分离出来
根据发送过来的消息内容来进行回复策略的控制，对应渲染一个 XML 的模板

### 容易出错的地方

tpl 模板字符串拼接

# access_token 票据

- 有效期两个小时
- 新老 token 在五分钟内平滑过渡

我和微信交互的钥匙 ：通过 request-promise 来获取 access_token 然后存到 mongoDB 中

# 生成二维码

1. 需要先获取到二维码 ticket，再根据 ticket 生成二维码

# 公众号菜单

1. 增删改
2. 菜单自定义事件 （用户与微信公众号产生的一种联系）
3. 网页授权获取用户资料
4. Pug 模板引擎接入 => 网页渲染
5. 票据 Ticket 网页签名验证 JS-SDK => 存数据库
6. JS-SDK 语言查询电影

# JS-SDK 使用

为了要连接 JS，要创建一个页面，所以使用到了模板引擎来生成页面，这里用到的是 pug（前身是 jade），它在 koa 中要结合 koa-view 来使用

# 获取票据

要使用 JS-SDK,就要获取到签名值，签名值需要获取到微信的 ticket
