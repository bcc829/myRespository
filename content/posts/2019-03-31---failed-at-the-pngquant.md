---
title: Window10에서 npm install시 Failed at the pngquant-bin@5.0.2 postinstall script 에러가 나는 경우 해결법
date: "2019-03-31T06:30:32.169Z"
template: "post"
draft: false
slug: "/posts/failed-at-the-pngquant/"
category: "개발"
tags:
  - "개발"
  - "이슈"
description: "pngquant-bin@5.0.2 postinstall script 에러 해결법"
---

블로그를 시작하기위해 Gatsby를 이용하여 환경 설정 중
package를 받기위해 npm install을 실행 하였는데 다음과 같은 에러가 났습니다.

```tsx
error code ELIFECYCLE
error errno 1
error pngquant-bin@5.0.2 postinstall: `node lib/install.js`
error Exit status 1
error Failed at the pngquant-bin@5.0.2 postinstall script.
error This is probably not a problem with npm. There is likely additional logging output above.
verbose exit [ 1, true ]
```

해당 라이브러리 폴더에 들어가서 파일들을 확인해 보던 중
pngquant-bin\vendor 폴더 안에 있는

![pngquant](/media/pngquant.PNG)

pngquant.exe를 실행하여 보았더니 다음과 같은 에러 창이 나왔습니다

![VCRUNTIME140-dll](/media/VCRUNTIME140-dll.PNG)

아래의 주소에서 Visual Studio 2015용 Visual C++ 재배포 가능 패키지를 설치 후
https://www.microsoft.com/ko-kr/download/details.aspx?id=48145

다시 npm install을 실행하니 에러 없이 정상적으로 작동 하였습니다.
