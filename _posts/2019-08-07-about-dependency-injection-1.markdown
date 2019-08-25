---
layout: post
title:  "Dependency Injection in Swift"
date:   2019-08-07 10:19:36 +0530
categories: Programming Swift
---
 Dependency Injection는 객체간 사이의 의존성을 최소화 해서 Coupling을 줄이고, 재사용성을 높이는 것을 목표로 합니다. 이는 테스트를 용이하게 해주는 역할도 하죵 
 
 코드에서 dependency injection을 사용할때, 우리는 객체에 인스턴스 변수들을 넘겨주는데요, 이를 통해 객체가 의존성을 스스로 생성하고 관리하지 않고, 우리가 객체에 의존성을 주입시켜주는 겁니다. 

아래를 보면, 같은 ViewModel 객체이지만, 
 얘는 스스로 Library를 만들어서 쓰고있고, 
```swift
class LibraryViewModel {
	 var library = Library()
 }
```
아래 코드에서는 만들어서 넣어준걸 쓰고있죠,  아래 LibraryViewModel 객체는 library는 property를 직접 생성하고 있지 않고, 밖에서 값을 만들어 넣어주고있기때문에 의존성을 주입받고 있다고 할 수 있습니다.

 이런 Dependency Injection 방식을 **Property Injection**이라고 부릅니다. 😀
단점이 하나 있다면, 해당 Property를 사용할때, 의존성 주입이 되었는지 확인하고 써줘야한다는 것 입니다. 
```swift
class LibraryViewModel {
	 var library = Library?
}
let libraryViewModel = LibraryViewModel()  
let library = Library()  
libraryViewModel.library = library
```

Dependency Injection의 다른 구현 방법은 iOS에서 제일 즐겨쓰는 방법인 **Constructor Injection** 이 있는데요
이 케이스는 constructor나 initializer를 통해 의존성을 주입해줍니다. 
아래 예시를 보면 
```swift
class LibraryViewModel { 
	var library: Library 
	
	init(library: Library) {  
		self.library = library  
	}
}
```

이렇게 initializer를 통해 외부에서 Library 객체를 생성해 넣어주고 있죠! 
이 방법은 객체를 생성할때 모든 의존성을 주입해주기때문에, 객체에 필요한 속성 일부를 까먹고 안넣어주는 경우를 방지해준다고 합니다. 

다음은 **Method Injection** 입니다. 이 방법은 Parameter-based Injection이라고 불리기도 하는데요~
아래 예시를 보면, protocol내 deleteAllBooks Method에서 Library 객체를 parameter로 
```swift
protocol LibraryManager { 
	func deleteAllBooks(in library: Library)  
}
```
만약 의존성이 딱 한번 필요한거라면, 객체 안에 property로 저장할 필요가 없습니다. 따라서 의존성을 그냥 Method의 Prameter로 넘겨주는 형태로 사용할 수 있죠
이 방법을 사용하면, method가 불릴때마다 의존성이 달라지고, 해당 reference를 유지하지 않기 때문에 local method scope에서만 사용할 수 있습니다. 

의존성 주입의 대표적인 세가지 예를 알아보았습니다! 

## 참고자료
[medium.com/@JoyceMatos/dependency-injection-in-swift](https://medium.com/@JoyceMatos/dependency-injection-in-swift-87c748a167be)


