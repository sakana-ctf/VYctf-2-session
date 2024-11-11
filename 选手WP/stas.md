## web

### 简易计算器

sub使用了os__execute以及`echo $(())`，可以通过命令注入，flag在程序中

```
`cat main;echo 1`
```

### 签到

http://139.155.139.109:8080/VYlicense 下搜索即可

## iot

MQTT协议获取加密的内容，解密程序，以及密码ProtoWare

解密会覆盖源文件，binwalk解包后即可获取文件系统，在文件系统中即可找到flag