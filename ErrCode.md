/*
Title: 错误码说明
Sort: 43
*/

## 错误码 

Matchvs SDK 一些附带 status 参数的回调接口中具体的参数值可参考下面表格的说明。


**注意** Matchvs相关的异常信息也可通过 errorResponse 接口统一获取。

| 错误码 | 含义                                                      |
| ------ | ------------------------------------------------------------ |
| 1001   | 网络错误                                                     |
| 200    | 成功                                                      |
| 201    | 重连到大厅，没有进入房间                                     |
| 400    | 请求不存在                                                   |
| 401    | 无效 appkey                                                  |
| 402    |应用校验失败，确认是否在未上线时用了release环境，并检查gameID、appkey 和 secret。|
| 403    | 访问禁止，该用户多端登录。                                   |
| 404    | 无服务                                                       |
| 405    | 房间已满                                                     |
| 406    | 房间关闭                                                     |
| 500    | 服务错误，请确认是否正确打开gameServer                       |
| 502    | 服务停止，许可证失效 或者账号欠费                            |
| 503    | ccu 超出额                                                   |
| 504    | 流量用完                                                     |
| 507    | 房间号不存在，或您没有进入房间                               |
| 521    | gameServer 不存在。                                          |
| 522    | 没有打开帧同步，请调用setFrameSync接口设置帧率               |
| 527    | 消息发送太频繁，请不要超过每个房间 500次(总人数*(总接收+总发送)) |
