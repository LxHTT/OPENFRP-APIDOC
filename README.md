# OpenFrp OPENAPI 使用指南

**欢迎使用 OpenFrp开放映射 API，本指南会帮助您使用我们所提供的API及相关内容。**

>## 阅读前请注意
>
>1. **注意：** OpenFrp API 均使用 POST/GET 方式请求，并
>JSON 格式请求。
>
>2. 本文将采用 Postman 工具做请求示例，您可自行前往 Postman 官网下载参照。
>
>3. 除本文所列出的 API 以外，您可自行使用网络抓包工具抓取 OpenFrp 相关的 API 请求，这不会违反用户协议、服务条款等。在传输时，我们采用明文方式传输数据，使用HTTPS保持安全加密。
>
>4. 本文中所显示的大多数密钥、会话ID、用户账户与密码均为虚构，不可用于登录等操作。一般情况下，我们会使用演示账户（test@test.com）来进行操作。本文将尽可能保证获取的量一致。
>

**全局键值说明:**

```json
{
    ...
    "flag": true,
    "msg": "Message"
}
```

 \* OpenFrp API 所有API均包含 ``flag``、``msg`` 两个键值，``flag`` 键值用于确定请求是否成功，返回值为 ``true``，``msg`` 键值用于返回API的消息，例如“登录成功”、“OK”等。需要您注意。

***

**目前, OpenFrp API地址为：**
>**<https://of-dev-api.bfsea.xyz>**

您可通过在地址后添加路径来访问API，下文仅向您提供相关API路径以供您参考。

***

## 1. Login 登录API

>API路径：
>/user/login

* 此 API 可帮助您通过账号密码登录到 OpenFrp，除获取节点信息和获取公告外，均需要登录获取会话ID与API认证信息才可用。

本 API 需要这些内容：用户账户（用户名或邮箱）、用户密码

### 请求示例

请求类型：``POST``

请求地址：``https://of-dev-api.bfsea.xyz/user/login``

请求内容：

```json
{
    "user": "test@test.com",
    "password": "test@test.com"
} 
```

>*``User``项支持用户名或邮箱(不区分大小写)，``Password``项请使用明文传输(区分大小写)*

在请求正常的情况下，您会得到以下返回值：

```json
{
    "data": "0ea7223e83d0482cb76eae26b9f7a602",
    "flag": true,
    "msg": "登录成功！"
}
```

> *``data`` 项为本次登录的会话ID(长期可用，可保存到文件中备用)。  ``flag`` 项为API请求状态，针对所有OpenFrp API均可用，返回值 ``true`` 则请求正常。``msg`` 项为登录后服务器的返回信息，可用于登录确认。*

在收到返回的同时，您需要同时读取返回值 **``Header``** 中的 ``Authorization``内容，并将其保存为变量备用。在文档标注 *``Header``* 的条目需要在请求中的 **``Header``** 附带``Authorization``及其内容。

如您获取到的Header中的``Authorization``为

```text
OPENFRPeyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIwZWE3MjIzZTgzZDA0ODJjYjc2ZWFlMjZiOWY3YTYwMiIsImV4cCI6MTY2NzgwMDUzNH0.jx3VZqUYNhtOYCauHHHb0L9gmQTLBelIsz7hN8zPdJPdpB5paPPYTG1tT7PMVWyGcoOezcyNSKWMpOPZChVuTw
```

请将其添加到您请求操作的Header中，如：
``Authorization:OPENFRPeyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIwZWE3MjIzZTgzZDA0ODJjYjc2ZWFlMjZiOWY3YTYwMiIsImV4cCI6MTY2NzgwMDUzNH0.jx3VZqUYNhtOYCauHHHb0L9gmQTLBelIsz7hN8zPdJPdpB5paPPYTG1tT7PMVWyGcoOezcyNSKWMpOPZChVuTw``

*此内容可能不时更新，需要您注意。请不要使用文档中的内容。*

***

## 2. 获取用户信息API *``Header``*

>API路径：
>/frp/api/getUserInfo

* 此 API 可帮助您获取用户账户的所有信息，非常有用。

本 API 需要用户已登录，程序已获取用户的会话ID和Authorization内容，并将Authorization写入到header中。

### 请求示例

请求类型：``POST``

请求地址：``https://of-dev-api.bfsea.xyz/frp/api/getUserInfo``

请求内容：

```json
{
    "session": "0ea7223e83d0482cb76eae26b9f7a602"
}
```

> *``session`` 值请填写登录时获取的会话ID，会话ID有效期为4小时，可能需要每4小时更新一次(重新登录)。*

在请求正常的情况下，您会得到以下返回值：

```json
    {
    "data": {
        "outLimit": 1536,
        "used": 0,
        "token": "e900d8f2498202114ec2e9b0597dfb66",
        "realname": false,
        "regTime": 1626512283.000000000,
        "inLimit": 1536,
        "friendlyGroup": "普通用户",
        "proxies": 4,
        "id": 4,
        "email": "test@test.com",
        "username": "test",
        "group": "normal",
        "traffic": 2171
    },
    "flag": true,
    "msg": "ok"
}
```

> *返回值解释：*
> ``data`` 下的键值
> 键名        | 值内容意
> ----------- | ----------------------------  
> outLimit    | 上行带宽（Kbps）
> used        | 已用隧道（条）
> token       | 用户密钥（32位字符）
> realname    | 是否已进行实名认证（已认证为 ``true`` ，未认证为 ``false`` 。）
> regTime     | 注册时间（Unix时间戳）
> inLimit     | 下行带宽（Kbps）
> friendlyGroup | 用户组名称（文字格式友好名称，可直接输出显示。）
> proxies     | 总共隧道条数（条）
> id          | 用户注册ID
> email       | 用户注册邮箱
> username    | 用户名（用户账户）
> group       | 用户组（系统识别标识）（``normal`` 为普通用户）
>traffic      | ~~红绿灯~~ 剩余流量（Mib）

***

## 3. 获取用户隧道列表 *``Header``*

>API路径：/frp/api/getUserProxies

* 此 API 可帮助您获取用户当前账户下的所有隧道信息（隧道列表）。

本 API 需要用户已登录，程序已获取用户的会话ID和Authorization内容，并将Authorization写入到header中。

### 请求示例

请求类型：``POST``

请求地址：``https://of-dev-api.bfsea.xyz/frp/api/getUserProxies``

请求内容：

```json
{
    "session": "0ea7223e83d0482cb76eae26b9f7a602"
}
```

> *``session`` 值请填写登录时获取的会话ID，会话ID有效期为4小时，可能需要每4小时更新一次(重新登录)。*

在请求正常的情况下，您会得到以下返回值：

```json
{
 "data": {
  "total": 2,
  "list": [{
   "id": 11451,
   "proxyName": "test",
   "proxyType": "tcp",
   "localIp": "127.0.0.1",
   "localPort": 1234,
   "useEncryption": true,
   "useCompression": false,
   "domain": "",
   "locations": "",
   "hostHeaderRewrite": "",
   "remotePort": 11451,
   "sk": "",
   "headerXFromWhere": "",
   "status": true,
   "node": 5,
   "lastUpdate": 1641049420.000000000,
   "lastLogin": 1660474229.000000000,
   "friendlyNode": "杭州多线1",
   "online": false,
   "connectAddress": "cn-hz-bgp-1.openfrp.top:11451"
  }, {
   "id": 19198,
   "proxyName": "test2",
   "proxyType": "tcp",
   "localIp": "127.0.0.1",
   "localPort": 1234,
   "useEncryption": true,
   "useCompression": true,
   "domain": "",
   "locations": "",
   "hostHeaderRewrite": "",
   "remotePort": 11451,
   "sk": "",
   "headerXFromWhere": "",
   "status": true,
   "node": 21,
   "lastUpdate": 1642085623.000000000,
   "lastLogin": 1660474299.000000000,
   "friendlyNode": "北京联通",
   "online": false,
   "connectAddress": "cn-bj-plc-300.openfrp.cc:11451"
  },{
    ...
  }]
 },
 "flag": true,
 "msg": "ok"
}
```

> *返回值解释：*
> ``data`` 数组的每一个组都代表着一条隧道，键值均可使用本表参考。 ``total`` 值为该用户的隧道总数。
> 键名        | 值内容意
> ----------- | ----------------------------  
> id    | 隧道ID（数字）
> proxyName        | 隧道名称（文本）
> proxyType       | 隧道类型（包括: ``tcp udp http https stcp xtcp``）
> localIp    | 隧道的本地IP地址
> localPort     | 隧道的本地端口
> useEncryption     | 启用数据加密（``true``/``false``）
> useCompression | 启用数据压缩（``true``/``false``）
> domain     | 隧道绑定的域名（仅HTTP/S）
> locations          | 用户注册ID
> email       | 用户注册邮箱
> username    | 用户名（用户账户）
> group       | 用户组（系统识别标识）（``normal`` 为普通用户）
>traffic      | ~~红绿灯~~ 剩余流量（Mib）

***