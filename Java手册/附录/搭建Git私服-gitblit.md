### 1. 搭建gitblit

下载地址：http://www.gitblit.com/

![](_v_images/20190507174827932_30381.png)





解压缩下载的压缩包即可，无需安装。


![](_v_images/20190507174959355_29077.png)



创建用于存储资料的文件夹。


![](_v_images/20190507175023192_3909.png)


### 2. 配置gitblit.properties 文件


找到Git目录下的data文件下的gitblit.properties文件，“记事本”打开。


![](_v_images/20190507175124250_7662.png)


找到git.repositoriesFolder(资料库路径)，赋值为之前创建好的文件目录。

![](_v_images/20190507175200669_16890.png)


找到server.httpPort，设定http协议的端口号

![](_v_images/20190507175222448_4142.png)

找到server.httpBindInterface，设定服务器的IP地址。这里就设定你的服务器IP。

![](_v_images/20190507175244862_14136.png)


找到server.httpsBindInterface，设定为localhost

![](_v_images/20190507175306089_2839.png)



### 3. 运行gitblit.cmd 批处理文件

![](_v_images/20190507175331144_31414.png)

运行结果如下，运行成功。

![](_v_images/20190507175408910_13696.png)


在浏览器中打开,现在就可以使用GitBlit了。

![](_v_images/20190507175426935_5922.png)


### 4. 设置以Windows Service方式启动Gitblit.


在Gitblit目录下，找到installService.cmd文件。

![](_v_images/20190507175535212_13610.png)


用“记事本”打开。

![](_v_images/20190507175553224_12986.png)


修改 ARCH


32位系统：SET ARCH=x86

64位系统：SET ARCH=amd64


添加 CD 为程序目录


SET CD=D:\Git\Gitblit-1.6.0(你的实际目录)

![](_v_images/20190507175639099_4547.png)


修改StartParams里的启动参数，给空就可以了。

![](_v_images/20190507175656563_4463.png)


保存，关闭文件。


以Windows Service方式启动Gitblit.


双击Gitblit目录下的installService.cmd文件(以管理员身份运行)。

![](_v_images/20190507175733423_4165.png)


在服务器的服务管理下，就能看到已经存在的gitblit服务了。

![](_v_images/20190507175752256_23973.png)


平时使用时，保持这个服务是启动状态就可以了。