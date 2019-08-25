---
layout: post
title:  "Maintaining State in Your Apps"
date:   2019-08-24 22:10:54 +0530
categories: Programming Swift
---

App 실행중 여러 상태를 나타내거나 유지하기위해 **Enumeration**을 써보자! 는 내용의 [Apple Article](https://developer.apple.com/documentation/swift/maintaining_state_in_your_apps) 번역입니다. 🙂

## Overview

앱을 개발하다보면, 순간순간 앱이 어떤 상태인지 알기 위해 (ex. 로그인/로그아웃 상태 등) State를 효율적으로 관리하는게 중요합니다. 

**Enumeration**은 

- 딱 정해진 갯수의 state를 선언해두고 쓸수있고
- 각각의 state에 관련된 값들을 같이 관리할 수 있기

때문에, 앱 내 프로세스와 state를 관리하기위해 쓰기 좋습니다. 🐥



## Use an Enumeration to Capture State

#### Enumeration을 이용해 State 캡쳐하기

 계정에 로그인이 필요한 앱이 있다고 가정해봅시다. 앱을 기동하면, 사용자가 누군지 모르기 때문에 app의 상태는 *unregistered(미등록)* 상태가 됩니다. 사용자가 회원가입 후 로그인을 하면, 상태는 *logged in(로그인)* 이 되겠죠. 그리고 앱을 오래 사용하지 않아 session이 만료되면, *session expired(세션만료)* 상태가 됩니다.

 이런 상태들을 관리하기위해 Enumeration을 쓸 수 있는데요 🧐 아래 예시 코드는 *App* 이라는 class를 선언하고 그안에 State라는 enumeration을 만들어서 특정 상태들을 관리하는 코드입니다. 

```swift
class App {
    enum State {
        case unregistered
        case loggedIn(User)
        case sessionExpired(User)
    }
  
    var state: State = .unregistered
  
    // ...
}
```

위 Model 안에서는 각 state들이 상태 이름으로 이루어진 case로 들어가있죠. *loggedIn*이나 *sessionExpired* 의 경우, 사용자 정보와 상관있는 케이스이기 때문에 *user*를 associated value로 포함하고 있습니다. app의 state를 바꿔줄 때는 그냥 state 변수 하나만 바꿔주면 되니까 정말 간단하죠 ! 



## Don't Spread State Across Multiple Variables

#### State를 여기저기 변수에 나눠서 관리하면 안됩니다! 

이번에는 이 아티클에서 비추하는 State 관리법입니다. State를 변수를 각각 만들어서 저장해놓고 쓰는 방법입니다.  

위의 예시와 같은 앱의 상태를 관리한다고 했을때, user 정보가 nil 인경우에는 *log out* 으로 판정하며 user 정보가 있을때는 *log in* 상태로 판단할 수 있습니다. 그리고 *sessionExpired* 라는 변수를 만들어두고 true/false를 set해주면서 관리할 수도 있습니다. 이 방법은 2개 변수를 이용해 3가지 State를 관리하고 있는데요 

이런식의 State 관리는 몇가지 이유로 버그나 실수를 유발하기 쉬운데염

- State 가 변경될때마다 *user*, *sessionExpired* 변수를 업데이트 해줘야합니다.
- 다른 State가 생기는 경우, 또 다른 변수를 만들어야합니다. ㅠㅠ 
- 두개 변수를 이용해서 3개 State를 만드는 경우 비는 케이스가 생기죠.. *user* 가 nil 인데 *sessionExpire* 가 true가 되어버리면.. 있을 수 없는 상태가 생겨버립니다. 😨



## 그러므로 

### App의 State 관리는 공통 class에서 Enumeration을 이용해서 합시다! 



## Reference

[Apple Development Article - Maintaining State in Your Apps](https://developer.apple.com/documentation/swift/maintaining_state_in_your_apps)
