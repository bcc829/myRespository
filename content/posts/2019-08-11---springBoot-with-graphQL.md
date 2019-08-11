---
title: Spring Boot와 graphql-java-kickstart를 이용한 GraphQL 서버 개발
date: "2019-08-11T07:31:32.169Z"
template: "post"
draft: false
slug: "/posts/make-GraphQLServer/"
category: "개발"
tags:
  - "개발"
  - "Spring Boot"
  - "graphql-java-kickstart"
  - "Kotlin"
  - "JPA"
description: "Spring Boot로 GraphQL 서버 개발(graphql-java-kickstart)"
---

기존 Restful Api 방식의 서버 개발을 주로 하다 GraphQL을 알고 난 이후 GraphQL을 이용한 서버 개발에 흥미가 생기기 시작하여
배우게 된것들을 정리 하여 봅니다.

---

우선 GraphQL의 정의를 위키 백과에서는 이렇게 설명 하고 있습니다

그래프QL(영어: GraphQL)은 페이스북이 2012년에 개발하여 2015년에 공개적으로 발표된 데이터 질의어이다. 그래프QL은 REST 및 부속 웹서비스 아키텍쳐를 대체할 수 있다. 클라이언트는 필요한 데이터의 구조를 지정할 수 있으며, 서버는 정확히 동일한 구조로 데이터를 반환한다. 그래프QL은 사용자가 어떤 데이터가 필요한 지 명시할 수 있게 해 주는 강타입 언어이다. 이러한 구조를 통해 불필요한 데이터를 받게 되거나 필요한 데이터를 받지 못하는 문제를 피할 수 있다.

##Ref https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%9E%98%ED%94%84QL

---

GraphQL에 대한 설명은 아래에 링크에 잘 나와 있어 따로 설명 드리진 않겠습니다

##Ref https://www.freecodecamp.org/news/so-whats-this-graphql-thing-i-keep-hearing-about-baf4d36c20cf

---

본론으로 들어가서 Spring Boot로 GraphQL서버를 만드는 법을 알아 봅시다.

다음과 같이 회원 정보를 저장한 테이블이 있고 그 회원의 소셜정보들을 저장하는 테이블이 있다고 가정하겠습니다 

![ERD](/media/ERD.PNG)

우선 member의 엔티티는 다음과 같이 작성하였습니다.(Kotlin으로 작성 하였습니다)

```tsx
@Entity
@Table(name = "member", schema = "test")
@EntityListeners(AuditingEntityListener::class) 
data class Member(
        @Id
        @GeneratedValue(strategy= GenerationType.IDENTITY)
        @Column(name="seq_id")
        val seqId : Long? = null,
        @Column(unique = true)
        var id: String? = null,
        var password: String? = null,
        var phoneNumber: String? = null,
        var address: String? = null,
        @Column(unique = true)
        var nickname: String? = null,
        @Column(unique = true)
        var email: String? = null,
        @CreatedDate
        val regDate: Date?  = null,
        @OneToMany(fetch = FetchType.LAZY, cascade = [CascadeType.ALL], mappedBy = "member")
        val socialMemberInfoList: MutableList<SocialMemberInfo>? = mutableListOf()
)
```
