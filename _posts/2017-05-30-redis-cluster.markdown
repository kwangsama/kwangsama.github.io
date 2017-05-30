---
layout: post
comments: true
title:  "redis cluster 간단 정리 및 설치"
date:   2017-05-30 11:07:41 +0900
categories: redis
---

레디스의 HA, partitioning 구성을 위해 redis 3.0.0 부터 레디스 클러스터를 지원하기 시작했다.

클러스터 모드로 구성된 각 레디스 서버에 데이터를 균등하게 분산해서 저장을 한다.

이때 각 노드에 대해 master - slave를 설정해서 fail over 역할을 할 수 있게 한다. 레디스 공식 문서에서는 master 3, slave 3를 최소 구성으로 하라고 추천하고 있다.

레디스의 기본 port 는 6379이고 이 port에 10000을 더한 값을 레디스 노드간에 데이터 버스 용도로 사용하고 있기 때문에 방화벽 정책도 같이 고려해야 될듯하다.

virtualbox로 3대의 서버를 구성했으며, 각 서버는 nat, 호스트 전용 어탭터 네트워크 설정으로 구성되어 있다.

{% highlight shell %}
#=> requirement
yum groupinstall 'Development Tools'

#=> redis install
wget http://download.redis.io/releases/redis-3.2.9.tar.gz
tar -xvf redis-3.2.9.tar.gz
cd redis-3.2.9
make

#=> ruby install
curl -L get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
rvm install 2.4.0
gem install redis

#-> redis.conf 수정
cluster-enabled yes
cluster-node-timeout 5000
appendonly yes

#=> redis stert
src/redis-server redis.conf
{% endhighlight %}

클러스터로 묶을 ip 연결


3대의 서버에 레디스를 설치하고 클러스터 모드로 시작했다면 루비 스크립트를 이용해서 클러스터로 묶을수 있다.

src/redis-trib.rb create 192.168.58.11:6379 192.168.58.12:6379 192.168.58.13:6379

3대의 서버를 클러스터로 묶었다. –replicas 옵션이 없기 때문에 슬레이브는 따로 구성되지 않았다.

<https://redis.io/topics/cluster-tutorial>
<http://www.redisgate.com/redis/cluster/cluster_introduction.php>
<http://blog.leekyoungil.com/?p=206>

{% include disqus.html %}
