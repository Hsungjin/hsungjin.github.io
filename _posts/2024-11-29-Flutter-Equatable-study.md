---
layout: post
title: Flutter Equatable의 이해
date: 2024-11-29 15:11:33 +0900
categories: [Flutter]
tags: [Flutter, Equatable]
description: Flutter의 Equatable 라이브러리에 대해 알아보자
toc: true
math: true
mermaid: true
image:
  path: /assets/post/equal/equal_1.png
  width: 800   # in pixels
  height: 500   # in pixels
  alt: equal
  align: center
---

원래는 Bloc에 대해서 자세하게 작성하고 싶었지만, Bloc에 대한 내용을 잘 정리하기엔 아직 초보 플러터 개발자의 입장에서 조금 더 내용의 구체화가 필요하다고 생각이 들었다.

그래서 Bloc 공식문서 예제에 자주 등장하는 Equtable 이라는 라이브러리가 나오는데 이 라이브러리에 대해서 어떤 역할을 하고 왜 사용하는게 좋은지

이 부분도 알고 넘어가면 좋을 것 같아서 자세하게 공부해봤다.

## 1. Equatable이란?

Equatable은 Dart와 Flutter에서 객체 간의 값 비교를 쉽게 구현할 수 있도록 도와주는 패키지이다.

그냥 값들을 비교하면 되는거 아니야? 라고 생각할 수 있지만 전혀 아니다.

Dart에서는 기본적으로 두 객체를 비교할 때 참조 동등성(reference equality)를 사용한다.

한마디로 두 객체가 동일한 메모리 주소를 가리킬 때만 같다고 판단한다.

그래서 객체 안의 값이 같다고 하더라도 실제로 두 객체가 다른 메모리 주소를 가리키고 있기 때문에 다르다고 판단한다.

하지만 Equatable을 사용하면 값 동등성(value equality)을 쉽게 구현할 수 있다.

즉 객체의 속성 값이 같다면 두 객체를 같다고 판단할 수 있게 된다.

### 1.1 왜 Equatable이 필요할까?

일반적인 Dart 클래스에서는 동일한 값을 가진 두 객체를 비교할 때 문제가 발생한다. 

아래의 예제를 보면

```dart
class Person {
  final String name;
  final int age;

  Person(this.name, this.age);
}

void main() {
  final person1 = Person('hsungjin', 27);
  final person2 = Person('hsungjin', 27);
  
  print(person1.name == person2.name); // true  
  print(person1.age == person2.age); // true
  print(person1 == person2); // false
}
```

위 코드에서 `person1`과 `person2`는 같은 값을 가지고 있지만, `==` 연산자는 `false`를 반환한다. 

아니 왜 person1 과 person2 가 다른거지?

이는 위에서 설명했듯이 Dart에서는 값이 같더라도 실제로 두 객체가 서로 다른 메모리 주소를 참조하고 있기 때문이다. 

만약 Equtable을 사용하지 않고 비교를 하려면 조금 복잡해질 수 있다.

두 값에 대한 hashCode를 비교하는 방법이 있으로 구현해야 한다.

```dart
class Person {
  final String name;
  final int age;

  const Person(this.name, this.age);

  @override
  bool operator ==(Object other) =>
    identical(this, other) ||
    other is Person &&
    runtimeType == other.runtimeType &&
    name == other.name &&
    age == other.age;

  @override
  int get hashCode => name.hashCode ^ age.hashCode;
}

void main() {
  final person1 = Person('hsungjin', 27);
  final person2 = Person('hsungjin', 27);
  
  print(person1 == person2); // true
}
```

하지만 Equatable을 사용하면 이러한 문제를 해결할 수 있다.

## 2. Equatable 사용하기

### 2.1 기본 사용법

Equatable을 사용하려면 먼저 `pubspec.yaml`에 의존성을 추가해야 한다

```yaml
dependencies:
  equatable: ^2.0.7
```

그런 다음 클래스에 Equatable을 적용할 수 있다.

```dart
import 'package:equatable/equatable.dart';

class Person extends Equatable {
  final String name;
  final int age;

  const Person(this.name, this.age);
  
  @override
  List<Object> get props => [name, age];
}

void main() {
  final person1 = Person('hsungjin', 27);
  final person2 = Person('hsungjin', 27);
  
  print(person1 == person2); // true
}
```

Equatable을 사용하면 위와 같이 간단하게 객체 비교를 할 수 있게 해준다.

여기서 주의 해야할 점은 Equatable은 변경 불가능한 객체에서만 작동하도록 설계 되어있으므로 모든 변수가 final로 선언되어야 한다.

### 2.2 props 메서드 이해하기

`props` getter는 Equatable의 핵심이다. 

이 메서드는 객체의 동등성 비교에 사용될 속성들의 리스트를 반환한다. 

`props`에 포함된 속성들만 비교에 사용되므로, 필요한 속성만 포함시키는 것이 중요하다.

```dart
class Person extends Equatable {
  final String name;
  final int age;
  final List<String> hobbies;
  final DateTime birthDate;

  const Person({
    required this.name,
    required this.age,
    required this.hobbies,
    required this.birthDate,
  });

  @override
  List<Object> get props => [name, age, hobbies, birthDate];

  // JSON 형식의 작업을 해야 할 경우
  factory Person.fromJson(Map<String, dynamic> json) {
    return Person(
      name: json['name'],
      age: json['age'],
      hobbies: json['hobbies'],
      birthDate: json['birthDate'],
    );
  }
}
```

## 3. Equatable의 추가적인 기능들

### 3.1 hashCode 자동 생성

Equatable은 `props`에 정의된 속성들을 기반으로 자동으로 `hashCode`를 생성한다. 

이는 객체를 Set이나 Map의 키로 사용할 때 유용하다

```dart
void main() {
  final person1 = Person('hsungjin', 27);
  final person2 = Person('hsungjin', 27);
  
  print(person1.hashCode == person2.hashCode); // true
  
  final set = {person1, person2};
  print(set.length); // 1 (중복이 제거됨)

  final map = {person1: 'hsungjin', person2: 'hsungjin'};
  print(map.length); // 1 (중복이 제거됨)
}
```

### 3.2 toString()

Equatable은 디버깅을 위한 유용한 `toString()` 메서드를 제공한다. 

이를 통해 객체의 상태를 쉽게 확인할 수 있다

```dart
class Person extends Equatable {
  final String name;
  final int age;

  const Person(this.name, this.age);
  
  @override
  List<Object> get props => [name, age];
  
  @override
  String toString() => 'Person(name: $name, age: $age)';
}

void main() {
  final person = Person('hsungjin', 27);
  print(person); // Person(name: hsunjin, age: 27)
}
```

### 3.3 불변성(Immutability) 보장

Equatable을 사용할 때는 클래스를 불변(immutable)으로 만드는 것이 권장된다. 

불변성을 통해 객체의 상태가 변경되지 않음을 보장할 수 있다.

```dart
class ImmutablePerson extends Equatable {
  final String name;
  final int age;

  // const 생성자 사용
  const ImmutablePerson(this.name, this.age);
  
  @override
  List<Object> get props => [name, age];
}
```

## 4. 사용 예제

### 4.1 Bloc 패턴에서의 활용

Bloc 패턴이나 다른 상태 관리 솔루션에서 Equatable은 매우 유용하다. 

상태 객체의 동등성을 쉽게 비교할 수 있어 상태 변화 감지에 효과적이다.

```dart
abstract class AuthState extends Equatable {
  const AuthState();
}

class AuthInitial extends AuthState {
  @override
  List<Object> get props => [];
}

class AuthLoading extends AuthState {
  @override
  List<Object> get props => [];
}

class AuthAuthenticated extends AuthState {
  final String username;
  
  const AuthAuthenticated(this.username);
  
  @override
  List<Object> get props => [username];
}
```

### 4.2 테스트에서의 활용

Equatable은 테스트 작성을 더 쉽게 만들어 준다.

객체의 동등성을 쉽게 비교할 수 있어 테스트 코드가 간결해진다.

```dart
void main() {
  test('두 Person 객체가 같은 값을 가질 때 동등해야 함', () {
    final person1 = Person('hsungjin', 27);
    final person2 = Person('hsungjin', 27);
    
    expect(person1, equals(person2));
  });

  test('두 Person 객체가 다른 값을 가질 때 동등하지 않아야 함', () {
    final person1 = Person('hsungjin', 27);
    final person2 = Person('hsungjin', 28);
    
    expect(person1, isNot(equals(person2)));
  });
}
```

## 5. 고려사항

Equatable은 매우 효율적이지만, 몇 가지 고려해야 할 사항이 있다.

### 필요한 속성만 참조하기

`props`에는 동등성 비교에 실제로 필요한 속성만 포함해야 한다. 

불필요한 속성을 포함하면 성능에 영향을 줄 수 있다

```dart
class User extends Equatable {
  final String id;
  final String name;
  final DateTime lastUpdated; // 비교에서 제외할 수 있음

  const User(this.id, this.name, this.lastUpdated);
  
  @override
  List<Object> get props => [id, name]; // lastUpdated는 제외
}
```

### 구체적인 비교 

Equatable은 표면적인 비교만 수행하므로, 중첩된 객체의 경우 추가적인 처리가 필요할 수 있다. 중첩된 리스트나 맵을 비교할 때는 그에 맞는 처리를 해줘야 한다.

```dart
class DeepPerson extends Equatable {
  final String name;
  final List<String> hobbies;

  const DeepPerson(this.name, this.hobbies);
  
  @override
  List<Object> get props => [name, List<String>.from(hobbies)];
}
```

### 불필요한 사용 피하기

모든 클래스에 Equatable을 적용하는 것은 좋지 않습니다. 

다음과 같은 경우에만 Equatable 사용을 고려하면 좋다.

- 객체의 값 비교가 자주 필요한 경우
- Bloc의 상태(State) 클래스
- 테스트에서 객체 비교가 필요한 경우
- Collection(Set, Map 등)의 키로 사용되는 객체

### 성능 영향

Equatable은 편리하지만 약간의 성능 오버헤드가 있다고 한다.

```dart
class ExpensivePerson extends Equatable {
  final String name;
  final List<String> hobbies;
  
  const ExpensivePerson(this.name, this.hobbies);
  
  @override
  List<Object> get props => [name, hobbies]; // 매번 새로운 리스트 생성
}

// 개선된 사용 방법
class BetterPerson extends Equatable {
  final String name;
  final List<String> hobbies;
  
  const BetterPerson(this.name, this.hobbies);
  
  // 캐시된 props 사용
  static final List<Object> _props = [];
  
  @override
  List<Object> get props {
    _props
      ..clear()
      ..add(name)
      ..add(hobbies);
    return _props;
  }
}
```

### 7.3 null 안전성 고려

Equatable을 사용할 때 null 안전성도 고려해야 합니다:

```dart
class NullSafePerson extends Equatable {
  const NullSafePerson({this.name, [this.age]});

  final String? name;
  final int? age;
  
  @override
  List<Object?> get props => [name, age]; // Object? 사용
}
```

## 6. Equatable vs 수동 구현

때로는 Equatable 대신 직접 `==` 연산자와 `hashCode`를 구현하는 것이 더 적절할 수 있다.

```dart
class ManualPerson {
  final String name;
  final int age;

  const ManualPerson(this.name, this.age);

  @override
  bool operator ==(Object other) {
    if (identical(this, other)) return true;
    return other is ManualPerson && 
           other.name == name && 
           other.age == age;
  }

  @override
  int get hashCode => name.hashCode ^ age.hashCode;
}
```

다음과 같은 경우에는 사용을 고려해 봐도 좋다고 한다

- 매우 간단한 클래스의 경우

- 특별한 비교 로직이 필요한 경우

- 성능이 매우 중요한 경우

## 결론

Equatable은 Flutter 애플리케이션에서 객체 비교를 단순화하고 예측 가능하게 만드는 라이브러리이다.

특히 상태 관리나 테스트 작성 시 매우 유용하며, 적절히 사용하면 코드의 품질과 유지보수성을 크게 향상시킬 수 있다.

하지만 모든 클래스에 무분별하게 적용하기보다는 실제로 값 비교가 필요한 경우에만 선택적으로 사용하는 것이 좋다. 

또한 불변성과 성능을 고려하여 적절히 설계하는 것이 중요하다.

## 참조

- [Equatable 공식 문서](https://pub.dev/packages/equatable)
- [Flutter Bloc 라이브러리](https://bloclibrary.dev/)
- [Dart 공식 문서 - Object equality](https://dart.dev/guides/language/language-tour#equality)
