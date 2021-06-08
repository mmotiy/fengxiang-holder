## windows powershell zip压缩脚本
```shell script
del .\build.zip;Compress-Archive -Path index.html,static,hybrid build.zip
```
上面是一个powershell 脚本用来批量压缩多个文件以及文件夹
`;`分割条语句。`del`删除指定文件命令。<br>
`Compress-Archive`windows压缩命令 -Path 指定需要压缩的文件目录。多文件使用`,`隔开。最后加上压缩包输出文件。
### 使用场景
在打包上传发布的时候，如果通过sftp上传整个build文件夹，会出现上传时间过长，文件不同不及时的问题。使用压缩包上传之后后，在服务端进行解压直接部署的方式，可以缩短部署时间。