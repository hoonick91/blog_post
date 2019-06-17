---
title: CI/CD
typora-copy-images-to: CI/CD
date: 2019-06-14 10:47:52
tags:
categories: devOps
---

## Jenkins + Gitlab

### Jenkins

```shell
docker pull jenkins/jenkins:lts
```

```shell
docker run -d --name jenkins -p 9001:8080 -p 9002:50000 -v /home/tacsadm/jenkins:/var/jenkins_home jenkins/jenkins:lts
```

가끔 jenkins folder의 권한이 문제가 생길 수 있다. ```chmod 777 /home/tacsadm/jenkins```

1. 초기 비밀번호 입력
2. Install suggested plugins
3. 회원가입, 로그인
   - 가끔 SetupWizard에서 blank 화면이 나올 수 있는데, 그냥 ```docker restart containerId``` 하면된다.
4. gitlab plugin 설치
5. 새작업 - freestyle project
6. 소스코드 관리 - git
   - Credentials를 등록 (gitlab API Token) : gitlab에서 발급받은 token을 이용
   - Username with password 등록
7. 빌드 유발
   - Build when a change is pushed to GitLab. GitLab webhook URL: http://121.156.46.166:9001/project/test-proj
8. Build
   - Execute shell Command 작성
   - 여기서 ./mvnw clean, test.. 같은 작업을 한다.
   - 가끔 webhook은 오는데 build가 안될때가 있다. 이때 webhook을 push event test해서 403 error가 나오면 다음과 같이 대처한다.
     - 빌드유발 - 고급 - Secret token - Generate
     - 발급 받은 token값을 gitlab의 webhook설정에 추가한다.
9. Jenkins 관리 
   - Global Tool Configuration - JDK - Name, JAVA_HOME 설정 (docker - Jenkins 내부의 openjdk 이용)

### Gitlab

```
docker pull gitlab/gitlab-ce
```

```shell
docker run -d --name gitlab -p 443:443 -p 80:80 -p 9022:22 -v /home/tacsadm/gitlab/config:/etc/gitlab -v /home/tacsadm/gitlab/logs:/var/log/gitlab -v /home/tacsadm/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce
```

1. 회원가입, 로그인
2. repository 생성
3. Push 
4. Settings - Access Tokens - Token 발급
5. Settings - Integrations - Jenkins 7에서 받은 http://121.156.46.166:9001/project/test-proj 입력



### Nexus

```shell
docker run -d --name nexus -p 8081:8081 -p 12000:12000 -v /home/tacsadm/nexus/nexus-data:/nexus-data sonatype/nexus3
```

1. 기본 ID/Password는 admin / admin1234

2. [http://download.sonatype.com/nexus/ci/latest.hpi](http://download.sonatype.com/nexus/ci/latest.hpi)를 다운 받아 Jenkins plugin을 upload한다.

3. jenkins의 시스템 설정에서 Sonatype Nexus의 계정 설정을 한다.

4. jenkins의 project 설정에서 Build - Invoke top-level Maven targets

   - Goals : clean package

5. Nexus Repository Manager Publisher 에서 원하는 형식의 dependency를 설정한다.

   - Artifacts에 build된 파일의 경로를 입력해주는 부분이 있는데, 이는 console out에 나오는 경로를 입력한다.

   https://support.sonatype.com/hc/en-us/articles/227256688-How-do-I-configure-the-Nexus-Jenkins-Plugin

#### Repository download

pom.xml에 repository와 dependency를 추가해 준다.

```xml
...
  <dependency>
    <groupId>org.jenkins-ci.main</groupId>
    <artifactId>jenkins-war</artifactId>
    <version>2.22</version>
  </dependency>
  </dependencies>
  
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>


    <repositories>
        <repository>
            <id>maven-releases</id>
            <url>http://121.156.46.166:8081/repository/maven-releases/</url>
        </repository>
    </repositories>
  </project>
```







docker run -d --name sonarqube -p 9004:9000 -p 9092:9092 sonarqube

zBBQKKZ8ci26vxA_RjAt

 docker run -d -p 9001:8080 -p 9002:50000 -v /home/tacsadm/jenkins:/var/jenkins_home jenkins/jenkins:lts



docker run -d -p 443:443 -p 80:80 -p 9022:22 -v /home/tacsadm/gitlab/config:/etc/gitlab -v /home/tacsadm/gitlab/logs:/var/log/gitlab -v /home/tacsadm/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce