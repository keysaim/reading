# 用docker搭建全栈式应用 (三）—— 管理篇
## 简介
在[上一篇][previous blog]我们讲述了如何基于Dockerfile实现docker image的自动构建，并基于此重新实现了咱们本系列中的全栈式应用。然而，虽然构建过程实现了自动化，但是在实际部署的时候我们还是需要手动的理清所有服务的依赖关系，并依次启动所有服务，更严重的是，每次重启的时候，我们都需要重复这些操作，这个是不能接受的。

那么有啥办法改善么？记得业内似乎总有这么个说法，大凡超过重复操作必须得用脚本实现自动化，此谓IT男的基本素养。既然如此，咱们也得用脚本自动化咱们的操作了。非常直接的一种方案就是咱自己写个shell的脚本，如下：
* 启动脚本

    ```shell
    docker run -d --name redis-master redis-master
    docker run -d --name redis-slave1 --link redis-master:master redis-slave1
    docker run -d --name redis-slave2 --link redis-master:master redis-slave2
    docker run -d --name app2 --link redis-master:redis app 0.0.0.0:8002
    docker run -d --name haproxy --link app1:app1 --link app2:app2 -p 6301:6301 ha-app
    ```
    看起来还可以吗，简单的几条语句。但是，咱可不要忘了一点，每个语句咱还木有检查执行的结果。还有这个只是启动的脚本，还有停止的脚本，清除的脚本等等。更要命的是，如果下次我部署其它类型的应用，尼玛还得把这一些列的脚本复制并修改一通，这个有点弱爆了的感觉啊。

一般来讲，IT界总是存在这一的规律，当一个烦恼只是你一个人的时候，OK你可以随便整了；当你觉得可能会是很多人的烦恼的时候，这时必须得已经有人提供了解决方案了。所以，在当下技术更新如此剧烈的年代，切记不要随便就自己吭哧吭哧的闷声瞎搞了，第一要务就是google之。一般情况下，总是与大神提供了解决方案，如果真心没有，那么恭喜你，赶紧自己整一个并开放出来，那么你就是那个大神了。

同样，在咱们本文碰到的问题中，早就有对应的解决方案了，那就是基于Docker compose工具的服务管理。本文将详述如何基于Docker compose管理服务。当然，例子还是那个例子咯。

## Docker Compose工具简介
关于Docker Compose，[官方](https://docs.docker.com/compose/overview/)其实有非常详尽的介绍。这里就简单的总结一下。

那么，Docker Compose是什么呢？
* Docker的一个工具
* 定义，运行并管理多个容器应用
* 基于Compose文件
* 通常应用于开发、测试、CI环境

Compose的使用一般与Dockerfile结合，包括三个步骤：
* 定义好所有应用的Dockerfile
* 在Compose文件中定义、配置好所有服务
* `docker-compose up`启动你的服务

下面，咱们就医本系列的经典例子：全栈式应用，来讲述Docker Compose的使用。

## 基于Docker Compose搭建、管理全栈式应用
### Docker Compose的安装
安装过程其实非常简单，[官方](https://docs.docker.com/compose/install/)提供了非常详细的安装过程。这里讲述其中一种基于command line的安装方式。
* 使用curl下载Compose工具

    ```
    curl -L https://github.com/docker/compose/releases/download/1.7.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    ```
    当然，这里使用的是1.7.1版本，如果有新版本发布了，你只需要修改成相应的版本即可。

* 添加Compose工具执行权限

    ```
    chmod +x /usr/local/bin/docker-compose
    ```

* 测试一下是否下载成功

    ```
    $ docker-compose --version
    docker-compose version: 1.7.1
    ```

### Compose文件的编写
前面我们讲了，一般来说，Compose的使用包括了三个步骤。由于在[上一篇][previous blog]已经编写好了Dockerfile文件，这里咱们就直接开始Compose文件的编写这一步骤了。

本文简介中，我们已经描述了如何用脚本实现管理。Compose文件中实现了对所有服务的定义，这些定义包括了所有服务之间的依赖关系已经每个服务启动所依赖的配置，如启动参数，是否挂载volume，是否发布服务端口等等。

[previous blog]: ../full-stack-demo2/README.md
