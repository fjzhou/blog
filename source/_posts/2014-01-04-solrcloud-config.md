---
layout: post
title: 配置双机SolrCloud
category: Solr
---

0. 物理环境：两台服务器，CentOS，分别是10.0.2.4和10.0.2.6。已经安装了Jetty和Solr。  
1. 下载安装ZooKeeper到/usr/local/zookeeper。  
2. 复制/usr/local/zookeeper/conf/zoo_example.cfg为zoo.cfg，修改其中的dataDir，默认是临时目录。启动ZooKeeper.  
3. 因为默认会使用第一个网卡的地址为基地址，如果Solr使用的网卡不是第一块网卡，则需要修改/usr/local/solr/example/solr/solr.xml。
根据环境这里修改其中的&lt;SolrCloud&gt;中的host为`${host:10.0.2.4}`。  
4. 然后修改其中一台jetty中的start.ini。加入以下参数：  

```bash
# 定义分片的个数
-DnumShards=2
# 因为是Jetty方式部署，所以不是默认的端口
-Djetty.port=8080
# 配置路径
-Dbootstrap_confdir=/usr/local/solr/example/solr/collection1/conf
# 配置名称
-Dcollection.configName=myconf
# ZooKeeper连接
-DzkHost=10.0.2.4:2181
```
5. 上面这是第一个节点的配置，其他Node的配置简单的多：

```bash
-Djetty.port=8080
-DzkHost=10.0.2.4:2181
```
