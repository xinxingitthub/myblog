### OAuth 就是一种授权机制

```
参考地址：https://mp.weixin.qq.com/s?__biz=MzI1NDY0MTkzNQ==&mid=2247488209&idx=2&sn=19b1e44fbb1f4c1210f0fa92a618d871&scene=21#wechat_redirect
```

### 令牌与密码

```
(1）令牌是短期的，到期会自动失效，用户自己无法修改。密码一般长期有效，用户不修改，就不会发生变化。

（2）令牌可以被数据所有者撤销，会立即失效。以上例而言，屋主可以随时取消快递员的令牌。密码一般不允许被他人撤销。

（3）令牌有权限范围（scope），比如只能进小区的二号门。对于网络服务来说，只读令牌就比读写令牌更安全。密码一般是完整权限。
```

### OAuth2

```
OAuth2 是 OAuth 协议的下一版本，但不向下兼容 OAuth 1.0。传统的 Web 开发登录认证一般都是基于 session 的，
但是在前后端分离的架构中继续使用 session 就会有许多不便，
因为移动端（Android、iOS、微信小程序等）要么不支持 cookie（微信小程序），
要么使用非常不便，对于这些问题，使用 OAuth2 认证都能解决。
```

### OAuth2 四种授权模式

1. 授权码模式：常见的第三方平台登录功能基本都是使用这种模式
2. 简化模式：简化模式是不需要客户端服务器参与，直接在浏览器中向授权服务器申请令牌（token），一般如果网站是纯静态页面则可以采用这种方式
3. 密码模式：密码模式是用户把用户名密码直接告诉客户端，客户端使用说这些信息向授权服务器申请令牌（token）。这需要用户对客户端高度信任，例如客户端应用和服务提供商就是同一家公司，我们自己做前后端分离登录就可以采用这种模式。
4. 客户端模式：客户端模式是指客户端使用自己的名义而不是用户的名义向服务提供者申请授权，严格来说，客户端模式并不能算作 OAuth 协议要解决的问题的一种解决方案，但是，对于开发者而言，在一些前后端分离应用或者为移动端提供的认证授权服务器上使用这种模式还是非常方便的。

**授权码模式**

1. 在授权码模式中，我们分授权服务器和资源服务器，授权服务器用来派发 Token，拿着 Token 则可以去资源服务器获取资源，这两个服务器可以分开，也可以合并。
2. https://wx.qq.com/oauth/authorize?response_type=code&client_id=javaboy&redirect_uri=www.javaboy.org&scope=all
   - response_type 表示授权类型，使用授权码模式的时候这里固定为 code，表示要求返回授权码（将来拿着这个授权码去获取 access_token）。
   - client_id 表示客户端 id，也就是我应用的 id。有的小伙伴对这个不好理解，我说一下，如果我想让我的 www.javaboy.org 接入微信登录功能，我肯定得去微信开放平台注册，去填入我自己应用的基本信息等等，弄完之后，微信会给我一个 APPID，也就是我这里的 client_id，所以，从这里可以看出，授权服务器在校验的时候，会做两件事：1.校验客户端的身份；2.校验用户身份。
   - redirect_uri 表示用户登录在成功/失败后，跳转的地址（成功登录微信后，跳转到 www.javaboy.org 中的哪个页面），跳转的时候，还会携带上一个授权码参数。
   - scope 表示授权范围，即 www.javaboy.org 这个网站拿着用户的 token 都能干啥（一般来说就是获取用户非敏感的基本信息）。

3. 接下来第四步，www.javaboy.org 这个网站，拿着第三步获取到的 code 以及自己的 client_id 和 client_secret 以及其他一些信息去授权服务器请求令牌，微信的授权服务器在校验过这些数据之后，就会发送一个令牌回来。这个过程一般是在后端完成的，而不是利用 js 去完成。
4. 接下来拿着这个 token，我们就可以去请求用户信息了。

*一般情况下我们认为授权码模式是四种模式中最安全的一种模式，因为这种模式我们的 access_token 不用经过浏览器或者移动端 App，是直接从我们的后台发送到授权服务器上，这样就很大程度减少了 access_token 泄漏的风险。*

**简化模式**

1. https://wx.qq.com/oauth/authorize?response_type=token&client_id=javaboy&redirect_uri=www.javaboy.org&scope=all
   - 这里的参数和前面授权码模式的基本相同，只有 response_type 的值不一样，这里是 token，表示要求授权服务器直接返回 access_token。

2. 用户点击我这个超链接之后，就会跳转到微信登录页面，然后用户进行登录。
3. 用户登录成功后，微信会自动重定向到 redirect_uri 参数指定的跳转网址，同时携带上 access_token，这样用户在前端就获取到 access_token 了。

*简化模式的弊端很明显，因为没有后端，所以非常不安全，除非你对安全性要求不高，否则不建议使用。*

**密码模式**

```
密码模式在 Spring Cloud 项目中有着非常广泛的应用，
这块松哥在本系列后面的文章中会重点讲解，这里我们先来了解下密码模式是怎么一回事
```

```
密码模式有一个前提就是你高度信任第三方应用，举个不恰当的例子：如果我要在 www.javaboy.org 这个网站上接入微信登录，
我使用了密码模式，
那你就要在 www.javaboy.org 这个网站去输入微信的用户名密码，
这肯定是不靠谱的，所以密码模式需要你非常信任第三方应用。
```

1. https://wx.qq.com/oauth/authorize?response_type=password&client_id=javaboy&username=江南一点雨&password=123
   - 这里的参数和前面授权码模式的略有差异，response_type 的值不一样，这里是 password，表示密码式，另外多了用户名/密码参数，没有重定向的 redirect_uri ，因为这里不需要重定向。

2. 微信校验过用户名/密码之后，直接在 HTTP 响应中把 access_token 返回给客户端。

**客户端模式**

1. GET https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&client_id=APPID&client_secret=APPSECRET
   - grant_type，获取access_token填写client_credential
   - client_id 和 client_secret 用来确认客户端的身份

2. 授权服务器通过验证后，会直接返回 access_token 给客户端。

*大家发现，在这个过程中好像没有用户什么事了！是的，客户端模式给出的令牌，就是针对第三方应用的，而不是针对用户的。*

![截图](357c8ea28a9f9e5f7e32573f3da9459b.png)

<br/>

### 示例：

```
https://blog.csdn.net/qq_41910048/article/details/104997710
https://www.cnblogs.com/crazy-lc/p/12361118.html
```