---
layout: post
title: Flutter Bloc 에 대한 기초 예제
date: 2024-11-26 15:11:33 +0900
categories: [Flutter]
tags: [Flutter, Bloc]
description: 플러터의 대표적인 상태관리 라이브러리 Bloc에 대해 알아보자
toc: true
math: true
mermaid: true
image:
  path: /assets/post/bloc/bloc_2.png
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: bloc
---

처음 플러터 개발자로 커리어를 시작하려고할때 면접에서 제일 많이 받은 질문이 상태관리 라이브러리에 대해 얼마나 아는지였다. 🤔

기존에 iOS만 준비하던 나는 플러터의 상태관리에 대해 자세히 알지 못했다.

iOS에서는 주로 Combine이나 RxSwift를 TCA를 사용했지만 플러터에서는 각 라이브러리의 장단점과 적절한 사용 시기를 파악하지 못하고 있었다.

지금 회사에서도 주로 GetX 를 쓰고 있는 상태이고,

어쩌다 보니 타회사에서 프로젝트를 넘겨받게 되었는데 기존에 구현된 provider를 사용해서 앱을 완성한 경험,

강의를 따라하면서 riverpod으로 Todo를 만드는 것 말고는 상태관리에 대해서 깊게 이해하지 못하는것 같다고 생각했다.

내가 생각했을때 그래도 보람찬 1년의 경력이라고 하면 대표적인 상태관리 라이브러리는 사용해보고 해당 앱들을 출시해야 된다고 생각한다. 실제 프로덕션 환경에서 각 라이브러리를 사용해봐야 진정한 이해가 가능하기 때문이다.

그래서 이번에는 사이드프로젝트에 Bloc을 적용하기 이전에 공식문서의 튜토리얼을 보면서 Bloc에 대해 자세하게 알아보려고한다. Bloc은 비즈니스 로직 컴포넌트(Business Logic Component)의 약자로, 구글이 추천하는 상태관리 패턴이기도 하다.

<!-- start post -->
## 1. Bloc에 대한 이해

Bloc은 비즈니스 로직 컴포넌트(Business Logic Component)의 약자로, 구글이 추천하는 상태관리 패턴이다.

Bloc 패턴은 Flutter 애플리케이션에서 상태 관리를 보다 구조적이고 일관되게 할 수 있도록 도와준다.

Bloc은 이벤트 기반으로 작동하며 이벤트를 수신하고 이를 상태로 변환하고 이 과정에서 `Streams`를 활용하여 비동기 데이터 흐름을 관리한다.

우선 공식문서 상에서 Bloc에 대해서 이해를 할려면 꼭 알아야 하는 개념이 있다고 안내를 해준다.

Cubit과 Streams을 기초적으로 이해를 해야 Bloc을 이해할 수 있다고 한다.

### 1. Streams

[Streams 공식문서](https://dart.dev/libraries/async/using-streams) [Bloc 공식문서](https://bloclibrary.dev/ko/bloc-concepts/) 들의 설명에 따르면 Streams는 연속적인 비동기 데이터의 흐름을 나타내는 객체라고 한다.

만약 비동기적으로 데이터를 처리하는 방법에 대해서 자세히 알고 싶다면 [Async Programming in Dart](https://dart.dev/language/async) 공식문서를 참고하면 된다.

Streams 는 비동기적으로 데이터를 처리하는 방법이라고 생각하면 되며 Dart의 `async*` 함수를 통해 생성할 수 있다.

아래 예제는 Bloc 공식문서에서 가져온 예제이다.

```dart
Stream<int> countStream(int max) async* {
    for (int i = 0; i < max; i++) {
        yield i;
    }
}
```

함수를 `async*`로 표시하면 `yield` 키워드를 사용하여 데이터의 Stream을 반환할 수 있다. 위 예시에서는 max 정수 파라미터까지의 정수 Stream을 반환하고 있다.

`async*` 함수에서 `yield` 할 때 마다 해당 데이터를 Stream을 통해 푸쉬한다.

위의 Stream을 여러 가지 방법으로 사용할 수 있다. 만약 정수로 이루어진 Stream의 합계를 반환하는 함수를 작성하고 싶다면 다음과 같이 작성할 수 있다:

```dart
Future<int> sumStream(Stream<int> stream) async {
    int sum = 0;
    await for (int value in stream) {
        sum += value;
    }
    return sum;
}
```

위의 함수를 async로 작성하면 await 키워드를 사용하여 정수의 Future를 반환할 수 있다.

이 예제에서는 Stream의 각 값을 기다렸다가 Stream에 있는 모든 정수의 합을 반환한다.

```dart
void main() async {
  // 0-9까지의 정수 Stream을 생성
    Stream<int> stream = countStream(10);
  // Stream의 모든 정수의 합을 반환
    int sum = await sumStream(stream);
    print(sum); // 45
}
```

이렇게 Streams를 사용하면 비동기 데이터에 대한 처리를 할 수 있다.

다음은 Bloc의 핵심 구성 요소인 Cubit에 대해 알아보자.

### 2. Cubit

![bloc_1](/assets/post/bloc/bloc_1.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

`Cubit`은 `BlocBase`를 확장한 클래스로, 상태를 관리하는 가장 기본적인 단위이다.

`Cubit`은 상태의 즉 State 변경에 대해 notify를 받고 현재 State에 따라서 일부를 다시 그리는 것을 처리한다.

> 참고로 Cubit은 상태를 관리하는 가장 기본적인 단위이기 때문에 상태를 관리하는 모든 로직을 담당하는 것이 아니라 단순히 상태를 관리하는 역할만 한다.
>
> [Cubit의 기원](https://github.com/felangel/cubit/issues/69)

아래는 공식문서에서 가져온 예제를 보면서 내가 이해한 내용이다.

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
}
```

Cubit을 만들때 Cubit이 관리할 상태의 타입을 정의해야 한다.

위에서 CounterCubit에서 state 타입은 int 타입을 관리하는 Cubit이다.

Cubit을 생성할때 두번째로 해야될 일은 초기 상태를 정의해야 한다.

위의 예시에서는 0을 초기 상태로 설정하였다.

이제 상태를 변경하는 함수를 추가해보자.

각 Cubit은 emit을 통해 새로운 state를 출력할 수 있다.

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);
  void decrement() => emit(state - 1);
}
```

위의 예시 코드에서 CounterCubit은 외부에서 호출하여 CounterCubit의 state를 연산하는 increment, decrement 라는 public method를 갖고 있다.

increment가 호출되면 state getter를 통해 Cubit의 현재 state에 접근하고, 현재 상태에 1을 더하여 새로운 state를 emit할 수 있다.

decrement도 마찬가지로 현재 state에서 1을 빼서 새로운 state를 emit할 수 있다.

```dart
void main() {
  final cubit = CounterCubit();
  print(cubit.state); // 0
  cubit.increment();
  print(cubit.state); // 1
  cubit.decrement();
  print(cubit.state); // 0
  cubit.close();
}
```

위의 예시 코드는 CounterCubit을 실제로 사용하는 예시이다.

CounterCubit의 인스턴스를 생성하고 초기 상태를 출력하면 0이 출력된다.

increment() 메서드를 호출하면 state가 1 증가하여 1이 출력되고,

decrement() 메서드를 호출하면 state가 1 감소하여 0이 출력된다.

마지막으로 cubit.close()를 호출하여 Cubit을 닫아준다.

이는 Cubit이 더 이상 필요하지 않을 때 리소스를 해제하기 위해 필요한 작업이다.

그럼 이제 사용하기 위해서 Cubit이 새로운 State를 emit 해서 값의 변화가 생겼을때는 어떻게 해야할까?

바로 `onChange` 메서드를 통해 값의 변화를 추적할 수 있다.

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);

  @override
  void onChange(Change<int> change) {
    super.onChange(change);
    print(change);
  }
}

void main() {
  CounterCubit()
    ..increment()
    ..close();
}
```

이렇게 사용하게 되면 Cubit의 state가 변경될때마다 onChange 메서드가 호출되어 변경된 state를 출력하게 된다.

> Change { currentState: 0, nextState: 1 } 이런식으로 출력이 될 것 이다.

또한 Cubit을 사용하다가 에러가 발생했을때는 addError 메서드를 통해 에러를 추적할 수 있다.

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() {
    addError(Exception('increment error!'), StackTrace.current);
    emit(state + 1);
  }

  @override
  void onChange(Change<int> change) {
    super.onChange(change);
    print(change);
  }

  @override
  void onError(Object error, StackTrace stackTrace) {
    print('$error, $stackTrace');
    super.onError(error, stackTrace);
  }
}
```

이렇게 하면 기초적으로 Bloc을 사용하기전에 이해해야 할 개념들은 알게된 것이다.

이제 Bloc에 대해서 설명해 보려고 한다

## 2.Bloc

![bloc_2](/assets/post/bloc/bloc_2.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

Bloc은 Cubit의 기능을 확장하여 더 복잡한 상태 관리 요구 사항을 처리할 수 있도록 설계되었다.

Bloc은 함수가 아닌 event를 통해 상태를 변경한다.

Bloc 자체는 Cubit과 유사한 상태로 사용되며 함수 호출해서 state를 직접 emit 하는 대신 event를 통해 state를 수신하고 이를 처리하는 방식으로 동작한다.

### Bloc 만들기

Bloc을 생성하는 것은 Cubit를 생성하는 것과 비슷하지만, 관리할 state를 정의하는 것 외에 Bloc이 처리할 event도 정의해야 한다는 점이 다르다.

## 내가 느낀 결론

이번 앱 생명주기 학습을 통해 몇 가지 중요한 깨달음을 얻었다.

첫째, Flutter의 생명주기 관리는 생각보다 더 깊이 있고 복잡하다. 처음에는 단순히 WidgetsBindingObserver의 4가지 상태만 알고 있었지만, AppLifecycleListener의 도입과 함께 더욱 세밀한 제어가 가능해졌다는 것을 알게 되었다.

둘째, 크로스 플랫폼 개발에서는 각 플랫폼의 특성을 정확히 이해하는 것이 매우 중요하다. iOS와 Android의 백그라운드 처리 방식 차이, 메모리 관리 정책 등 플랫폼별 특성을 고려하지 않으면 예상치 못한 문제가 발생할 수 있다.

셋째, 실제 프로젝트 경험을 통해 이론적 이해를 넘어선 실전 지식의 중요성을 깨달았다. 특히 iOS의 엄격한 백그라운드 제한과 Android의 상대적으로 유연한 처리 방식의 차이는 실제 개발 과정에서 중요한 고려사항이 된다.

결론적으로, Flutter 개발자는 단순히 프레임워크 수준의 이해를 넘어, 각 플랫폼의 특성과 제약사항을 깊이 있게 이해하고 있어야 한다. 이는 안정적이고 효율적인 앱 개발을 위한 필수 조건이라고 할 수 있다.

## [전체코드](https://github.com/Hsungjin/Flutter/tree/main/app_life_cycle)

## 참조

[[Flutter] 앱의 라이프사이클 다루기](https://medium.com/@cody.yun/flutter-%EC%95%B1%EC%9D%98-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4-%EB%8B%A4%EB%A3%A8%EA%B8%B0-5a2aafbf7f4e)

[안드로이드 액티비티 생명주기(Life Cycle)](https://brunch.co.kr/@mystoryg/80)

[[iOS/Swift] 앱의 생명주기 (App’s life cycle)](https://velog.io/@ahneve/iOSSwift-%EC%95%B1%EC%9D%98-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-Apps-life-cycle)

<!-- - [Mastering Shapes and Clipping in Flutter](https://medium.com/@moo_min/rive%EB%A5%BC-flutter%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-1533ccbfc7ac){:target="_blank"}
- [패스트 캠퍼스 강의](https://fastcampus.co.kr/dev_online_dartflutter){:target="_blank"} -->
<!-- end post -->
