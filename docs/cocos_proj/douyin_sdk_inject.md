# 抖音SDK接入
[toc]

### PS：这里讲解的是项目需要上传到服务的数据格式类型，目的是确保数据格式同相对应，减少SDK接入时的工作量。主要对于新人，大佬确实用不上

#### 一般情况下需要导入的类：UploadDataModule、ByteSDK(脚本要挂载到场景里，因为调用了start和update方法)、Logger；

### 一、SDK接入（根据个人项目而定）

#### ps：一般情况下开始时是全部复制后进行修改的，基本是在项目基本开发完成后进行接入，可以提前了解二、三中的数据格式

#### 1、首先依据上方提示进行对应信息修改（基本上改完就差不多），若APPID暂无权限可使用其他项目的APPID

![](https://secure2.wostatic.cn/static/sQgFM5VoNp8ZDAM4pAwrLk/image.png?auth_key=1682152221-2w9fH7NwcAmN1epdbcUL7S-0-fcae558eaa4d3b06f1e88bce8b671bd4)

2、start()中判定条件等等可以直接使用，根据需求添加

![](https://secure2.wostatic.cn/static/gRhEqFCb4PW2ypjp4AZWjJ/image.png?auth_key=1682152221-4TBAhNivX9viw639m1CmEi-0-fe15e8b027efaeabd0c5f42fdecdbe91)

### 二、世界排行（游戏结束→上传本局花费玩家ID以及抖币数量→拉取本局获胜玩家排行数据→显示至排行页面）

#### 1、上传本局玩家ID以及对应消费（胜利方全额上传，失败方上传抖币数量的0.8）。

上传之前将ID存入ByteSDK的UploadPlayers中，依据项目对玩家消费数额的记录存入UploadPlayers中（依据个人习惯编写，可在游戏结束存入，也可在上传方法中存入）

![](https://secure2.wostatic.cn/static/dBLzu4KFD6aa4328kZnUXP/image.png?auth_key=1682152221-wkL5Hdvfa31BbvWRahT2kn-0-ec190587335d4cf14e6e2fb491261b80)

![](https://secure2.wostatic.cn/static/u6QqMUwcwsa6QytKiLKtkK/image.png?auth_key=1682152221-4yyVv3Muq3usXLAjHQZYRg-0-db299fa352edf5b654a8efa076c6a9f4)

#### 2、调用上传方法

![](https://secure2.wostatic.cn/static/gMxb5FHPHmg3w4doFjt3iK/image.png?auth_key=1682152221-8hBTJWJ5eES2php7o1kvmy-0-303f585b9cf5104f45770165df7bfaaf)

#### 3、数据上传方法成功回调，调用拉取排行方法

![](https://secure2.wostatic.cn/static/p3S4cpyLyvrjDSWWuq6CVe/image.png?auth_key=1682152221-uJccUS49a51DosqW4eKgZW-0-1ae6e734e687fbccebae5ac9c4f5aba1)

#### 4、最后就是将EnduiPlayers中的排名展示到排行榜上即可

### 三、数据上报

#### 数据上报调用的是UploadDataModule中的UploadDataTikTok()方法，进行数据调用方法之前，应确保对应的参数都有对应数值；

![](https://secure2.wostatic.cn/static/7194ytEuEeK9qFD6x2zNET/image.png?auth_key=1682152222-t9VVuhu7wreYYedEugmuvm-0-373873dd061e6bedbe8b2e6946dbecd4)

#### 设置游戏开始玩家列表方法SetStartPlayers()，游戏开始时，输入此时玩家ID，以及消费抖币数量，在游戏开始时调用。

![](https://secure2.wostatic.cn/static/dfQh4uPgLZjYPpM75PzE6k/image.png?auth_key=1682152222-opupuTwkt4aTNtvzZf8C6V-0-a6eb5041535f14ee26041026565644bf)

#### 游戏开始时调用方法：

![](https://secure2.wostatic.cn/static/8Rj6KCVhNs9LKEhzLAntEe/image.png?auth_key=1682152222-wPLu4pM5vhYeS8cTtWTbqe-0-b93f4df685f85e92c4beae722620ccda)

#### 设置游戏结束玩家列表方法SetEndPlayers()，游戏结束结算时，输入此时玩家ID，以及消费抖币数量，在游戏结束时调用

![](https://secure2.wostatic.cn/static/mtTSUTPz3CeCkZ6T1pTem1/image.png?auth_key=1682152222-cu94dr6dYc1TCgFfqa3GkL-0-b60cc512165e2d9c3b439093b3d20bf7)

#### 设置单局MVP玩家信息方法SetMvpPlayers()，游戏结束时，输入第一名玩家ID，游戏结束时调用

![](https://secure2.wostatic.cn/static/cRaJWNCriXimZDx97xayis/image.png?auth_key=1682152222-myxuDqhdHhGR9Lai5zTVgX-0-a7c5cd688328bfb776de781a6090a35f)

#### 游戏结束时调用方法：

![](https://secure2.wostatic.cn/static/oYrfdpZbmtnCH2AVDE8gy1/image.png?auth_key=1682152222-52EXEdjrYN1CvxGzQXvFNW-0-333a5f2f2d4b783e81217249feb7ac93)

![](https://secure2.wostatic.cn/static/6pBzkDVivcJQJwKt78hcM5/image.png?auth_key=1682152222-aPJzsggcV9FEP5gtFEuDsk-0-93bbd3f5d156e6f105e9f4a586ee1971)

#### 数据报调用方法（应在所有数据存入后调用上报）：

![](https://secure2.wostatic.cn/static/sdvJ9MzVtzpVqYFBtNi8kB/image.png?auth_key=1682152222-ibSdebsMzkBv1Yt45o4FhG-0-1378d069cd9f238b106244c62fc2d925)