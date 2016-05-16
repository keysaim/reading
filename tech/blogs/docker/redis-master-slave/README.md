# 用docker搭建redis的master-slave
## 简介
本文将以docker为基础，搭建一套简单的应用案例。该案例的应用架构将采用redis的master-slave模式作为数据存储，以django为框架提供web访问，以haproxy作为负载均衡。其架构图如下.

<div style="text-align:center">
    <img src="demoarch.png" alt="" width="400"/>
</div>


