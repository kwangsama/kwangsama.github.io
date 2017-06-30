---
layout: post
comments: true
title:  "Elasticsearch kibana logstash setup"
date:   2017-06-30 11:00:00 +0900
categories: elk
---

Centos6 기반에서 5.4.3 버전을 설치하기로 한다.

설치를 위해서는 java 1.8 버전을 필요로 하기 때문에 선행되어야 한다.
https://zetawiki.com/wiki/CentOS_JDK_%EC%84%A4%EC%B9%98

## Elasticsearch 설치

{% highlight shell %}
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
{% endhighlight %}

/etc/yum.repos.d/ 경로에 elasticsearch.repo 파일을 생성하고 아래의 설정을 붙여넣는다.

{% highlight shell %}
[elasticsearch-5.x]
name=Elasticsearch repository for 5.x packages
baseurl=https://artifacts.elastic.co/packages/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
{% endhighlight %}

{% highlight shell %}
sudo yum install elasticsearch
sudo chkconfig --add elasticsearch
{% endhighlight %}

## kibana 설치

{% highlight shell %}
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
{% endhighlight %}

/etc/yum.repos.d/ 경로에 kibana.repo 파일을 생성하고 아래의 설정을 붙여넣는다.

{% highlight shell %}
[kibana-5.x]
name=Kibana repository for 5.x packages
baseurl=https://artifacts.elastic.co/packages/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
{% endhighlight %}

{% highlight shell %}
sudo yum install kibana
sudo chkconfig --add kibana
{% endhighlight %}

## config 수정

# /etc/elasticsearch/elasticsearch.yml

cluster.name: 고유한 이름으로 수정.
network.host: 0.0.0.0
transport.host: 127.0.0.1

network.host를 0.0.0.0으로 수정하면 보안 이슈가 생길 수 있기 때문에 개발 환경에서만 지정.

#/etc/kibana/kibana.yml

server.host: "0.0.0.0"
elasticsearch.url: "http://localhost:9200" -> 설치된 elasticsearch서버의 host를 적어준다.

## 리눅스 설정 수정

특별히 수정하지 않았다면 elasticsearch는 9200, 9300 포트를 사용하고 kibana는 5601 포르를 사용하기 때문에 포트 오픈이 필요하다.

linux kernel parameter 수정

{% highlight shell %}
sudo sysctl -w fs.file-max=65536
{% endhighlight %}

/etc/security/limits.conf에 아래의 설정을 추가한다.

{% highlight shell %}
elasticsearch    hard    nofile          65536
elasticsearch    soft    nofile          65536
elasticsearch    hard    nproc           65536
elasticsearch    soft    nproc           65536
{% endhighlight %}

## 서비스 시작
여기까지 문제없이 되었다면 서비스를 시작해보자.

{% highlight shell %}
sudo service elasticsearch start
sudo service kibana start
{% endhighlight %}

## logstash 설치

logstash는 rpm 설치시 init.d에 실행 스크립트가 제대로 만들어 지지않아 압축파일을 받아 서비스를 실행하는 형태로 시작한다.

{% highlight shell %}
wget https://artifacts.elastic.co/downloads/logstash/logstash-5.4.3.tar.gz
tar xvfz logstash-5.4.3.tar.gz
ln -s logstash-5.4.3 logstash
{% endhighlight %}

기본 실행
{% highlight shell %}
./bin/logstash -f 설정파일
{% endhighlight %}

백그라운드 실행
{% highlight shell %}
nohup ./bin/logstash -f 설정파일 &
{% endhighlight %}

{% include disqus.html %}
{% include analytics.html %}
