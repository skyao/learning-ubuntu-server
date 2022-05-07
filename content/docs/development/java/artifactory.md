---
title: "安装Artifacory"
linkTitle: "Artifacory"
weight: 30
description: >
  安装并配置Artifacory
---



## 安装

准备安装：

```bash
wget -qO - https://api.bintray.com/orgs/jfrog/keys/gpg/public.key | sudo apt-key add -
echo "deb https://jfrog.bintray.com/artifactory-debs focal main" | sudo tee /etc/apt/sources.list.d/jfrog.list
sudo apt update
```

执行安装：

```bash
sudo apt install jfrog-artifactory-oss

Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  jfrog-artifactory-oss
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 484 MB of archives.
After this operation, 989 MB of additional disk space will be used.
Get:1 https://jfrog.bintray.com/artifactory-debs focal/main amd64 jfrog-artifactory-oss amd64 7.37.15 [484 MB]
Fetched 484 MB in 38s (12.9 MB/s)                                              
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_TIME = "zh_CN.UTF-8",
	LC_MONETARY = "zh_CN.UTF-8",
	LC_ADDRESS = "zh_CN.UTF-8",
	LC_TELEPHONE = "zh_CN.UTF-8",
	LC_NAME = "zh_CN.UTF-8",
	LC_MEASUREMENT = "zh_CN.UTF-8",
	LC_IDENTIFICATION = "zh_CN.UTF-8",
	LC_NUMERIC = "zh_CN.UTF-8",
	LC_PAPER = "zh_CN.UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
locale: Cannot set LC_ALL to default locale: No such file or directory
Selecting previously unselected package jfrog-artifactory-oss.
(Reading database ... 105976 files and directories currently installed.)
Preparing to unpack .../jfrog-artifactory-oss_7.37.15_amd64.deb ...
dpkg-query: no packages found matching artifactory
Checking if group artifactory exists...
Group artifactory doesn't exist. Creating ...
Checking if user artifactory exists...
User artifactory doesn't exist. Creating ...
Checking if artifactory data directory exists
Removing tomcat work directory
Unpacking jfrog-artifactory-oss (7.37.15) ...
Setting up jfrog-artifactory-oss (7.37.15) ...
Adding the artifactory service to auto-start... DONE

************ SUCCESS ****************
The Installation of Artifactory has completed successfully.

NOTE: It is highly recommended to use Artifactory with an external database (MyS
QL, Oracle, Microsoft SQL Server, PostgreSQL, MariaDB).
      For details about how to configure the database, refer to https://service.
jfrog.org/installer/Configuring+the+Database

Start Artifactory with:
> systemctl start artifactory.service

Check Artifactory status with:
> systemctl status artifactory.service


Installation directory was set to /opt/jfrog/artifactory
You can find more information in the log directory /opt/jfrog/artifactory/var/lo
g
System configuration templates can be found under /opt/jfrog/artifactory/var/etc
Copy any configuration you want to modify from the template to /opt/jfrog/artifa
ctory/var/etc/system.yaml

Triggering migration script, this will migrate if needed ...
Processing triggers for systemd (245.4-4ubuntu3.17) ...
```

start 并 enable artifactory：

```bash
sudo systemctl start artifactory.service
sudo systemctl enable artifactory.service
```

检验一下：

```bash
$ systemctl status artifactory.service
● artifactory.service - Artifactory service
     Loaded: loaded (/lib/systemd/system/artifactory.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-05-06 03:06:34 UTC; 55s ago
   Main PID: 44611 (java)
      Tasks: 0 (limit: 154104)
     Memory: 20.8M
     CGroup: /system.slice/artifactory.service
             ‣ 44611 /opt/jfrog/artifactory/app/third-party/java/bin/java -Djava.util.logging.config.file=/opt/jfrog/artifactory/app/>

May 06 03:06:33 skyserver su[45384]: (to artifactory) root on none
May 06 03:06:33 skyserver su[45384]: pam_unix(su:session): session opened for user artifactory by (uid=0)
May 06 03:06:33 skyserver su[45384]: pam_unix(su:session): session closed for user artifactory
May 06 03:06:33 skyserver su[45497]: (to artifactory) root on none
May 06 03:06:33 skyserver su[45497]: pam_unix(su:session): session opened for user artifactory by (uid=0)
May 06 03:06:34 skyserver su[45497]: pam_unix(su:session): session closed for user artifactory
May 06 03:06:34 skyserver su[45628]: (to artifactory) root on none
May 06 03:06:34 skyserver su[45628]: pam_unix(su:session): session opened for user artifactory by (uid=0)
May 06 03:06:34 skyserver su[45628]: pam_unix(su:session): session closed for user artifactory
May 06 03:06:34 skyserver systemd[1]: Started Artifactory service.

$ ps -ef | grep artifactory                               
sky        39708   33533  0 03:05 pts/0    00:00:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox artifactory

$ nc  -zv  127.0.0.1 8081     
Connection to 127.0.0.1 8081 port [tcp/tproxy] succeeded!
```





## 配置Artifactory

访问：

http://192.168.0.40:8081

会被重定向到：

http://192.168.0.40:8082/ui/login/

默认账号为： Username: **admin** Password: **password**

设置 baseURL： http://sz.springmesh.io/

（注意在路由器中开启 8081 、 8082 两个端口的端口映射，以便远程访问）

创建仓库时选择 maven ，Repositories created during the Onboarding:

- libs-snapshot-local
- maven-remote
- libs-snapshot

登录之后会要求修改默认的 admin 密码。

### 配置密码加密方式

为了避免在 maven 的 settings.xml 文件中出现密码的明文，要启用密码加密方式，在 "security" -> "settings" 中修改 "Password Encryption Policy" 为 "support"。

备注： unsupport 是明文，support 是可以明文也可以加密，required 是必须加密。

> 遇到的特殊问题：我在 settings.xml 中使用明文密码 + artifactory 设置 unsupport 可以工作，在 settings.xml 中使用加密密码 + artifactory 设置 support 也可以工作，但我在 settings.xml 中使用明文密码 + artifactory 设置 required 就会报错 401，不清楚为什么。 

## 配置 maven

修改 maven 的  settings 配置文件 (`~/.m2/settings.xml`)，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0 http://maven.apache.org/xsd/settings-1.2.0.xsd" xmlns="http://maven.apache.org/SETTINGS/1.2.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <servers>
    <server>
      <username>admin</username>
      <password>{lnSEkwEnmhaaaaaaaaaaaaaaaaaaaaaa=}</password>
      <id>central</id>
    </server>
    <server>
      <username>admin</username>
      <password>{lnSEkwEnmaaaaaaaaaaaaaaaaaaaaa=}</password>
      <id>snapshots</id>
    </server>
  </servers>
  <mirrors>
    <mirror>
      <mirrorOf>*</mirrorOf>
      <name>libs-release</name>
      <url>http://192.168.0.40:8081/artifactory/libs-release</url>
      <id>central</id>
    </mirror>
  </mirrors>
  <profiles>
    <profile>
      <repositories>
        <repository>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
          <id>central</id>
          <name>libs-release</name>
          <url>http://192.168.0.40:8081/artifactory/libs-release</url>
        </repository>
        <repository>
          <snapshots />
          <id>snapshots</id>
          <name>libs-snapshot</name>
          <url>http://192.168.0.40:8081/artifactory/libs-snapshot</url>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
          <id>central</id>
          <name>libs-release</name>
          <url>http://192.168.0.40:8081/artifactory/libs-release</url>
        </pluginRepository>
        <pluginRepository>
          <snapshots />
          <id>snapshots</id>
          <name>libs-snapshot</name>
          <url>http://192.168.0.40:8081/artifactory/libs-snapshot</url>
        </pluginRepository>
      </pluginRepositories>
      <id>artifactory</id>
    </profile>
  </profiles>
  <activeProfiles>
    <activeProfile>artifactory</activeProfile>
  </activeProfiles>
</settings>

```

> 注意：在使用mirror的情况下，mirror的id要和 server 的 id 保持一致，否则会报错 401。

其中密码加密的方式参考官方文档：

https://maven.apache.org/guides/mini/guide-encryption.html

```bash
mvn --encrypt-master-password
{jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=}
```

输入一个master密码，可以理解为是一个加密因子，总之和服务器端admin的实际密码没任何关系，随便输入一堆数字和字母，然后就会得到一组类似 {jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=} 的字符串。

新建 `${user.home}/.m2/settings-security.xml` 文件，内容为:

```xml
<settingsSecurity>
  <master>{jSMOWnoPFgsHVpMvz5VrIt5kRbzGpI8u+9EF1iFQyJQ=}</master>
</settingsSecurity>
```

再执行下面的命令加密admin的实际密码：

```bash
mvn --encrypt-password
{COQLCE6DU6GtcS5P=}
```

将得到的 类似 {COQLCE6DU6GtcS5P=} 的加密后的字符串保存在  `settings.xml` 文件中。

```xml
  <servers>
    <server>
      <username>admin</username>
      <password>{lnSEkwEnmhaaaaaaaaaaaaaaaaaaaaaa=}</password>
      <id>central</id>
    </server>
    <server>
      <username>admin</username>
      <password>{lnSEkwEnmaaaaaaaaaaaaaaaaaaaaa=}</password>
      <id>snapshots</id>
    </server>
  </servers>
```

 

## 参考资料

- https://computingforgeeks.com/how-to-install-jfrog-artifactory-on-ubuntu-linux/
- https://maven.apache.org/guides/mini/guide-encryption.html
- 

