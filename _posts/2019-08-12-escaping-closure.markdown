---
layout: post
title:  "Escaping Closure"
date:   2019-08-12 21:25:36 +0530
categories: Programming Swift
---

 Closure가 함수의 인자로 전달되지만, 해당 함수가 return 된 이후 불리는 경우 escape라고 합니다. 아래 예시에서도 나오겠지만 흔히 completion handler로 쓰이는 Closure가 Escaping Closure 라고 할 수 있습니다.



### 사용 방법

 함수의 매개변수 중 하나로 closure를 넘기는경우, 매개변수 타입 전에 **@escaping**을 적어주면 closure가 escape (함수 return되어도 실행 가능) 로 허용된다는 것을 명시합니다.

 Closure가 escape하는 방법은 Closure를 함수 밖에서 선언된 변수에 저장되는 것입니다.
예를 들면, 비동기 연산을 하는 함수들은 completion handler 역할을 하는 closure를 매개 변수로 넘깁니다. 해당 함수가 연산을 모두 끝내고 return되어도, closure는 불리지 않고 나중에 불리죠 ! 이 경우를 escape (탈출) 했다고 합니다 🙃 

 예시 코드를 보면,

```swift
 var completionHandlers: [() -> Void] = []
 func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
     completionHandlers.append(completionHandler)
 }
```

 _someFunctionWithEscapingClosure_ 함수는 closure를 인자로 받아서 함수 밖에 선언된 _completionHandlers_ 배열에 더해주고 있습니다. 매개변수 내용에 **@escaping**을 적어주지 않았다면 compile error가 나버립니다. 



### Escaping Closure 내에서 self 참조하기

 @escaping closure는  closure내에서 self를 참조하는 경우에는 꼭! 명시해줘야합니다 (**_self._**)

 위에서 선언된 _someFunctionWithEscapingClosure_ 함수에서 전달된 closure는 escaping closure입니다. 따라서 아래 코드를 보면 Closure 내부에서 self를 명시적으로 적어주고 있습니다. 

반면에 _someFunctionWithEscapingClosure_ 에서 전달된 closure는 함수내에서 실행되기 때문에 escaping closure가 아니죠! 따라서 암시적으로 self 참조를 할 수 있습니다. 

```swift
func someFunctionWithNonescapingClosure(closure: () -> Void) {
    // 함수 내에서 실행되기 때문에, NoneEscaping임
    closure()
}

class SomeClass {
   var x = 10
   func doSomething() {
       // 명시적 self 참조 필수 
       someFunctionWithEscapingClosure { self.x = 100 }

       // 암시적 self 참조 가능
       someFunctionWithNonescapingClosure { x = 200 }
   }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Prints "200"

completionHandlers.first?()
print(instance.x)
// Prints "100"
```


### 참고자료
[docs.swift.org](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)
