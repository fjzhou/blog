---
layout: default
title: 使用Jetty环境配置Solr和中文分词
---

1. Java环境  
2. 下载、解压缩Solr和Jetty。假设路径是`/usr/local/jetty`和`/usr/local/solr`  
3. 此时可以直接 `java -jar /usr/local/solr/example/start.jar`运行嵌入的简约版Jetty进行测试  
4. 复制`/usr/local/solr/example/lib/ext`里的jar到`/usr/local/jetty/lib/ext`  
5. 复制`/usr/local/solr/example/webapps/solr.war`到`/usr/local/jetty/webapps`（可以解压缩，低版本Jetty上不解压缩有可能有奇葩bug)  
6. 在Jetty的start.ini中增加 `-Dsolr.solr.home=/usr/local/solr/example/solr`  
7. 执行 `java -jar /usr/local/jetty/start.jar`

###IKAnalyzer中文分词的配置
0. 这里下载支持Solr4.0的2012版，因为Solr4开始改变了一部分接口，所以2012以前的有点兼容性问题  
1. 复制IKAnalyzer.jar和stopword.dic到`/usr/local/solr/example/solr/lib`  
2. `/usr/local/solr/example/solr/collection1/conf/solrconfig.xml`中增加:

```xml
<lib path="/usr/local/solr/example/solr/lib/IKAnalyzer.jar" />
```

3. 然后可以在`Schema.xml`中使用了：

```xml
<fieldType name="text_ik" class="solr.TextField">
    <analyzer type="index" class="org.wltea.analyzer.lucene.IKAnalyzer" isMaxWordLength="false" />
    <analyzer type="query" class="org.wltea.analyzer.lucene.IKAnalyzer" isMaxWordLength="true" />
</fieldType>
```
