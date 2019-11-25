---
title: Spring Boot와 graphql-java-kickstart를 이용한 GraphQL 서버 개발2
date: "2019-11-25T07:31:32.169Z"
template: "post"
draft: false
slug: "/posts/make-GraphQLServer2/"
category: "개발"
tags:
  - "개발"
  - "Spring Boot"
  - "graphql-java-kickstart"
  - "Kotlin"
  - "JPA"
description: "Spring Boot로 GraphQL 서버 개발(graphql-java-kickstart)"
---

이전 Spring Boot로 GraphQL 서버 개발에 이어 mutation을 작성 하는 법에 대해 알아 보겠습니다.

---

다음과 같이 회원이 작성 할 수 있는 게시글 테이블이 있다고 가정 하여 보겠습니다.

![ERD](/media/ERD2.PNG)

post 엔티티는 다음과 같이 작성하였습니다.(Kotlin으로 작성 하였습니다)

```tsx
@Entity
@EntityListeners(AuditingEntityListener::class)
@Table(name = "post", schema = "test")
data class Post(
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        val seqId: Long? = null,

        var title: String,

        var content: String,

        var readCount: Long? = 0,

        var deleteFlag: Boolean? = false,

        @ManyToOne(fetch = FetchType.LAZY)
        var member: Member? = null,
)
```

그 다음으로 스키마를 만듭니다 resources 폴더 아래의 schema.graphqls 파일을 만든 후 아래와 같이 작성 하였습니다.

```tsx
scalar Long
scalar Date

type Mutation {
 createPost(createPostInput: CreatePostInput!): Post!
}

#게시글
type Post {
 #게시글 seq id
 seqId: Int!
 #게시글 제목
 title: String!
 #게시글 내용
 content: String!
 #게시글 조회 수
 readCount: Int!
 #게시글 등록일
 regDate: Date
 #게시글 수정일
 updDate: Date
 #등록한 사용자 정보
 member: Member
}


#게시글 추가 Input
input CreatePostInput{
 #게시글 제목
 title: String
 #게시글 내용
 content: String
 #회원 seqId
 memberSeqId: Long!
}

```

위의 스키마에서 createPost mutation의 인자를 CreatePostInput타입으로 정의 하였습니다

아래와 같이 CreatePostInput타입에 매핑되는 클래스를 작성해 줍니다.

```tsx
data class CreatePostInput (
        val title: String,
        val content: String,
        val memberSeqId: Long
)
```


그리고 스키마에 정의한 mutation인 createPost의 GraphQLMutationResolver 작성 합니다. (Repository의 코드는 생략 하겠습니다.)

```tsx
@Component
class PostQueryResolver : GraphQLMutationResolver {

    @Autowired
    lateinit var postRepository: PostRepository

    @Autowired
    lateinit var memberRepository: MemberRepository

    fun createPost(createPost: CreatePostInput): Post {

        val post = Post(title = createPost.title, content = createPost.content)

        val member = memberRepository.findBySeqIdEquals(createPost.memberSeqId)
                ?: throw CustomException(NoteErrorCode.UNAUTHORIZED_USER)

        post.member = member

        return postRepository.save(post)
    }

}
```

이제 코드 작성은 끝났습니다. 이제 작성한 mutation이 잘 작동하는지 테스트 해봅니다.

<img src="/media/mutation.PNG" width="100%" height="100%">

---

GraphQL을 조금 사용해보며 느낀 점은 기존 rest api의 경우 front end 개발자와의 협업시 api에 대한 문서화 작업이 필요 하다는 점이 불편하게 느껴졌었는데요. 

GraphQL을 사용하게 되면 스키마만 공유 되면 어떤 형식으로 데이터를 주고 받을 지에 대해 알 수 있기 때문에
협업 하기 더 좋을것 같습니다 

작성 중인 코드는 아래의 링크에 있습니다. 

https://github.com/bcc829/noteGraphQLServer