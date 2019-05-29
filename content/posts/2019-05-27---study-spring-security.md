---
title: Spring Security 삽질 기록...
date: "2019-05-27T07:31:32.169Z"
template: "post"
draft: false
slug: "/posts/study-spring-security/"
category: "개발"
tags:
  - "개발"
  - "Spring"
  - "Spring Security"
description: "Spring Security 삽질 기록"
---

OAuth 2.0 인증 서버(자체 로그인 및 SNS 로그인을 할 수 있은 인증 서버)를 만들기 위해 Spring Security를 이용 하였으나

모르는 내용이 너무 많아 삽질한 기록들을 조금 정리하여 볼까 합니다... ㅠㅠ

Spring 사이트의 Spring Boot and OAuth2 튜토리얼(https://spring.io/guides/tutorials/spring-boot-oauth2/)을 베이스로 만들고 있는 중입니다.

* * *
**SNS 로그인 이후 client-side web으로 Redirect를 하지 않음(Authorization Code Grant Type)**

SNS 로그인 완료 후 client-side web으로 Redirect 하지 않아 이전 페이지로 돌아 갈려면 AuthenticationSuccessHandler를 직접 구현 하면
된다고 나와 있어 직접 구현을 해보려 했으나... 

요청한 client-side web의 redirect_url을 어떻게 가지고 있어야 하며 redirect를 할 때 code값을 주어야 하는데 어떻게 구현 하여야 할지 막막 하였는데

SavedRequestAwareAuthenticationSuccessHandler를 확장해서 사용하면 로그인 후 요청에 저장된 URL로 Redirect 한다고 하여 사용하여 보니... 정말 왜 이걸 못 찾아서 해맸나 싶어 자괴감이... ㅠㅠ
###Ref
https://www.baeldung.com/spring-security-redirect-login
* * *
**SNS 로그인 후 SNS 사용자의 정보 가져오기**

SNS 로그인 후 SuccessHandler에서 authentication 객체에 들어있는 userAuthentication에 접근 하여 했으나 가져 올 수 있는 건 Authorities, Credentials, Details, Principal 밖에 없어 의아 했으나 

authentication을 OAuth2Authentication으로 형변환 해주면 userAuthentication에 접근 할 수 있습니다.
###Ref
https://www.popit.kr/spring-security-oauth2-%EC%86%8C%EC%85%9C-%EC%9D%B8%EC%A6%9D/
* * *

추가적으로 정리 할게 있으면 다시 정리하여 글을 써볼 생각입니다.


별로 도움은 안되겠지만(ㅠ_ㅠ) 현재 개발중인 소스는 아래에 있습니다.
https://github.com/bcc829/noteAuthorizeServer