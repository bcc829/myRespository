---
title: 윈도우용 Package Manager인 Chocolatey 사용하기
date: "2019-04-24T07:31:32.169Z"
template: "post"
draft: false
slug: "/posts/package-manager-for-window-Chocolatey/"
category: "개발"
tags:
  - "개발"
  - "Chocolatey"
description: "Chocolatey 사용하기"
---

데비안(Debian)계열의 리눅스에서 쓰이는 apt나 macOS에서 쓰이는 Homebrew처럼 윈도우에서도 package manager가 있었으면 좋겠다고 생각 했었는데 이미 그러한 package manager가 이미 여럿 나와 있었습니다.

그중에서도 Chocolatey를 사용 해본 결과 상당히 맘에 들어 Chocolatey사용법을 간략하게나마 적어 봅니다.


**Chocolatey를 설치하기위해 필요한 사항은 아래와 같습니다.**

+ Windows 7+ / Windows Server 2003+
+ PowerShell v2+
+ .NET Framework 4+ (.NET 4.0이 설치되지 않았다면 installer가 .NET 4.0설치를 하려고 하기 때문에 따로 설치하실 필요는 없습니다)


Chocolatey의 설치는 간단합니다. Powershell을 실행하여 아래의 명령어를 입력 후 실행 하시면 됩니다

```tsx
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

이제 https://chocolatey.org/packages 에 가셔서 필요한 package를 검색하고 다운 받으시면 됩니다.
예를 들어 java8을 설치할 경우 PowerShell에서 아래의 명령어만 실행시키면 됩니다.

```tsx
choco install jdk8
```

Chocolatey를 사용하면 이전 처럼 jdk설치후 환경변수를 잡고 해야되는 번거로운 일을 많이 줄일 수 있겠습니다.

##Ref
https://chocolatey.org/