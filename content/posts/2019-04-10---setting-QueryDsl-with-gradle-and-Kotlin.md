---
title: Kotlin에서 QueryDsl 셋팅하기(Gradle)
date: "2019-04-10T07:31:32.169Z"
template: "post"
draft: false
slug: "/posts/setting-QueryDsl-with-gradle-and-Kotlin/"
category: "개발"
tags:
  - "개발"
  - "Kotlin"
  - "Gradle"
  - "spring boot"
  - "QueryDsl"
description: "Kotlin에서 QueryDsl 셋팅"
---

개인 공부를 위해 Kotlin + Spring boot를 이용하여 프로젝트를 진행하던 중
QueryDsl을 사용하기 위해 셋팅을 하여도 잘 되지 않아 고생 한 적이 있습니다.

![querydsl_commit](/media/querydsl_commit.PNG)
(spring boot도 JPA도 Kotlin도 gradle마저 익숙하지 않아 QueryDsl을 설정 하다가 오랜 기간 손을 놓은 모습...)

example 코드를 찾았지만 QueryDsl 버전을 3.xx로 쓰고 있어 아래와 같이 버전을 4.xx로 수정하였습니다.

```tsx
buildscript {
  	ext {
		kotlinVersion = '1.3.11'
		queryDslVersion = '4.1.3' //QueryDsl version
  }
  repositories {
    mavenCentral()
  }

  dependencies {
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
  }
}

apply plugin: 'kotlin'
apply plugin: 'kotlin-kapt' //Annotation Processing with Kotlin
apply plugin: 'idea'

dependencies {
  compile ("com.querydsl:querydsl-jpa:${queryDslVersion}")
  kapt ("com.querydsl:querydsl-apt:${queryDslVersion}:jpa")
}

idea {
  module {
    def kaptMain = file('build/generated/source/kapt/main')
    sourceDirs += kaptMain
    generatedSourceDirs += kaptMain
  }
}
```
build.gradle에 위의 내용들을 추가 후 

```tsx
gradlew compileKotlin
```
compileKotlin task를 실행 하게 되면

다음과 같이 build/generated/source/kapt/main에 Qdomain이 생성 됩니다.

![Qdomain](/media/Qdomain.PNG)



##Ref
https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-querydsl
