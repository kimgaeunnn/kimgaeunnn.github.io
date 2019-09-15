---
layout: post
title:  "Swift Protocol에 대하여"
date:   2019-09-15 13:58:46 +0530
categories: Programming Swift
---

Apple에서는 Swift는 OOP에도 훌륭하지만, 본질적으로는 **Protocol 지향 언어**라고 소개합니다. 처음엔 Delegate나 DataSource를 위해 Protocol을 사용했었는데요, Protocol을 어떻게 지향하면 좋은 코드를 짤 수 있을지 알아보겠습니다! 🔥



### 왜 Protocol-Oriented 인가?

Swift가 Protocol 지향 언어가 된 이유가 무엇일까요? 🤔

#### 상속

- Swfit는 **multiple inheritance (다중 상속)** 를 허용하지 않습니다. 따라서 여러 종류의 기능을 구현하는 class에서는 조금 불편한 점이 있습니다. 

  - 하지만, **Protocol은 다중 채택 및 구현이 가능**하기때문에 여러 종류의 기능을 가지는 class를 만들 수 있게 해줍니다.

- Class 상속시에는 Super Class의 모든 API (사용하지 않을 API에도)에 접근하지만, 

  - Protocol은 해당 Protocol에서 정의한 API만 가져오기 때문에 **가볍습니다!**

- Class 상속시에는 여러 Class에  유사한 기능이 구현된 중복코드가 생기기 쉽습니다. 

  - **Protocol Default Implementation**을 이용하면 해결가능합니다.! 

    

#### Reference Type VS Value Type

- Class 같은 Reference Type은 원본이 여기저기서 참조되고 MultiThread 환경에서는 값이 꼬이기 쉽고, 많은 자원을 소모합니다.
  - 그래서 굳이 원본만 둘 필요가 없는 경우면, Enum/Struct 같은 ValueType을 사용하는것이 권장됩니다. 하지만, Value Type들은 상속이 안되기때문에 쓰기 불편합니다. 
    - 💡 Protocol을 이용하면 Enum, Struct 같은 **ValueType도 상속의 효과를 줄 수 있습니다.**



 기존에는 Swift 내 자료형들 대부분이 Class로 이루어져있었는데, POP를 발표하고나서부터 이런 기본 자료형이나 Built-in Method들도 Struct/Protocol로 변경 정의하고 있습니다. 

한가지 예시로 아래 SwiftUI의 View 선언부를 살펴보면 Protocol로 되어있음을 알 수 있죠 

![image-20190915124006622](/assets/images/2019-09-15-about-protocols-1.png)



이제 Protocol의 기본적인 개념과 사용법을 알아보겠습니다. 🙀

## Protocol 이란?

**특정 기능을 구현하기 위해 필요한  Method나 Property등의 Requirements의 청사진** 입니다.

특정 기능을 위한 청사진이 무엇인지 알아보기위해, 예시로 Vehicle Protocol을 채택하는 자동차 Struct를 구현해보겠습니다.  

```swift
protocol Vehicle {
    var speed: Int { get }
    var color: String { get }
    var price: Int { get }
 }
```

위 Protocol을 통해 자동차의 기능을 나타내는 Property Requirements들을 미리 선언해두었습니다. 아래 코드는 위 청사진을 이용해 만든 자동차 구조체 구현부 입니다. 

```swift
struct Car: Vehicle {
  	var speed: Int = 100
    var color: String = "Orange"
    var price: Int = 9999999
}
```

운송 수단은 속도, 색깔, 가격이라는 Property Requirements를 꼭 가져야한다는 내용의 Protocol을 선언하고, 운송수단 Protocol을 채택한 자동차 구조체에서는 해당 속성들의 값을 넣어서 구현해주고있습니다. 



- Class, Struct, Enumeration 등이 요구사항을 실제로 구현함으로써 protocol을 **adopt** 할 수 있고 

- Protocol의 요구사항을 모두 만족한 type들은 해당 Protocol을 **conform** 했다고 할 수 있다. 

- Requirements들을 모두 만족하지 못하면 compile 에러를 내뱉습니다. 

  

위 예제에서 *Car* Struct는 *Vehicle* Protocol을 adopt하고, conform 하고 있습니다. 만약 *price* property를 구현해주지 않으면, 

![image-20190915134024149](/assets/images/2019-09-15-about-protocols-2.png)

이렇게 compile Error 가 나게 됩니다. 



## Protocol Syntax

Protocol 선언 방식은 Class, Structure, Enumeration과 비슷합니다. 

Protocol을 Define과, 해당 Protocol을 Adopt(채택) 방법은 아래와 같습니다. 

```swift
// Protocol 선언
protocol SomeProtocol {
}

// 여러개의 Protocol들을 채택(adopt)하는 Struct
struct SomeStructure: FirstProtocol, AnotherProtocol {
}
```

Type 이름 뒤에 : 를 붙이고, 채택하고자 하는 Protocol의 이름들을 써주면 됩니다. 여러개의 Protocol를 채택하려면, 콤마로 구분해 나열해주면 됩니다. ! 😏  

만약 Protocol을 채택한 Class가 다른 Class를 상속하고있다면, SuperClass의 이름을 먼저 써주고, 채택한 Protocol의 이름들을 아래와같이 나열해주면 됩니다. 

```swift
// SomeSuperClass를 상속받고, 여러개의 Protocol들을 채택한 Class
class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
}
```



이번 포스팅에서는

- Swift가 Protocol-Oriented Language가 된이유 
- Protocol의 개념과 기본적인 사용 Syntax 

에 대해 알아보았습니다!  다음 포스팅에서 좀 더 자세한 사용법에 대해 알아보겠습니다. 다음 뽀스팅에서 만나염 😎



## 참고

[docs.swift.org (5.1)](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
