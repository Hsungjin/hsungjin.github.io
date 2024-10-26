---
layout: post
title: Flutter Rive 애니메이션을 곁들인
date: 2024-10-26 15:11:33 +0900
categories: [Flutter]
tags: [Flutter, rive]
description: Flutter 애니메이션을 넣을수 있는 방법중에 하나인 Rive에 대해서 알아보자
toc: true
math: true
mermaid: true
image:
  path: /assets/post/rive/rive_thumb_nail.png
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: 애니메이션을 곁들인
---

처음에 ShoreBird 관련해서 글을 올리고 다음글은 어떤글을 써야할지에 대해서 크게 고민이 있었다.

그러다가 문득 든 생각이 내가 왜 모바일 개발자가 되었는지였다.

사실 그냥 개발을 좋아했던 학생이였던 나는 ROTC 복무 이후에 다시 개발을 시작할때 원래하던 머신러닝을 해야겠다고 생각을 했었다.

근데 마침 내 눈앞에 놓인 맥북과 과선배의 iOS에 대한 추천이였다. 그렇게 나는 Xcode 를 설치해서 뷰를 그려보았다.

머신러닝만하던 나에겐 신세계였다 나는 정확도 하나를 낮추기 위해 많은 노력을했는데 이렇게 코드 한줄에 뷰를 그려준다고?

이게 내가 모바일 개발에 빠지게된 이유였고 항상 앱을 만들때 이쁨을 추구하는 나로써 이쁜 애니메이션을 넣는 방법을 찾게되었다.

그럼 왜 Rive 를 사용하려고 할까? iOS 네이티비를 공부할때는 Rive의 존재조차 몰랐다. 나에게 애니메이션을 로티 밖에 없었다.

근데 문득 강의를 듣다가 로티에서는 보여줄수 없는 신기한 애니메이션들을 보았다.

그중 이번에는 회원가입 관련 애니메이션을 한번 설명해보려고한다.

<!-- start post -->
## What is Rive?

그럼 Rive 란 무엇일까?

> 인터랙티브 애니메이션을 만드는 플랫폼

이라고 한다.

이게 뭐지? 라고 의문이 들수 있는데 기존의 방식과는 다르게 Hover를 지원한다던가 로티와 다르게 인터랙티브 한 것을 느꼈다.

~~근데 로티보단 사용이 어렵다~~

[Rive 공식 사이트](https://rive.app/)에 접속하게 되면 수많은 애니메이션을 볼 수 있다.

물론 직접 만들고 다른 사람의 창작물을 변경할수 있지만, 나는 디자인 감각이 없어서 그냥 가져다가 쓰는걸 더 좋아한다.

오늘은 그중에서 JcToon 님이 만든 [Login Screen Character](https://rive.app/community/files/3469-7899-login-screen-character/)라는 귀여운 곰을 사용해보려고한다.

## How to use Rive

### Install Rive

[pub.dev](https://pub.dev/packages/rive) 에 방문하게 되면 Rive 관련 라이브러리를 찾아볼수있다.

```bash
$ flutter pub add rive
```

또는

yaml 파일에

```yaml
dependencies:
  rive: ^0.13.15
```

추가해 주두록 하자!

### Rive의 기초 동작원리

![rive#1](/assets/post/rive/rive_bear.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

라이브 관련 페이지에 들어가게되면 제일먼저 보이게 되는것이 State Machine 이다

#### StateMachine

StateMachine 은 각 상태를 컨트롤 할 수 있게해 준다.
추후 상태를 StateMachineController로 조절하여 애니메이션의 상태를 APP 내부에서 동작 시킬수 있도록 해준다.

#### Input

![rive#2](/assets/post/rive/rive2.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

어떻게 동작할건지 에 대한 부분이다
Boolean, Trigger, Number 가 있는데

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
