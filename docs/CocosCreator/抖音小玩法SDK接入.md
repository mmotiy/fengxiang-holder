##抖音小玩法数据格式（对接SDK参考项目：噩梦之夜）

### PS：这里讲解的是项目需要上传到服务的数据格式类型，目的是确保数据格式同相对应，减少SDK接入时的工作量。主要对于新人，大佬确实用不上

#### 一般情况下需要导入的类：UploadDataModule、ByteSDK、OnDmHandleModule 、Logger（基本均位于文件夹SDK中）；

### 一、SDK接入

（根据个人项目而定，基本步骤为：替换APPID→.获取LiveRoomId和resToken→链接并维持WebSocket→处理收到的WebSocket消息）

其他需求如获取玩家排行，获取关卡记录等已有的接口可以参考已使用的项目根据自己项目需求接入。

#### ps：一般情况下开始时是全部复制后进行修改的，基本是在项目基本开发完成后进行接入，可以提前了解二、三中的数据格式

#### 1、替换APPID（在ByteSDK中更换APPID）

![](https://secure2.wostatic.cn/static/ssEVfqb3mxZhtWWRP76GtJ/image.png?auth_key=1682158104-oyiqocvW3pXN2heXTZLyQn-0-77e4b00ffee549e1f46c4708153f09cc)

#### 2、获取LiveRoomId和resToken

SDK初始化可以个人选择时间进行，一般是选择进入游戏时

获取roomID，ByteSDK的init()方法中调用tt.login，获取到roomID

![](https://secure2.wostatic.cn/static/fKRHW2ceZcxy2YfCs15aB5/image.png?auth_key=1682158104-326UFfknaBmsYwCRQwG43y-0-bf19d0d85fb2a6758b629ac3b6f92220)

获取resToken ：ByteSDK中的GetLogintemp()方法进行获取

  ![](https://secure2.wostatic.cn/static/mYvDy3au4VGYrz2ewsBg/image.png?auth_key=1682158104-tsqWnxrLAiFigUMhXCssKq-0-5728b24d0a750f8536fb97831bc46a43)

成功回调时调用StartWsConnect()方法链接WebSocket。

#### 3、链接并维持WebSocket

链接WebSocket

![](https://secure2.wostatic.cn/static/u4VVx2wthFh8sB65LWNi4o/image.png?auth_key=1682158104-51s3KqtKTchT2HRHWF9gMd-0-104d50c24cbe0f68ceab0d2de9757755)

断线重连（检测到断开连接，调用StartWsConnect()方法重新链接）

![](https://secure2.wostatic.cn/static/pSY8rnKNnLtF3iR7fY8V3E/image.png?auth_key=1682158104-gQ1wGnStQwzUjYS3zUXG2z-0-321e8a6698e792385c1267379cd36708)

#### 4、处理收到的WebSocket消息（依据需求文档中对应的效果，进行调用与实现，噩梦中比较复杂，可简化一部分）

**输入弹幕消息（玩家在直播间中输入弹幕）位于OnDmHandleModule中的OnDmDefaultMode()方法**

![](https://secure2.wostatic.cn/static/pjg2TCGFzPfBEeWwF4TBTA/image.png?auth_key=1682158104-nGdJGTDp2kmTqMnSx7US7X-0-55a05c44694ed40547877771e554e1c6)

**礼物消息（玩家在加入游戏后送出的礼物）位于OnDmHandleModule中的OnDmDefaultMode()方法**

![](https://secure2.wostatic.cn/static/aXNKhdndkY3bw1xDCm6dou/image.png?auth_key=1682158104-cXGDkRn5dTYh7JStLVWDde-0-ad90794860bc024d5a4af7e779aeb8bc)

**点赞消息（参与游戏的玩家在直播中点赞）位于OnDmHandleModule中的OnDmDefaultMode()方法**

![](https://secure2.wostatic.cn/static/kWckJnad85VFvdD6HUY7jY/image.png?auth_key=1682156301-boC9zERVGbaEPQaXbEiBXP-0-af2e8b1b6e06caef9e0d916ace0f6507)

### 二、世界排行（游戏结束→上传本局花费玩家ID以及抖币数量→拉取本局获胜玩家排行数据→显示至排行页面）

#### 1、上传本局玩家ID以及对应消费（胜利方全额上传，失败方上传抖币数量的0.8）。

上传之前将ID存入ByteSDK的UploadPlayers中，依据项目对玩家消费数额的记录存入UploadPlayers中（依据个人习惯编写，可在游戏结束存入，也可在上传方法中存入）

![](https://secure2.wostatic.cn/static/dBLzu4KFD6aa4328kZnUXP/image.png?auth_key=1682158104-ok8uniaZbJ2HapY3s3vBag-0-23b0615bbb738e4da223d22e149af0bf)

![](https://secure2.wostatic.cn/static/u6QqMUwcwsa6QytKiLKtkK/image.png?auth_key=1682158104-t9Kn6HCr2SJfrQNd1gFPoo-0-f21d199cab9989e0bae8b555c757f12d)

#### 2、调用上传方法

![](https://secure2.wostatic.cn/static/gMxb5FHPHmg3w4doFjt3iK/image.png?auth_key=1682158104-CKBj7MXD7gHV3iCJf8i8W-0-91e627adf87486a50416796b7d36f7d0)

#### 3、数据上传方法成功回调，调用拉取排行方法

![](https://secure2.wostatic.cn/static/p3S4cpyLyvrjDSWWuq6CVe/image.png?auth_key=1682158104-pEz9EPbDAZxcBGRe1zDKye-0-2e2a29e6454a018b7c8dbdd262b886c0)

#### 4、最后就是将EnduiPlayers中的排名展示到排行榜上即可

### 三、数据上报

#### 数据上报调用的是UploadDataModule中的UploadDataTikTok()方法，进行数据调用方法之前，应确保对应的参数都有对应数值；

![](https://secure2.wostatic.cn/static/7194ytEuEeK9qFD6x2zNET/image.png?auth_key=1682158104-4z51jqBTbH558f3zY9d882-0-71810f7d3dc9194092b2ad462d4910bd)

#### 设置游戏开始玩家列表方法SetStartPlayers()，游戏开始时，输入此时玩家ID，以及消费抖币数量，在游戏开始时调用。

![](https://secure2.wostatic.cn/static/dfQh4uPgLZjYPpM75PzE6k/image.png?auth_key=1682158105-29XtSiVSVtkFZd1ncCEiyH-0-ce246baeaa1547d882ad8a92d98fab94)

#### 游戏开始时调用方法：

![](https://secure2.wostatic.cn/static/8Rj6KCVhNs9LKEhzLAntEe/image.png?auth_key=1682158105-8ov2o8CnSd3nP5DgP5YDKf-0-ef84d369cabd5b90c281daadbacf9359)

#### 设置游戏结束玩家列表方法SetEndPlayers()，游戏结束结算时，输入此时玩家ID，以及消费抖币数量，在游戏结束时调用

![](https://secure2.wostatic.cn/static/mtTSUTPz3CeCkZ6T1pTem1/image.png?auth_key=1682158105-eZbjvGZrMzT8qMk9XET2qT-0-7943bfc84e42f1da79574104b73b4d39)

#### 设置单局MVP玩家信息方法SetMvpPlayers()，游戏结束时，输入第一名玩家ID，游戏结束时调用

![](https://secure2.wostatic.cn/static/cRaJWNCriXimZDx97xayis/image.png?auth_key=1682158105-oWZAjwkvVKphcPWhMrTzMs-0-40bdf2e8d5b2a41b865b4fdeb5151fc1)

#### 游戏结束时调用方法：

![](https://secure2.wostatic.cn/static/oYrfdpZbmtnCH2AVDE8gy1/image.png?auth_key=1682158105-mcPtSHneznYdBbvMkZSg32-0-db97b94c318539333c33a12d1791698e)

![](https://secure2.wostatic.cn/static/6pBzkDVivcJQJwKt78hcM5/image.png?auth_key=1682158105-rEKqfohqanbNHH7dkmK7jW-0-e5be5fd363ae3642fd4c90c9f13fbc95)

#### 数据报调用方法（应在所有数据存入后调用上报）：

![](https://secure2.wostatic.cn/static/sdvJ9MzVtzpVqYFBtNi8kB/image.png?auth_key=1682158105-eBehQjeiDGTbQ6JFHjER1X-0-d533ce3e67d86b09ffe6ecd357f6a685)

