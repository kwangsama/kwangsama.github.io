---
layout: post
comments: true
title:  "CentOS7 firewall"
date:   2017-06-02 12:00:00 +0900
categories: linux
---

기존에 CentOS6.x 에서는 iptables 서비스를 이용해서 방화벽 port를 관리했는데 7로 가면서
firewalld를 사용하는것으로 바뀐것을 확인했다.

firewalld서비스의 상태 확인을 위해 systemctl을 이용한다.

{% highlight shell %}
systemctl [ status | start | stop | restart | enable | disable ] firewalld

#=> active한 상태의 모든 서비스 출력
systemctl list-units --type service
{% endhighlight %}

firewalld의 관리는 firewall-cmd 도구를 이용해 컨트롤한다.

{% highlight shell %}
#=> 목록
firewall-cmd --get-services

#=> 상태확인
firewall-cmd --state

#=> 추가 / 삭제
firewall-cmd --permanent --zone=public --add-port=3306/tcp
firewall-cmd --permanent --zone=public --remove-port=3306/tcp

#=> relaod
firewall-cmd --reload

{% endhighlight %}

참고 자료

<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sect-Managing_Services_with_systemd-Services.html>

<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html>

{% include disqus.html %}
{% include analytics.html %}
