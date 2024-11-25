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
  path: /assets/post/applife/applife_3.png
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: tt
---

처음 플러터 개발자로 커리어를 시작하려고할때 면접에서 제일 많이 받은 질문이 상태관리 라이브러리에 대해 얼마나 아는지였다. 🤔

기존 iOS만 준비하던 나는 상태관리에 대해 자세히 알지 못했다.

iOS에서는 주로 Combine이나 RxSwift를 TCA를 사용했지만 플러터에서는 각 라이브러리의 장단점과 적절한 사용 시기를 파악하지 못하고 있었다.

지금 회사에서도 주로 Get 을 쓰고 있는 상태이고,

어쩌다 보니 타회사에서 프로젝트를 넘겨받게 되었는데 기존에 구현된 provider를 사용해서 앱을 완성한 경험,

강의를 따라하면서 riverpod으로 Todo를 만드는 것 말고는 상태관리에 대해서 깊게 이해하지 못하는것 같다고 생각했다.

내가 생각했을때 그래도 보람찬 1년의 경력이라고 하면 대표적인 상태관리 라이브러리는 사용해보고 해당 앱들을 출시해야 된다고 생각한다. 실제 프로덕션 환경에서 각 라이브러리를 사용해봐야 진정한 이해가 가능하기 때문이다.

그래서 이번에는 사이드프로젝트에 Bloc을 적용하기 이전에 공식문서의 튜토리얼을 보면서 Bloc에 대해 자세하게 알아보려고한다. Bloc은 비즈니스 로직 컴포넌트(Business Logic Component)의 약자로, 구글이 추천하는 상태관리 패턴이기도 하다.

<!-- start post -->
## 1. Bloc에 대한 이해

Bloc은 비즈니스 로직 컴포넌트(Business Logic Component)의 약자로, 구글이 추천하는 상태관리 패턴입니다. Bloc 패턴은 Flutter 애플리케이션에서 상태 관리를 보다 구조적이고 일관되게 할 수 있도록 도와줍니다. Bloc은 이벤트 기반으로 작동하며, 이벤트를 수신하고 이를 상태로 변환합니다. 이 과정에서 `Streams`를 활용하여 비동기 데이터 흐름을 관리합니다.

### Bloc의 주요 구성 요소

- **Streams**: Bloc은 `Streams`를 사용하여 비동기 데이터 흐름을 관리합니다. `Streams`는 연속적인 비동기 데이터의 흐름을 나타내며, Dart의 `async*` 함수를 통해 생성할 수 있습니다.

- **Cubit**: `Cubit`은 `BlocBase`를 확장한 클래스로, 상태를 관리하는 가장 기본적인 단위입니다. `Cubit`은 상태의 변경을 트리거하기 위해 호출할 수 있는 함수를 외부로 노출합니다.

  ```dart
  class CounterCubit extends Cubit<int> {
    CounterCubit() : super(0);

    void increment() => emit(state + 1);
    void decrement() => emit(state - 1);
  }
  ```

  위의 예제에서 `CounterCubit`은 `Cubit`을 상속받아 상태를 관리하며, `increment`와 `decrement` 함수를 통해 상태를 변경합니다.

- **Bloc**: `Bloc`은 `Cubit`과 유사하지만, 함수 대신 이벤트를 통해 상태 변경을 트리거합니다. `Bloc`은 이벤트를 수신하고 이를 상태로 변환하는 역할을 합니다.

  ```dart
  enum CounterEvent { increment, decrement }

  class CounterBloc extends Bloc<CounterEvent, int> {
    CounterBloc() : super(0);

    @override
    Stream<int> mapEventToState(CounterEvent event) async* {
      switch (event) {
        case CounterEvent.increment:
          yield state + 1;
          break;
        case CounterEvent.decrement:
          yield state - 1;
          break;
      }
    }
  }
  ```

  `CounterBloc`은 `Bloc`을 상속받아 이벤트를 처리하며, `mapEventToState` 메서드를 통해 이벤트를 상태로 변환합니다.

### Bloc의 장점

- **단순성**: `Cubit`은 단순한 구조로, 상태와 상태를 변경하기 위한 함수만 정의하면 됩니다.
- **추적가능성**: `Bloc`은 상태 변경의 원인과 순서를 명확히 파악할 수 있어, 디버깅과 유지보수에 유리합니다.
- **고급 Event Transformations**: `Bloc`은 `buffer`, `debounceTime`, `throttle` 등과 같은 반응형 연산자를 활용하여 이벤트 흐름을 제어할 수 있습니다.

Bloc 패턴을 사용하면 Flutter 애플리케이션에서 상태 관리를 보다 체계적이고 일관되게 할 수 있으며, 다양한 상태 관리 요구 사항에 유연하게 대응할 수 있습니다.

## 1. Flutter 앱 생명주기

Flutter의 앱 생명주기는 네이티브 플랫폼의 생명주기 이벤트를 Flutter 엔진이 처리하고, 이를 Flutter 앱에 전달하는 방식으로 동작한다.

예를 들어, iOS에서 applicationDidEnterBackground가 호출되면 Flutter 엔진은 이를 감지하고 AppLifecycleState.paused 상태로 변환하여 Flutter 앱에 알려준다.

이러한 방식 덕분에 개발자는 플랫폼별 차이를 신경 쓰지 않고도 일관된 방식으로 앱의 상태를 관리할 수 있다.

Flutter의 생명주기를 관리하는 방법은 크게 두 가지가 있다

1. **WidgetsBindingObserver**
   - 전통적으로 많이 사용되던 방식
   - 간단하고 직관적인 인터페이스 제공
   - 기본적인 생명주기 이벤트만 처리 가능

2. **AppLifecycleListener** (Flutter 3.13 이후)
   - 더 세분화된 생명주기 이벤트 제공
   - 플랫폼별 특수한 기능 지원 (예: 데스크톱의 종료 요청(onExitRequested))
   - 코드가 더 명확하고 관리하기 쉬움

우선 이번에 공부하면서 처음 알게 되었는데 Flutter 3.13 버전에 들어서면서 앱의 생명주기를 더욱 유연하게 다룰 수 있는 AppLifecycleListener class 가 추가 되었다.

기존에는 WidgetsBindingObserver 를 사용해 왔는데, 두개의 차이점은 크게 없다고한다.

하지만 AppLifecycleListener는 앱이 종료될때 onExitRequested 콜백을 통해 사용자에게 앱 종료 여부를 물어볼 수 있는 기능이 추가되었다는 점이 특별하다.

앱이 종료될때 onExitRequested 콜백을 통해 설정할수 있는 것 말고는 큰 차이가 없지만, 더 직관적이고 깔끔한 API를 제공한다는 장점이 있다.

그래도 두 가지 방식 모두 알아두면 좋겠지 라는 마음가짐으로 WidgetsindingObserver 버전과 AppLifecycleListener 버전 두가지를 만들어보면서 실제로 어떤 차이가 있는지 살펴보려고 한다.

### 1.1 WidgetsBindingObserver Class

[WidgetsBindingObserver 공식문서](https://api.flutter.dev/flutter/widgets/WidgetsBindingObserver-class.html)

아마 WidgetsBindingObserver는 대중적으로 가장 많이 쓰이는 방법이라고 생각된다.

코드 자체도 완전히 직관적이고 4가지의 상태로 구분된다.

- inactive: 앱이 포그라운드에 있지만 사용자와 상호작용하지 않는 상태이다. (예: 전화가 걸려올 때)

- paused: 앱이 백그라운드에 있지만, 사용자에게 보이지 않으며, 상호작용할 수 없는 상태로 CPU 자원을 최대한 절약하는 상태이다.

- resumed: 앱이 포그라운드에 있고, 사용자와 상호작용 가능한 상태이다. 일반적인 사용 상태이다.

- detached: 앱이 완전히 종료되기 직전에 발생하며, Flutter 엔진이 제거된 상태이다.

그럼 이제 각 상태에 대해서 Observer를 추가하고 매초에 1씩 증가하는 카운터 앱을 만들어보자

전체 코드는 아래와 같이 생겼다.

```dart
class _WidgetBindingObserverState extends State<WidgetBindingObserver> with WidgetsBindingObserver {
  int counter = 0;
  late Timer _timer;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    _startTimer();
  }

  void _incrementCounter() {
    setState(() {
      counter += 1;
    });
  }

  void _startTimer() {
    print("타이머 시작한다");
    _timer = Timer.periodic(
      const Duration(seconds: 1),
      (_) => _incrementCounter(),
    );
  }

  void _stopTimer() {
    print("타이머 멈춘다");
    _timer.cancel();
  }

  void _taptaptap() {
    print("inactive 상태다 ${counter}");
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    _stopTimer();
    super.dispose();
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    switch (state) {
      case AppLifecycleState.paused:
        _stopTimer();
      case AppLifecycleState.resumed:
        _startTimer();
      case AppLifecycleState.inactive:
        _taptaptap();
      case AppLifecycleState.detached:
        print("앱 종료한다");
      default:
        print(state);
    }
  }

  // 이하 생략(풀버전은 깃허브 코드 참조)
```

우선 WidgetsBindingObserver를 사용하기 위해서는 WidgetsBindingObserver abstract mixin class를 적용해야 한다.

```dart
class _WidgetBindingObserverState extends State<WidgetBindingObserver> with WidgetsBindingObserver
```

그 다음 init 과 dispose 구간에서 timer 와 앱 상태 관리에 대해서 addObserver와 removeObserver 처리를 해줘야 한다.

```dart
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    _startTimer();
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    _stopTimer();
    super.dispose();
  }
```

이렇게 사용해서 앱의 상태의 조건에 따라서 특정 코드를 실행 시켜 줄 수 있다.

```dart
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    switch (state) {
      case AppLifecycleState.paused:
        _stopTimer();
      case AppLifecycleState.resumed:
        _startTimer();
      case AppLifecycleState.inactive:
        _taptaptap();
      case AppLifecycleState.detached:
        print("앱 종료한다");
      default:
        print(state);
    }
  }
```

이번 예제의 경우에서는 initState 에서 타이머가 호출되고

앱이 백그라운드 상태(AppLifecycleState.paused)에서 _stopTimer 를 실행

앱이 다시 포그라운드 상태(AppLifecycleState.resumed)에서 _startTimer 를 실행

앱이 사용자와의 상호 작용이 없을때(AppLifecycleState.inactive)에서는 _taptaptap 을 실행

앱이 종료될때는 print를 통해서 출력되게 끔 만들어 줬다.

![life#1](/assets/post/applife/applife_1.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

![life#2](/assets/post/applife/applife_2.gif){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

출력된 로그와 gif 파일을 보면 특이점이 있다.

앱이 백그라운드 포그라운드를 이동할때 inactive를 갔다가 resumed 또는 paused 상태로 전환되는걸 알 수 있다.

그럼 이제 AppLifecycleListener을 사용해서 앱의 상태관리에 대해서 알아보자

### 1.2 AppLifecycleListener Class

[AppLifecycleListener 공식문서](https://api.flutter.dev/flutter/widgets/AppLifecycleListener-class.html?source=post_page-----5a2aafbf7f4e--------------------------------)

![life#3](/assets/post/applife/applife_3.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

```dart
class _AppLifeCycleListenerState extends State<AppLifeCycleListener> {
  late final AppLifecycleListener _listener;
  final ScrollController _scrollController = ScrollController();
  final List<String> _states = <String>[];
  late AppLifecycleState? _state;

  @override
  void initState() {
    super.initState();
    _state = SchedulerBinding.instance.lifecycleState;
    _listener = AppLifecycleListener(
      onShow: () => _handleTransition('show'),
      onResume: () => _handleTransition('resume'),
      onHide: () => _handleTransition('hide'),
      onInactive: () => _handleTransition('inactive'),
      onPause: () => _handleTransition('pause'),
      onDetach: () => _handleTransition('detach'),
      onRestart: () => _handleTransition('restart'),
      onStateChange: _handleStateChange,
    );
    if (_state != null) {
      _states.add(_state!.name);
    }
  }

  @override
  void dispose() {
    _listener.dispose();
    super.dispose();
  }

  void _handleTransition(String name) {
    setState(() {
      _states.add(name);
    });
    _scrollController.animateTo(
      _scrollController.position.maxScrollExtent,
      duration: const Duration(milliseconds: 200),
      curve: Curves.easeOut,
    );
  }

  void _handleStateChange(AppLifecycleState state) {
    setState(() {
      _state = state;
    });
  }
  // 이하 생략(풀버전은 깃허브 코드 참조)
}
```

찾아보니 onExitRequested 콜백은 웹, 데스크탑과 같은 특정 플랫폼에서만 지원한다고 한다.

![life#4](/assets/post/applife/applife_4.gif){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

위의 이미지를 보면 그냥 바로 백그라운드로 갔다가 다시 포그라운드로 들어가는게 아니라

inactive -> hide -> pause -> restart -> show -> resume 의 순서로 들어오는걸 볼수있다.

기존 WidgetsBindingObserver 는 크게 4가지 상태로 관리할 수 있었는데 보다 디테일하게 앱의 상태에 대해서 관리할 수 있는것 같다

AppLifecycleListener는 조금더 세분화 해서 사용할 수 있다는 생각이 들었다.

하지만 막상 구현해서 사용하게되면 특수한 상황을 제외하곤 결국 onResume 과 onPause를 주로 사용할 것 같다.

## 2. iOS 앱 생명주기

iOS에서는 UIApplicationDelegate와 UISceneDelegate를 통해 앱의 생명주기를 관리한다.
주요 생명주기 메서드는 다음과 같다

- application(_:didFinishLaunchingWithOptions:) : 앱이 처음 실행될 때 호출된다. 앱의 초기 설정이나 초기 데이터 로드를 수행할 때 사용

- applicationDidBecomeActive : 앱이 포그라운드에 있어 사용자와 상호작용할 준비가 되었을 때 호출

- applicationWillResignActive : 앱이 비활성화되기 직전에 호출되며, 전화가 오거나 잠금 화면이 활성화될 때 발생

- applicationDidEnterBackground : 앱이 백그라운드 상태로 전환될 때 호출 된다. 백그라운드에서 작업을 이어갈 수 있도록 필요한 설정을 한다

- applicationWillEnterForeground : 백그라운드에서 포그라운드로 전환될 때 호출된다. 사용자 인터페이스를 갱신할 때 사용된다

- applicationWillTerminate : 앱이 종료되기 직전에 호출된다. 상태 저장 작업 등을 여기에 포함시킬 수 있다

iOS 생명주기 특징

iOS는 상태 복원과 세션 관리를 위한 NSUserActivity와 UIApplicationDelegate 메서드를 제공하여 앱 상태와 사용자 데이터를 저장하고 복구할 수 있다.

### 2.1 상세 구현 예시

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, 
                    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // 앱 초기화 코드
        return true
    }

    func applicationWillResignActive(_ application: UIApplication) {
        // 예: 민감한 데이터 가리기
        myViewController.hideConfidentialData()
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        // 백그라운드 작업 설정
        let taskId = UIApplication.shared.beginBackgroundTask {
            // 백그라운드 시간 만료시 처리
        }
        
        // 데이터 저장 등의 작업
        saveApplicationState()
        
        UIApplication.shared.endBackgroundTask(taskId)
    }
}
```

## 3. Android 앱 생명주기

Android의 생명주기는 Activity를 중심으로 작동하며, 각 상태에 따른 콜백 메서드를 제공한다. 주요 생명주기 단계는 다음과 같다

- onCreate() : 액티비티가 생성될 때 호출 된다. 초기 설정 작업이나 UI를 구성하는 코드가 여기에 들어간다

- onStart() : 액티비티가 사용자에게 보이기 시작할 때 호출

- onResume() : 액티비티가 포커스를 받아 사용자와 상호작용이 가능한 상태

- onPause() : 액티비티가 일시 정지 상태로 전환될 때 호출되며, 사용자가 다른 액티비티로 전환하거나 홈 화면으로 나갈 때 발생

- onStop() : 액티비티가 더 이상 사용자에게 보이지 않을 때 호출

- onRestart() : 액티비티가 일시 정지 상태에서 다시 포그라운드로 돌아올 때 호출

- onDestroy() : 액티비티가 소멸될 때 호출됩니다. 자원 해제와 같은 종료 작업이 필요할 때 사용

Android 생명주기 특징

Android는 각 상태에서 메모리 자원을 관리하기 위해 onSaveInstanceState() 메서드를 사용하여 데이터를 저장할 수 있으며, 장치의 화면 회전과 같은 이벤트에도 대응할 수 있다.

### 3.1 상세 구현 예시

```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 상태 복원
        savedInstanceState?.let {
            viewModel.restoreState(it)
        }
    }

    override fun onSaveInstanceState(outState: Bundle) {
        super.onSaveInstanceState(outState)
        // 상태 저장
        viewModel.saveState(outState)
    }

    override fun onPause() {
        super.onPause()
        // 백그라운드 작업 처리
        if (isFinishing) {
            cleanup()
        }
    }
}
```

## 간단 비교

| 상태 | Flutter | Android | iOS |
| --- | --- | --- | --- |
| 초기화 | - | onCreate() | didFinishLaunchingWithOptions |
| 포그라운드 진입 | resumed | onStart() -> onResume() | applicationDidBecomeActive |
| 일시정지 | paused | onPause() | applicationWillResignActive |
| 백그라운드 진입 | inactive | onStop() | applicationDidEnterBackground |
| 종료 전 호출 | detached | onDestroy() | applicationWillTerminate |

각 플랫폼의 생명주기는 서로 다른 특성을 가지고 있지만, Flutter는 이를 추상화하여 일관된 인터페이스를 제공한다:

1. **메모리 관리**:
   - iOS: 메모리 부족 시 백그라운드 앱을 자동으로 종료
   - Android: 메모리 부족 시 우선순위가 낮은 백그라운드 앱부터 종료
   - Flutter: 플랫폼의 메모리 관리 정책을 따르되, paused 상태에서 리소스 해제 가능

2. **백그라운드 처리**:
   - iOS: 제한된 시간 동안만 백그라운드 작업 허용
   - Android: 더 유연한 백그라운드 작업 지원
   - Flutter: 플랫폼의 제약을 따르면서 isolate를 통한 백그라운드 작업 지원

3. **상태 저장**:
   - iOS: NSUserDefaults, State Restoration
   - Android: onSaveInstanceState, SharedPreferences
   - Flutter: 모든 플랫폼에서 동일하게 작동하는 상태 관리 메커니즘 제공

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
