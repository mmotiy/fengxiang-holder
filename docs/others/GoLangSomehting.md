### 关于GO的一些东西

* go run <dir> 运行某个项目下main包下的main方法
* go test 测试某个包下面的所有_test.go 结尾的所有方法。项目根目录
* go build 编译生成对应平台的二进制文件 默认输出项目根目录下
* go list -f '{{.Target}}' 输出安装文件的绝对路劲
* 可以使用 GOPATH 环境变量设定build输出路劲 go env -w GOBIN=D:\go\build
* go mod tidy 导入必须要包，移除不适用的包
* go clean -modcache 移除所有下载的modules包
 


