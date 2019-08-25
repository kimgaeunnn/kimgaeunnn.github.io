---
layout: post
title:  "Subscripts란"
date:   2019-08-21 12:15:46 +0530
categories: Programming Swift
---

Subscript란, collection이나 list, sequence등의 각 요소들에 간단하게 접근할 수 있는 방법입니다. Class, Structures, Enumeration에서 subscripts를 선언해서 사용할 수 있습니다. Array에서는 arr[index], Dictionary에서는 dic["key"] 와 같이 따로 get/set 함수를 구현하지 않고도 요소들에 접근해서 Subscripts를 사용하고 있습니다. 🙂

특정 Type에 대한 여러개의  subscript를 선언할 수도 있고, subscript에 이용된 index의 type에 따라 그에 맞는 overload subscript가 사용될 수도 있습니다. Subscript는 1차원으로 한정되어있지 않기때문에, 필요하다면 여러개의 input parameter를 index로 사용할 수도 있게 됩니다. 

## Syntax

Subscript는 instance의 이름 뒤에 한개 이상의 변수를 담는 bracket을 이용해 instance내 요소에 접근할 수 있게 해줍니다. ( ex. instance[idx1, idx2] ) 

Subscript 내용 작성 Syntax는, instance method나 computed property 작성 방법과 정말 비슷합니다. 선언 할때는 method를 선언하는 것처럼, subscript 키워드를 써주고 input parameter 들과 return type을 명시해줍니다. method 선언과 다른점은, subscripts는 read-write or read-only라는 점입니다. == computed property처럼 getter, setter 를 이용해줘야 한다는 겁니다.

```swift
// read-write
subscript(index: Int) -> Int {
  get {
    	// subscript 될 값을 리턴
  }
  set (newValue) {
    	// 새로운 값을 세팅
  }
}
```

위에서 newValue의 Type은 subscript의 return Type과 같아야 합니다. Computed property와 마찬가지로 setter의 newValue parameter를 사용하지 않아도 됩니다. newValue parameter는 default value로 setter에 값을 직접 설정하지 않은 경우 사용됩니다. 

read-only 속성을 가진 computed paramater처럼, read-only subscript도 설정 가능합니다. get 키워드와 해당 중괄호만 지워주면 됩니다! 🤓

```swift
// read-only
subscript(index: Int) -> Int {
    	// subscript 될 값을 리턴
}
```

아래는 read-only subscript 예시 코드 입니다. 

```swift
struct TimesTable {
	  let multiplier: Int
	  subscript(index: Int) -> Int {
 	  		 return multiplier * index    
	  }
}

let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// Prints "six times three is 18"
```

 이 예시를 보면, *TimesTable*의 새 instance는 3배로 곱해주는 time table인데요, *threeTimesTable[6]*을통해 subscript를 호출하면 3 * 6 결과인 18을 return 해주는 것을 볼 수 있습니다. 



## Usage

Subscripts는 input parameter의 갯수나 Type에 상관없이 사용할 수 있습니다. return type도 뭐든지 가능합니다. Subscript는 variadic parmeter, default parameter value 모두 가질 수 있지만, in-out parameter는 사용이 안됩니다. 

Class나 Struct 는 subscript를 여러개로 구현 해서 쓸 수있고, subscript 사용시 넘겨지는 index의 type이나 갯수에 따라서 맞는 subscript가 호출됩니다. 이렇게 여러개의 subscript를 선언하는걸 **subscript overloading** 이라고 합니다. 

아래 예시는 *Matrix* structure가 두개의 integer parameter를 이용해 2차원 행렬을 subscript 하고 있는 내용입니다. 

```swift
struct Matrix {
  let rows: Int, columns: Int
  var grid: [Double]
  
  init(rows: Int, columns: Int) {
    self.rows = rows
    self.columns = columns
    grid = Array(repeating: 0.0, count: rows * columns)
  }  
  
  func indexIsValid(row: Int, column: Int) -> Bool {
    return row >= 0 && row < rows && column >= 0 && column < columns    
  }  
  
  subscript(row: Int, column: Int) -> Double {
    get {
      assert(indexIsValid(row: row, column: column), "Index out of range")
      return grid[(row * columns) + column]        
    }        
    set {
      assert(indexIsValid(row: row, column: column), "Index out of range")
      grid[(row * columns) + column] = newValue
    }
  }
}
```

1차원 배열 *grid*를 이용해 2차원 행렬을 구성하고 있지만, get/set  함수를 이용해 subscript할때는 마치 2차원 행렬에 접근하는 것 처럼 사용할 수 있게 구현된 내용입니다. 

```swift
var matrix = Matrix(rows:2, columns:2)

matrix[0,1] = 1.5
matrix[1,0] = 3.2 
```

get/set 함수에서 index out of range 관련 에러처리가 되어있기때문에, 아래 코드에서는 assert가 나오겠죠

```swift
let someValue = matrix[2,2]
// Assert Index out of range !
```



## Type Subscripts

위에서 설명한 것처럼 Instance Subscripts는 특정 type의 instance를 subscript 하고 있습니다. Type 자체에서 subscripts를 선언해서 사용할 수도 있습니다. 이를 **Type Subscripts**라고 부릅니다.

Type Subscript 선언시에는 subscripts 키워드를 써주기 전에 **static** 키워드를 써줘야 합니다.   

아래 코드는 Type Subscripts 예시 코드 입니다. 

```swift
enum Planet: Int {
  	case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
  	static subscript(n: Int) -> Planet {
      	return Planet(rawValue: n)!
    }
}

let mars = Planet[4]
print(mars)
```



## Reference

[docs.swift.org (5.1)](https://docs.swift.org/swift-book/LanguageGuide/Subscripts.htmll)

