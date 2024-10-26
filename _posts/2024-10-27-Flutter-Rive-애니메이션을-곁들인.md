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

Boolean -> true 와 false로 구분
Trigger -> 클릭했을때 한번만 동작
Number -> 특정 값이 포함되어 있거나 그 값이면 동작

이렇게 세가지 Input Type이 있다.
이 타입을 우리는 Flutter 코드에서는 SMIInput을 사용한다.
SMIBool, SMITrigger, SMINumber 이렇게 사용하게 된다.

그럼 기초적인 동작원리는 알았고 어떻게 사용하면 될까?

### Rive 회원가입 곰 예제

우선 사용하기 위해선 [해당 애니메이션](https://rive.app/community/files/3469-7899-login-screen-character/)에 대해서 export 해주게 되면 .riv 라는 파일이 하나가 나온다.
그걸 assets 에 추가해줘야 한다.

![rive#3](/assets/post/rive/rive3.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

그 다음 텍스트 필드(ID, PW)용 두개, 로그인 버튼을 간단하게 만들어보자

```dart
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(widget.title),
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(20.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              TextField(
                controller: idTextController,
                decoration: const InputDecoration(
                  hintText: "ID를 입력해주세요.",
                  border: OutlineInputBorder(),
                ),
              ),
              const SizedBox(height: 20),
              TextField(
                controller: pwTextController,
                decoration: const InputDecoration(
                  hintText: "PW를 입력해주세요.",
                  border: OutlineInputBorder(),
                ),
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                style: ElevatedButton.styleFrom(
                    minimumSize: const Size(88, 36),
                    padding: const EdgeInsets.symmetric(horizontal: 16),
                    shape: const RoundedRectangleBorder(
                      borderRadius: BorderRadius.all(Radius.circular(2)),
                    ),
                ),
                onPressed: () {},
                child: Text('로그인'),
              )
            ],
          ),
        ),
      ),
    );
  }
```

다음과 같이 간단한 로그인 화면을 만들어 주었다.

그 다음 StateMachineController 와 Input에 대한 부분을 만들어 주어야 한다

```dart
  // StateMachine을 컨트롤 할 수 있게 도와주는 Controller
  late StateMachineController stateMachineController;
  late SMIBool _check;
  late SMINumber _look;
  late SMITrigger _sucess;
  late SMITrigger _fail;
  late SMIBool _handUp;
  
  void _onInit(Artboard art) {
    //StateMachine 이름 일치하게 입력
    stateMachineController = StateMachineController.fromArtboard(art, 'State Machine 1')!;
    stateMachineController.isActive = true;
    art.addController(stateMachineController);

    // Input에 있는 이름도 동일하게 입력
    _check = stateMachineController.findInput<bool>('Check') as SMIBool;
    _look = stateMachineController.findInput<double>('Look') as SMINumber;
    _sucess = stateMachineController.findInput<bool>('success') as SMITrigger;
    _fail = stateMachineController.findInput<bool>('fail') as SMITrigger;
    _handUp = stateMachineController.findInput<bool>('hands_up') as SMIBool;
  }
```
다음과 같이 State를 관리할수 있는 Controller 부분과 각각의 input에 의해 정리된 부분을 선언해주어야 사용 할수 있다.

그러면 저기서 이름규칙이 한글자라도 틀리게 되면 오류가 나니까 조심하세요! 물론 제가 그랬다고요.. ㅎㅎ

솔직히 여기까지 왔으면 이제 끝이라고 볼수있다.

그냥 RiveAnimation.asset 위젯을 사용해서 나타내 주고 해당 부분에 대해서 조건문을 넣어주면 된다.

```dart
RiveAnimation.asset('assets/login.riv', stateMachines: ['State Machine 1'] , onInit: _onInit, fit: BoxFit.fitWidth, alignment: Alignment.center,)
```

이런식으로 말이다.

나와 같은 경우에는 ID를 입력할때는 Check 가 사용되게하고 아이디의 길이에 따라 Look 을 사용해서 눈이 움직이도록 했다.

비밀번호를 입력할땐 손이 올라오고 로그인의 조건(현재는 간단하게 ID - 123, PW - 123)에 따라서 성공과 실패를 반환하도록 했다.

그러기 위해서

```dart
  final FocusNode idFocus = FocusNode();
  final FocusNode pwFocus = FocusNode();
```

각각의 텍스트 필드에 대한 FocusNode를 선언해 주고

```dart
  void initState() {
    idTextController.addListener(() {
      setState(() {
        _look.value = idTextController.text.length.toDouble();
      });
    });

    idFocus.addListener(() {
      setState(() {
        _check.value = idFocus.hasFocus;
      });
    });

    pwFocus.addListener(() {
      _handUp.value = pwFocus.hasFocus;
    });

    super.initState();
  }
```
initState에서 각각의 Lintner를 달아주고

```dart
ElevatedButton(
  style: ElevatedButton.styleFrom(
    padding: const EdgeInsets.symmetric(horizontal: 16),
    shape: const RoundedRectangleBorder(
      borderRadius: BorderRadius.all(Radius.circular(2)),
    ),
  ),
  onPressed: () {
    FocusScope.of(context).unfocus();
    if (idTextController.text == "123" && pwTextController.text == "123") {
      _sucess.fire();
    } else {
      _fail.fire();
    }
  },
  child: Text('로그인'),
)
```

조건에 따라서 Trigger에 대한 부분을 사용해주면된다

여기서 주의해야 할 점은 Trigger 이녀석들은 fire() 를 사용해 주도록해야한다.

이러면 회원가입 조건에따라서 다음 동영상 처럼 귀여운 곰이 나타난다.

![rive#4](/assets/post/rive/rive4.gif){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### [관련 전체 코드](https://github.com/Hsungjin/Flutter/tree/main/rive_example)

## 내가 느낀 결론

확실히 그냥 가져와서 크기를 정해주면 쉽게 사용할수있는 Lottie 와는 다르게 생각해야 되는 것도 많고 적용할때 처음에 헤매는 부분이 생길수밖에 없는것같다.

물론 강의를 듣고 글을 찾아봐도 잘 안되는 부분도 존재했고 그냥 이럴거면 Lottie에서 기깔나는걸 쓰는게 더 좋지않을까? 했지만 내 생각에 모바일은 애니메이션이 이뻐야한다는 생각이 강하다.

Rive를 사용하면 Lottie에서는 느낄수 없는 경험을 느끼는것 같아서 언젠간 적용해보겠다는 다짐을 하지만 아직은 모르겠다.

애니메이션을 이쁘게 넣을수 있다면 그게 나는 좋은것 같다.

## 참조

- [Rive를 Flutter에서 사용하는 방법](https://medium.com/@moo_min/rive%EB%A5%BC-flutter%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-1533ccbfc7ac){:target="_blank"}
- [패스트 캠퍼스 강의](https://fastcampus.co.kr/dev_online_dartflutter){:target="_blank"}
<!-- end post -->
