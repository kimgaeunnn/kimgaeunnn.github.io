---
layout: post
title:  "Preventing Timing Problems When Using Closures"
date:   2019-08-25 18:56:50 +0530
categories: Programming Swift
---

# Preventing Timing Problems When Using Closures

Closure를 실행하는 여러개의 API를 호출했을때, App에 무슨일 이 생길까요? Closure를 사용했을 때 생길 수 있는 Timing 문제와 해결방법에 대해 알아보겠습니다 😎 



## Overview

Swift를 이용해 API 호출 및 응답을 받을때 Parameter로 Closure를 자주 넘기죠! Closure에는 앱의 다른 부분과 상호작용하는 코드를 넣을 수 있기 때문에, **API에 Closer를 담아서 넘기는 여러가지 방법**에 대해 이해하는게 중요합니다. API 호출시 담긴 Closure는 **동기(즉시), 비동기(일정시간이 지난 후)**로 불릴 수 있죠. 그리고 **한번 호출될수도, 여러번 호출될 수도, 호출이 안될 수도** 있습니다. 



> 중요! 
>
> closure에 대해 잘못된 가정을 하고 호출하면, 앱이 죽거나 data 일관성이 깨질 수 있습니다. 



## Understand the Results of Synchronous and Asynchronous Calls

#### 동기 호출과 비동기 호출의 결과에 대해 이해해보자



API 호출시 closure를 보낼때, closure가 앱내 다른 코드와 비교해서 **언제** 불릴지를 고려해야합니다. 동기식 API에서는, clousre를 넘기자마자 실행될 수 있습니다. 그리고 비동기식 API에서는 시간이 조금 지나도 result 값 사용이 불가할 수 있습니다. 이 차이점은 closure 내의 코드와 closure 실행에 뒤따라오는 코드 모두를 어떻게 쓰느냐에 따라 다른 영향을 미칠 수 있습니다. 



아래 예시에서는 *now(_:)*,*later(_:)*두개의 함수를 선언하고 있습니다. 두 함수 모두 매개변수가 없는 trailing closure를 이용해 호출할 수 있습니다. 두 함수 모두 closure를 호출하고 있지만, *later(_:)* 함수는 closure 호출 전 2초정도 기다립니다. 

```swift
import Dispatch
let queue = DispatchQueue(label: "com.example.queue")

func now(_ closure: () -> Void) {
    closure()
}

func later(_ closure: @escaping () -> Void) {
    queue.asyncAfter(deadline: .now() + 2) {
        closure()
    }
}
```

위 두 함수는 API 호출시 가장 흔하게 closure를 사용하는 방식이죠 *now(_:)*는 동기방식, *later(_:)*는 비동기 방식과 유사합니다. 

Closure 호출은 앱의 상태를 나타내는 지역/전역변수를 변경할 수 있기 때문에, closure를 넘긴 직후 코드라인은 closure가 어느 시점에 불릴지 잘 생각해서 짜야합니다. 간단하게 문자를 순서대로출력하는 기능도 closure 호출로 인해 영향받을 수 있습니다. 



```swift
later {
    print("A") // "A" 일정 시점 이후 출력
}
print("B") // "B" 바로 출력

now {
    print("C") // "C" 바로 출력
}
print("D") // "D" 바로 출력

// Prevent the program from exiting immediately if you're running this code in Terminal.
let semaphore = DispatchSemaphore(value: 0).wait(timeout: .now() + 10)

// 출력결과: B C D A
```

 위 예시코드를 실행하면, *B C D A* 순서로 출력됩니다. "A" 문자 출력코드가 제일 상단에 짜여있지만, 출력에서는 가장 마지막입니다. 순서 섞임은 *now(_:)*,*later(_:)* 함수가 선언된 방식때문에 일어납니다. 각 함수가 어떻게 closure를 호출하고 실행 순서가 어떻게 될지 알고 소스를 짤 필요가 있습니다. 😶



>  메모 
>
> 다른 문자와 비교해서 A가 프린트되는 시점은 언제다! 라고 보장할 수가 없습니다. 일반적인 시스템 환경에서는 마지막에 출력되겠지만, 동기로 실행되는 코드와 비동기로 실행되는 코드의 실행 순서가 필요한 코드는 짜지 않는것이 좋습니다. 스레드간의 동기화를 확실히 해주는 경우가 아니라면요!  

API 호출시 closure를 사용할 때에는, 위와 같은 시점/순서 기반의 이슈들에 대해 잘 생각해야합니다. 대부분의 경우에 여러분의 앱에서는 딱 한가지 실행 순서가 알맞은 경우가 많기때문에, 주어진 API를 사용했을때 App의 State가 어떻게 될지에 대해 고민하는게 중요합니다. API 이름이나 매개변수 이름을 지을때 동기/비동기 여부를 명시해 주는것이 좋습니다. 

자주 일어나는 타이밍 관련 실수는, 비동기 호출 결과가 동기 함수 호출 전에 일어날 수 있을거라고 생각하는 경우 일어납니다. 예를들어, *later(_:)* 메소드는 비동기 방식이기때문에 [`URLSession`](https://developer.apple.com/documentation/foundation/urlsession)의  [`dataTask(with:completionHandler:)`](https://developer.apple.com/documentation/foundation/urlsession/1410330-datatask) 메소드와 비교 가능합니다. *dataTask(with:completionHandler:)* 함수를 *viewDidLoad()* 와 같은 함수에서 호출하고 그 결과를 closure 밖에서 completion handler를 이용해 사용하는 등의 코드는 짜지 맙시다!!! 



## Don't Write Code That Makes a One-Time Change in a Closure That's Called Multiple Times

#### 여러번 호출되는 Closure에 일회성 변화 코드를 넣지 맙시다 ! 

API호출시 여러번 호출 되는 Closure를 전달했는데, 외부 State를 변경하는 코드가 들어있다면 어떻게 될까요? 

아래 예시는 [`FileHandle`](https://developer.apple.com/documentation/foundation/filehandle)을 생성하고, 데이터 배열을 해당 handle이 참조하는 파일에 write 하는 내용입니다. 

```swift
import Foundation

let file = FileHandle(forWritingAtPath: "/dev/null")!
let lines = ["x,y", "1,1", "2,4", "3,9", "4,16"]
```

 각 라인들을 파일에 쓰기위해, *forEach* 메소드를 이용해 closure를 넘기고있습니다. 

```swift
lines.forEach { line in
    file.write("\(line)\n".data(using: .utf8)!)
}
```

*FileHandle* 사용이 끝난 후,  *closeFile()* (일회성 변화! 한번 닫으면 FileHandle로 다시 열어줘야되죠.. )을 이용해 닫아주고있습니다. *closeFile()* 의 알맞은 호출 위치는 closure 밖입니다. 

만약 *closeFile()* 을 잘못해서 closure 내에 넣어버리면 

```swift
lines.forEach { line in
    file.write("\(line)\n".data(using: .utf8)!)
    file.closeFile() // Error
}
```

이후 Closure 호출시 닫힌 파일에 쓰려고 하기 때문에, 앱이 죽어버립니다... 😫



## Don't Put Critical Code in a Closure That Might Not Be Called

#### 불리지 않을 수도 있는 Closure에 중요한 코드를 넣지 맙시다! 

만약 Closure가 불리지 않을 수도 있다면, 앱 실행시 중요한 코드를 넣으면 안됩니다. 아래 예시를 보면 *Lottery* Enumeration을 선언해서 당첨번호를 뽑고, 당첨되었는지 확인되는 코드가 completion Handler 에 들어있습니다. 

```swift
enum Lottery {
    static var lotteryWinHandler: (() -> Void)?
    
    @discardableResult static func pickWinner(guess: Int) {
        print("Running the lottery.")
        if guess == Int.random(in: 0 ..< 100_000_000), let winHandler = lotteryWinHandler {
            winHandler()
            return true
        }
        
        return false
    }
}
```

 Completion handler에 의존적인 코드를 쓰는건 위험합니다. 랜덤으로 뽑은 수가 당첨될거라는 보장이 없기때문에, 당첨되면 실행되는 *paying bills*와 같은 중요한 코드가 아예 실행이 되지 않을 수 있겠죠. 



## Reference

[Apple Development Article - Preventing Timing Problems When Using Closures](https://developer.apple.com/documentation/swift/preventing_timing_problems_when_using_closures)
