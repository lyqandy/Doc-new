/*
Title: JavaScript
Sort: 37
*/


## JavaScriptSDK接口说明

```javascript
var Matchvs = require("matchvs.all");
var engine = new Matchvs.MatchvsEngine();
```

#### 说明
- 根据上面的代码片段获取Matchvs引擎的一个实例，接下来可以调用这个实例的方法实现联网对战功能。
- 建议在获取一个实例之后，将其作为单例或全局变量。  

## response

```javascript
var Matchvs = require("matchvs.all");
var response = new Matchvs.MatchvsResponse();
```

#### 说明
- 根据上面的代码片段获取Matchvs引擎的一个实例，接下来可以调用这个实例的回调方法实现联网对战功能。
- 建议在获取一个实例之后，将其作为单例或全局变量。

## init

**注意** 发布之前须到官网控制台申请“发布上线”，申请通过后在调用init方法时传“release”才会生效，否则将不能使用release环境。

```javascript
engine.init(response, channel, platform, gameID, appkey, gameVersion)
```

#### 参数

| 参数        | 类型   | 描述                                   | 示例值    |
| ----------- | ------ | -------------------------------------- | --------- |
| response    | object | 回调对象                               | {}        |
| channel     | string | 渠道，固定值                           | "Matchvs" |
| platform    | string | 平台，选择测试or正式环境               | "alpha"   |
| gameID      | number | 游戏ID                                 | 200978    |
| appkey      | string | 游戏App key，来自Matchvs控制台游戏信息 |           |
| gameVersion | number | 游戏版本，自定义，用于隔离匹配空间     | 1         |

#### 说明

- response中设置一些回调方法，在执行注册、登录、发送事件等操作对应的方法之后，reponse中的回调函数会被SDK异步调用。
- 在连接至 Matchvs前须对SDK进行初始化操作。此时选择连接测试环境（alpha）还是正式环境（release）。
- 如果游戏属于调试阶段则连接至测试环境，游戏调试完成后即可发布到正式环境运行。
- 如果 Matchvs 服务正在升级，init 接口会放回 510 错误码，开发者可以选择是否需要展示“服务升级”的提示。



#### 错误码

| 错误码 | 含义                                                      |
| ------ | --------------------------------------------------------- |
| 0      | 成功                                                      |
| -1     | 失败                                                      |
| -25    | channel 非法，请检查是否正确填写为 “Matchvs”              |
| -26    | platform 非法，请检查是否正确填写为 “alpha”  或 “release” |

#### 示例

```javascript
var result = engine.init(response,'Matchvs','alpha','200978', "4fd4a67c10e84e259a2c3c417b9114f4", 1);
if(result === 0) {
	console.log(“初始化请求成功”);
}
```

### premiseInit

使用独立部署的游戏调用此接口初始化SDK。

```typescript
function engine.premiseInit(response:MatchvsResponse, endPoint:string, gameID:number):number
```

#### 参数

| 参数     | 类型            | 描述                          | 示例值         |
| -------- | --------------- | ----------------------------- | -------------- |
| response | MatchvsResponse | 回调类型MatchvsResponse的对象 | response       |
| endPoint | string          | 服务配置的域名地址            | test.xxxxx.com |
| gameID   | number          | 服务配置的游戏ID              | 123456         |

#### 返回值

| 错误码 | 含义         |
| ------ | ------------ |
| 0      | 接口调用成功 |
| -1     | 接口调用失败 |

## initResponse

```javascript
response.initResponse(status)
```

#### 参数

| 参数   | 类型   | 描述              | 示例值  |
| ------ | ------ | ----------------- | ------- |
| status | number | 状态返回，200表示成功，其他失败 | 200  |

#### 说明

- response是engine.init方法中传入的对象，init初始化完成之后，会异步回调initResponse方法

#### 示例

```javascript
response.initResponse = this.initResponse.bind(this);

initResponse:function (status) {
    if (status === 200) {
        console.log("初始化成功");
    } else {
        console.log("初始化失败"+status);
    }
}
```


## uninit

```javascript
engine.uninit()
```

#### 说明

SDK反初始化工作

#### 错误码

| 错误码  | 含义   |
| ---- | ---- |
| 0    | 成功   |
| -1   | 失败   |



## registerUser

```javascript
engine.registerUser()
```

#### 返回值

| 错误码 | 含义     |
| ------ | -------- |
| 0      | 成功     |
| -1     | 失败     |
| -2     | 未初始化 |



## registerUserResponse
```javascript
response.registerUserResponse(userInfo)
```

#### 参数userInfo的属性

| 属性   | 类型   | 描述                               | 示例值                                               |
| ------ | ------ | ---------------------------------- | ---------------------------------------------------- |
| userID | number | 用户ID                             | 123456                                               |
| token  | string | 用户Token                          | "XGBIULHHBBSUDHDMSGTUGLOXTAIPICMT"                   |
| name   | string | 用户名称                           | "张三"                                               |
| avatar | string | 头像                               | "http://pic.vszone.cn/upload/head/1416997330299.jpg" |
| status | number | 接口回调状态码,0表示成功，其他失败 | 0 成功                                               |

#### 说明

- response是engine.init方法中传入的对象，调用engine.registerUser注册成功之后，response对象的registerUserResponse方法如果存在则会被调用，调用时传入一个封装了用户信息的参数userInfo。

- 注册用户信息，用以获取一个合法的userID，通过此ID可以连接至Matchvs服务器。一个用户只需注册一次。

> 需要注意：调用此接口成功注册用户后，SDK会缓存用户信息。即使再次调用`regist()`会返回相同的UserID,如需重新注册新的用户须调用SDK的 `LocalStore_Clear()` 函数以清除缓存的用户信息。
>
> 如果需要同时调试多个客户端，则需要打开多个不同的浏览器进行调试。

#### 示例

```javascript
response.registerUserResponse = this.registerUserResponse.bind(this);

registerUserResponse :function (userInfo) {
    if (userInfo.status === 0) {
        console.log("注册成功");
		// 用户ID
		console.log("userID: ", userInfo.userID);
		// token
		console.log("token: ", userInfo.token);
    } else {
        console.log("注册失败"+userInfo.status);
    }
}
```

## login

```javascript
engine.login(userID, token, deviceID)
```

#### 参数

| 参数        | 类型   | 描述                                     | 示例值 |
| ----------- | ------ | ---------------------------------------- | ------ |
| userID      | number | 用户ID，调用注册接口后获取               | 123546 |
| token       | string | 用户token，调用注册接口后获取            | ""     |
| deviceID    | string | 设备ID，用于多端登录检测，请保证是唯一ID | ""     |

#### 返回值

| 错误码 | 含义                         |
| ------ | ---------------------------- |
| 0      | 成功                         |
| -1     | 失败                         |
| -2     | 未初始化，请先调用初始化接口 |
| -3     | 正在初始化                   |
| -5     | 已经登录，请勿重复登录       |
| -6     | 正在登出                     |

#### 示例

```javascript
//userID 和token 在注册回调接口中获得
var DeviceID = 'TestDevice';
var gatewayID = 0;
var result = engine.login(userID,token,DeviceID);
console.log("登录result"+result);
```

## loginResponse

```javascript
response.loginResponse(loginRsp)
```
#### 参数loginRsp的属性

| 属性   | 类型   | 描述                            | 示例值 |
| ------ | ------ | ------------------------------- | ------ |
| status | number | 状态返回 <br>200 成功<br>402 应用校验失败，确认是否在未上线时用了release环境，并检查gameID、appkey 和 secret<br>403 检测到该账号已在其他设备登录<br>404 无效用户 <br>500 服务器内部错误 | 200    |
| roomID | string | 房间号                          | "210039" |

#### 说明

- 登录Matchvs服务端，与Matchvs建立连接。
- 服务端会校验游戏信息是否合法，保证连接的安全性。
- 如果一个账号在两台设备上登录，则后登录的设备会连接失败。
- 如果用户加入房间之后掉线，再重新登录进来，则roomID为之前加入的房间的房间号。

#### 示例

```javascript
loginResponse:function (loginRsp) {
    if (MsLoginRsp.status == 200) {
        console.log("登录成功");
    } else {
        console.log("登录失败"+ MsLoginRsp.status);
    }
}
```

## logout

```javascript
engine.logout(cpProto)
```

#### 参数

| 参数      | 类型     | 描述   | 示例值  |
| ------- | ------ | ---- | ---- |
| cpProto | string | 负载信息 | ""   |

#### 返回值

| 错误码 | 含义   |
| ------ | ------ |
| 0      | 成功   |
| -1     | 失败   |
| -4     | 未登录 |

#### 说明

- 退出登录，断开与Matchvs的连接。



## logoutResponse

```javascript
response.logoutResponse(status)
```

#### 参数

| 参数     | 类型     | 描述                | 示例值  |
| ------ | ------ | ----------------- | ---- |
| status | number | 状态返回，200表示成功 <br>500 服务器内部错误| 200  |



## joinRandomRoom

```javascript
 engine.joinRandomRoom(maxPlayer, userProfile)
```

#### 参数

| 参数          | 类型     | 描述       | 示例值  |
| ----------- | ------ | -------- | ---- |
| maxPlayer   | number | 房间内最大玩家数 | 3    |
| userProfile | string | 玩家简介     | ""   |

#### 返回值

| 错误码 | 含义                                   |
| ------ | -------------------------------------- |
| 0      | 成功                                   |
| -1     | 失败                                   |
| -2     | 未初始化                               |
| -3     | 正在初始化                             |
| -4     | 未登录                                 |
| -7     | 正在创建或者进入房间                   |
| -8     | 已经在房间中                           |
| -20    | 1 <maxPlayer超出范围 ，maxPlayer须≤100 |
| -21    | userProfile 过长，不能超过512个字符    |

#### 说明

- 当房间里人数等于maxPlayer时，房间人满。系统会将玩家随机加入到人未满且没有[joinOver](#joinOver)的房间。
- 如果不存在人未满且没有joinOver的房间，则系统会再创建一个房间，然后将玩家加入到该房间。
- 玩家userProfile的值可以自定义，接下来会通过回调函数（如joinRoomResponse）传给其他客户端。

#### 示例

```javascript
var mxaNumer = 3;
var result = engine.joinRandomRoom(mxaNumer,"I Love China");
console.log("随机匹配result"+result);
```



## joinRoomWithProperties

```javascript
engine.joinRoomWithProperties(matchInfo, userProfile, watchSet)
```

#### 参数

| 参数        | 类型           | 描述     | 示例值 |
| ----------- | -------------- | -------- | ------ |
| matchinfo   | MsMatchInfo    | 配置信息 |        |
| userProfile | string         | 玩家简介 | ""     |
| watchSet    | MVS.MsWatchSet | 观战信息 |        |

#### MsMatchInfo 的属性

| 属性         | 类型   | 描述                         | 示例值                        |
| ------------ | ------ | ---------------------------- | ----------------------------- |
| maxPlayer    | number | 玩家最大人数                 | 3                             |
| mode         | number | 模式可 默认填0               | 0                             |
| canWatch     | number | 是否可以观战 1-可以 2-不可以 | 1                             |
| tags         | object | 匹配属性值                   | {title:"Matchvs",name:"demo"} |
| visibility   | number | 是否可见 0-不可见 1-可见     | 1                             |
| roomProperty | string | 自定义房间附加信息           | “roomProperty”                |

#### MVS.MsWatchSet 的属性

| 属性       | 类型    | 描述                 | 示例值          |
| ---------- | ------- | -------------------- | --------------- |
| cacheMS    | number  | 缓存多久的数据       | 6*1000（6分钟） |
| maxWatch   | number  | 最大人数             | 3               |
| delayMS    | number  | 观看延迟多久后的数据 | 2000            |
| persistent | boolean | 是否持久缓存         | false           |

#### 返回值

| 错误码 | 含义                                   |
| ------ | -------------------------------------- |
| 0      | 成功加入房间                           |
| -1     | 正在加入房间                           |
| -4     | 未登录，请先调用login                  |
| -20    | 1 <maxPlayer超出范围 ，maxPlayer须≤100 |
| -21    | userProfile 过长，不能超过512个字符    |

#### 说明

- 同 joinRandomRoom，对应的回调接口也是 joinRoomResponse

  tags为匹配标签，开发者通过设置不同的标签进行自定义属性匹配，相同MsMatchInfo的玩家将会被匹配到一起。
  
#### 示例

```javascript
var mxaNumer = 3;
var matchinfo = new Matchvs.MsMatchInfo();
matchinfo.maxPlayer =3;
matchinfo.mode = 0;
matchinfo.canWatch = 1;
matchinfo.tags = {"title": "A"};
var result = mvs.engine.joinRoomWithProperties(matchinfo,"I Love China");
console.log("属性匹配result"+result);
```

## joinRoom

```
engine.joinRoom(roomID, userProfile)
```

#### 参数

| 参数        | 类型   | 描述     | 示例值  |
| ----------- | ------ | -------- | ------- |
| roomID      | string | 房间号   | "1344333" |
| userProfile | string | 玩家简介 | ""      |

#### 返回值

| 错误码 | 含义                                |
| ------ | ----------------------------------- |
| 0      | 成功                                |
| -1     | 失败                                |
| -2     | 未初始化                            |
| -3     | 正在初始化                          |
| -4     | 未登录                              |
| -7     | 正在创建或者进入房间                |
| -8     | 已经在房间中                        |
| -21    | userProfile 过长，不能超过512个字符 |

#### 说明

- 客户端可以通过调用该接口加入指定房间，roomID为加入指定房间的房间号
- 指定房间号必须是由 createRoom接口创建的房间。

#### 示例

```javascript
var roomID = ”12456544323432432432432“;
var result = engine.joinRoom(roomID,"I Love China");
console.log("加入指定房间 result"+result);
```


## joinRoomResponse

```javascript
response.joinRoomResponse(status, roomUserInfoList, roomInfo)
```

#### 参数

| 参数             | 类型   | 描述                            | 示例值 |
| ---------------- | ------ | ------------------------------- | ------ |
| status           | number | 状态返回，200表示成功 <br>400 客户端参数错误 <br>404 指定房间不存在 <br>405 房间已满 <br>406 房间已joinOver <br>500 服务器内部错误        | 200    |
| roomUserInfoList | array  | 房间内玩家信息列表              |        |
| roomInfo         | object | 房间信息构成的对象              |        |

#### <span id="roomUserInfo">roomUserInfoList中每个元素包含的属性</span>

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

#### 参数roomInfo的属性

| 属性         | 类型   | 描述                             | 示例值 |
| ------------ | ------ | -------------------------------- | ------ |
| roomID       | string | 房间号                           | "238211" |
| roomProperty | string | 房间属性                         | ""     |
| owner        | number | 房间创建者的用户ID(房主ID)       | 0      |
| state        | number | 状态值 0-未知， 1-打开， 2- 关闭 |        |

#### 说明
- 如果本房间是某个玩家调用joinRandomRoom随机加入房间时创建的，那么roomInfo中的owner为服务器随机指定的房主ID。在调用engine.createRoom主动创建房间时owner为创建房间者（即房主）的ID。以上两种情况下，如果房主离开房间，服务器均会指定下一个房主，并通过`leaveRoomNotify`通知房间其他成员。
- roomUserInfoList用户信息列表是本玩家加入房间前的玩家信息列表，不包含本玩家。
- roomUserInfoList中的玩家的userProfile的值来自于其他客户端调用joinRandomRoom时传递的userProfile的值。

#### 示例

```javascript
response.joinRoomResponse = this.joinRoomResponse.bind(this)
joinRoomResponse : function (status, userInfoList, roomInfo) {
    if (status === 200) {
        console.log("进入房间成功");
		console.log("房间用户列表：", roomUserInfoList);
		console.log("房间信息：", roomInfo);
    } else {
        console.log("进入房间失败"+status);
    }
}
```


## joinRoomNotify

```javascript
response.joinRoomNotify(roomUserInfo)
```

#### 参数

| 参数         | 类型   | 描述                 | 示例值 |
| ------------ | ------ | -------------------- | ------ |
| roomUserInfo | object | 房间新加的用户的信息 |        |

#### 说明
- 某个玩家加入房间之后，如果该房间后来又有其他玩家加入，那么将会收到回调通知，response.joinRoomNotify方法会被SDK调用，调用时传入的roomUserInfo是新加入的其他玩家的信息，不是本玩家的信息。
- roomUserInfo的属性与response.joinRoomResponse中的[roomUserInfoList中的元素包含的属性](#roomUserInfo)相同。

#### 示例

```javascript
response.joinRoomNotify = this.joinRoomNotify.bind(this)
joinRoomNotify : function (roomUserInfo) {
    console.log(roomUserInfo.userID+"加入了房间");
}
```

## joinOver

```javascript
engine.joinOver(cpProto)
```

#### 参数

| 参数    | 类型   | 描述     | 示例值 |
| ------- | ------ | -------- | ------ |
| cpProto | string | 负载信息 | ""     |

#### 返回值

| 错误码 | 含义                            |
| ------ | ------------------------------- |
| 0      | 成功                            |
| -1     | 失败                            |
| -2     | 未初始化                        |
| -3     | 正在初始化                      |
| -4     | 未登录                          |
| -7     | 正在创建或者进入房间            |
| -6     | 不在房间                        |
| -21    | cpProto过长，不能超过 1024 字符 |

#### 说明

- 客户端调用该接口通知服务端：房间人数已够，不要再向房间加人。

#### 示例

```javascript
var result = engine.joinOver("关闭房间");
console.log("joinOver result"+result);
```


## joinOverResponse

```javascript
response.joinOverResponse(joinOverRsp)
```

#### 参数joinOverRsp的属性

| 属性    | 类型   | 描述                            | 示例值 |
| ------- | ------ | ------------------------------- | ------ |
| status  | number | 状态返回，200表示成功<br>400 客户端参数错误 <br>404 用户或房间不存在 <br>403 该用户不在房间 <br>500 服务器内部错误 | 200    |
| cpProto | string | 负载信息                        |        |

#### 说明
- 客户端调用engine.joinOver发送关闭房间的指令之后，SDK异步调用reponse.joinOverResponse方法告诉客户端joinOver指令的处理结果。


#### 示例

```javascript
response.joinOverResponse = this.joinOverResponse.bind(this);
joinOverResponse : function (joinOverRsp) {
    if(joinOverRsp.status == 200) {
        console.log("房间关闭成功");
    } else  {
        console.log("房间关闭失败"+joinOverRsp.status);
    }
}
```


## joinOverNotify

```
response.joinOverNotify(notifyInfo);
```

#### 参数 notifyInfo 的属性

| 属性      | 类型   | 描述               | 示例值 |
| --------- | ------ | ------------------ | ------ |
| roomID    | string | 房间ID             |        |
| srcUserID | number | 发起关闭房间玩家ID |        |
| cpProto   | string | 负载信息           |        |

#### 说明

- 当有人调用了 joinOver 接口，同一房间的其他用户就会收到这个 回调信息。

#### 示例

```javascript
response.joinOverNotify = this.joinOverNotify.bind(this);
joinOverNotify : function (notifyInfo) {
    console.log("房间关闭通知");
	console.log(srcUserID+"关闭了房间");
}
```


## leaveRoom

```javascript
engine.leaveRoom(cpProto)
```

#### 参数

| 参数    | 类型   | 描述     | 示例值 |
| ------- | ------ | -------- | ------ |
| cpProto | string | 负载信息 | ""     |

#### 返回值

| 错误码 | 含义                      |
| ------ | ------------------------- |
| 0      | 成功                      |
| -1     | 失败                      |
| -2     | 未初始化                  |
| -3     | 正在初始化                |
| -4     | 未登录                    |
| -7     | 正在创建或者进入房间      |
| -21    | cpProto过长，不能超过1024 |

#### 说明

- 客户端调用该接口通知服务端该客户端对应的用户要离开房间。

#### 示例

```javascript
var result = engine.leaveRoom("I Love China");
console.log("leaveRoom result"+result);
```

## leaveRoomResponse

```javascript
response.leaveRoomResponse(leaveRoomRsp)
```

#### 参数leaveRoomRsp的属性

| 属性    | 类型   | 描述                            | 示例值 |
| ------- | ------ | ------------------------------- | ------ |
| status  | number | 状态返回，200表示成功<br>400 客户端参数错误 <br>404 房间不存在 <br>500 服务器内部错误| 200    |
| roomID  | string | 房间号                          | "317288" |
| userID  | number | 用户ID                          | 317288 |
| cpProto | string | 负载信息                        |        |

#### 说明
- 客户端调用engine.leaveRoom发送关闭房间的指令之后，SDK异步调用reponse.leaveRoomResponse方法告诉客户端leaveRoom指令的处理结果。

#### 示例

```javascript
response.leaveRoomResponse=this.leaveRoomResponse.bind(this);
leaveRoomResponse : function (leaveRoomRsp) {
    if (leaveRoomRsp.status == 200) {
        console.log("离开房间成功");
    } else {
        console.log("离开房间失败"+leaveRoomRsp.status);
    }
}
```

## leaveRoomNotify

```javascript
response.leaveRoomNotify(leaveRoomInfo)
```

#### 参数 leaveRoomInfo属性


| 参数    | 类型   | 描述                     | 示例值 |
| ------- | ------ | ------------------------ | ------ |
| userID  | number | 刚刚离开房间的用户的ID   | 223545 |
| roomID  | string | 房间号					  |'234566'|
| owner   | number | 房主                     |        |
| cpProto | string | 附加信息                 |        |

#### 说明
- 当同房间中的其他玩家调用leaveRoom发送离开房间的指令之后，本客户端将会收到回调通知，response.leaveRoomNotify方法会被SDK调用，调用时传入的roomUserInfo是离开房间的玩家的信息。
- roomUserInfo的属性与response.joinRoomResponse中的[roomUserInfoList中的元素包含的属性](#roomUserInfo)相同。

#### 示例

```javascript
response.leaveRoomNotify = this.leaveRoomNotify.bind(this);
leaveRoomNotify : function (leaveRoomInfo) {
	console.log(leaveRoomInfo.userID+'离开了房间,roomID是'+leaveRoomInfo.roomID);
	console.log("新的房主是:"+leaveRoomInfo.owner);
}
```


## createRoom

```javascript
engine.createRoom(createRoomInfo, userProfile,watchSet)
```

#### 参数

| 参数           | 类型                    | 描述           | 示例值 |
| -------------- | ----------------------- | -------------- | ------ |
| createRoomInfo | object<MsCreateRoomRsp> | 创建房间的信息 |        |
| userProfile    | string                  | 玩家简介       | ""     |
| watchSet       | object<MVS.MsWatchSet>  | 观战服务参数   |        |

#### createRoomInfo 的属性

| 属性         | 类型   | 描述                         | 示例值         |
| ------------ | ------ | ---------------------------- | -------------- |
| roomName     | string | 房间名称                     | "MatchvsRoom"  |
| maxPlayer    | number | 最大玩家数                   | 3              |
| mode         | number | 模式                         | 1              |
| canWatch     | number | 是否可以观战 1-可以 2-不可以 | 2              |
| visibility   | number | 是否可见默认 0不可见 1可见   | 1              |
| roomProperty | string | 房间属性                     | "roomProperty" |

#### MVS.MsWatchSet

| 属性       | 类型   | 描述                 | 示例值          |
| ---------- | ------ | -------------------- | --------------- |
| cacheMS    | number | 缓存多久的数据       | 6*1000（6分钟） |
| maxWatch   | number | 最大人数             | 3               |
| delayMS    | number | 观看延迟多久后的数据 | 2000            |
| persistent | number | 是否持久缓存         | false           |

#### 返回值

| 错误码 | 含义                          |
| ------ | ----------------------------- |
| 0      | 成功                          |
| -1     | 失败                          |
| -2     | 未初始化                      |
| -3     | 正在初始化                    |
| -4     | 未登录                        |
| -7     | 正在创建或者进入房间          |
| -8     | 已在房间                      |
| -21    | userProfile 过长，不能超过512 |

#### 说明

- 开发者可以在客户端主动创建房间，创建成功后玩家会被自动加入该房间，创建房间者即为房主，如果房主离开房间则Matchvs会自动转移房主并通知房间内所有成员，开发者通过设置CreateRoomInfo创建不同类型的房间
- 创建房间时，如果没有设置watchSet 参数，则默认是不可观战的， watchSet 参数要与canWatch参结合使用，如果没有设置观战参数，再获取房间列表的时候 canWatch参数需要设置0。
- 创建房间成功，如果需要再次创建房间需要调用离开房间接口(leaveRoom)先离开当前房间。

#### 示例

```javascript
var createRoom = new Matchvs.MsCreateRoomInfo();
createRoom.name = 'roomName';
createRoom.maxPlayer = 3;
createRoom.mode = 0;
createRoom.canWatch = 1;
createRoom.visibility = 1;
createRoom.roomProperty = '白天模式';
var result = engine.createRoom(createRoom,"Matchvs");
console.log("createRoom result"+result);
```

## createRoomResponse

```javascript
response.createRoomResponse(CreateRoomRsp)
```

#### 参数

| 参数   | 类型   | 描述              | 示例值 |
| ------ | ------ | ----------------- | ------ |
| status | number | 状态返回，200表示成功<br>400 客户端参数错误 <br>500 服务器内部错误 | 200    |
| roomID | string | 房间号            |"210039"|
| owner  | number | 房主              | 210000 |

#### 说明

- response是engine.createRoom方法中传入的对象，createRoom完成之后，会异步回调createRoomResponse方法

#### 示例

```javascript
response.createRoomResponse = this.createRoomResponse.bind(this);
createRoomResponse : function (CreateRoomRsp) {
    if (CreateRoomRsp.status == 200) {
        console.log("创建指定类型房间接口成功");
    } else {
        console.log("创建指定类型房间接口失败 status" + CreateRoomRsp.status);
    }
}
```

## getRoomList

```javascript
engine.getRoomList(filter)
```

#### 参数

| 参数   | 类型   | 描述                                                         | 示例值                                                       |
| ------ | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| filter | object | maxPlayer:最大玩家数;<br />mode:模式;<br />canWatch:是否可以观战<br />roomProperty:房间属性 | maxPlayer:3;<br />mode:0;<br />canWatch:0<br />roomProperty:'roomProperty' |

#### 返回值

| 错误码 | 含义                            |
| ------ | ------------------------------- |
| 0      | 成功                            |
| -1     | 失败                            |
| -2     | 未初始化                        |
| -3     | 正在初始化                      |
| -4     | 未登录                          |
| -7     | 正在创建或者进入房间            |
| -8     | 已在房间                        |
| -21    | filter 过长，总字节不能超过1024 |

#### 说明

- 开发者通过该接口可以获取所有客户端主动创建的房间列表。不同模式(mode)下的玩家不会被匹配到一起，开发者可以利用mode区分竞技模式，普通模式等游戏模式。开发者可以通过设置RoomFilter来对获取的房间进行过滤，getRoomList的过滤规则是并集过滤，与filter中的过滤字段有一个相同都会通过接口返回，getRoomListEx提供了严格匹配。

#### 示例

```javascript
var createRoom = new Matchvs.MsCreateRoomInfo();
createRoom.maxPlayer = 3;
createRoom.mode = 0;
createRoom.canWatch = 1;
createRoom.roomProperty = '白天模式';
var result = engine.getRoomList(createRoom,"I Love China");
console.log("createRoom result"+result);
```

## getRoomListResponse

```javascript
response.getRoomListResponse(status, roomInfos)
```

#### 参数

| 参数      | 类型                | 描述                            | 示例值 |
| --------- | ------------------- | ------------------------------- | ------ |
| status    | number              | 状态返回，200表示成功<br>500 服务器内部错误 | 200    |
| roomInfos | Array<MsRoomInfoEx> |                                 |        |

#### MsRoomInfoEx 的属性

| 属性         | 类型   | 描述                         | 示例值         |
| ------------ | ------ | ---------------------------- | -------------- |
| roomID       | string | 房间ID                       | "123456786"    |
| roomName     | string | 房间名称                     | “matchvsRoom”  |
| maxPlayer    | number | 最大人数                     | 3              |
| mode         | number | 模式                         | 0              |
| canWatch     | number | 是否可以观战 1-可以 2-不可以 | 2              |
| roomProperty | string | 房间属性                     | “roomProperty” |

#### 说明

- response是engine.getRoomList方法中传入的对象，getRoomList完成之后，会异步回调getRoomListResponse方法。

#### 示例

```javascript
response.getRoomListResponse = this.getRoomListResponse.bind(this);
getRoomListResponse : function (status, roomInfo) {
    if (status == 200) {
        console.log("获取房间列表成功");
    } else {
        console.log("获取房间列表失败 status：" + status);
    }
}
```

## getRoomListEx

```
engine.getRoomListEx(filter);
```

**注意** 不参与过滤的条件可以不填，或者填null。

#### 参数 filter属性

| 参数         | 类型   | 描述                                              | 示例值         |
| ------------ | ------ | ------------------------------------------------- | -------------- |
| maxPlayer    | number | 房间最大人数 (0-全部)                             | 3              |
| mode         | number | 模式（0-全部）*创建房间时，mode最好不要填0        | 0              |
| canWatch     | number | 是否可以观战 0-全部 1-可以 2-不可以               | 1              |
| roomProperty | string | 房间属性                                          | “roomProperty” |
| full         | number | 0-全部 1-满 2-未满                                | 0              |
| state        | number | 0-全部 1-开放 2-关闭                              | 0              |
| sort         | number | 0-不排序 1-创建时间排序 2-玩家数量排序 3-状态排序 | 0              |
| order        | number | 0-ASC  1-DESC                                     | 0              |
| pageNo       | number | 页码，0为第一页                                   | 0              |
| pageSize     | number | 每一页的数量应该大于 0                            | 10             |

#### 返回值

| 错误码 | 含义                            |
| ------ | ------------------------------- |
| 0      | 成功                            |
| -1     | 失败                            |
| -2     | 未初始化                        |
| -3     | 正在初始化                      |
| -4     | 未登录                          |
| -7     | 正在创建或者进入房间            |
| -21    | filter 过长，总字节不能超过1024 |

#### 说明

- getRoomListEx 是 getRoomList 接口的扩展功能接口，只能获取调用 createRoom 接口创建的房间，获取房间列表参数必须和createRoom接口创建的房间参数完全一致而且 createRoom中的参数 visibility 必须设置为1(可见)比如：createRoom 参数结构 如下

#### 示例

```
var createRoomInfoEx = new MsRoomFilterEx()
createRoomInfoEx.name = 'roomName';
createRoomInfoEx.maxPlayer = 3;
createRoomInfoEx.mode = 0;
createRoomInfoEx.canWatch = 0;
createRoomInfoEx.visibility = 1;
createRoomInfoEx.roomProperty = '白天模式';
var result = engine.getRoomListEx(createRoomInfoEx,"I Love China");
console.log("getRoomListEx result"+result);
```


## getRoomListExResponse

```
response.getRoomListExResponse(rsp);
```

#### 参数 rsp 的属性

| 参数      | 类型                   | 描述          | 示例值 |
| --------- | ---------------------- | ------------- | ------ |
| status    | number                 | 状态 200 成功 <br>500 服务器内部错误| 200    |
| total     | number                 | 房间总数量    | 2      |
| roomAttrs | Array<MsRoomAttribute> | 房间信息列表  | []     |

#### 参数 MsRoomAttribute 的属性

| 参数         | 类型   | 描述                         | 示例值         |
| ------------ | ------ | ---------------------------- | -------------- |
| roomID       | string | 房间号                       | “”             |
| roomName     | string | 房间名称                     | “roomName”     |
| maxPlayer    | number | 最大人数                     | 3              |
| gamePlayer   | number | 游戏人数                     | 2              |
| watchPlayer  | number | 观战人数                     | 1              |
| mode         | number | 模式                         | 0              |
| canWatch     | number | 是否可以观战 1-可以 2-不可以 | 2              |
| roomProperty | string | 房间属性                     | “roomProperty” |
| owner        | number | 房主                         | 326541         |
| state        | number | 房间状态  1-开放 2-关闭      | 1              |
| createTime   | string | 创建时间                     |                |

#### 说明

- 是接口 getRoomListEx 的回调，参数total  与 roomAttrs 列表length 可能会不同，但是 total 会 >= roomAttrs 列表的 length。

#### 示例

```javascript
response.getRoomListExResponse = this.getRoomListExResponse.bind(this);
getRoomListExResponse : function (rsp) {
    if (rsp.status == 200) {
        console.log("获取房间列表成功");
    } else {
        console.log("获取房间列表失败 status：" + rsp.status);
    }
}
```


## getRoomDetail

```
engine.getRoomDetail(roomID);
```

#### 参数

| 参数   | 类型   | 描述   | 示例值                 |
| ------ | ------ | ------ | ---------------------- |
| roomID | string | 房间ID | "16323532544156875354" |

#### 返回值

| 错误码 | 含义         |
| ------ | ------------ |
| 0      | 调用成功     |
| -1     | 调用失败     |
| -2     | 未初始化     |
| -4     | 未登陆       |
| -7     | 正在加入房间 |
| -3     | 正在初始化   |

#### 说明

- 获取房间详细信息，可获取的时候 createRoom 接口创建的房间。

#### 示例

```
var roomID = "34532423423423";
var result = engine.getRoomDetail(roomID);
console.log("获取房间详情 result"+ result);
```

## getRoomDetailResponse

```
response.getRoomDetailResponse(rsp);
```

#### 参数 rsp 的属性

| 参数         | 类型                  | 描述                                                        | 示例值 |
| ------------ | --------------------- | ----------------------------------------------------------- | ------ |
| status       | number                | 接口状态 200 成功 <br>404 房间不存在 <br>500 服务器内部错误 |        |
| state        | number                | 房间状态 1-开放 2-关闭                                      |        |
| maxPlayer    | number                | 最大人数                                                    |        |
| mode         | number                | 模式                                                        |        |
| canWatch     | number                | 是否可以观战 1-可以 2-不可以                                |        |
| roomProperty | string                | 房间属性                                                    |        |
| owner        | number                | 房主                                                        |        |
| createFlag   | number                | 创建方式 0-未知 1-系统创建 2-玩家创建                       |        |
| userInfos    | Array<MsRoomUserInfo> | 用户列表信息                                                |        |
| watchinfo    | object                | 观战信息                                                    |        |
| brigades     | Array<object>         | 组队列表信息                                                |        |

#### MsRoomUserInfo 的属性

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

#### watchinfo 属性

| 属性       | 类型    | 描述                                         | 示例值 |
| ---------- | ------- | -------------------------------------------- | ------ |
| curWatch   | number  | 房间当前观战者人数                           | 3      |
| persistent | boolean | 观战信息是否持久保存                         | false  |
| maxWatch   | number  | 最大观战人数                                 | 6      |
| delayMS    | number  | 延迟时间，可观看延迟多久的数据（单位毫秒）   | 6000   |
| cacheTime  | number  | 缓存时间，游戏最大能缓存多久的数据(单位毫秒) | 60000  |

#### brigades 属性

| 属性      | 类型          | 描述           | 示例值 |
| --------- | ------------- | -------------- | ------ |
| brigadeID | number        | 大队伍的ID     | 1      |
| teamList  | Array<object> | 小队伍信息列表 |        |

#### teamList 数据项属性

| 属性       | 类型          | 描述       | 示例值                |
| ---------- | ------------- | ---------- | --------------------- |
| teamID     | string        | 小队伍ID号 | 131113213211323121231 |
| capacity   | number        | 小队伍人数 | 5                     |
| mode       | number        | 自定义参数 | 0                     |
| owner      | number        | 队长       | 123456                |
| playerList | Array<object> | 队伍成员   |                       |



#### 说明

- 是 getRoomDetail 接口的回调。

#### 示例

```javascript
response.getRoomDetailResponse = this.getRoomDetailResponse.bind(this);
getRoomDetailResponse : function (rsp) {
    if (rsp.status === 200) {
        console.log("获取房间详情成功");
    } else {
        console.log("获取房间详情失败 status "+ rsp.status);
    }
};
```


## setRoomProperty 

```javascript
engine.setRoomProperty(roomID, roomProperty)
```

#### 参数

| 参数         | 类型   | 描述             | 示例值               |
| ------------ | ------ | ---------------- | -------------------- |
| roomID       | string | 房间号           | "654354323413134354" |
| roomProperty | string | 要修改的房间属性 | "changeRoomProperty" |

#### 返回值

| 错误码 | 含义                                    |
| ------ | --------------------------------------- |
| 0      | 调用成功                                |
| -1     | 调用失败                                |
| -2     | 未初始化                                |
| -3     | 正在初始化                              |
| -4     | 未登陆                                  |
| -7     | 正在加入房间                            |
| -10    | 正在登出                                |
| -11    | 正在离开房间                            |
| -21    | roomProperty 长度过长，不能超过1023字符 |

#### 说明

- 在进入房间后，可以调用 `setRoomProperty` 修改房间的属性。

#### 示例

```
var roomID = "34532423423423";
var newRoomProperty = 'Matchvs';
var result = engine.setRoomProperty(roomID,newRoomProperty);
console.log("修改房间属性 result"+ result);
```


## setRoomPropertyResponse

```typescript
response.setRoomPropertyResponse(rsp);
```

#### 参数 rsp 的属性

| 参数         | 类型   | 描述            | 示例值               |
| ------------ | ------ | --------------- | -------------------- |
| status       | number | 状态值，200成功<br>400 客户端参数错误 <br>404 房间不存在 <br>500 服务器内部错误 | 200                  |
| roomID       | string | 房间号          | "654354323413134354" |
| userID       | number | 玩家            | 123                  |
| roomProperty | string | 修改后的属性值  | "changeRoomProperty" |

#### 说明

- 开发者调用 setRoomProperty 接口，服务器会回调 setRoomPropertyResponse 接口。

#### 示例

```javascript
response.setRoomPropertyResponse = this.setRoomPropertyResponse.bind(this);
setRoomPropertyResponse : function (rsp) {
    if (rsp.status === 200) {
        console.log("修改房间属性成功");
    } else {
        console.log("修改房间属性失败 status "+ rsp.status);
    }
}
```

## setRoomPropertyNotify

```typescript
response.setRoomPropertyNotify(notify);
```

#### 参数 notify 的属性

| 参数         | 类型   | 描述           | 示例值               |
| ------------ | ------ | -------------- | -------------------- |
| roomID       | string | 房间号         | "654354323413134354" |
| userID       | number | 玩家           | 123                  |
| roomProperty | string | 修改后的属性值 | "changeRoomProperty" |

#### 说明

- 调用 `setRoomProperty ` 接口后，其他玩家会收到 这个setRoomPropertyNotify 接口的回调。

#### 示例

```javascript
response.setRoomPropertyNotify = this.setRoomPropertyNotify.bind(this);
setRoomPropertyNotify : function (notify) {
	console.log("修改房间属性通知");
	console.log(notify.userID+"修改了房间属性，新的房间属性是"+notify.roomProperty);
}
```

## sendEvent

```javascript
engine.sendEvent(msg)
```

**注意** 给GameServer发送消息调用sendEventEx()。

#### 参数

| 参数 | 类型   | 描述     | 示例值  |
| ---- | ------ | -------- | ------- |
| msg  | string | 消息内容 | "hello" |

#### 返回值
- 返回值为一个对象，该对象包含以下属性：

| 属性     | 类型   | 描述                                                         | 示例值 |
| -------- | ------ | ------------------------------------------------------------ | ------ |
| result   | number | 错误码，0表示成功，其他表示失败                              | 0      |
| sequence | number | 事件序号，作为事件的唯一标识。客户端发送消息后收到的sendEventResponse 也会收到 sequence 标识，通过此标识来确定这个sendEventResponse 是由哪次sendEvent 发送的。主要用于在游戏中做信息同步的时候，网络传输都有延迟会出现sendEvent与sendEventResponse 收到顺序不同。 | 231212 |

#### result 说明

| 返回码 | 含义                                  |
| ------ | ------------------------------------- |
| 0      | 成功                                  |
| -1     | 失败                                  |
| -2     | 未初始化                              |
| -3     | 正在初始化                            |
| -4     | 未登录                                |
| -7     | 正在创建或者进入房间                  |
| -6     | 未加入房间                            |
| -21    | data 过长, data长度不能超过1024个字符 |

#### 说明

- 在进入房间后即可调用该接口进行消息发送，消息会发给房间里除自己外其他所有成员。回调函数是   sendEventResponse
- 同一客户端多次调用engine.sendEvent方法时，每次返回的sequence都是唯一的。但同一房间的不同客户端调用sendEvent时生成的sequence之间会出现重复。
- 可以发送二进制数据，开发者可以将数据用json、pb等工具先进行序列化，然后将序列化后的数据通过SendEvent的一系列接口发送。

#### 示例

```javascript
var data = engine.sendEvent(msg);
console.log("发送信息 result"+ data.result);
}
```


## sendEventEx

```javascript
engine.sendEventEx(msgType, data, destType, userIDs)
```

#### 参数

| 参数     | 类型   | 描述                                                         | 示例值      |
| -------- | ------ | ------------------------------------------------------------ | ----------- |
| msgType  | number | 消息类型。0表示转发给房间成员；1表示转发给game server；2表示转发给房间成员及game server | 0           |
| data     | string | 消息内容                                                     | "hello"     |
| destType | number | 目标类型。0表示发送目标为目标列表成员；1表示发送目标为除目标列表成员以外的房间成员 | 0           |
| userIDs  | array  | 目标列表                                                     | [1001,1002] |

#### 返回值

- 返回值为一个对象，该对象包含以下属性：

| 属性     | 类型   | 描述             | 示例值 |
| -------- | ------ | ---------------- | ------ |
| result   | number | 错误码，0表示成功，其他表示失败 | 0      |
| sequence | number | 事件序号，作为事件的唯一标识   | 231212 |

#### result 说明

| 返回码 | 含义                                   |
| ------ | -------------------------------------- |
| 0      | 成功                                   |
| -1     | 失败                                   |
| -2     | 未初始化                               |
| -3     | 正在初始化                             |
| -4     | 未登录                                 |
| -7     | 正在创建或者进入房间                   |
| -6     | 未加入房间                             |
| -21    | data 过长， data长度不能超过1024个字符 |
| -23    | msgType 非法                           |
| -24    | desttype 非法                          |

#### 说明

- 在进入房间后即可调用该接口进行消息发送，msgType 参数控制消息发送到哪了， destType 和 userIDs 参数控制消息发送给谁。消息回调接口 sendEventResponse
- 同一客户端多次调用engine.sendEvent方法时，每次返回的sequence都是唯一的。但同一房间的不同客户端调用sendEvent时生成的sequence之间会出现重复。

#### 示例

```javascript
//发送给房间中的全部玩家，destType = 1, userIDs = []
var data = mvs.engine.sendEventEx(0,msg,1,[]);
console.log("发送信息 result"+ data.result);
}
//发送指定玩家 123456，destType = 0, userIDs = [123456]
var data = mvs.engine.sendEventEx(0,msg,0,[123456]);
console.log("发送信息 result"+ data.result);
}
//发送 排除 123456 玩家，destType = 1, userIDs = []
var data = mvs.engine.sendEventEx(0,msg, 1,[123456]);
console.log("发送信息 result"+ data.result);
}
```


## sendEventResponse

```javascript
response.sendEventResponse(sendEventRsp)
```

#### 参数sendEventRsp的属性

| 属性     | 类型   | 描述                                                         | 示例值 |
| -------- | ------ | ------------------------------------------------------------ | ------ |
| status   | number | 状态返回，200表示成功<br>521 gameServer不存在，请检查是否已开启本地调试或在正式环境发布运行gameServer | 200    |
| sequence | number | 事件序号，作为事件的唯一标识，可以参考sendEvent，对这个字段的详细说明 | 231212 |

#### 说明
- 客户端调用engine.sendEvent或engine.sendEventEx发送消息之后，SDK异步调用reponse.sendEventResponse方法告诉客户端消息是否发送成功。

#### 示例

```javascript
response.sendEventResponse = this.sendEventResponse.bind(this);
sendEventResponse :function (sendEventRsp) {
    if (sendEventRsp.status == 200) {
        console.log("发送消息成功");
    } else {
        console.log("发送消息失败 status"+sendEventRsp.status);
    }
}
```

## sendEventNotify

```javascript
response.sendEventNotify(eventInfo)
```

#### 参数eventInfo的属性

| 参数      | 类型   | 描述                                  | 示例值  |
| --------- | ------ | ------------------------------------- | ------- |
| srcUserID | number | 推送方用户ID，表示是谁发的消息        | 321     |
| cpProto   | string | 消息内容，对应[sendEvent](#sendEvent)中的msg参数 | "hello" |

#### 说明

- 在其他客户端调用engine.sendEvent或engine.sendEventEx方法之后，本客户端的response.sendEventNotify会被SDK调用，调用时传入其他玩家的用户ID和发送的消息。

#### 示例

```javascript
response.sendEventNotify = this.sendEventNotify.bind(this);
sendEventNotify : function (eventInfo) {
    console.log(eventInfo);
    console.log('ID为'+eventInfo.srcUserID+'通知了'+eventInfo.cpProto);
}
```


## networkStateNotify

```javascript
response.networkStateNotify(netnotify);
```

#### 参数 netnotify 的属性

| 参数   | 类型   | 描述                                                         | 示例值 |
| ------ | ------ | ------------------------------------------------------------ | ------ |
| roomID | string | 房间ID                                                       |        |
| userID | number | 断开网络的玩家ID                                             |        |
| state  | number | 网络断开状态 1-网络异常，正在重连  2-重连成功 3-重连失败，退出房间 |        |
| owner  | number | 房主ID                                                       |        |

#### 说明

- 在房间中如果有其他玩家网络断开就会收到这个异步回调的接口，通过这个接口就可以知道其他玩家的网络状态啦。

#### 示例

```javascript
response.networkStateNotify = this.networkStateNotify.bind(this);
networkStateNotify : function (eventInfo) {
    console.log("netNotify.owner:" + eventInfo.owner);
}
```


## gameServerNotify

```
response.gameServerNotify(eventInfo);
```

#### 参数 eventInfo 属性

| 参数      | 类型   | 描述                       | 示例值       |
| --------- | ------ | -------------------------- | ------------ |
| srcUserID | number | gameServer推送时 这个值为0 | 0            |
| cpProto   | string | 推送的消息内容             | “gameServer” |

#### 说明

- 开发者有使用 gameServer 的时候，如果有gameServer发送消息到客户端就会收到这个回调，回调的 srcUserID 是固定为0。

#### 示例

```javascript
response.gameServerNotify = this.gameServerNotify.bind(this);
gameServerNotify : function (eventInfo) {
    console.log("推送的消息内容:" + eventInfo.cpProto);
}
```

## kickPlayer

```javascript
engine.kickPlayer(userID, cpProto)
```

#### 参数

| 参数    | 类型   | 描述    | 示例值 |
| ------- | ------ | ------- | ------ |
| userID  | number | 用户ID  | 655444 |
| cpProto | string | 自定义数据 | “kick” |

#### 返回值

| 错误码 | 含义                             |
| ------ | -------------------------------- |
| 0      | 成功                             |
| -1     | 失败                             |
| -2     | 未初始化                         |
| -3     | 正在初始化                       |
| -4     | 未登录                           |
| -7     | 正在创建或者进入房间             |
| -6     | 未加入房间                       |
| -21    | cpProto 过长，不能超过1024个字符 |

#### 说明

- kickPlayer 用于剔除玩家，房间任何人都可以调用这个接口，参数userID 可以是房间内任意一个，自己也可以剔除自己。主要剔除方式由开发者自己制定。
- 如果是房主被踢出房间，服务器会在房间中剩下的玩家中重新指定一个房主通过`kickPlayerResponse`,`kickPlayerNotify`返回给房间中剩余的玩家。

#### 示例

```javascript
var userID = 4324532;
var result = engine.kickPlayer(userID,"I Love China");
console.log(userID+"被踢出游戏 result"+result);
```

## kickPlayerResponse

```javascript
response.kickPlayerResponse(KickPlayerRsp)
```

#### 参数 KickPlayerRsp 的属性

| 参数   | 类型   | 描述         | 示例值     |
| ------ | ------ | ------------ | ---------- |
| status | number | 接口状态 200 成功<br>400 客户端参数错误 <br>404 用户或房间不存在        | status:200 |
| owner  | number | 房主         | 65522      |
| userID | number | 被踢者玩家ID |            |

#### 说明

- response是engine.kickPlayerResponse方法中传入的对象，kickPlayer完成之后，会异步回调kickPlayerResponse方法

#### 示例

```javascript
response.kickPlayerResponse = this.kickPlayerResponse.bind(this);
kickPlayerResponse : function (KickPlayerRsp) {
    if (kickPlayerRsp.status == 200) {
        console.log("踢出指定玩家成功");
    } else {
        console.log("踢出指定玩家失败 status" + kickPlayerRsp.status);
    }
}
```

## kickPlayerNotify

```javascript
response.kickPlayerNotify(KickPlayerNotify)
```

#### 参数

| 参数             | 类型   | 描述                                     | 示例值                                   |
| ---------------- | ------ | ---------------------------------------- | ---------------------------------------- |
| KickPlayerNotify | object | srcUserID:踢人用户ID<br />userID:被踢用户ID<br />cpProto:自定义数据<br />owner:房主 | srcUserID:223333<br />userID:344222<br />cpProto:'kick'<br />owner:223333 |

#### 说明

- 某个玩家被踢之后，房间里的其它玩家会收到回调通知，response.kickPlayerNotify方法会被SDK调用，调用时传入的KickPlayerNotify是踢人的信息。

#### 示例

```javascript
response.kickPlayerNotify = this.kickPlayerNotify.bind(this);
kickPlayerNotify : function (KickPlayerNotify) {
    console.log("踢出指定玩家通知");
}
```


## subscribeEventGroup

```javascript
engine.subscribeEventGroup([confirms], [cancles])
```

#### 参数

| 参数     | 类型  | 描述       | 示例值      |
| -------- | ----- | ---------- | ----------- |
| confirms | array | 订阅组     | ["Matchvs"] |
| cancles  | array | 取消订阅组 | ["hello"]   |

#### 返回值

| 错误码 | 含义                           |
| ------ | ------------------------------ |
| 0      | 成功                           |
| -1     | 失败                           |
| -2     | 未初始化                       |
| -3     | 正在初始化                     |
| -4     | 未登录                         |
| -7     | 正在创建或者进入房间           |
| -6     | 未加入房间                     |
| -20    | confirms 和 cancles 不能都为空 |

#### 说明

- 开发者通过该接口可以订阅和取消订阅相关的组。

## subscribeEventGroupResponse

```javascript
response.subscribeEventGroupResponse(status, [group])
```

#### 参数

| 参数   | 类型   | 描述       | 示例值      |
| ------ | ------ | ---------- | ----------- |
| status | number | 状态，200代表成功 | 200  |
| group  | array  | 组数组     | ["Matchvs"] |

#### 说明

- response是engine.subscribeEventGroupResponse方法中传入的对象，subscribeEventGroup完成之后，会异步回调engine.subscribeEventGroupResponse方法

## sendEventGroup

```
engine.sendEventGroup(cpProto, [group])
```

#### 参数

| 参数    | 类型   | 描述   | 示例值      |
| ------- | ------ | ------ | ----------- |
| cpProto | string | 自定义消息  | "test"      |
| group   | array  | 发送的组列表 | ["Matchvs"] |

#### 返回值

| 错误码 | 含义                          |
| ------ | ----------------------------- |
| 0      | 成功                          |
| -1     | 失败                          |
| -2     | 未初始化                      |
| -3     | 正在初始化                    |
| -4     | 未登录                        |
| -7     | 正在创建或者进入房间          |
| -6     | 未加入房间                    |
| -20    | groups 不能都为空             |
| -21    | data 过长, 不能超过 1024 字符 |

#### 说明

- 开发者可以通过该接口给对应的组发消息。

## sendEventGroupResponse

```
response.sendEventGroupResponse(status, dstNum)
```

#### 参数

| 参数   | 类型   | 描述       |示例值|
| ------ | ------ | ---------- | ---- |
| status | number | 状态，200代表成功 | 200  |
| dstNum | number | 推送的用户个数    | 5    |

#### 说明

- response是engine.sendEventGroupResponse方法中传入的对象，sendEventGroup完成之后，会异步回调engine.sendEventGroupResponse方法

## sendEventGroupNotify

```
response.sendEventGroupNotify(srcUid, [group], cpProto)
```

#### 参数

| 参数      | 类型   | 描述     | 示例值      |
| --------- | ------ | -------- | ----------- |
| srcUserID | number | 源用户ID | 277773      |
| groups    | number | 组数组   | ["Matchvs"] |
| cpProto   | string | 消息内容 | "test"      |

#### 说明

- response是engine.sendEventGroupNotify方法中传入的对象，收到订阅组的推送之后，会异步回调engine.sendEventGroupNotify方法

## setFrameSync

```
engine.setFrameSync(frameRate, enableGS, other)
```

#### 参数

| 参数      | 类型   | 描述                                                         | 示例值               |
| --------- | ------ | ------------------------------------------------------------ | -------------------- |
| frameRate | number | 每秒钟同步的帧数 : 0关闭。                                   | 5                    |
| enableGS  | number | 是否启用gameServer帧同步 0-启用 1-不启用                     | 0                    |
| other     | object | 其他数，目前包含一个值：cacheFrameMS 断线后缓存帧数据的时间，只有帧同步有效，单位毫秒，最多有效一个小时 | {cacheFrameMS:10000} |

#### 返回值

| 错误码 | 含义                             |
| ------ | -------------------------------- |
| 0      | 成功                             |
| -1     | 失败                             |
| -2     | 未初始化                         |
| -3     | 正在初始化                       |
| -4     | 未登录                           |
| -7     | 正在创建或者进入房间             |
| -6     | 未加入房间                       |
| -20    | frameRate 不能超过 20，不能小于0 |

#### 说明

- 开发者可以通过该接口设置帧同步的帧率。同步帧率有效范围在1≤n≤20，n=0代表取消帧同步。当设置的同步帧率不在合法范围内，将会收到 -20 的错误码。
- 帧率须能被1000整除

#### 示例

```javascript
var frameRate = 15;
var result = engine.setFrameSync(frameRate, 0, {cacheFrameMS:10000});
console.log('设置帧率 result：'+result);
```

## setFrameSyncResponse

```
response.setFrameSyncResponse(rsp)
```

#### 参数 rsp 的属性

| 参数    | 类型   | 描述           | 示例值 |
| ------- | ------ | -------------- | ------ |
| status | number | 状态：<br>200 成功<br>519 重复设置<br>500 帧率需被1000整除 | 200    |

#### 说明

- response是engine.setFrameSyncResponse方法中传入的对象，setFrameSync完成之后，会异步回调engine.setFrameSyncResponse方法

#### 示例

```javascript
response.setFrameSyncResponse = this.setFrameSyncResponse.bind(this);
setFrameSyncResponse = function (rsp) {
    if (rsp.status == 200) {
        console.log('帧率设置成功');
    } else if (rsp.status ==519 ) {
        console.log('帧率设置失败,重复设置');
    } else if (rsp.status == 500) {
        console.log('帧率设置失败,帧率需被1000整除');
    }
}
```

## setFrameSyncNotify

设置帧同步异步回调同时会回调给自己。可能是gameServer 设置的帧同步，也可以是玩家设置的帧同步。

```typescript
response.setFrameSyncNotify(rsp);
```

#### 参数 rsp 属性

| 参数         | 类型   | 描述                                                         | 示例值 |
| ------------ | ------ | ------------------------------------------------------------ | ------ |
| frameRate    | number | 帧率                                                         | 10     |
| startIndex   | number | 序号                                                         | 1      |
| timestamp    | string | 时间戳                                                       |        |
| enableGS     | number | 是否启用gameServer帧同步 0-启用 1-不启用                     | 0      |
| cacheFrameMS | number | 断线后缓存帧数据的时间，只有帧同步有效，单位毫秒，最多有效一个小时 | 10000  |



## sendFrameEvent

```
engine.sendFrameEvent(cpProto, op)
```

#### 参数

| 参数    | 类型   | 描述    | 示例值    |
| ------- | ------ | ------- | --------- |
| cpProto | string | 帧同步消息内容 | "message" |
| op | number | 帧同步数据操作 0-只发送客户端 1-只发送GS 2-客户端和GS | 0 |

#### 返回值

| 错误码 | 含义                           |
| ------ | ------------------------------ |
| 0      | 成功                           |
| -1     | 失败                           |
| -2     | 未初始化                       |
| -3     | 正在初始化                     |
| -4     | 未登录                         |
| -7     | 正在创建或者进入房间           |
| -6     | 未加入房间                     |
| -21    | cpProto 过长，不能超过1024字符 |

#### 说明

- 开发者可以通过该接口发送帧同步消息。

#### 示例

```javascript
var self = this;
var userID = "235445";
isAllowInput: false,
var frameData = JSON.stringify({
	"userID": userID,
	"action": "playerPosition",
	"x": self.getPostion(),
	"arrow": self.isUserInputing
});
var result = mvs.engine.sendFrameEvent(frameData);
```

## sendFrameEventResponse

```
response.sendFrameEventResponse(rsp)
```

#### 参数 rsp 的属性

| 参数    | 类型   | 描述         | 示例值 |
| ------- | ------ | ------------ | ------ |
| status  | number | status:状态  | 200    |

#### 说明

- response是engine.sendFrameEventResponse方法中传入的对象，sendFrameEvent完成之后，会异步回调engine.sendFrameEventResponse方法

#### 示例

```javascript
response.sendFrameEventResponse = this.sendFrameEventResponse.bind(this);
sendFrameEventResponse : function (rsp) {
    if (rsp.status == 200) {
		console.log('帧同步消息发送成功');
    }
}
```


## frameUpdate

```
response.frameUpdate(data)
```

#### 参数 data 的属性

| 参数           | 类型               | 描述                     | 示例值 |
| -------------- | ------------------ | ------------------------ | ------ |
| frameIndex     | number             | 帧序号                   |        |
| frameItems     | Array<MsFrameItem> | 同步帧内的数据包数组     |        |
| frameWaitCount | number             | 同步帧内的数据包数组数量 |        |

#### MsFrameItem 的属性

| 参数      | 类型   | 描述     | 示例值 |
| --------- | ------ | -------- | ------ |
| srcUserID | number | 用户ID   |        |
| cpProto   | string | 附加消息 |        |
| timestamp | string | 时间戳   |        |

#### 说明

- frameUpdate是engine.frameUpdate方法中传入的对象，收到帧同步推送之后，会异步回调engine.frameUpdate方法

#### 示例

```javascript
response.frameUpdate = this.frameUpdate.bind(this);
frameUpdate : function (rsp) {
	console.log('帧序号是'+data.frameIndex);
}
```



## reconnect

```typescript
engine.reconnect();
```

#### 参数

无

#### 返回值

| 错误码 | 含义     |
| ------ | -------- |
| 0      | 成功     |
| -1     | 其他错误 |
| -2     | 未初始化 |
| -9     | 正在重连 |

#### 说明

- 用户在中途断线后服务器会保存用户20秒在房间状态，20秒内用户可以重新登录连接到原来的房间里面。
- 在游戏里面如果网络断开，可以调用 reconnect 函数重新连接，断线重新连接分为两种情况，第一种没有重新启动程序：在游戏进行时网络断开，直接调用 reconnect 重新连接到游戏。第二种重新加载程序：先调用login 然后判断 loginResponse 中的参数 roomID 是否为0 如果不为 0 就调用reconnect 重连到房间
- reconnect 接口调用，其他玩家收到 netWorkStateNotify 接口信息，接口详情请看netWorkStateNotify的接口说明。

#### 示例

```javascript
var result = engine.reconnect();
console.log("重连result"+result);
```

## reconnectResponse

```typescript
response.reconnectResponse(status, roomUserInfoList, roomInfo);
```

#### 参数

| 参数             | 类型                  | 描述                            | 示例值 |
| ---------------- | --------------------- | ------------------------------- | ------ |
| status           | number                | 状态返回，200表示成功，其他失败 | 200    |
| roomUserInfoList | Array<MsRoomUserInfo> | 房间内玩家信息列表              |        |
| roomInfo         | MsRoomInfo            | 房间信息构成的对象              |        |

#### MsRoomUserInfo 的属性

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

#### MsRoomInfo 的属性

| 属性         | 类型   | 描述                              | 示例值 |
| ------------ | ------ | --------------------------------- | ------ |
| roomID       | string | 房间号                            | 238211 |
| roomProperty | string | 房间属性                          | ""     |
| owner        | number | 房间创建者的用户ID                | 0      |
| state        | number | 房间状态 0-未知， 1-打开 ，2-关闭 | 1      |

#### 说明

- 玩家掉线后调用reconnect接口重新连接会收到此接口的回调。

#### 示例

```javascript
response.reconnectResponse = this.reconnectResponse.bind(this);
reconnectResponse :function (status,roomUserInfoList,roomInfo) {
    if(status == 200) {
        console.log("重连成功");
    } else {
        console.log("重连失败"+status);
    }
}
```

## setReconnectTimeout

用户进入房间后默认断线20秒会被剔除，在用户加入房间之前调用这个接口，服务就会从新设置断线重连时间，设置范围为 `0-600 秒` 如果设置的值为0, 则在用户断开就马上被踢出。

```javascript
engine.setReconnectTimeout(timeout)
```

#### 参数

| 参数    | 类型   | 描述             | 示例值 |
| ------- | ------ | ---------------- | ------ |
| timeout | number | 断线重连超时时间 | 60     |

#### 返回值

| 返回码 | 说明                                |
| ------ | ----------------------------------- |
| 0      | 接口调用成功                        |
| -2     | 未初始化                            |
| -3     | 正在初始化                          |
| -4     | 未登录                              |
| -5     | 正在登录                            |
| -7     | 正在创建房间，或者正在加入游戏房间  |
| -6     | 没有进入房间                        |
| -10    | 正在离开房间                        |
| -11    | 正在登出                            |
| -12    | 正在加入观战房间                    |
| -27    | timeout 超出范围  0=< timeout <=600 |
| -30    | 设置的 rType 值与当前模式冲突。     |

## setReconnectTimeoutResponse

设置重连时间回调

```javascript
response.setReconnectTimeoutResponse(status)
```

#### 参数

| 参数   | 类型   | 描述                | 示例值 |
| ------ | ------ | ------------------- | ------ |
| status | number | 状态值 200 设置成功 | 200    |

## getOffLineData

获取断线期间的帧数据，只有在开启了帧同步的时候使用，调用这个接口后，在断线期间游戏的数据会通过 frameUpdate 接口返回指定时间内的数据。
```typescript
engine.getOffLineData(cacheFrameMS:number)
```

#### 参数

| 参数         | 类型   | 描述                                                         | 示例值 |
| ------------ | ------ | ------------------------------------------------------------ | ------ |
| cacheFrameMS | number | 获取断线多久之内的缓存数据，上限为1个小时与setFrameSync 接口中的参数同步最好，cacheFrameMS 为-1 代表获取缓存这个房间里面所有的缓存帧数据，同时 setFrameSync 接口也要设置为 -1 | 10000  |

#### 返回码

- 略 可参考其他接口的返回码。

## getOffLineDataResponse

调用 getOffLineData 接口获取断线期间的帧数据，这个接口会返回是否调用成功通知和，缓存的帧数据数量。

```javascript
response.getOffLineDataResponse(rsp)
```

#### 参数 rsp 属性

| 属性       | 类型   | 描述     | 示例值 |
| ---------- | ------ | -------- | ------ |
| status     | number | 状态值   | 200    |
| frameCount | number | 帧数量   | 10     |
| msgCount   | number | 消息数量 | 20     |

## joinOpen

房间重新打开,运行他人匹配加入, 注意 `在房间的情况才可以调用,否则函数直接返回错误码`

```javascript
engine.joinOpen = function (cpProto) ;//cpProto:string 
```
#### 参数

cpProto: 附带信息,会通过joinOpenNotify广播给其他人

#### 返回值

参考错误码

## joinOpenNotify&joinOpenResponse

#### 参数

```javascript
//数据结构
function MsReopenRoomResponse(Status, cpProto) {
    this.status = Status;
    this.cpProto = cpProto;
}

function MsReopenRoomNotify(roomID, userID, cpProto) {
    this.roomID = roomID;
    this.userID = userID;
    this.cpProto = cpProto;
}

//函数签名
joinOpenNotify = function (rsp)
joinOpenResponse = function (notify)
```

MsReopenRoomResponse:

| 属性         | 类型   | 描述               | 示例值 |
| ------------ | ------ | ------------------ | ------ |
| status       | number | 接口调用的服务器返回码,200为正确| 200 |
| cpProto      | string | 调用者附带的信息   | ""     |

MsReopenRoomNotify:

| 属性         | 类型   | 描述               | 示例值 |
| ------------ | ------ | ------------------ | ------ |
| roomID       | string | 房间号             | "123123123" |
| cpProto      | string | 调用者附带的信息   | ""     |
| userID       | number | 调用者用户ID       | 0      |



#### 返回值

无

#### 示例代码片段

```javascript
initReopenRoom: function (self) {
        var isOpen = true;
        var checkBox = self.joinopen.getComponent(cc.Toggle);

        mvs.response.joinOpenResponse = function () {
            self.labelLog("我设置允许房间加人");
            checkBox.isChecked = true;
        };
        mvs.response.joinOpenNotify = function () {
            self.labelLog("有人设置了允许房间加人");
            checkBox.isChecked = true;
        };
        mvs.response.joinOverNotify = function () {
            self.labelLog("有人设置了不允许房间加人");
            checkBox.isChecked = false;
        };
        mvs.response.joinOverResponse = function () {
            self.labelLog("我设置了不允许房间加人");
            checkBox.isChecked = false;
        };
        self.joinopen.on(cc.Node.EventType.TOUCH_END, function () {
            isOpen = !isOpen;
            if (isOpen) {
                mvs.engine.joinOpen("");
            } else {
                mvs.engine.joinOver("");
            }
            console.log("joinopen:" + isOpen);
        });
    }
```



## getWatchRoomList

获取可以观战房间的列表信息。可观战的房间是由 createRoom 接口创建，并且 canWatch 参数配置为可观战模式。

```javascript
engine.getWatchRoomList(filter)
```

#### 参数 

| 参数   | 类型           | 描述                 | 示例值 |
| ------ | -------------- | -------------------- | ------ |
| filter | MsRoomFilterEx | 获取房间信息过滤条件 |        |

#### MsRoomFilterEx

| 参数         | 类型   | 描述                                              | 示例值         |
| ------------ | ------ | ------------------------------------------------- | -------------- |
| maxPlayer    | number | 房间最大人数 (0-全部)                             | 3              |
| mode         | number | 模式（0-全部）*创建房间时，mode最好不要填0        | 0              |
| canWatch     | number | 是否可以观战 0-全部 1-可以 2-不可以               | 1              |
| roomProperty | string | 房间属性                                          | “roomProperty” |
| full         | number | 0-全部 1-满 2-未满                                | 0              |
| state        | number | 0-全部 1-开放 2-关闭                              | 0              |
| sort         | number | 0-不排序 1-创建时间排序 2-玩家数量排序 3-状态排序 | 0              |
| order        | number | 0-ASC  1-DESC                                     | 0              |
| pageNo       | number | 页码，0为第一页                                   | 0              |
| pageSize     | number | 每一页的数量应该大于 0                            | 10             |

#### 返回值

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |

#### 示例代码

```javascript
function GetWatchRooms(){
    var filter = new MsRoomFilterEx(3, 0, 1, "roomProperty", 0, 1, 0, 0, 0, 3);
    engine.getWatchRoomList(filter);
}
```



## getWatchRoomsResponse

获取观战列表房间回调

```javascript
response.getWatchRoomsResponse(rooms);
```

#### 参数

| 参数  | 类型               | 说明                     | 示例值 |
| ----- | ------------------ | ------------------------ | ------ |
| rooms | MsGetRoomListExRsp | 获取观战房间列表回调信息 |        |

#### MsGetRoomListExRsp

| 参数      | 类型                   | 说明                                                         | 示例值 |
| --------- | ---------------------- | ------------------------------------------------------------ | ------ |
| status    | number                 | 接口调用状态 200 成功，其他值请看 [错误码说明](../ErrCode) |        |
| total     | number                 | 房间                                                         |        |
| roomAttrs | Array<MsRoomAttribute> | 房间信息列信息                                               |        |

MsRoomAttribute 属性可以参考 getRoomListExResponse 接口。

#### 示例代码

```javascript
response.getWatchRoomsResponse = function(rooms){
    if(status == 200){
        console.log("当前房间数量："，total);
        roomAttrs.forEach((e)={
            console.log("房间信息："，JSON.stringify(e));
        });
    }
}
```



## joinWatchRoom

加入观战房间请求接口，观战房间必须是在 getWatchRoomsResponse 接口中获取到的房间。

```javascript
engine.joinWatchRoom = function(roomID, userProfile){}
```

#### 参数

| 参数        | 类型   | 说明                                                         | 示例值 |
| ----------- | ------ | ------------------------------------------------------------ | ------ |
| roomID      | string | 有效的房间号 (房间必须是可观战房间)在调用createRoom的时候设置 |        |
| userProfile | string | 附加信息                                                     |        |

#### 返回值

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -8     | 已经在(观战)房间                   |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -14    | 正在观战离开房间                   |

#### 示例代码

```javascript
function JoinWatch(){
    var resNo = engine.joinWatchRoom("12345678900000000","nickName+avatar");
    if(resNo = 0){
        console.log("OK");
    }
}
```



## joinWatchRoomResponse

加入观战房间回调函数，调用 joinWatchRoom 接口后通过这个接口告知加入结果

```javascript
response.joinWatchRoomResponse= function (rsp){};
```

#### 参数

| 参数 | 类型                   | 说明 | 示例值 |
| ---- | ---------------------- | ---- | ------ |
| rsp  | MVS.MsJoinWatchRoomRsp |      |        |

#### MVS.MsJoinWatchRoomRsp

| 参数       | 类型                | 说明                                                         | 示例值 |
| ---------- | ------------------- | ------------------------------------------------------------ | ------ |
| status     | number              | 状态值 200 成功，其他错误值请看 [错误码文档](../ErrCode) | 200    |
| roomStatus | number              | 当前房间状态                                                 |        |
| reserved   |                     |                                                              |        |
| wathchInfo | MVS.MsLiveWatchInfo | 观战房间信息                                                 |        |
|            |                     |                                                              |        |

#### MVS.MsLiveWatchInfo

| 参数          | 类型               | 说明                                             | 示例值 |
| ------------- | ------------------ | ------------------------------------------------ | ------ |
| roomID        | string             | 房间ID                                           |        |
| startTS       | string             | 游戏开始时间                                     |        |
| delayMS       | number             | 延迟时间（观战延缓的时间，不与游戏当前进度同步） |        |
| cacheMS       | number             | 缓存时间                                         |        |
| maxAudiences  | number             | 最大观战                                         |        |
| curAudiences  | number             | 当前观战                                         |        |
| peakAudiences | number             |                                                  |        |
| lastAudiences | MVS.MsLiveAudience | 观战列表信息                                     |        |

#### MVS.MsLiveAudience

| 参数      | 类型   | 说明         | 示例值  |
| --------- | ------ | ------------ | ------- |
| userID    | number | 用户ID       | 123456  |
| profile   | string | 用户附带消息 | “hello” |
| enterTime | string | 进入时间     |         |

#### 示例代码

```javascript
response.joinWatchRoomResponse = function(status){
    if(status == 200){
        console.log("加入观战房间成功");
    }
}
```


## joinWatchRoomNotify

加入观战房间异步回调

```javascript
response.joinWatchRoomNotify = function (user) {}
```

#### 参数

| 参数 | 类型           | 说明     | 示例值 |
| ---- | -------------- | -------- | ------ |
| user | MsRoomUserInfo | 用户信息 |        |

#### MsRoomUserInfo

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

#### 示例代码

```javascript
response.joinWatchRoomNotify = function(user){
    console.log("用户加入观战：",user.userID);
    console.log("用户加入观战时附带的信息：",user.userProfile);
}
```



## setLiveOffset

设置观战数据偏移位置，指定从哪里开始播放。

```javascript
engine.setLiveOffset = function(offsetMS){};
```

#### 参数

| 参数     | 类型   | 说明                                                   | 示例值 |
| -------- | ------ | ------------------------------------------------------ | ------ |
| offsetMS | number | 偏移时间，-1 表示从头， 0 表示不追， >0 表示最近多少ms | -1     |

#### 返回值

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 不在观战房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |

#### 示例代码

```javascript
function SetLiveOffset(){
    var resNo = engine.setLiveOffset(-1);
    if(resNo == 0){
        console.log("OK");
    }
}
```



## setLiveOffsetResponse

设置观战数据偏移位置接口请求回调函数。

```javascript
response.setLiveOffsetResponse = function(status){};
```

#### 参数

| 参数   | 类型   | 说明                                                         | 示例值 |
| ------ | ------ | ------------------------------------------------------------ | ------ |
| status | number | 200 成功，其他错误值请看 [错误码文档](../ErrCode) | 200    |

#### 示例代码

```javascript
response.setLiveOffsetResponse = function(status){
    if(status == 200){
        console.log("设置观战数据偏移位置成功");
    }
};
```

## liveFrameUpdate

设置观战数据偏移值后，在游戏中对战的数据就会通过这个接口根据游戏的帧率返回数据。游戏帧率设置是 setFrameSync 接口。

```javascript
response.liveFrameUpdate = function(data){}
```

#### 参数

| 参数 | 类型        | 说明       | 示例值 |
| ---- | ----------- | ---------- | ------ |
| data | MsFrameData | 游戏帧数据 |        |

#### MsFrameData

| 参数           | 类型               | 描述                     | 示例值 |
| -------------- | ------------------ | ------------------------ | ------ |
| frameIndex     | number             | 帧序号                   |        |
| frameItems     | Array<MsFrameItem> | 同步帧内的数据包数组     |        |
| frameWaitCount | number             | 同步帧内的数据包数组数量 |        |

#### MsFrameItem 的属性

| 参数      | 类型   | 描述     | 示例值 |
| --------- | ------ | -------- | ------ |
| srcUserID | number | 用户ID   |        |
| cpProto   | string | 附加消息 |        |
| timestamp | string | 时间戳   |        |

#### 示例代码

```javascript
response.liveFrameUpdate = function(data){
    var i = 0;
    while ( i < data.frameItems.length){
        console.log("[Rsp]liveFrameUpdate cpProto:"+ data.frameItems[i++].cpProto);
    }
};
```



## changeRole

游戏角色与观战者角色的身份转换请求接口。在游戏中，如果需要转换为观战者就需要调用该接口，反之如果观战者需要加入游戏中也要调用此接口。切换角色的时候会 在产生changeRole 回调后，根据不同的模式分别触发 joinRoomResponse 和joinWatchRoomResponse 回调接口。

```javascript
engine.changeRole = function(userProfile, rType){}
```

#### 参数

| 参数        | 类型   | 描述                                                         | 示例值 |
| ----------- | ------ | ------------------------------------------------------------ | ------ |
| userProfile | string | 附带消息，默认填空值                                         | ""     |
| rType       | number | 要转换的模式，0-切换到游戏模式，1-切换到观战模式，这是一个可选参数，如果不填该参数，接口会根据你当前处于什么模式自动转换。 | 1      |

#### 返回值

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -6     | 没有进入房间                       |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |
| -30    | 设置的 rType 值与当前模式冲突。    |
| -1     | 其他错误                           |

#### 示例代码

```javascript
function ChangeRole(){
    var resNo = engine.changeRole("ChangeRole",1);
    if(resNo == 0){
        console.log("OK");
    }
}
```



## changeRoleResponse

切换角色游戏模式与观战模式请求接口回调

```javascript
response.changeRoleResponse = function(rsp){}
```

#### 参数

| 参数 | 类型                | 描述             | 示例值 |
| ---- | ------------------- | ---------------- | ------ |
| rsp  | MVS.MsChangeRoleRsp | 角色切换回调信息 |        |

#### MVS.MsChangeRoleRsp

| 参数           | 类型   | 描述                                                         | 示例值 |
| -------------- | ------ | ------------------------------------------------------------ | ------ |
| status         | number | 200 成功，其他错误值请看 [错误码文档](../ErrCode) | 200    |
| targetRoomType | number | 当前的模式 0-游戏模式 1-观战模式                             | 0      |

#### 示例代码

```javascript
response.changeRoleResponse = function(rsp){
    if(rsp.status == 200){
        console.log("成功切换到");
    }
}
```



## leaveWatchRoom

离开观战房间请求接口。

```javascript
engein.leaveWatchRoom = function(cpProto){}
```

#### 参数

| 参数    | 类型   | 描述              | 示例值 |
| ------- | ------ | ----------------- | ------ |
| cpProto | string | 附加值默认填 空值 | “”     |

#### 返回值

| 返回码 | 说明                               |
| ------ | ---------------------------------- |
| 0      | 接口调用成功                       |
| -2     | 未初始化                           |
| -3     | 正在初始化                         |
| -4     | 未登录                             |
| -5     | 正在登录                           |
| -7     | 正在创建房间，或者正在加入游戏房间 |
| -10    | 正在离开房间                       |
| -11    | 正在登出                           |
| -12    | 正在加入观战房间                   |

#### 示例代码

```javascript
function LeaveWatchRoom(){
    var resNo = engine.leaveWatchRoom("leaveWatchRoom");
    if(resNo == 0){
        console.log("ok");
	}
}
```

## leaveWatchRoomResponse

离开观战房间回调

```javascript
response.leaveWatchRoomResponse = function(status){}
```

#### 参数

| 参数   | 类型   | 描述                                                         | 示例值 |
| ------ | ------ | ------------------------------------------------------------ | ------ |
| status | number | 200 成功，其他错误值请看 [错误码文档](../ErrCode) | 200    |

#### 代码示例

```javascript
response.leaveWatchRoomResponse = function(status){
    if(status == 0){
        console.log("退出观战房间成功");
	}
}
```

## leaveWatchRoomNotify

离开观战房间请求异步回调，其他观战者可收到这个回调。

```javascript
response.leaveWatchRoomNotify = function(user){}
```

#### 参数

| 参数 | 类型                     | 描述               | 示例值 |
| ---- | ------------------------ | ------------------ | ------ |
| user | MVS.MsExitLiveRoomNotify | 离开房间的用户信息 |        |

#### MVS.MsExitLiveRoomNotify

| 属性        | 类型   | 描述     | 示例值 |
| ----------- | ------ | -------- | ------ |
| userID      | number | 用户ID   | 32322  |
| userProfile | string | 玩家简介 | ""     |

#### 代码示例

```javascript
response.leaveWatchRoomNotify = function(user){
    console.log("用户离开观战：",user.userID)；
    console.log("用户离开时附带的信息：",user.userProfile)
}
```



## 日志开关

注意：如果要关闭SDK中的日志就调用 MatchvsLog.closeLog()。如果打开SDK中的日志就调用MatchvsLog.openLog();



## 错误码

```
response.errorResponse = function(error) {
	console.log("错误信息：", error);
}
```
**注意** Matchvs相关的异常信息可通过该接口获取 更多[错误码说明](../ErrCode) 



## CHANGELOG

时间：2018.07.13

小版本：v1.7.000

```
1.添加观战功能，此功能添加了一些新接口.
2.修复若干bug

```



时间：2018.07.13

JSSDK版本：v3.7.3.0+

```
1. 新增 joinRoomResponse 接口参数 state
2. 所有包含userId参数的数据类型都新增了一个userID字段，原来userId也存在，建议使用userID。
```

时间：2018.05.29

JSSDK版本：v1.6.202

    1. 新增joinOpen 房间重新打开功能
    2. 修复微信小游戏真机断线问题
    3. 调整微信小游戏适配机制,只需引用matchvs.all.js,不再引用matchvs.all.weixin.js
    4. 修复Egret打包H5平台 `找不到 wx define` 的问题
    5. 修复uninit后不能后登录的问题
    6. 修复被kickPlayer后不能进入房间,返回-8或-10的问题.
    7. 代码优化,减少代码体积

