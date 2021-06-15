## 快速搜索文件脚本

### 场景
无法通过文件名来得知，具体的小程序代码目录在那个文件夹下面。可以使用shell命令组合的方式，进行过滤。如 `d-name`会使职业趣味测一测。

### code
```shell script
 find ./ -maxdepth 4 -name "manifest.json" | xargs grep "appid.*wxf1d6ac0271bc5281"
```
将对应的appid `wxf1d6ac0271bc5281`替换成需要查找的目标appid就可以快手找到对应的小程序目录了。在windows上面可以使用`gitbash`。在安装git时默认安装的bash环境。
![运行结果图](../images/20210615103835.png)