---
layout: post
title: Flutter Custom Shape를 알아보자
date: 2024-10-29 15:11:33 +0900
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

이전에도 말했듯이 모바일 개발자로써 디자인에 대한 갈망이 있다.
어떻게하면 조금더 기깔나게 만들수 있을까? 라는 생각이 가장 크다.
그게 내가 모바일 개발자가 되고 싶었던 이유였으니까.
그러던 중 오픈카톡방에서 쿠로곰님이 아티클을 정리해서 올려주신 글을 봤는데 딱 눈에 들어온 Medium 글이 있었다.
[Mastering Shapes and Clipping in Flutter](https://blog.stackademic.com/mastering-shapes-and-clipping-in-flutter-7c7caee5bcba) 라는 제목을 가졌는데 
글을 보자마자 뭐야 너무 내맘에 쏙이잖아 라는 생각이 들었다.
게다가 이번 Future<Flutter>에서 ABC Studio @Line 에서 박유진님이 연사하신 *풍성한 디자인 요청사항에 대응하기*을 들으면서
와 저 모양을 저렇게 만들수도 있구나 느끼면서 저렇게 창의적으로 디자인 사항을 반영해보고 싶었던 부분도 컸던것 같다. 
나였으면 그냥 이미지를 넣지 않았을까? 라고 생각한 부분도 컴포넌트화를 하고 해당 도형이 어디서든 쓰일수 있도록 하셨던 부분에 대해서 반성을 하게 되었다.
그래서 이번에는 이런 도형을 만들어보고 어떻게 쓰일수 있을지 알아보고자 한다.

<!-- start post -->
## 1. Shape 와 Box 란?

Shape: Path로 정의된 어떤 형태를 의미한다.
Box: 4개의 점으로 구성된 직사각형 모양을 나타냅니다. 추가로 borderradius 같은 속성을 가질 수 있다.

<!-- Flutter에서는 여러 컨텍스트에서 Box가 사용된다
RenderBox, BoxDecoration, Boxborder, ShapeDecoration, ShapeBorder 등이 사용되고
주로 Container, DecoratedSlivers, DecoratedBoxes 를 스타일링하기 위해 <span style='background-color:#ffdce0'>BoxDecoration</span>을 사용한다. -->

### 1.1 기본적인 Box 구현
```dart
Widget _item1() {
  return DecoratedBox(
    decoration: ShapeDecoration(
      color: Color(0xFFd6f5ff),
      shape: BeveledRectangleBorder(borderRadius: BorderRadius.circular(20))
          .lerpTo(
          BeveledRectangleBorder(borderRadius: BorderRadius.circular(1)), 0.4)!,
    ),
    child: const SizedBox(height: 200, width: 200),
  );
}
```
여기서 `BeveledRectangleBorder`는 모서리가 깎인 사각형을 만드는 위젯이다. 
`lerpTo` 메서드를 사용하면 두 개의 다른 모양 사이를 보간할 수 있다. 
`lerpTo(ShapeBorder? b, double t)`  이런식으로 생겼는데 ShapeBorder 우리가 자주 사용하던 ShapeBorder를 넣어주면 되고 그 뒤에 t는 보간을 나타낸다.
0은 시작 모양 즉 BeveledRectangleBorder 팔각형이 나타나고, 
1은 끝 모양을 의미한다. 1을 넣으면 직사각형이 나타난다.
이를 통해 두 모양의 중간 형태를 만들 수 있다.

![shape#1](/assets/post/shape/shpae1.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 1.2 일반적인 둥근 모서리 Box
```dart
Widget _item2() {
  return DecoratedBox(
    decoration: BoxDecoration(
      borderRadius: BorderRadius.circular(10),
      shape: BoxShape.rectangle,
      color: Color(0xFFd6f5ff),
    ),
    child: const SizedBox(height: 200, width: 200),
  );
}
```
`BoxDecoration`은 Flutter에서 가장 일반적으로 사용되는 장식 위젯이다. `borderRadius`로 모서리의 둥글기를 지정할 수 있으며, `shape`으로 기본 형태를 설정할 수 있다. `BoxShape.rectangle`은 사각형 형태를 의미한다.

![shape#2](/assets/post/shape/shpae2.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 1.3 ShapeDecoration을 활용한 특별한 모양
```dart
Widget _item3() {
  return DecoratedBox(
    decoration: ShapeDecoration(
      color: Colors.amber,
      shape: BeveledRectangleBorder(
        borderRadius: BorderRadius.circular(20)
      ),
    ),
    child: const SizedBox(height: 200, width: 200),
  );
}
```
`ShapeDecoration`은 `BoxDecoration`보다 더 다양한 형태를 구현할 수 있다. `BeveledRectangleBorder`를 사용하면 모서리가 대각선으로 깎인 8각형 형태를 만들 수 있다.

![shape#3](/assets/post/shape/shpae3.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 1.4 미리 정의 된 모양을 사용
```dart
Widget _item4() {
  return DecoratedBox(
    decoration: ShapeDecoration(
      shape: StarBorder(),
      color: Colors.amber,
    ),
    child: const SizedBox(height: 100, width: 100),
  );
}
```
Flutter는 `StarBorder`와 같은 미리 정의된 특수 도형들을 제공한다. 
이러한 내장 도형들은 별도의 복잡한 구현 없이도 특별한 모양을 쉽게 만들 수 있게 해준다.
물론 나도 처음 써본다..!

![shape#4](/assets/post/shape/shpae4.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

## 2. 커스텀 ShapeBorder 구현하기

메시지 버블 형태의 커스텀 도형을 만들어보자

```dart
class MessageShapeBorder extends OutlinedBorder {
  final double borderRadius;
  final double weight;

  const MessageShapeBorder({
    super.side,
    this.borderRadius = 50,
    this.weight = 2.5
  });

  @override
  Path getInnerPath(Rect rect, {TextDirection? textDirection}) {
    final strokeWidth = side.width;
    // rect의 경계값 계산
    final double left = rect.left + strokeWidth;
    final double right = rect.right - strokeWidth;
    final double top = rect.top + strokeWidth;
    final double bottom = rect.bottom - strokeWidth;

    final radius = math.max(0, borderRadius - 10);
    final offset = 10;

    // Path를 사용한 도형 그리기
    return Path()
      ..moveTo(left + offset + radius, bottom)  // 시작점 이동
      ..conicTo(  // 코닉 곡선으로 둥근 모서리 구현
          left + offset, bottom, 
          left + offset, bottom - 2 * radius, 
          weight)
      // ... 나머지 경로 정의
      ..close();  // 경로 닫기
  }
  // ... 나머지 구현
}
```

이 커스텀 ShapeBorder는
1. `weight` 파라미터로 곡선의 둥글기를 조절할 수 있다
2. `borderRadius`로 모서리의 전체적인 둥글기를 설정할 수 있다
3. `conicTo` 메서드로 부드러운 곡선을 그릴 수 있다

```dart
Widget _item5() {
  return MessageBubble(text: "메세지다 야호야호");
}
```

![shape#5](/assets/post/shape/shpae5.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 2.1 메시지 버블 활용 예시
```dart
Widget _item6() {
  return DecoratedBox(
    decoration: ShapeDecoration(
      shape: MessageShapeBorder(
        borderRadius: 50,
        side: BorderSide(width: 0, color: Color(0xFF7ADEFF))
      ),
      color: Color(0xFF7ADEFF),
    ),
    child: SizedBox(width: 500, height: 200),
  );
}
```
커스텀 `MessageShapeBorder`를 사용하여 채팅 버블과 같은 모양을 만들 수 있다. 
`borderRadius`와 `side` 속성으로 테두리의 스타일을 조절할 수 있다.

![shape#6](/assets/post/shape/shpae6.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

### 2.2 이미지 클리핑 예시
```dart
Widget _item7() {
  return ClipPath(
    clipper: const ShapeBorderClipper(
      shape: MessageShapeBorder(),
    ),
    child: Image.asset(
      "assets/tt.jpeg",
      height: 200,
      width: 300,
      fit: BoxFit.cover,
      cacheHeight: (300 * MediaQuery.of(context).devicePixelRatio).toInt(),
    ),
  );
}
```
`ClipPath`와 `ShapeBorderClipper`를 사용하면 이미지를 원하는 형태로 잘라낼 수 있다. 여기서는 우리가 만든 `MessageShapeBorder`를 사용하여 이미지를 메시지 버블 형태로 잘라낸다.

![shape#7](/assets/post/shape/shpae7.png){:style="border:1px solid #eaeaea; border-radius: 7px; padding: 0px;" }

## 내가 느낀 결론


## 참조

<!-- - [Rive를 Flutter에서 사용하는 방법](https://medium.com/@moo_min/rive%EB%A5%BC-flutter%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-1533ccbfc7ac){:target="_blank"}
- [패스트 캠퍼스 강의](https://fastcampus.co.kr/dev_online_dartflutter){:target="_blank"} -->
<!-- end post -->
