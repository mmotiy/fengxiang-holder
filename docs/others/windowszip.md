## windows powershell zip压缩脚本
```shell script
del .\build.zip;Compress-Archive -Path index.html,static,hybrid build.zip
```
上面是一个powershell 脚本用来批量压缩多个文件以及文件夹
`;`分割条语句。