抖音小玩法数据格式（对接SDK参考项目：噩梦之夜）
PS：这里讲解的是项目需要上传到服务的数据格式类型，目的是确保数据格式同相对应，减少SDK接入时的工作量。主要对于新人，大佬确实用不上
一般情况下需要导入的类：UploadDataModule、ByteSDK、OnDmHandleModule 、Logger（基本均位于文件夹SDK中）；
一、SDK接入
（根据个人项目而定，基本步骤为：替换APPID→.获取LiveRoomId和resToken→链接并维持WebSocket→处理收到的WebSocket消息）

其他需求如获取玩家排行，获取关卡记录等已有的接口可以参考已使用的项目根据自己项目需求接入。

ps：一般情况下开始时是全部复制后进行修改的，基本是在项目基本开发完成后进行接入，可以提前了解二、三中的数据格式
1、替换APPID（在ByteSDK中更换APPID）


2、获取LiveRoomId和resToken
SDK初始化可以个人选择时间进行，一般是选择进入游戏时

获取roomID，ByteSDK的init()方法中调用tt.login，获取到roomID



获取resToken ：ByteSDK中的GetLogintemp()方法进行获取



成功回调时调用StartWsConnect()方法链接WebSocket。

3、链接并维持WebSocket
链接WebSocket



断线重连（检测到断开连接，调用StartWsConnect()方法重新链接）



4、处理收到的WebSocket消息（依据需求文档中对应的效果，进行调用与实现，噩梦中比较复杂，可简化一部分）
输入弹幕消息（玩家在直播间中输入弹幕）位于OnDmHandleModule中的OnDmDefaultMode()方法



礼物消息（玩家在加入游戏后送出的礼物）位于OnDmHandleModule中的OnDmDefaultMode()方法



点赞消息（参与游戏的玩家在直播中点赞）位于OnDmHandleModule中的OnDmDefaultMode()方法



二、世界排行（游戏结束→上传本局花费玩家ID以及抖币数量→拉取本局获胜玩家排行数据→显示至排行页面）
1、上传本局玩家ID以及对应消费（胜利方全额上传，失败方上传抖币数量的0.8）。
上传之前将ID存入ByteSDK的UploadPlayers中，依据项目对玩家消费数额的记录存入UploadPlayers中（依据个人习惯编写，可在游戏结束存入，也可在上传方法中存入）





2、调用上传方法


3、数据上传方法成功回调，调用拉取排行方法


4、最后就是将EnduiPlayers中的排名展示到排行榜上即可
三、数据上报
数据上报调用的是UploadDataModule中的UploadDataTikTok()方法，进行数据调用方法之前，应确保对应的参数都有对应数值；


设置游戏开始玩家列表方法SetStartPlayers()，游戏开始时，输入此时玩家ID，以及消费抖币数量，在游戏开始时调用。


游戏开始时调用方法：


设置游戏结束玩家列表方法SetEndPlayers()，游戏结束结算时，输入此时玩家ID，以及消费抖币数量，在游戏结束时调用


设置单局MVP玩家信息方法SetMvpPlayers()，游戏结束时，输入第一名玩家ID，游戏结束时调用


游戏结束时调用方法：




数据报调用方法（应在所有数据存入后调用上报）：
