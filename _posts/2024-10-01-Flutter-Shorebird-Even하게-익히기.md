---
layout: post
title: Flutter Shorebird Even하게 익히기
date: 2024-10-01 15:11:33 +0900
categories: [Flutter]
tags: [Flutter, Shorebird, codepush]
description: Flutter에서 사용하는 Shorebird는 무엇이고 왜 사용해야할까? Even하게 익혀보자!
toc: true
math: true
mermaid: true
image:
  path: /assets/post/shorebird/even.png
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: Even하게 익히자
---

글또에 참여하게 되었고 처음에는 어떤 주제로 글을 써야할지 고민이 많았다.

그러던 중 24년 9월 마지막주에 [Future\<Flutter>](https://festa.io/events/5704) 에 참여하여 연사를 듣게되었다.

그 중 내 관심을 끌었던건 Eric Seidal 님의 Flutter ShoreBrid 작동원리였다.

이전에는 Shorebirde 가 정확히 어떤 원리로 동작하는지, 사용하면 어느 부분에서 이점을 갖는지 정확하게 알지못했다.

연사를 들으면서도 영어로 설명했던 연사라 이해가 잘 되지 않았다.

그래서 이번 기회에 제가 이해한 내용을 정리하고 추가적으로 공부해서 기록하려고 해당 내용을 쓰기로 다짐헸다.

그럼 Flutter Shorebird에 대해서 Even하게 익혀보자!

<!-- start post -->
## What is Shorebird?

![Shorebird #1](/assets/post/shorebird/shorebird.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

Shorebird는 Flutter 애플리케이션을 위한 코드 업데이트 배포 플랫폼이다.

근데 이렇게만 들어서 이해를 하는 사람은 없을거다.

물론 나도 연사를 들으면서 이해를 하지 못했다.

Eric이 말하기에는 ReactNative 에서 사용되는 기술로 기존 틱톡이 사용하는 CodePush와 비슷하다고 설명했는데 나는 CodePush를 사용해본 경험이 없어서 이해를 하지 못했다.

영어 연사를 들으면서 대충 어떤 느낌으로 쓰고있구나 감만 잡고있는 상태에서 조금더 자세히 알아보았다.

쇼어버드를 사용하면 **각 배포플랫폼(iOS, Android)** 에서 빌드하는 과정에서 소스코드를 추가하는 방식으로 동작한다.

예를들어 우리가 기존의 방식으로 앱을 출시한다고 예를 들어보자.

우리는 앱을 출시하기 위해서 각 플랫폼에 맞는 빌드를 하고 심사를 받고 심사가 통과되면 배포를 하게 된다.

그러면 추가적으로 코드를 수정해서 버그를 고치거나 기능울 추가해야한다면 어떻게 해야될까?

수정된 코드의 앱을 빌드하고 다시 심사를 받고 배포를 하게 된다. 이 과정에서 리젝이라는 결과를 받게 되면 아주 슬픈 일이 일어날 수도 있다.

이런 심사와 배포의 과정을 줄일수있는 것이 CodePush 즉 Flutter 에서의 CodePush 도구는 Shorebird 이다.

> 이름의 유래는 플러터의 첫 사무실이 마운틴 뷰의 Shorebird Way에 있어서 그렇게 이름이 지어졌다고 한다.

![Shorebird #2](/assets/post/shorebird/app.jpeg){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

<center>기존 배포 방식에 대한 간단한 예시 그림</center>

[이미지 출처](https://gsretail.tistory.com/43)

## Feature of Shorebird

- Flutter를 위한 OTA(Over The Air) 업데이트 제공

- 5분안에 앱에 적용 가능하며 실제 코드의 변경이 필요하지 않음. 플러터 앱의 Dart 코드를 즉각 업데이트 해줌.

- 앱의 패치 횟수에 따라 과금
  - 리액트 네이티브 인 경우 코드 푸시가 무료로 진행되는데 Shorebird 는 유료이고 install 수로 카운팅 됩니다.
  - 무료 플랜인 경우 매달 5,000 번 무료 그 이후 매 install 시 0.005달러 추가 부과
  - $20 for up to 50K patch installs
  - $100 for up to 300K patch installs
  - $300 for up to 1M patch installs
  - $700 for up to 2.5M patch installs
  - $1,250 for up to 5M patch installs
  - $2,000 for up to 10M patch installs
  - 큰 앱인 경우 컨택하여 조정 가능하다고 합니다.

- iOS의 경우 Shorebird와 Flutter stable 최신 버전 필요

- 앱스토어와 플레이스토어 규정을 준수(준수 하지 않으면 계정 자체가 정지 당할 수 있다고 함)

## How does it work Shorebird?

![Shorebird #3](/assets/post/shorebird/shorebird_2.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

<center>Shorebird의 동작 원리</center>

[이미지 출처](https://haragoo30.medium.com/%ED%95%AD%EC%83%81-%EC%BC%9C%EC%A0%B8%EC%9E%88%EB%8A%94-%ED%83%9C%EB%B8%94%EB%A6%BF%EC%95%B1%EC%97%90-shorebird-%EC%A0%81%EC%9A%A9-%ED%9B%84%EA%B8%B0-e312caeda363)

Shorebird는 3가지 툴로 구성되어있다.

- 배포와 빌드를 용이하게 해주는 CLI 툴

- 자체적으로 수정한 Flutter 엔진

- 패치를 담당할 host server

Shorebird로 빌드된 앱에는 수정된 Flutter 엔진이 포함되어 있어 앱 시작시 앱의 Dart 코드에 대한 업데이트를 확인 후 업데이트가 있으면 엔진이 업데이트를 다운로드 한다.

정리하면 개발자가 Shorebird 서버에 코드를 업로드 해 놓은 후, 앱 시작 시 변경 사항이 있는지 확인 후 필요 시 다운로드를 진행하는 방식이라고 한다.

그럼 여기서 의문이 들 수 있다. Shorebird가 내 코드를 저장하고있나? 라고 생각이 들 수 있다.

하지만 Shorebird는 내 코드를 저장하고 있지 않다. QnA에서 보면 소스코드를 보고있지 않는다고 한다.

[관련 공식사이트](https://shorebird.dev/privacy/?source=post_page-----0fd514528917--------------------------------)

## How to use Shorebird?

### 1. Shorebird 회원가입

일단 Google 또는 Microsoft 계정으로 회원가입을 해야한다.

[Shorebird 회원가입 링크](https://console.shorebird.dev/login?source=post_page-----0fd514528917--------------------------------)

### 2. Shorebird CLI 설치 📦

Shorebrid 는 CLI를 기반으로 배포와 빌드가 동작하니 때문에 설치해주어야 한다.

회원가입시 나타나는 공식문서 기준의 가이드를 사용했다.

**MAC 설치**

Open a terminal and run
나는 맥밖에 업어서 이쁜이 터미널 iTerm2를 사용했다

```bash
curl --proto '=https' --tlsv1.2 https://raw.githubusercontent.com/shorebirdtech/install/main/install.sh -sSf | bash
```

**Windows 설치**

Open PowerShell and run:

```bash
Set-ExecutionPolicy RemoteSigned -scope CurrentUser # Needed to execute remote scripts
iwr -UseBasicParsing 'https://raw.githubusercontent.com/shorebirdtech/install/main/install.ps1'|iex
```

**CLI 설치 확인**
우리에게 친숙한 flutter doctor 명령여와 비슷하게
터미널에서 다음 명령어를 실행시켜 확인할 수있다.

```bash
shorebird doctor
```

다음 이미지와 같이 정상적으로 나오는 걸 볼수있다.

![Shorebird #4](/assets/post/shorebird/shorebird_3.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 3. Shorebird 로그인  🔐

터미널에서 다음 명령어를 입력하면 https ~~ 어쩌구 저쩌구하는 URL이 나오는데
해당 링크에 방문해서 로그인을 하면 정상적으로 로그인이 되는 것을 볼 수 있다.

```bash
shorebird login
```

![Shorebird #5](/assets/post/shorebird/shorebird_4.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 4.Initialize Shorebird in your Flutter project 🐦

필자는 제일 기본적인 카운터 앱에 적용해보려고 한다.

자신이 적용할 프로젝트에 들어가 루트의 위치에서 다음 명령어를 입력한다.

```bash
shorebird init
```

다음 명령어를 입력하게 되면 다음 이미지와 같은 값이 출력 되고

![Shorebird #6](/assets/post/shorebird/shorebird_5.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

shorebird.yaml 파일이 자동으로 생성되고, pubsepc.yaml의 assets에도 추가가되는걸 볼수있다.

shorebird.yaml 파일 안에는 app_id 가 들어가있는데 공식문서 상에서는 비밀로 유지할 필요가 없다고 한다!

### 5. Create a release 🚀

다음 명령어를 통해서 안드로이드, iOS에 대한 릴리즈 파일을 만들 수 있다.
이렇게 만들어진 앱을 제출해 앱스토어, 플레이스토어에 올리게 되면 제출된 Shorebird를 기준으로 앱의 변경 사항을 체크하고, 무선으로 업데이트 해주는 방식이다.

```bash
shorebird release android 

shorebird release ios
```

안드로이드 기준 결과

![Shorebird #7](/assets/post/shorebird/shorebird_6.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

iOS 기준 결과

![Shorebird #8](/assets/post/shorebird/shorebird_7.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 6. Shorebird Preview

그러면 우리가 해당 앱을 미리 시뮬을 통해서 봐야되는 상황에는 해당 명령어를 통해 접근 할 수 있다.

```bash
shorebird preview
```

### 7. Create a patch 🧩

패치 명령어를 통해 5번에서 Relase된 apk 또는 ipa 파일에 적용시켜줄 수 있다

```bash
shorebird patch android

shorebird patch ios
```

최초에 release 된 앱은 아래 이미지와 같이 기본 카운터 앱이다.

![Shorebird #9](/assets/post/shorebird/shorebird_8.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

근데 patch를 통해 카운터를 마이너스로 바꾸고 색상도 바꾸어 주었다.

기존과 같으면 새로운 빌드를 통해 앱을 적용시키거나 apk파일을 다시 업데이트 해야되었지만,
patch 를 사용하면 앱을 재실행만 하면 다음과 같이 별도의 앱 설치과정 없이 code push를 할 수 있는 것이다.

![Shorebird #10](/assets/post/shorebird/shorebird_9.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 8. Shorebird Console 🎛️

Shorebird Console 에서는 버전을 관리하고, 앱의 목록을 보고, 앱을 언제 Patch했고 몇명이 Patch를 받았고 등의 정보를 받을수 있다고 한다.

![Shorebird #11](/assets/post/shorebird/shorebird_10.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

## Shorebird 의 한계

- pubspec.yaml 네이티브 코드 변경 사항은 반영되지 않음

- asset(이미지 글꼴) 변경 사항은 반영되지 않음

- 앱의 플러터 버전 적용 불가

- 특정 조건에 따라 요금이 존재한다

## 내가 느낀 결론

Shorebird는 RN을 겪어보지 않고 codepush의 개념을 모르는 경우에 처음 접했을 때 되게 신선한 충격으로 느껴졌다.

아니 이러면 기존에 까다로운 심사를 우회할 수 있는거 아니야?

하지만 그러면 안된다 언젠가 정책을 위반한 사실이 걸리게 되면 계정자체가 정지될 수 있다고 한다.

그래서 이걸 어떻게 사용할까? 매번 귀찬은 심사를 피하고 오류를 수정할때(hotfix)의 경우에 크게 사용할 수 있다고 생각된다.

하지만 **비싼 요금**에 대한 생각때문에 앱에 적용하는게 맞을까? 라는 생각이 들게한다.

물론 플러터에서 사용할 수 있는 좋은 도구임은 절대 변하지 않는 사실이다.

그래서 나는 사이드 프로젝트에 적용해보면서 기술스택을 쌓는다는 느낌으로 적용해보려고한다.

아직 배포에 대한 내용은 포함되어 있지 않고, 추후 사이드프로젝트에 적용하면서 추가 하면서 실직적으로 사용해보고 심화해볼 예정이다.

## 참조

- [Shorebird(Flutter CodePush) 사용방법](https://medium.com/@moo_min/shorebird-flutter-codepush-%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95-0fd514528917){:target="_blank"}
- [Shorebird 공식사이트](https://shorebird.dev/){:target="_blank"}
- [항상 켜져 있는 태블릿 앱에 Shorebird 적용 후기](https://haragoo30.medium.com/%ED%95%AD%EC%83%81-%EC%BC%9C%EC%A0%B8%EC%9E%88%EB%8A%94-%ED%83%9C%EB%B8%94%EB%A6%BF%EC%95%B1%EC%97%90-shorebird-%EC%A0%81%EC%9A%A9-%ED%9B%84%EA%B8%B0-e312caeda363){:target="_blank"}

<!-- end post -->
