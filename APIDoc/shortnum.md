/*
Title: 短号映射
Sort: 20
*/

## 域名

Matchvs 环境分为测试环境（alpha）和 正式环境（release），所以在使用http接口时，需要通过域名进行区分。使用正式环境需要先在官网控制台将您的游戏发布上线。

- alpha环境域名：alphavsopen.matchvs.com
- release环境域名：vsopen.matchvs.com

## 接口签名

接口需要校验签名，参数中都必须包含sign、mode参数，ts、seq 2个参数可选

ts为请求时间戳，精确到秒。

seq为请求序号，每次请求递增变化由发起方自行维护。

mode指定本次请求使用哪种签名方式（1:token, 2:secret) 。

sign由签名参数和签名方法计算得到的签名值。

如果同时设置ts和seq可用于避免重复请求攻击，当前支持如下两种方式签名方式:

### 1 普通用户类接口

```
1. 按照如下格式拼接出字符串:
appKey&param1=xxx&param2=xxx&...[&seq=xxx&ts=xxx]&token
params为参与签名的参数，并按照参数名字母序升序排列
[&seq=xxx&ts=xxx]表示如果设置了seq和ts，seq和ts会参与签名计算，mode参数不参与签名计算
appKey为您在官网配置游戏所得，token通过用户注册、登录请求获取
2. 计算第一步拼接好的字符串的MD5值，即为sign的值。
```

### 2 管理员类接口

```
1. 按照如下格式拼接出字符串:
appkey&param1=xxx&param2=xxx&...[&seq=xxx&ts=xxx]&appSecret
params为参与签名的参数，并按照参数名字母序升序排列
[&seq=xxx&ts=xxx]表示如果设置了seq和ts，seq和ts会参与签名计算，mode参数不参与签名计算
appKey和appSecret为您在官网配置游戏所得
2. 计算第一步拼接好的字符串的MD5值，即为sign的值。
```

## 短号生成

在获取到房间号或小队号之后，可以调用以下接口生成对应的短号，然后将短号分享给其他玩家。

请求地址：/extra/shortCreate?userID=10000&mode=1&sign=xxx

请求方式：POST

Content-Type：application/json

请求参数：

URL 参数：

| 字段   | 类型   | 是否必须 | 是否参与签名                  | 说明                                       | 示例                             |
| ------ | ------ | -------- | ----------------------------- | ------------------------------------------ | -------------------------------- |
| userID | number | 是       | 是                            | 用户ID                                     | 1000                             |
| mode   | number | 是       | 否                            | 签名方式，1:token, 2:secret                | 1                                |
| seq    | number | 否       | 如果设置了seq和ts，则参与签名 | 请求序号，每次请求递增变化由发起方自行维护 | 1                                |
| ts     | number | 否       | 如果设置了seq和ts，则参与签名 | 请求时间戳，精确到秒                       | 1551255392                       |
| sign   | string | 是       | 否                            | 签名值                                     | 4ee9db3a5bd68f2e610bd4e19988b431 |

Body：

| 字段    | 类型   | 是否必须 | 是否参与签名 | 说明                                         | 示例                  |
| ------- | ------ | -------- | ------------ | -------------------------------------------- | --------------------- |
| gameID  | number | 是       | 是           | 游戏ID                                       | 201001                |
| longstr | string | 是       | 是           | 长字符串，例如 roomID、teamID                | "1738555257257988168" |
| expire  | number | 是       | 是           | 短号过期时间，秒，最长过期时间86400秒（1天） | 300                   |
| length  | number | 否       | 否           | 短号长度，默认6位，最长10位                  | 6                     |

请求示例：

```
curl -X POST \
 'http://zwopen.matchvs.com/extra/shortCreate?userID=3307434&mode=1&sign=4ee9db3a5bd68f2e610bd4e19988b431' \
 -H 'Content-Type: application/json' \
 -d '{
   "gameID": 102003,
   "longstr": "1738555257257988168",
   "expire": 6000,
   "length": 6
}'
```

响应结果示例：

```
{
   "data": {
       "longstr": "1738555257257988168",
       "shortstr": "000002"
  },
   "status": 0
}
```

响应参数说明：

| 字段     | 类型   | 说明                 |
| -------- | ------ | -------------------- |
| status   | number | 错误码               |
| shortstr | string | 短字符串             |
| longstr  | string | 长字符串（原字符串） |

## 查询短号

当使用`/extra/shortCreate`成功创建一个短号后，你可以调用以下接口查询一个长号对应的短号。如果未使用过该长号创建短号，接口会返回相应的错误消息。

请求地址：/extra/longQuery?userID=10000&mode=1&sign=xxx

请求方式：POST

Content-Type：application/json

请求参数：

URL 参数：

| 字段   | 类型   | 是否必须 | 是否参与签名                  | 说明                                       | 示例                             |
| ------ | ------ | -------- | ----------------------------- | ------------------------------------------ | -------------------------------- |
| userID | number | 是       | 是                            | 用户ID                                     | 1000                             |
| mode   | number | 是       | 否                            | 签名方式，1:token, 2:secret                | 1                                |
| seq    | number | 否       | 如果设置了seq和ts，则参与签名 | 请求序号，每次请求递增变化由发起方自行维护 | 1                                |
| ts     | number | 否       | 如果设置了seq和ts，则参与签名 | 请求时间戳，精确到秒                       | 1551255392                       |
| sign   | string | 是       | 否                            | 签名值                                     | 4ee9db3a5bd68f2e610bd4e19988b431 |

Body：

| 字段    | 类型   | 是否必须 | 是否参与签名 | 说明                          | 示例                  |
| ------- | ------ | -------- | ------------ | ----------------------------- | --------------------- |
| gameID  | number | 是       | 是           | 游戏ID                        | 201001                |
| longstr | string | 是       | 是           | 长字符串，例如 roomID、teamID | "1738555257257988168" |

请求示例：

```
curl -X POST \
 'http://zwopen.matchvs.com/extra/longQuery?userID=3307434&mode=1&sign=4ee9db3a5bd68f2e610bd4e19988b431' \
 -H 'Content-Type: application/json' \
 -d '{
   "gameID": 102003,
   "longstr": "1738555257257988168"
}'
```

响应结果示例：

```
{
   "data": {
       "longstr": "1738555257257988168",
       "shortstr": "000002"
  },
   "status": 0
}
```

响应参数说明：

| 字段     | 类型   | 说明                 |
| -------- | ------ | -------------------- |
| status   | number | 错误码               |
| shortstr | string | 短字符串             |
| longstr  | string | 长字符串（原字符串） |


## 短号还原

其他玩家拿到短号后，可以调用以下接口还原成长号，然后利用长号加入房间/小队。

请求地址：/extra/shortQuery?userID=10000&mode=1&sign=xxx

请求方式：POST

Content-Type：application/json

请求参数：

URL 参数：

| 字段   | 类型   | 是否必须 | 是否参与签名                  | 说明                                       | 示例                             |
| ------ | ------ | -------- | ----------------------------- | ------------------------------------------ | -------------------------------- |
| userID | number | 是       | 是                            | 用户ID                                     | 1000                             |
| mode   | number | 是       | 否                            | 签名方式，1:token, 2:secret                | 1                                |
| seq    | number | 否       | 如果设置了seq和ts，则参与签名 | 请求序号，每次请求递增变化由发起方自行维护 | 1                                |
| ts     | number | 否       | 如果设置了seq和ts，则参与签名 | 请求时间戳，精确到秒                       | 1551255392                       |
| sign   | string | 是       | 否                            | 签名值                                     | 4ee9db3a5bd68f2e610bd4e19988b431 |

Body：

| 字段     | 类型   | 是否必须 | 说明     | 示例     |
| -------- | ------ | -------- | -------- | -------- |
| gameID   | number | 是       | 游戏ID   | 201001   |
| shortstr | string | 是       | 短字符串 | "106613" |

请求示例：

```
curl -X POST \
 'http://zwopen.matchvs.com/extra/shortQuery?userID=3307434&mode=1&sign=978ad9668e28d9521d2f07bda3308c8e' \
 -H 'Content-Type: application/json' \
 -d '{
   "gameID": 102003,
   "shortstr": "0001"
}'
```

响应结果示例：

```
{
   "data": {
       "longstr": "1738555257257988168",
       "shortstr": "0001"
  },
   "status": 0
}
```

响应参数说明：

| 字段     | 类型   | 说明                 |
| -------- | ------ | -------------------- |
| status   | number | 错误码               |
| shortstr | string | 短字符串             |
| longstr  | string | 长字符串（原字符串） |

## Creator代码示例

```javaScript

let GLB = require("../interface/Glb");
let CornetMappingHost = GLB.platform === "release"? "http://vsopen.matchvs.com":"http://alphavsopen.matchvs.com";
let shortCreate = "/extra/shortCreate?"; // 短号生成
let shortQuery = "/extra/shortQuery?"; //短号还原


/**
 * 生成短房间号
 * @param gameID 游戏ID
 * @param userID 用户ID
 * @param expire 短号过期时间，秒
 * @param longstr 	长字符串，例如 roomID、teamID
 * @param appKey 游戏appKey
 * @param token 用户token
 * @param length 短号长度，默认6位，最长10位，最小1位
 * @param callback 回调函数
 * @param seq 请求序号，每次请求递增变化由发起方自行维护  可选参数
 * @param ts 请求时间戳，精确到秒  可选参数
 */
function shortCreateRoomID(gameID,userID,expire,longstr,appKey,token,length,callback,seq,ts) {
    let request = new XMLHttpRequest();
    let url = CornetMappingHost + shortCreate;
    url += "&userID="+userID+"&mode="+1+"&sign="+getShortCreateUserSign(token,appKey,expire,gameID,longstr,seq,ts,userID);
    request.open("post",url , true);
    request.setRequestHeader("Content-Type", "application/json");
    request.onreadystatechange = function () {
        if (request.readyState === 4 && (request.status >= 200 && request.status < 400)) {
            try{
                let response = request.responseText;
                let data = JSON.parse(response);
                callback(data);
            } catch(error){
                console.warn(error.message);
                callback(error);
            }
        }
    };
    let jsonParam ={
        gameID:gameID,
        longstr:longstr,
        expire:expire,
        length:length,
    };
    request.send(JSON.stringify(jsonParam));
};

/**
 * 还原短房间号
 * @param gameID 游戏ID
 * @param userID 用户ID
 * @param shortstr 生成的短房间号
 * @param appKey 游戏appKey
 * @param token 用户token
 * @param callback 回调函数
 * @param seq 	请求序号，每次请求递增变化由发起方自行维护  可选参数
 * @param ts 请求时间戳，精确到秒 可选参数
 */
function shortQueryRoomID(gameID,userID,shortstr,appKey,token,callback,seq,ts) {
    let request = new XMLHttpRequest();
    let url = CornetMappingHost + shortQuery;
    url += "&userID="+userID+"&mode="+1+"&sign="+getShortQueryUserSign(token,appKey,gameID,seq,shortstr,ts,userID);
    request.open("post",url , true);
    request.setRequestHeader("Content-Type", "application/json");
    request.onreadystatechange = function () {
        if (request.readyState === 4 && (request.status >= 200 && request.status < 400)) {
            try{
                let response = request.responseText;
                let data = JSON.parse(response);
                callback(data);
            } catch(error){
                console.warn(error.message);
                callback(error);
            }
        }
    };
    let jsonParam ={
        gameID:gameID,
        shortstr:shortstr,
    };
    request.send(JSON.stringify(jsonParam));
};



/**
 * 生成短房间号普通用户sign生成
 * @param token 用户token
 * @param appKey 游戏appKey
 * @param gameID 游戏ID
 * @param seq 请求序号，每次请求递增变化由发起方自行维护   可选参数
 * @param shortstr 生成的短房间号
 * @param ts 请求时间戳，精确到秒    可选参数
 * @param userID 用户ID
 * @returns {*}
 */
function getShortQueryUserSign(token,appKey,gameID,seq,shortstr,ts,userID) {
    let signStr;
    if (seq === undefined || ts === undefined) {
        signStr = hex_md5(appKey+"&gameID="+gameID+"&shortstr="+shortstr+"&userID="+userID+"&"+token);
    } else {
        signStr = hex_md5(appKey+"&gameID="+gameID+"&seq="+seq+"&shortstr="+shortstr+"&ts="+ts+"&userID="+userID+"&"+token);
    }
    return signStr;
};


/**
 * 生成短房间号普通用户sign生成
 * @param token 用户token
 * @param appKey 游戏appKey
 * @param expire 	短号过期时间
 * @param gameID 游戏ID
 * @param longstr 房间号或小队号
 * @param seq 请求序号，每次请求递增变化由发起方自行维护   可选参数
 * @param ts 请求时间戳，精确到秒    可选参数
 * @param userID 用户ID
 * @returns {*}
 */
function getShortCreateUserSign(token,appKey,expire,gameID,longstr,seq,ts,userID) {
    console.log("token",token);
    let signStr;
    if (seq === undefined || ts === undefined) {
        signStr = hex_md5(appKey+"&expire="+expire+"&gameID="+gameID+"&longstr="+longstr+"&userID="+userID+"&"+token);
    } else  {
        signStr = hex_md5(appKey+"&expire="+expire+"&gameID="+gameID+"&longstr="+longstr+"&seq="+seq+"&ts="+ts+"&userID="+userID+"&"+token);
    }
    return signStr;
}



window.getShortCreateUserSign = getShortCreateUserSign;
window.shortCreateRoomID = shortCreateRoomID;
window.shortQueryRoomID = shortQueryRoomID;
window.getShortQueryUserSign = getShortQueryUserSign;

```

- 代码地址:https://github.com/matchvs/demo-creator/blob/dev/assets/scripts/Utils/CornetMapping.js
- hex_md5()加密地址: https://github.com/matchvs/demo-creator/blob/dev/assets/scripts/Utils/md5.js

```javaScript
//使用代码示例
shortCreateRoomID(gameID,userID,300,roomID,appKey,token,6,function (data) {
	console.log("shortCreateRoomID",data);
	shortQueryRoomID(gameID,userID,data.data.shortstr,appKey,token,function (data) {
		console.log("shortQueryRoomID",data);
	})
});
```

