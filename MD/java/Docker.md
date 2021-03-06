参考文档：  
https://yeasy.gitbooks.io/docker_practice/image/list.html  
https://juejin.im/post/5d4522c1f265da03e05af5f5
# 为什么用Docker
1. 传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。
2. 开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。

![1](https://github.com/jiangxk/study/blob/master/images/16c71a127f858a94.jpg)  
![2](https://github.com/jiangxk/study/blob/master/images/16c71c3c7c64067f.jpg)  
![3](https://github.com/jiangxk/study/blob/master/images/16c995c9dc77ca55.jpg)  


# Docker 包括三个基本概念
* 镜像（Image）
* 容器（Container）
* 仓库（Repository）  
理解了这三个概念，就理解了 Docker 的整个生命周期。
# Docker 包括三个基本概念介绍
1. Docker镜像  
- Docker镜像（Image）类似于虚拟机的镜像，可以将他理解为一个面向Docker引擎的只读模板，包含了文件系统。
例如：一个镜像可以完全包含了Ubuntu操作系统环境，可以把它称作一个Ubuntu镜像。镜像也可以安装了Apache应用程序（或其他软件），可以把它称为一个Apache镜像。

镜像是创建Docker容器的基础，通过版本管理和增量的文件系统，Docker提供了一套十分简单的机制来创建和更新现有的镜像。用户可以从网上下载一个已经做好的应用镜像，并通过命令直接使用。总之，应用运行是需要环境的，而镜像就是来提供这种环境。

2. Docker容器  
- Docker容器（Container）类似于一个轻量级的沙箱子（因为Docker是基于Linux内核的虚拟技术，所以消耗资源十分少），Docker利用容器来运行和隔离应用。
容器是从镜像创建的应用运行实例，可以将其启动、开始、停止、删除，而这些容器都是相互隔离、互不可见的。  
可以吧每个容器看作一个简易版的Linux系统环境（包括了root用户权限、进程空间、用户空间和网络空间），以及与运行在其中的应用程序打包而成的应用盒子。  
镜像自身是只读的。容器从镜像启动的时候，Docker会在镜像的最上层创建一个可写层，镜像本身将保持不变。就像用ISO装系统之后，ISO并没有什么变化一样。  

3. Docker仓库
- Docker仓库（Repository）类似与代码仓库，是Docker集中存放镜像文件的场所。  
有时候会看到有资料将Docker仓库和注册服务器（Registry）混为一谈，并不严格区分。实际上，注册服务器是存放仓库的地方，其上往往存放着多个仓库。每个仓库  集中存放某一类镜像，往往包括多个镜像文件，通过不同的标签（tag）来进行区分。例如存放Ubuntu操作系统镜像的仓库，称为Ubuntu仓库，其中可能包括  14.04,12.04等不同版本的镜像。    
根据存储的镜像公开分享与否，Docker仓库分为公开仓库（Public）和私有仓库（Private）两种形式。  
目前，最大的公开仓库是Docker Hub，存放了数量庞大的镜像供用户下载。国内的公开仓库包括Docker Pool等，可以提供稳定的国内访问。如果用户不希望公开分享自己的镜像文件，Docker也支持用户在本地网络内创建一个只能自己访问的私有仓库。    
当用户创建了自己的镜像之后就可以使用push将它上传到指定的公有或则私有仓库。这样用户下次在另一台机器上使用该镜像时，只需将其从仓库pull下来就可以了。

# Docker springboot mysql
1. springboot项目打包 
2. 创建一个文件存放springboot jar包 和Dockerfile（docker需要的） 文件  
内容是：
```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD spring-boot-docker.jar app.jar
ENV JAVA_OPTS=""
ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar

```
3. 在路径下构建镜像 docker build -t xxx .
4. springboot里面连接mysql地址要是mysql容器的地址
