---
title: Spring Boot와 graphql-java-kickstart를 이용한 GraphQL 서버 개발
date: "2019-08-14T07:31:32.169Z"
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

기존 Restful Api 방식의 서버 개발을 주로 하다 GraphQL을 알고 난 이후 GraphQL을 이용한 서버 개발에 흥미가 생기기 시작하여 Spring Boot로 GraphQL 서버 개발하는 법을 정리해 봅니다.

---

먼저 GraphQL의 정의를 위키 백과에서는 이렇게 설명 하고 있습니다

그래프QL(영어: GraphQL)은 페이스북이 2012년에 개발하여 2015년에 공개적으로 발표된 데이터 질의어이다. 그래프QL은 REST 및 부속 웹서비스 아키텍쳐를 대체할 수 있다. 클라이언트는 필요한 데이터의 구조를 지정할 수 있으며, 서버는 정확히 동일한 구조로 데이터를 반환한다. 그래프QL은 사용자가 어떤 데이터가 필요한 지 명시할 수 있게 해 주는 강타입 언어이다. 이러한 구조를 통해 불필요한 데이터를 받게 되거나 필요한 데이터를 받지 못하는 문제를 피할 수 있다.

##Ref https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%9E%98%ED%94%84QL

---

GraphQL에 대한 설명은 아래에 링크에 잘 나와 있어 참고 하시면 좋을것 같습니다.

##Ref https://www.freecodecamp.org/news/so-whats-this-graphql-thing-i-keep-hearing-about-baf4d36c20cf

---

본론으로 들어가서 Spring Boot로 GraphQL서버를 만드는 법을 알아 봅시다.

먼저 아래의 library를 추가 합니다

```tsx
// graphql-java-tool
implementation("com.graphql-java-kickstart:graphql-java-tools:5.6.0")
// graphql-java-kickstart 
implementation("com.graphql-java-kickstart:graphql-spring-boot-starter:5.10.0")
// graphiql-java-kickstart - graphiql을 사용할 경우 추가
implementation("com.graphql-java-kickstart:graphiql-spring-boot-starter:5.10.0")
// scalar Date를 사용하기 위해 추가
implementation("com.zhokhov.graphql:graphql-datetime-spring-boot-starter:1.5.1")
```

다음과 같이 회원 정보를 저장한 테이블이 있고 그 회원의 소셜정보들을 저장하는 테이블이 있다고 가정하겠습니다.

![ERD](/media/ERD.PNG)

member 엔티티는 다음과 같이 작성하였습니다.(Kotlin으로 작성 하였습니다)

```tsx
@Entity
@Table(name = "member", schema = "test")
@EntityListeners(AuditingEntityListener::class) 
data class Member(
        
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        val seqId: Long? = null,

        @Column(unique = true)
        val id: String,

        var password: String? = null,

        var phoneNumber: String? = null,

        var address: String? = null,

        @Column(unique = true)
        val nickname: String,

        @Column(unique = true)
        val email: String,

        @CreatedDate
        var regDate: Date? = null,

        @LastModifiedDate
        var updDate: Date? = null,

        @OneToMany(fetch = FetchType.LAZY, cascade = [CascadeType.ALL], mappedBy = "member")
        var socialMemberInfoList: MutableList<SocialMemberInfo>? = mutableListOf()
)
```

SocialMemberInfo 엔티티는 다음과 같이 작성하였습니다

```tsx
@Entity
@Table(schema = "test", name = "social_member_info")
data class SocialMemberInfo(

        @Id
        @GeneratedValue(strategy= GenerationType.IDENTITY)
        val seqId: Long? = null,

        val providerType: String,

        val principal: String,

        @CreatedDate
        var regDate: Date? = null,

        @LastModifiedDate
        var updDate: Date? = null,

        @ManyToOne(fetch = FetchType.LAZY)
        var member: Member? = null
)
```

그 다음으로 스키마를 만듭니다 resources 폴더 아래의 schema.graphqls 파일을 만든 후 아래와 같이 작성 하였습니다.

```tsx
scalar Long
scalar Date

type Query {
    #회원 seq number로 회원 조회
    getMemberBySeqId(
        #회원 seq number
        seqId: Long!
    ): Member
}

#회원
type Member {
    #회원 seq number
    seqId : Long
    #회원 ID
    id: ID
    #휴대전화 번호
    phoneNumber: String
    #주소
    address: String
    #닉네임
    nickname: String
    #이메일
    email: String
    #등록 일자
    regDate: Date
    #수정 일자
    updDate: Date
    #Social 회원 정보 리스트
    socialMemberInfoList: [SocialMemberInfo]

}

#소셜 회원 정보
type SocialMemberInfo{
    #소셜 계정 제공 사이트
    providerType: String
    #소셜 계정 principal
    principal: String
}
```

그리고 스키마에 정의한 Query인 getMemberBySeqId의 QueryResolver를 작성 합니다. (Repository의 코드는 생략 하겠습니다.)

```tsx
@Component
class MemberQueryResolver : GraphQLQueryResolver {

    @Autowired
    private lateinit var memberRepository: MemberRepository

    fun getMemberBySeqId(seqId: Long): Member? {
        return memberRepository.findBySeqIdEquals(seqId)
    }
}
```

member type에는 socialMemberInfoList라는 서브필드가 존재 합니다. 서브 필드의 Resolver를 작성 합니다.

```tsx
@Component
class MemberResolver: GraphQLResolver<Member> {

    @Autowired
    private lateinit var socialMemberInfoRepository: SocialMemberInfoRepository

    fun socialMemberInfoList(member: Member): List<SocialMemberInfo>? {
        return socialMemberInfoRepository.findByMemberSeqId(member.seqId!!)
    }

}
```

또는 이미 member 엔티티에  socialMemberInfoList 필드가 있기 때문에 socialMemberInfoList의 Fetch전략을 EAGER로 바꿔주면 됩니다.

```tsx
@OneToMany(fetch = FetchType.EAGER, cascade = [CascadeType.ALL], mappedBy = "member")
var socialMemberInfoList: MutableList<SocialMemberInfo>? = mutableListOf()
```

이제 코드 작성은 끝났습니다. 이제 작성한 GraphQL서버가 잘 작동하는지 테스트 해봅니다.

위의 추가한 graphiql-spring-boot-starter library가 있기 때문에 http://localhost:8080/graphiql로 접근시 아래의 화면이 나옵니다.

<img src="/media/graphIql1.PNG" width="100%" height="100%">

아래와 같이 쿼리를 작성 후 실행시키면 실제 GraphQL query를 작성한 GraphQL서버에 요청하고 받아온 값을 보여 줍니다.

<img src="/media/graphIql2.PNG" width="100%" height="100%">

socialMemberInfoList 서브필드도 잘 조회하는 것을 볼 수 있습니다.

<img src="/media/graphIql3.PNG" width="100%" height="100%">

---

아직 GraphQL에 대한 이해가 부족하여 GraphQL에 대한 설명이 부족하여 코드 작성 위주로 글을 작성 하였습니다.

추후 조금 GraphQL을 공부를 한 후 GraphQL에 대한 설명과 mutation query에 대해 작성 하도록 하겠습니다.

작성 중인 코드는 아래의 링크에 있습니다. 

https://github.com/bcc829/noteGraphQLServer