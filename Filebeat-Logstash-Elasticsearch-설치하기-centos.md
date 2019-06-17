---
title: 'Filebeat, Logstash, Elasticsearch 설치하기(centos)'
typora-copy-images-to: 'Filebeat, Logstash, Elasticsearch 설치하기(centos)'
date: 2019-04-17 18:51:50
tags:
categories: elastic-stack
---



Centos 처음 설치했을때 외부인터넷과 연결이 되는지 체크해야한다. centos에서 외부인터넷 연결이 되는지 $> ping google.com 로 확인할 수 있고, 외부에서는 ping centos주소(ip addr을 통해 얻을 수 있다.) 로 확인할 수 있다. 

virtualbox의 인터넷연결은  장치 - 네트워크 연결 - 어댑터 브릿지

Filebeat 설치

Logstash 설치

Elasticsearch 설치





## 실행

> Filebeat, Logstash, Elasticsearch는 모두 Service를 이용하여 실행, 재실행, 종료를 할 수 있다.

```shell
$> service filebeat start
$> service logstash start
$> service elasticsearch start
```



Filebeat는 설정값인 filebeat.yml을 기반으로 실행할 수 있다.

```shell
$> pwd
/usr/share/filebeat/
$> ./filebeat -f /etc/filebeat/filbeat.yml
```





### Logstash 실행

> Service를 이용한 실행

```shell
service logstash start
```



> logstash.conf를 기반으로 logstash 실행

```shell
cd /usr/share/logstash
./logstash -f /etc/logstash/con.d/logstash.conf
```

```shell
$> vi logstash.conf

```



### Elasticsearch 실행





ping

ps -ef | grep logstash

cd /var/log/logstash

cd /etc/logstash/con.d/logstash.conf



tail -f

Netstat -ant

Firewall