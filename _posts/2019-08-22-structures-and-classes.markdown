---
layout: post
title:  "Choosing Between Structures and Classes"
date:   2019-08-22 20:30:46 +0530
categories: Programming Swift
---

 Data Model 을 저장하고 다룰때 Class로 구현할지 Structure로 구현할지 고민하는 경우가 많았는데, 괜찮은 [Apple Article](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)이 있어서 번역해 보았습니다 🤓



## Overview

 App개발시 데이터를 저장/모델링하기위해 **Class** 와 **Structure**를 많이 사용하는데, 비슷한 점이 많다보니 상황에 어떤걸 써야하는지 결정하기 쉽지 않습니다. 

 이를 위해 애플에서 아래와 같이 몇가지 가이드를 주고 있습니다. 아래 설명을 통해 좀 더 자세히 알아보겠습니다. 

- Use structures by default.
- Use classes when you need Objective-C interoperability.
- Use classes when you need to control the identity of the data you're modeling. 
- Use structures along with protocols to adopt behavior by sharing implementations.



## Choose Structures by Default

###  일단 Structure를 써보세염

 일반적인 Data를 나타낼때는 Structure를 쓰는게 좋습니다. Swift의 Structure는 다른 언어에서는 Class에서만 제한적으로 제공되는 여러 기능을 사용할 수 있습니다. (stored property, computed property, method 등을 가질 수 있죠) 게다가 Swift Structure는 protocol을 adopt 해서 쓸 수도 있습니다. Swfit Standard Library와 Foundation에서 제공하는 Number, String, Array, Dictionary등의 많은 type들이 Structure로 이루어져 있기도 합니다. 

 또한, Structure를 이용하면 앱 전제의 state를 고려하지 않고도 코드를 이해하기 쉽습니다. Structure가 Class와 다르게 ValueType이기 때문에, Structure내의 변경사항이 생기는 경우 (따로 앱의 흐름에 끼워넣거나 하지 않는 이상) 앱 내 다른 부분에서 신경쓰지 않아도 됩니다. 결과적으로, **Structure를 이용하면 코드 일부만 보거나 변경하기 쉬워집니다.**



## Use Classes When You Need Objective-C Interoperability

###  Objective-C로 짜인 무언가와 연관되어있다면 Class를 사용하세염

만약 개발시 data 처리를 위해 Objective-C API를 사용하고 있거나, Data Model을  Objective-C Framework에 선언된 class  hierarchy내에 적용해야 한다면 class나 class 상속을 사용해야합니다. 실제로, 많은 Objective-C Framework들이 데이터 저장을 위한 subclass 목적으로 class를 제공합니다. 



## Use Classes When You Need to Control Identity

### 우리가 직접 동일성 ( Identity ) 제어 해줘야 할 때는 Class를 사용하세염

 Swift의 Class는 ReferenceType이기 때문에, **동일성(Identity)**에 관한 개념을 가지고 있습니다. 이게 무슨말이냐면 서로 다른 두 Class 가 있을때, 서로 모든 Stored Property에 대해 같은 값을 갖고 있다고 하더라도 Identity Operator (===)를 이용해 두 Class가 같은지 알아보면 다르다고 나온다는 겁니다. 이는 곧, App 내에서 Class Instance를 여기저기서 사용하고 있을 때 해당 instance내의 값을 변경하면, 해당 instance를 참조하고 있는 모든 곳에서 변경된다는걸 의미하기도 합니다. 

따라서 Instance의 **Identity**가 위와 같은 방식으로 다뤄져야한다면, Class를 사용하면 됩니다. 대표적으로 File Handling, Network Connection, Hardware intermeiaries (CBCentralManager - core bluetooth) 등에서 이런 방식으로 사용한다고 하네요 🙂

> 중요! 
>
> Identity(동일성)은 조심히 다뤄야합니다. Class Instance를 App 내에서 광범위하게 공유해버리면, logic상의 에러가 나기 쉽습니다. 여기저기서 공유된 instance에 대한 결과값은 확신할 수 없기 때문에, 코드를 잘 짜도록 노력하세염 



## Use Structures When You Don't Control Identity

### 직접 동일성 ( Identity ) 제어할 필요가 없을때는 Structure를 사용하세염

Modeling중인 Data가 담고있는 정보의 Identity를 우리가 직접 제어해줄 필요가 없다면, structure를 사용하면 됩니다. 

예를 들어 Remote Database를 사용하는 앱인 경우에는, instance의 Identity가 외부 entity에 의해 제어됩니다. 만약 app의 Model의 consistency(일관성)가 server에 저장되어 있다면, model 을 그냥 identifier들로 이루어진 structure에 저장해버리면 됩니다.

 아래 예시코드를 살펴보겠습니다. 

```swift
struct PenPalRecord {
    let myID: Int
    var myNickname: String
    var recommendedPenPalID: Int
}

var myRecord = try JSONDecoder().decode(PenPalRecord.self, from: jsonResponse)
```

 위코드는 서버에서 받아온 *PenPalRecord* instance를 저장하는 sturct 입니다. 

Local에서 위의 *PenPalRecord*와 같은 instance 를 변경하는 건 문제가 없습니다. 예를 들면, 사용자 피드백으로 인해 여러개의 펜팔을 보여줘야하는 경우가 생길수 있습니다. 이때, *PenPalRecord* Structure는 실제 database 내용을 제어할 수 없기 때문에, 이 구조체를 바꾼다고 해도 실제 DB의 내용이 바뀔 걱정을 안해도 되죠~! 

만약 app내 다른 부분에서 *myNickName*을 변경하고 server로 request를 보냈다고 하더라도, myID 가 상수로 선언되어있어 local에서 변경될 리가 없기 때문에 Server Database는 변경 될 수가 없겠죠! 😀 



## Use Structures and Protocols to Model Inheritance and Share Behavior

### Model 의 상속이나 sharing을 위해서는 Stucture + Protocol을 쓰세요! 

Structure와 Class는 둘다 inheritance(상속) 형태를 지원합니다. 

Structure는 protocol을 adopt해서 상속 느낌을 내고있죠 🤔 (Class를 상속받지는 못합니다.) 하지만 Structure + Protocol 조합이면 Class 상속과 같은 계층 구조를 만들어 사용할 수 있습니다. 

개발 초기부터 상속 구조를 만드는 경우, **Protocol 상속**을 추천합니다. Protocol은 Class, Structure, Enumeration을 허용해주지만, Class는 Class끼리만 상속이 가능하기때문이죠. Data Modeling을 고민할 때, Protocol 상속을 통해 Data Type의 계층구조를 먼저 설계하고 Structure가 해당 protocol들을 adopt하도록 구현하시면 됩니다! 🙂


## Reference

[Apple Development Article - Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)
