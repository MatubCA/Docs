# Maven

## 一、Maven简介

### 什么是Maven

Maven 是阿帕奇基金会管理的一个开源项目，用于来管理和构建 Java 项目的工具。

Maven 提供的功能主要有：

- 提供了一套标准化的项目结构

  Maven 构建的标准化的项目结构，创建的项目在各种使用Maven的IDE中都能使用。

- 提供了一套标准化的项目构建流程

  比如编译、打包、运行、部署等等，Maven都提供有命令。

- 提供了一套依赖管理机制

  在 Pom 文件中只要添加依赖坐标，就可以将依赖导入项目。

## 二、Maven配置

### Maven 仓库

Maven 仓库主要分为三种

- `本地仓库`

  自己电脑中存在 jar 包的文件夹。

- `中央仓库`

  Maven 官方维护的一个管理所有常用依赖（jar包）服务器。

- `镜像仓库`

  因为中央仓库在国外，国内访问速度非常慢，所以一些大公司在国内搭建镜像仓库，拷贝中央仓库的所有依赖，提升加载依赖的速度。

### 仓库配置

我们需要配置自己的本地仓库以及添加镜像仓库。

打开Maven目录下的conf目录，找到setting.xml配置文件。打开配置文件，在setting标签下添加本地仓库的位置。

```xml
<localRepository>xxx(仓库文件夹)</localRepository>
```

添加镜像仓库：

```xml
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
或者
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>
<mirror>
    <id>repo1</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo1.maven.org/maven2/</url>
</mirror>
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
```

### 依赖加载顺序

加载依赖时，会先在本地仓库寻找，如果没有会去镜像仓库查找，如果还是没有就会去中央仓库，找到之后，先拷贝到镜像仓库，再拷贝到本地仓库。

如果不添加镜像仓库，则会去中央仓库寻找。



## 三、Idea集成Maven







## 四、依赖管理

