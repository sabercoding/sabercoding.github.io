---
layout: post
title: "es2.3.3+ik1.9.3安装"
date: 2017-02-09 02:28:56.000000000 +09:00
---

## 安装es2.3.3
```
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.3/elasticsearch-2.3.3.tar.gz
tar -zxvf elasticsearch-2.3.3.tar.gz
./bin/elasticsearch //加上 -d为后台运行
```
## 安装ik
es2.3.3对应的ik版本是1.9.3

```
wget https://codeload.github.com/medcl/elasticsearch-analysis-ik/zip/v1.9.3
unzip v1.9.3
```
#### 安装maven
```
wget http://mirrors.cnnic.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.zip
unzip apache-maven-3.3.9-bin.zip

//修改配置
sudo vi /etc/profile
export M2_HOME=/home/shuibo.cai/apache-maven
export PATH=$PATH:$M2_HOME/bin
source /etc/profile
mvn -version  //检测是否成功
//构建java包
mvn clean package 
```
取出“target/releases/”下的zip包，在plugins目录中新建目录“ik”，将zip包解压到此目录中，重启elasticsearch即可
