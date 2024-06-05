---
title: 스프링 프로젝트 환경 원하는 대로 설정하기(feat. IntelliJ)
date: 2024-06-05 23:43:51 +0900
categories:
  - Spring
tags:
  - IntelliJ
  - Gradle
---
공부를 하고 싶은 게 있는데, 프로젝트 환경을 과거에 맞춰야 했다. 필요한 스택은 아래와 같다.
<br>
- Java 8
- Gradle 4.x
- Spring Boot 2.1.x
<br>

반면, 현재 [Spring Initializr](https://start.spring.io/)에서 지원하는 버전은 이렇다.
<br>
- Java 17
- Spring Boot 3.3.0
- Gradle 8.x
<br>
평소와 같이 initalizr로 쉽게 프로젝트를 시작할 수 없는 건 당연한 일.. 일단 IntelliJ로 간단한 프로젝트를 생성해주고 원하는 대로 환경을 설정해주기로 했다. 아래는 그 방법을 정리한 내용이다.
<br>
<br>
**Spring Initializr가 지원하지 않는 버전으로 스프링 환경 설정하기!**
<br>
<br>
<br>
# Gradle 버전 설정
<br>
## Gradle 버전 확인
IntelliJ를 활용해 프로젝트를 생성했으므로, 처음 사용된 Gradle 버전 먼저 확인한다. 
<br>
![image](https://github.com/yj-melissa/yj-melissa.github.io/assets/109330621/8794443c-14d7-4c1f-94d1-1286eada484f)
<br>
사진과 같이, `gradle/wrapper/gradle-wrapper.properties` 파일을 확인한다. 
<br>
```
distributionBase=GRADLE_USER_HOME  
distributionPath=wrapper/dists  
distributionUrl=https\://services.gradle.org/distributions/gradle-8.6-bin.zip  
networkTimeout=10000  
validateDistributionUrl=true  
zipStoreBase=GRADLE_USER_HOME  
zipStorePath=wrapper/dists
```
<br>
파일을 열면 위와 같은 항목이 보이는데, 그 중 `distributionUrl` 부분을 확인하면 현재 프로젝트의 Gradle 버전을 확인할 수 있다. 여기서는 8.6 버전인 것.
<br>

## Gradle 버전 수정
<br>
```bash
./gradlew wrapper --gradle-version 4.x.x
```

현재 프로젝트 위치에서 터미널을 연 후 명령어를 입력해준다. 버전은 원하는 대로 입력하면 된다. 그러면 성공!

<br>
<br>
# Spring Boot 버전 설정
<br>
## Spring Boot 버전 체크
```
plugins {  
    id 'java'  
    id 'org.springframework.boot' version '3.2.4'  
    id 'io.spring.dependency-management' version '1.1.4'  
}
```

`build.gradle` 파일을 열면 바로 확인 가능하다. 친절하게도 파일 최상단에 위치하고 있다. 이 프로젝트의 현재 버전은 '3.2.4'다.

<br>
## Spring Boot 버전 설정
```
buildscript {  
    ext {  
        springBootVersion = '2.x.x.RELEASE'  
    }    repositories {  
        mavenCentral()  
    }  
    dependencies {  
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")  
    }  
}
```
마찬가지로 `build.gradle` 파일 최상단에 이 코드를 추가해주면 된다.  코드의 '2.x.x' 부분에 원하는 버전을 입력해준 후, build를 다시 시도해보면 된다.

<br>
<br>
# JAVA 버전 설정
IntelliJ 기준으로 프로젝트를 생성할 때부터 원하는 대로 JDK를 설정해 생성할 수 있다. 다만 생성 후 바꿔야 할 때가 있으니, <u>IntelliJ 기준으로</u> JDK 버전을 바꾸는 방법을 정리한다.


> Settings(`ctrl` + `alt` + `s`) > Build, Execution, Deployment > Build Tools > Gradle

Gradle 탭 하단부에 아래 사진과 같이 **Gradle JVM** 항목이 있다. 이미 원하는 버전의 JDK가 있다면 그걸 설정해주면 되고, 만일 없으면 Download JDK를 눌러서 원하는 버전의 JDK를 다운받아 설정해주면 된다. 

![image](https://github.com/yj-melissa/yj-melissa.github.io/assets/109330621/6b42c891-71bc-430f-872b-c2b624741262)

<br>
위와 같은 방법으로 처음 필요했던 프로젝트 환경을 구성할 수 있었다. 