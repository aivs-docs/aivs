# OAuth 回调Service
为了方便测试，提供OAuth 回调服务。

##1. 产生申请令牌的URL
#### 请求
使用者发送如下请求以获得申请令牌的URL。
```
GET /device_oauth/{appid}/login
Host https://aivs-stag.api.xiaomi.net
  
device_id={deviceid}&request_id={requestid}
```
**appid**：在小爱开放平台注册的应用id。（目前此service只支持应用id 407555958751889408）
**deviceid**：设备id。appid+deviceId唯一确定一台设备。service以设备为粒度授权。
**requestid**：请求id，应保证每次请求唯一。
#### 响应
Service会拼接申请令牌的Url，并通过response下发。
```
https://account.xiaomi.com/oauth2/authorize?client_id={appid}&redirect_uri={redirecturl}&response_type=code&device_id={deviceid}&pt=1&state={state}
```
**appid**：应用id与请求一致。
**deviceid**：设备id。与请求一致。
**redirecturl**：回调地址。（目前写死指向本机回调地址）。
**state**：随机产生字符串。

使用者可以使用返回的URL直接申请授权，不需要任何修改。

#### 示例
请求
```
https://aivs-stag.api.xiaomi.net/device_oauth/407555958751889408/login?device_id=handevice&request_id=1234
```
响应
```
https://account.xiaomi.com/oauth2/authorize?client_id=407555958751889408&redirect_uri=https%3A%2F%2Faivs-stag.api.xiaomi.net%2Fdevice_oauth%2F407555958751889408%2Fcallback&response_type=code&device_id=handevice&pt=1&state=4adb127c45c454635380f96a6c42c47d931ba700
```
##2. 申请授权及用户登录
使用者直接使用浏览器访问上一阶段产生的URL申请授权。
如果没有登陆过小米开发平台，则会跳转到登录页面。用户在页面上登录后，会跳转到授权页面。
用户授权后，service会自动处理回调请求以及申请令牌。
最终会直接返回下发的令牌到浏览器。

##3. 查询令牌
#### 请求
授权成功后使用者可以访问如下URL查询Token：
```
GET /device_oauth/{appid}/token
Host https://aivs-stag.api.xiaomi.net
  
device_id={deviceid}&request_id={requestid}
```
**appid**：应用id。与申请token时保持一致。
**deviceid**：设备id。与申请token时保持一致。
**requestid**：请求id。与申请token时保持一致。

#### 响应
Service会将小米开放平台授予的Token直接通过response下发。

#### 示例
请求
```
https://aivs-stag.api.xiaomi.net/device_oauth/407555958751889408/token?device_id=handevice&request_id=1234
```
响应

```json
{
    "access_token":"V3_Wn_BIsM1Q5T5hPUfFp8Phel1a0uiIOGk3gR815QRDnf9x7h3X2nhnqXSLKIPsYlSNyhcJLBsjnqEdH1pnZNZ94vD3QehrpnxYTd2xg_yJUXGTu0eVxliHmAeu0rHjZZdsyPpxnyoW10ET-ro1D5HbHG2IYs-kvYaVN_fThsYhCU",
    "refresh_token":"R3_YnElv8rwKppsKG-q1HhIP3uP2CQV4lFjx0fd6FL3jMaEUYSml8-KlBE_ABd0ZJzxMeeEWW0KSN7CxEcLqm_Djh9RWhxBdzSIhWmdJ267vMmEnRUO8uPJMMX_hrjspL6A1eSmSNs3VCxQfIesR6YOSVJlI6tY8975bDoRiCUojIc",
    "mac_key":"xts4BSI4LAPFCVKRWHi0y9__qwQ",
    "mac_algorithm":"HmacSHA1",
    "device_id":"handevice",
    "open_id":"ZQ+XQwHaDlvt8pPvnDzaAgbfr8aKTFCs",
    "scope":"1 20000 3 6000",
    "token_type":"mac",
    "expires_in":7200
}
```

##4. 注意

本服务只是方便测试使用，需要注意以下事项：

1. 本服务只负责获取令牌，不负责维护令牌。令牌过期后使用者需要自己刷新token或申请新的令牌。
2. 本服务查询令牌功能只保存5分钟内申请的令牌。超过时间不会返回令牌信息。但是此时如果令牌没有过期则仍然可以正常使用。
3. 本服务目前只支持应用id **407555958751889408**。如有其它应用需要使用本服务，需要在回调列表中添加本服务地址（https://aivs-stag.api.xiaomi.net/device_oauth），并且需与开发者联系添加白名单。

##5. 参考资料
[OAuth2.0 协议原理简介](https://dev.mi.com/console/doc/detail?pId=711)
[授权码授权模式](https://dev.mi.com/console/doc/detail?pId=707)
[令牌更新接口](https://dev.mi.com/console/doc/detail?pId=712)
[令牌生命周期说明](https://dev.mi.com/console/doc/detail?pId=766)