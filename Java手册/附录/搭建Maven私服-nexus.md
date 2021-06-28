### 1. 安装

![](_v_images/20190628180002054_6133.png)

#### 1.1. 访问
![](_v_images/20190628180013951_3666.png)


### 2. 3配置maven镜像

![](_v_images/20190628180037944_1888.png)

#### 2.1. 配置成功的标志
![](_v_images/20190628180109084_6776.png)


### 3. 更新nexus的索引

将nexus的服务停止

替换索引文件

![](_v_images/20190628180219892_28252.png)


### 4. 4添加nexus的远程代理仓库

地址：

- 开源中国社区：http://maven.oschina.net/content/groups/public/
- sonatype：http://repository.sonatype.org/content/groups/public/

![](_v_images/20190628180433178_29530.png)

点击保存之后刷新浏览器

![](_v_images/20190628180452647_10704.png)






### 5. 项目发布

向指定仓库中发布jar

```shell
mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.1.0 -Dpackaging=jar -Dfile=C:/oracle/product/10.2.0/db_1/jdbc/lib/ojdbc14.jar
```

#### 5.1. Nexus中仓库的类型

宿主仓库：一般我们发布的项目全部搞到这里

代理仓库：osichina，一般情况下备用仓库

虚拟机仓库：为了兼容以前的版本而保留的仓库,但是现在基本废弃了

仓库组：可以包含多种仓库


![](_v_images/20190628181055018_28535.png)


#### 5.2. 发布的版本

一般情况下版本发布：junit4.10.1

< 主版本 >  ------   < 次版本 > ------ < 增量版本 > ------ < 里程碑版本 >


Maven版本的划分

- 快照版：Snapshot版本代表不稳定、尚处于开发中的版本

- 发布版：Release版本则代表稳定的版本

### 6. 3在nexus中发布项目

#### 6.1. 创建宿主仓库

![](_v_images/20190628181413300_24615.png)

![](_v_images/20190628181422039_22880.png)


#### 6.2. 创建一个可以使用本仓库的用户

![](_v_images/20190628181448731_7241.png)

![](_v_images/20190628181458097_22145.png)


![](_v_images/20190628181509957_13036.png)


#### 6.3. 创建角色

![](_v_images/20190628181528430_162.png)


![](_v_images/20190628181536651_8718.png)


#### 6.4. 给角色赋予权限

![](_v_images/20190628181555303_17362.png)

![](_v_images/20190628181603975_7008.png)

![](_v_images/20190628181611392_12630.png)

给角色绑定权限

![](_v_images/20190628181621138_24582.png)


#### 6.5. 给用户绑定角色

![](_v_images/20190628181652703_28395.png)


#### 6.6. 配置发布项目的账号

![](_v_images/20190628181728462_27468.png)


远程发布的指令：deploy


启用快照版本支持

```xml
	<profile>
		<id>local-rep</id>
			<repositories>
				<repository>
					<id>local-rep</id>
					<url>http://192.168.1.106:8083/nexus/content/groups/public/</url>
				<snapshots>
					<enabled>true</enabled>
				</snapshots>
					<release>
						<enabled>true</enabled>
					</release>
				</repository>
			</repositories>
	</profile>

    </profiles>
	 <activeProfiles>
		<activeProfile>local-rep</activeProfile>
	</activeProfiles>
```