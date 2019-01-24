/*
Title: 快速入门-Cocos2dx
Sort: 8
*/

## 导入SDK

1.前往服务中心-应用中心--SDK下载页面，下载jdge SDK-Cocos。

2.将SDK导入至你的项目  

### 创建Cocos 2d 项目

![image](http://imgs.matchvs.com/static/chuangjian.png)





### 导入对应的头文件
![image](http://imgs.matchvs.com/static/chuangjian1.png)
![image](http://imgs.matchvs.com/static/chuangjian2.png)
![image](http://imgs.matchvs.com/static/chuangjian3.png)
![image](http://imgs.matchvs.com/static/chuangjian4.png)
![image](http://imgs.matchvs.com/static/chuangjian5.png)
![image](http://imgs.matchvs.com/static/chuangjian6.png)


## 初始化

jdge提供了两个环境，alpha调试环境和release正式环境。  

游戏开发调试阶段请使用alpha环境，即platform传参"alpha"。如下：

```
MatchVSEngine::getInstance()->init(&m_Response_Test, sChannel.c_str(), sPlatform.c_str(), iGameId);
```

## 建立连接

如果是第一次使用SDK，需调用注册接口获取一个用户ID。

```
MatchVSEngine::getInstance()->registerUser();
```

调用登录接口即可建立连接，此时用户ID和创建游戏后获取的appKey、secret、GameID为必要参数。

```
MatchVSEngine::getInstance()->login(userid, token.c_str(), gameid, gameversion, appkey.c_str(), secretkey.c_str(), deviceid.c_str(), gatewayid);
```

## 游戏逻辑

接下来就可以使用jdge提供的接口进行游戏逻辑的实现啦，详情请参考 [接入指南](../APIBasic/2dxGuide)


## 发布上线

开发和调试过程在测试环境（alpha）下进行，调试完成后即可申请将游戏转到正式环境（release）：

1. 前往官网控制台进行“发布上线”操作，如图，点击按钮后即向jdge提交了上线申请。 ![](http://imgs.matchvs.com/static/2_4.png)
2. 申请通过后，在客户端的初始化接口将 platform 置为 release。  

至此，游戏就可以运行在正式环境下啦！
