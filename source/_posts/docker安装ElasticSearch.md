---
title: 'docker安装ElasticSearch'
date: 2020-07-05 09:47:53
tags:
  - ElasticSearch
  - docker
  - 教程
categories:
  - [教程,docker,ElasticSearch]
cover: 'http://img5.imgtn.bdimg.com/it/u=2677121778,4211715873&fm=26&gp=0.jpg'
---
# Elastic Search 安装

## version: 7.6.0

+ 搜索：

> docker search elasticsearch

+ 拉去镜像：

> docker pull elasticsearch:7.6.0

+ 启动

> docker run --name icop-dev-es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -d elasticsearch:7.6.0

+ 验证(出现结果验证成功)

  浏览器访问：`http://ip:9200`

```json
{
  "name": "808f2cae048d",
  "cluster_name": "docker-cluster",
  "cluster_uuid": "0H1lU6zpSyOy-_LzXe-0nQ",
  "version": {
    "number": "7.6.0",
    "build_flavor": "default",
    "build_type": "docker",
    "build_hash": "7f634e9f44834fbc12724506cc1da681b0c3b1e3",
    "build_date": "2020-02-06T00:09:00.449973Z",
    "build_snapshot": false,
    "lucene_version": "8.4.0",
    "minimum_wire_compatibility_version": "6.8.0",
    "minimum_index_compatibility_version": "6.0.0-beta1"
  },
  "tagline": "You Know, for Search"
}
```

+ 进入elastic search容器

> docker exec -it icop-dev-es /bin/bash

+ 跨域问题解决

> cd /usr/share/elasticsearch/config/
> vi elasticsearch.yml  
>
> http.cors.enabled: true
> http.cors.allow-origin: "*"

+ 修改配置后重启容器

> docker restart icop-dev-es

# 安装ik分词器
注意：elasticsearch的版本和ik分词器的版本需要保持一致，不然在重启的时候会失败。可在官网查看对应的版本并获取下载连接： https://github.com/medcl/elasticsearch-analysis-ik/releases  ，并在下载完成之后退出容器并重启容器。 

> docker exec -it icop-dev-es /bin/bash
>
> cd /usr/share/elasticsearch/plugins/
> elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.6.0/elasticsearch-analysis-ik-7.6.0.zip



# kibana安装

### version: 7.6.0

+ 拉取镜像

> docker pull kibana:7.6.0

+ 启动容器，并修改 kibana.yml中的地址：`elasticsearch.hosts: [ "http://ip:9200" ]`

> docker run --name icop-dev-kibana -e ELASTICSEARCH_URL=http://IP:9200 -p 5601:5601 -d kibana:7.6.0
>
> docker exec -it icop-dev-kibana /bin/bash
>
> /usr/share/kibana/config
>
> vi kibana.yml
>
> docker start  icop-dev-kibana

+ 访问验证：http://ip:5601/


