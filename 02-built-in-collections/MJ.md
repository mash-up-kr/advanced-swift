#Collection Type

# **Array**

-    **같은 타입**의 데이터를 **일렬**로 **순서대로** 저장

-    중복 가능

-    1

-    **struct**이므로 **value type**

     ```swift
        var x = [1,2,3]
        var y = x
        y.append(4)
        x	// [1,2,3]
        y	// [1,2,3,4]
     ```

-    let : 수정 불가 , var : 수정 가능

     ```swift
        var numbers = [1,2,3,4,5,6,7,8]	//배열 생성 + Type Inference
        //var numbers = Array<Int>()	배열 생성 + Type Inference

        numbers.append(1)				//[1,2,3,4,5,6,7,8,1]	//value를 추가
        numbers.append(contentsOf: [100,101])	//[1,2,3,4,5,6,7,8,1,100,101]	배열을 추가
     ```

-    배열 생성방법

     ```swift
        var array : [String] = []	//타입 명시시 []만으로 빈 배열 생성 가능
        [Int]()			// 빈배열
        Array<Int>()	// 빈배열
        [2,3,4]			
        Array(repeating: 123, count: 4)		//[123,123,123,123]
        Array(repeating: 1, count: 2) + [123,123,123,123]		//[1,1,123,123,123,123]
     ```

-    배열 접근

     ```swift
        var array = [1,2,3,4,5]
        array.isEmpty		//return Bool
        array.count 		//return Int

        //범위연산자 사용하여 읽기와 수정 가능
        array[1…3]             //읽기
        array[1…3] = [“1”,”2”,”3”]       //수정
     ```


- **index로 접근하지 말것**
  - 함수나 프로퍼티 사용하기
    - array[0] -> array.first
    - array[array.count - 1] -> array.last

- removeLast() vs popLast()

  - 마지막 element를 제거하고 return 한다
  - popLast() : 빈 배열인 경우 nil을 return
  - removeLast() : 빈 배열일 경우 error

- insert(contentsOf:)를 통해 배열도 삽입 가능

  ```swift
  array.insert(contentsOf: [“123”,”123”])
  ```

- remove(_:) 이용시, 해당 요소가 삭제된 후 반환

- subscript를 통해 index 접근 가능 - extension

  ```swift
  //public subscript(index: Int) -> Element 를 변경하는것
  extension Array {
      subscript(safe index: Int) -> Element? {
          if index >= self.count {
              return nil
          }
          return self[index]
      }
  }

  array[safe: 100]	//return nil
  ```


## **Array Iterate**


```swift
for x in array {
   //모든 index iterate
 }
```


```swift
 for x in array.dropFirst() {
   //index == 0 제외한 iterate
 }
```


```swift
 for x in array.dropLast(5){
   //뒤에서 5개 제외한 iterate.
   //array의 lastindex보다 큰 값이 들어가는 경우 nothing happen
 }
```


```swift
 for (index, element) in array.enumerated() {
   //index와 element에 각각 접근 가능
 }
```


```swift
 //조건에 부합하는 val의 index 반환. 없으면 nil반환
 array.index({(val: T) in return 조건})
 //즉 특정한 element의 index 찾음
```

```swift
 //배열의 각 element들을 transform
 array.map({(val: T) in 로직 })
 //flatmap과의 비교
 1. 옵셔널을 푼다
 let mapItems : [Int?] = [1,2,3,nil]
 mapItems.map { $0 }	// [1,2,3,nil]
 mapItems.flatMap { $0 }	//[1,2,3]

 2. 중첩배열 풀기
 let 중첩배열 = [[1,2,3],[4,5,6]]
 중첩배열.flatMap { $0 }	//[1,2,3,4,5,6]
 //[Any]형식의 배열은 안됨
```


```swift
 //조건에 부합하는 element들만 가져옴
 array.filter({val: T} in return 조건)
```


```swift
 //오름차순 정리
 array.sort()

 array.sort { $0.age < $1.age }
 people.contains { $0.age < 18 }
 people.sort { $0.name.uppercased() < $1.name.uppercased() 
```

```swift
 //array가 otherArray보다 사전적으로 앞에 오는지 검사(return Bool)
 array.lexicographicallyPrecedes(otherArray)

 //컬렉션의 요소 순서를 재정렬하고 주어진 조건자를 요소 간 비교로 사용하여 피벗 인덱스를 반환.
 var numbers = [50, 30, 60, 50, 80, 10, 40, 30]
 numbers.partition { a, b in a > b}	// 2
 numbers //[60, 80, 50, 50, 30, 10, 40, 30]
```


```Swift
 //두 sequence가 같은 순서의 elements를 갖는지 검사(return Bool)
 array.elementsEqual(otherArray)
 //시퀀스의 초기 요소가 다른 시퀀스의 요소와 같은지 여부를 나타내는 부울 값을 반환.
 array.starts(with: otherArray)

 [1,2,3,4,5].elementsEqual([1,2,3])	//false
 [1,2,3].elementsEqual([1,2,3,4,5])	//false
 [1,2,3,4,5].starts(with: [1,2,3])	//true
 [1,2,3].starts(with: [1,2,3,4,5])	//false
```


```swift
 //separator로 배열을 나눈다
 [1,2,3,4,5].split(separator: 3)		//[[1,2],[4,5]]
```


```swift
 [1,2,3,4,5].forEach({(val: Int) in 로직})	
```
**이 모든 기능의 목표는 새로운 배열의 생성, 소스 데이터에 대한 for 루프 등과 같이 코드의 중요하지 않은 부분이 혼란스럽게되는 것을 없애는 것.**


-    **NSArray**는 immutable한 Obj-c의 class => **reference type**

     - mutable한 NSArray를 사용하려면 NSMutableArray 사용
     - 값타입




## **Mutation And Stateful Closures ?????**

- 단순히 insert의 반복 ->  map의 용도(각 elements의 transform) 와 다르게 사용

  ```swift
  array.map { item in 
  	table.insert(item)	//용도에 맞지 않게 map사용한 것 -> forloop 사용하는것이 나음
  }
  ```

  ​

## **Filter**

- 특정 조건과 일치하는 요소만 포함하는 새로운 배열을 만듦. 
  - 컨테이너 내부의 값을 걸러서 추출.
  - **새로운 컨테이너에 값을 담아 반환**
  - filter함수의 매개변수로 전달되는 함수는 return Bool
    - **새로운 컨테이너에 포함되려면 return true, 아니면 return false**

```swift
extension Array {
  func filter(_ isIncluded: (Element) -> Bool) -> [Element] {
    var result: [Element] = []
    for x in self where isIncluded(x) {
    	result.append(x) 
    }
    return result 
  }
}

//map과 filter 연계사용
(1..<10).map {(val: Int) in return val * val }.filter {(val: Int) in return val % 2 == 0 }
(1..<10).map { $0 * $0 }.filter {(val: Int) in return val % 2 == 0 }
(1..<10).map { $0 * $0 }.filter { $0 % 2 == 0 } // [4, 16, 36, 64]
```

- **filter** vs **contains**
  - contains: 요소의 전체 배열을 작성하지 않고 첫 번째 요소와 일치하는 즉시 조기에 종료.
    - 새로운 배열 안만들기 때문에 메모리 사용도 적음
  - 일반적으로 모든 결과를 원할 경우에만 필터를 사용.




## **Reduce**

- 컨테이너 내부의 콘텐츠를 하나로 합쳐주는 기능

  - 배열 요소들을 하나의 값으로.

- 초기 값 (이 경우 0)과 중간 값 (result)과 요소(nextValue)를 결합하는 함수의 두 부분을 추상화함.

  ```swift
  let numbers = [1,2,3,4,5]
  numbers.reduce(0, {(result: Int, nextValue: Int) in result + nextValue})	//15

  //연산자도 함수이므로 (reduce(value, function))
  numbers.reduce(0,+)	//15
  ```

- **출력 유형은 요소의 유형과 같을 필요 없다**

  ```swift
  numbers.reduce(""){ str, num in "\(str)" + "\(num)"}	//"12345"
  ```

- Reduce는 매우 유연하며 일반적으로 배열을 만들고 다른 작업을 수행하는 데 사용.

  - 예를 들어, Reduce 만 사용하여 Map 및 Filter를 구현할 수 있음

    ```swift
    extension Array {
      func map2<T>(_ transform: (Element) -> T) -> [T] { return reduce([]) {
      	$0 + [transform($1)] 
      	}
      }
      func  filter2(_ isIncluded: (Element) -> Bool) -> [Element] { return reduce([]) {
      	isIncluded($1) ? $0 + [$1] : $0 
      	}
      } 
    }
    ```

    ​

## **Map**

- 배열, 딕셔너리, 세트, 옵셔널 등에서 사용 가능

  - Sequence, Collection 프로토콜을 따르는 타입과 옵셔널은 모두 map 사용 가능

- 기존 컨테이너의 값은 변경되지 않고 새로운 컨테이너가 생성되어 반환

- **기존 데이터를 변형하는데 많이 사용**

- 다중 스레드 환경일 때 대상 컨테이너의 값이 스레드에서 변경되는 시점에 다른 스레드에서도 동시에 변경되려고 할 때 예측하지 못한 결과가 발생하는 부작용 방지

- 1:1 대응

- 클로저의 재사용화

  ```swift
  let evenNumbers = [0,2,4,6,8]
  let oddNumbers = [0,1,3,5,7]
  let multiplyTwo = { $0 * 2 }

  let doubledEvenNumbers = evenNumbers.map(multiplyTwo)
  let doubledOddNumbers = oddNumbers.map(multiplyTwo)
  ```

- 배열 이외의 컨테이터에서 map 적용

  ```swift
  //Dictionary
  let alphabetDictionary = ["a":"A","b":"B"]
  var keys : [String] = alphabetDictionary.map { (tuple: (String,String)) -> String in
      return tuple.0		//왜 tuple이지..? -> dic의 map은 func map<T>(_ transform: (Key, Value) throws -> T) rethrows -> [T] 형태이므로 tuple!!
  }
  keys = alphabetDictionary.map{ $0.0 }

  //Set
  let numberSet : Set<Int> = [1,2,3,4,5]		//[5,2,3,1,4]
  let resultSet = numberSet.map{ $0 * 2 }		//[10,4,6,2,8]

  //Optional - 모나드와 이어짐
  let optionalInt : Int? = 3
  let resultInt : Int? = optionalInt.map({(unwrappedInt: Int?) in
    guard let unWrappedInt = unwrappedInt else { return 0 }
        return unWrappedInt * 2
    })
  ```




## **FlatMap**

- 중첩배열 푼다

```swift
let 중첩배열 = [[1,2,3],[4,5,6]]
중첩배열.flatMap { $0 }	//[1,2,3,4,5,6]
//[Any]형식의 배열은 안됨
```

- 다른 배열의 요소를 결합할 수 있다

```swift
let suits = ["♠", "♥", "♣", "♦"]
let ranks = ["J","Q","K","A"]
let result = suits.flatMap { suit in
    ranks.map { rank in
    (suit, rank)
    }
}
print(result)	
/*
[
	("♠", "J"), ("♠", "Q"), ("♠", "K"), ("♠", "A"), ("♥", "J"), ("♥", "Q"), ("♥", "K"), ("♥", "A"), ("♣", "J"), ("♣", "Q"), ("♣", "K"), ("♣", "A"), ("♦", "J"), ("♦", "Q"), ("♦", "K"), ("♦", "A")
]
*/

let result2 = suits.map { suit in
    ranks.map { rank in
        (suit, rank)
    }
}
print(result2)
/*
[
	[
		("♠", "J"), ("♠", "Q"), ("♠", "K"), ("♠", "A")
	], 
	[
		("♥", "J"), ("♥", "Q"), ("♥", "K"), ("♥", "A")
	], 
	[
		("♣", "J"), ("♣", "Q"), ("♣", "K"), ("♣", "A")
	], 
	[
		("♦", "J"), ("♦", "Q"), ("♦", "K"), ("♦", "A")
	]
]
*/
```



## **forEach**

- for와 거의 동일

- whatsideeffecttoperformwithanelement - element가 가져오는 side effect관련

- void

- forEach에서는 where 사용 못함

  ```swift
  extension Array where Element: Equatable { 
  	func index(of element: Element) -> Int? {
  	  for idx in self.indices where self[idx] == element { 
    		return idx
    	  }
    	return nil
    } 
  }
  //same
  extension Array where Element: Equatable {
    func index_foreach(of element: Element) -> Int? {
      self.indices. lter { idx in 
        self[idx] == element
      }.forEach { idx in 
      return idx
    }
    return nil
    } 
  }
  ```

- forEach 클로저 내부의 리턴은 외부 함수를 벗어나지 않음

  - 클로저 자체에서만 반환

  ```swift
  (1..<10).forEach { number in 
    print(number)
    if number > 2 { return }	//루프가 끝나지 않고 다시 돌아옴. break시 'Unlabeled break is only allowed inside a loop or switch, a labeled break is required to exit an if or do'
  }
  //1,2,3,4,5,6,7,8,9

  //for in loop
  for idx in 1..<10 {
      print(idx)
      if idx > 2 { break }	//return시 'Return invalid outside of a func'
      print("asdf")
  }
  //1,asdf,2,asdf,3
  ```




## **ArraySlices**

- 배열조각

  ```swift
  let slice = numbers[1..<numbers.endIndex]
  type(of: slice)	//ArraySlice<Int>
  ```

- 배열에 대한 뷰

- 배열 복사할 필요 없음

- 포인터는 동일

- copy-on-write

- 배열처럼 사용 가능(배열과 동일한 메서드 가짐)

- 배열로 변환 가능

  ```swift
  Array(slice)
  ```

  ​

## **Bridging**

- 스위프트 Array는 Objective-C에 연결할 수 있음 
- NSArray는 객체 만 저장할 수 있기 때문에 Swift 배열의 요소를 연결할 수 있도록 AnyObject로 변환 할 수 있어야 했다.
- 이로 인해 클래스 인스턴스와 Objective-C 클래스에 자동 브리징을 지원하는 소수의 값 유형 (예 : Int, Bool 및 String)에 대한 브리징이 제한되었다.
- 이 제한 사항은 Swift 3에서는 더 이상 존재하지 않는다. Objective-C id 유형은 AnyObject 대신 **Any**로 Swift로 가져 오므로 Swift 배열은 이제 NSArray에 연결할 수 있다.




# **Dictionaries**

- struct

- key, value pair

- key는 Hashable protocol을 구현한 type만 가능

- **isEmpty** 이용하여 빈 딕셔너리 확인( true / false 반환)

- 특정 요소에 대한 검색시, Array는 크기에 따라 선형적으로 증가 / Dictionary는 O(1)

- subscripting을 사용하여 value 가져올 수 있음

  ```swift
  public subscript(key: Key) -> Value?
  //subscripting : 
  //클래스, 구조 그리고 열거형은 서브스크립트를 정의할 수 있는데 컬렉션, 리스트 또는 순열의 멤버 항목에 접근하기 위한 단축키임. - http://minsone.github.io/mac/ios/swift-subscripts-summary
  ```

  ```swift
  enum Setting {
    case text(String)
    case int(Int)
    case bool(Bool)
  }
  let defaultSettings: [String: Setting] = [
    "Airplane Mode":.bool(true),
    "Name":.text("My iPhone"),
  ]

  defaultSettings["Name"]		//subscripting
  ```

- **Dictionary lookup은 항상 optional을 반환**

  - **키가 존재하지 않으면 nil을 리턴**.
  - **subscripting을 사용하여 값을 가져 올 때 범위를 벗어나면 crash를 일으키는 배열과는 차이가 있음.**
    - 이 이유는 Array의 index와, Dictionary의 key가 다르게 사용되기 때문

- **for-in 사용하면 tuple로 지정되어 넘어옴**

  ```swift
  let friends = ["Jay":35, "Joe":29,"Jenny":31]

  for tuple in friends {
    print(tuple)	
  }
  //("Joe", 29)
  //("Jay", 35)
  //("Jenny",31)

  for (key, value) in friends {	//key, value에 각각 접근도 가능
    print("\(key) : \(value)")
  }
  ```

  ​

## **Mutation**

- Dictionary도 Array와 같이 struct이므로 let을 이용하여 정의하면 non-mutating
- **Dictionary에서 value remove 방법**
  - subscripting을 사용하여 nil로 설정(dictionary[key] = nil )
  - **removeValue (forKey :)**
    - **키가 존재하지 않으면 nil, 존재하면 삭제된 값 return**
- immutable dictionary의 값 변경을 하려면 copy 해야함(**immutable dictionary는 실제로 변경 X**)

```swift
let defaultSettings: [String: Setting] = [
  "Airplane Mode":.bool(true),
  "Name":.text("My iPhone"),
]
var localizedSettings = defaultSettings
localizedSettings["Name"] = .text("Main iPhone")
localizedSettings["Do Not Disturb"] = .bool(true)	
```

- **Dictionary에서 value update 방법**

  - subscripting을 통해 value를 update
  - **updateValue (_ : forKey :)**
    - 값이 있는 경우 이전 값 반환, 없으면 nil

  ```swift
  let oldName = localizedSettings.updateValue(.text("Il mio iPhone"), forKey: "Name")
  localizedSettings["Name"]	//Optional(Setting.text("Il mio iPhone"))
  oldName // Optional(Setting.text("Mein iPhone"))
  ```




## **Some Useful Dictionary Extensions**

### **Merge**

위 코드에서 설정한 defaultSettings라는 dictionary와 사용자가 변경한 customSettings라는 dictionary를 merge하려 한다. - 두 dictionary에는 중복되는 키가 존재하는 상황.

이를 extension으로 만든다( 기본 라이브러리에서 지원하지 않으므로 )
argument에 대한 필요사항은, 반복할 수 있는 sequence여야 하며 sequence의 요소는 대상 dictionary와 동일한 유형의 key - value pair여야 한다.
Iterator.Elemtens가 (Key, Value)인 쌍인 모든 sequence는 이러한 요규사항을 충족하므로, 메서드의 generic 제약조건을 표현해야 한다. (Key 및 Value는 확장 할 Dictionary 유형의 제네릭 형식 매개 변수).)

```swift
extension Dictionary {
  mutating func merge<S>(_ other: S)
  	where S: Sequence, S.Iterator.Element == (key: Key, value: Value){	//where절이 generic 제약조건
      for(k,v) in other {
        self[k] = v
      }
  	}
}
```

다음 예제처럼, 한 dictionary를 다른 dictionary와  merge할 수 있지만, method의 인자는 key-value 또는 다른 sequence의 array(key, value를 인자로 갖는 배열)일 수 있다. 

```swift
var settings = defaultsSettings	//["Airplane Mode":.bool(true), "Name":.text("My iPhone")]
let overridenSettings: [String: Setting] = ["Name": .text("Jane\'s iPhone")]
settings.merge(overridenSettings)
settings
//["Name":Setting.text("Jane's iPhone"), "Airplane Mode":Setting.bool(true)]
```

- (Key, Value)쌍의 sequence로부터 dictionary를 생성할 수도 있다.
  (**표준 라이브러리는 매우 자주 발생하는 배열에 대해 유사한 초기화 프로그램을 제공**)
  범위 (Array (1 ... 10))에서 배열을 만들거나 ArraySlice를 적절한 배열 (Array (someSlice))로 다시 변환 할 때마다 이 메서드를 사용합니다. 그러나 **Dictionary 용 초기화 프로그램은 없다** 

```swift
extension Dictionary {
  init<S: Sequence>(_ sequence: S)
  	where S.Iterator.Element == (key: Key, value: Value){
      self = [:]
      self.merge(sequence)
  	}
}

//All alarms are turned off by default
let defaultAlarms = (1..<5).map { (key: "Alarm \($0)", value: false)}	
//[("Alarm 1", false),("Alarm 2", false),("Alarm 3", false),("Alarm 4", false),("Alarm 5", false)]
let alarmDictionary = Dictionary(defaultAlarms)
//["Alarm 1": false, "Alarm 4": false, "Alarm 2": false, "Alarm 3": false]
```



### **Map**

- Dictionary는 Sequence이기 때문에 이미 **배열을 생성하는 map 메서드를 가지고 있다.** 

  - map을 이용하면 tuple형식으로 반환됨
  - Dictionary의 구조( [ key: value ] )를 손상시키지 않고 값을 변환하기를 원하는 경우 사용할 때는 extension을 이용.

- mapValues 메소드는 우선 standard map을 호출하여 (키, 변환 된 값) 쌍의 배열을 만든 다음 위에서 정의한 새 이니셜 라이저를 사용하여 사전으로 되돌린다.

  ```swift
  extension Dictionary {
    func mapValues<NewValue>(transform: (Value) -> NewValue) -> [Key:NewValue] {
      return Dictionary<Key, NewValue>( map { (key, value) in 	//standard map호출하여 배열 생성. (key, value)의 배열을 argument로 갖는 init 필요함
      	return (key, transform(value))
      })
    }
  }
  //settings = ["Airplane Mode":.bool(true), "Name":.text("My iPhone")]
  let settingsAsStrings = settings.mapValues { setting -> String in
  	switch setting {
        case .text(let text): return text
        case .int(let number): return String(number)
        case .bool(let value): return String(value)	//enum의 associated value
  	}
  }
  settingsAsSettings
  //["Name":"Jane's iPhone", "Airplane Mode":"true"]
  ```

  ​

## **Hashable Requirement**

- Dictionary는 HashTable.

  - HashTable : 레코드를 한 개 이상 보관하는 버킷들의 집합. 표와 같다고 생각하면 됨

    - 데이터가 저장되는 버킷들의 배열로 만들어 짐.
    - 한 버킷은 하나 이상의 레코드를 수용 가능.(column : 슬롯 /  row : 버킷)

    |      | 슬롯0  | 슬롯1  |
    | ---- | ---- | ---- |
    | 버킷0  |      |      |
    | 버킷1  |      |      |
    | 버킷2  |      |      |

  - Dictionary는 key의 hashValue를 기반으로 기본 저장소 배열의 위치 할당

  - 이 때문에 Dictionary의 key 타입이 Hashable protocol을 따르는 이유

    - 문자열, 정수, 부동 소수점, bool 등 모든 기본 데이터 형식.
    - associated value가 없는 enum

  - 사용자 정의 유형을 dictionary의 key로 사용하려면 Hashable 적합성을 수동으로 추가해야 함

    - Hashable protocol을 이용해 커스텀 구조 및 고유 값을 만들 수 있다.

      ```swift
      struct Point {
      	let x: Int
      	let y: Int
      }

      extension Point: Hashable {
      	var hashValue: Int {	
      		return x.hashValue ^ y.hashValue	//배타적 논리 합. 둘 중 하나만 true이면 true (여기선 0, 1)
      	}
      }

      func == (lhs: Point, rhs: Point) -> Bool {
      	return lhs.x == rhs.x && lhs.y == rhs.y
      }
      ```






      //이렇게 생김
      public protocol Hashable : Equatable {
          public var hashValue: Int { get }
      }
      public protocol Equatable {
          public static func ==(lhs: Self, rhs: Self) -> Bool
      }
    
      ```
    
    - Hashable 프로토콜을 사용하면 hashValue 계산 속성과 == 연산자를 선언해야 함.
    
      - Hashable이 Equatable을 extends 하므로, == 연산자의 overload 발생.
      - 두 인스턴스가 동일하면 동일한 hashValue 가져야 함 / 반대의 경우는 항상 참은 아님
        - A - 1 , B - 2, C - 3, D - 4 ….이런 값으로 저장된다 가정.
        - ABC 를 해싱한다면, 123(이어쓰기) or 6(합)이 될 수 있음.
          - 이렇게 되는 과정에서 반대의 경우는 항상 참이 아니게 될 수 있음
    
    ​```swift
    //이제 Point 배열에서 고유 값들만 추출 가능
    extension Array where Element : Hashable {
    	var unique: [Element] {
    		return Array(Set(self))
    	}
    }
    
    let uniqueList = [Point(x: 1, y: 1), ... ].unique
    ​```

-   중복된 hashValue의 가능성이 존재하므로, 이는 Dictionary가 충돌을 처리할 수 있어야 한다는 것을 의미

    - 최대한 적은 수의 충돌횟수를 추구해야 함.
    - **모든 hashValue가 같은 경우(최악의 상황), dictionary의 lookup 성능이 O(n)으로 저하됨**

    ​

-   Hashable 자체인 기본 데이터 유형으로 구성된 type의 경우, hashValue를 XOR 하면 좋은 시작이 될 수 있음.

    - XOR(배타적 논리 합): 2개의 명제 가운데 1개만 참일 경우를 판단하는 논리연산

```swift
  struct Person {
    var name: String
    var zipCode: Int
    var birthday: Date
  }
  extension Person: Equatable {
    static func == (lhs: Person, rhs: Person) -> Bool {
      return lhs.name == rhs.name 
      	&& lhs.zipCode == rhs.zipCode 
      	&& lhs.birthday == rhs.birthday
    }
  }

  extension Person: Hashable {
    var hashValue: Int {
      return name.hashValue ^ zipCode.hashValue ^ birthday.hashValue
    }
  }
```

- 이 기법의 한계 중 하나는 XOR이 해시되는 데이터의 특성에 따라 대칭 (대칭) (즉, a ^ b == b ^ a)하여 충돌을 필요 이상으로 높일 수 있다는 것. 
- 이것을 피하기 위해 비트로테이션을 믹스에 추가 할 수 있음. ???????????????????????????????????


- value type (예 : 변경 가능한 객체)이 아닌 유형을 dictionary의 key로 사용할 때는 주의가 요구됨. 
  - 해시 값 등을 변경하는 방식을 dictionary key로 사용한 후에 개체를 변형하면 사전에서 다시 찾을 수 없다. - key값도 변경되니까???????



# **Sets**

- element의 **순서가 없는** collection

- 각 element는 **중복 없이** 한 번만 나타남

- key 만 저장하는 dictionary와 같음

- Dictionary처럼 HashTable로 구성.

  - **연산 시간은 O(1) - (dictionary와 같음)**
  - set의 **element**도 dictionary의 key처럼 **Hashable**

- **순서가 중요치 않은 경우, 중복데이터가 없음을 보장해야 하는 경우 사용**

- ExpressibleByArrayLiteral Protocol을 따르므로 Array Literal로 초기화 가능

  ```swift
  let naturals: Set = [1,2,3,2]
  naturals	//[2,3,1]
  naturals.contains(3)	//true
  naturals.contains(0)	//false

  var numbers = [1,2,3] // 타입 추론시에는 Int 배열로 인식함
  ```

- 다른 collection처럼 common operation을 지원.

  - **for loop, map, filter, sorts 등**

- 새로운 요소 추가시에는 **insert(_:)**

- 요소 삭제시에는 **remove(_:)**

- 정렬시에는 **sorted()**




## **Set Algebra(Set 대수학???)**

- 수학적인 모든 공통 집합 연산을 지원

  - 차집합

    ```swift
    //차집합
    let iPods: Set = ["iPod touch", "iPod nano", "iPod mini", "iPod shuffle", "iPod Classic"]
    let discountinuedIPods: Set = ["iPod mini", "iPod Classic"]
    let currentIPods = iPods.substracting(discountinuedIPods)	//iPods - discountinuedIPods
    //["iPod shuffle", "iPod nano", "iPod touch"]
    ```

  - 교집합(intersection)

    ```swift
    //교집합
    let touchscreen: Set = ["iPhone","iPad","iPod touch","iPod nano"]
    let iPodsWithTouch = iPods.intersection(touchscreen)
    //["iPod touch","iPod nano"]
    ```

  - 합집합(중복 element 제거)

    ```swift
    var discountinued: Set = ["iBook", "Powerbook","Power Mac"]
    discountinued.formUnion(discountinuedIPods)	//void
    //union은 새로운 set return
    discountinued	//["iBook","iPod mini", "Powerbook","Power Mac","iPod Classic"]
    ```

  - 여집합의 합(배타적 논리합)

    ```swift
    //여집합의 합(배타적 논리합) : {"명재3", "명재2", "명재4", "명재5"}
    let englishClassStudent : Set<String> = ["명재","명재2","명재3"]
    let koreanClassStudent : Set<String> = ["명재","명재4","명재5"]
    let unionSet = englishClassStudent.union(koreanClassStudent)
    ```

    ```swift
    let englishClassStudent : Set<String> = ["명재","명재2","명재3"]
    let koreanClassStudent : Set<String> = ["명재","명재4","명재5"]

    englishClassStudent.isDisjoint(with: koreanClassStudent)    //서로 배타적인가? false
    englishClassStudent.isSubset(of: koreanClassStudent)    //eng이 kor의 부분집합인가? false
    englishClassStudent.isSuperset(of: koreanClassStudent)  //eng은 kor의 전체집합인가? false
    ```

    ​

  - 거의 모든 Set의 연산은 non-mutating과 mutating 형식을 모두 갖는다.

    - mutating은 form prefix. (원래의 set을 변형함)

  ​

  ​

## **IndexSet and CharacterSet**

- Set는 표준 라이브러리에서 SetAlgebra를 준수하는 유일한 type이지만 SetAlgebra 프로토콜은 Foundation의 두 가지 유형인 IndexSet 및 CharacterSet에서도 adopted 된다.

- **IndexSet는 양의 정수 값 집합**을 나타낸다.

  - Set<Int>를 사용하여이 작업을 수행 할 수 있지만 **IndexSet**은 내부적으로 범위 목록을 사용하기 때문에 **더 효율적인 저장 방법**
    -  1,000 개의 element가있는 tableView가 있고 사용자가 선택한 행의 index를 관리하기 위해 Set을 사용하고자한다고 가정해보자. Set <Int>는 선택된 행 수에 따라 최대 1000 개의 요소를 저장해야 한다. 반면에 **IndexSet은 연속 범위를 저장**하므로 테이블의 처음 500 행을 선택하면 **두 개의 정수만 저장**됩니다 (섹션의 **상한 및 하한**).
  - IndexSet은 RangeView 속성을 통해 뷰를 노출합니다.이 뷰 자체는 컬렉션.
    - IndexSet은 범위를 저장하므로, 각 범위가 떨어져 있을 수 있음. 그럼 그 떨어져있는 부분을 RangeView라는 개념으로 각각 저장하여 관리

  ```swift
  //인덱스 집합에 몇 가지 범위를 추가 한 다음 개별 멤버 인 것처럼 인덱스를 매핑 할 수 있습니다.

  import Foundation

  var indices = IndexSet()
  indices.insert(integersIn: 1..<5)
  indices.insert(integersIn: 11..<15)
  let evenIndices = indices.filter { $0 % 2 == 0 }	//[2,4,12,14]
  ```

- CharacterSet은 유니 코드 문자 집합을 저장하는 효율적인 방법. 

  - 특정 문자열에 문자와 숫자 또는 십진숫자와 같은 특정 문자 하위 집합의 문자만 포함되어 있는지 확인하는 데 자주 사용.
  - IndexSet과 달리 characterSet은 콜렉션이 아님.




## **Using Sets Inside Closures**

- Dictionary과 Set는 호출자에게 노출되지 않을 때도 함수 내부에서 사용할 수있는 매우 유용한 데이터 구조가 될 수 있습니다.??????????????????????????????????????????????

  - Sequence의 extension을 작성하여 Sequence의 모든 고유 요소를 검색하고자 한다면, 요소를 set에 쉽게 넣고 그 내용을 반환 할 수 있습니다. 그러나 안정적이지는 않습니다. 세트에 정의 된 순서가 없기 때문에 입력 요소가 결과에서 재정렬됩니다 (-> 즉 기존의 순서와는 다르게 결과가 나올 수 있다는 말). 이 문제를 해결하기 위해 내부 설정을 사용하여 순서를 유지 관리하는 확장 프로그램을 작성할 수 있습니다.

    ```swift
    extension Sequence where Iterator.Elemtns: Hashable {
      func unique() -> [Iterator.Element] {
        var seen: Set<Iterator.Element> = []
        return filter {
          if seen.contains($0) {	//set에서의 contain은 O(1) , array로 한다면 O(n)
            return false
          }else{
            seen.insert($0)
            return true
          }
        }
      }
    }
    [1,2,3,12,1,3,4,5,6,4,6].unique()
    //[1,2,3,12,4,5,6]
    ```

    - 위의 방법을 사용하면 원래 순서 (요소가 Hashable이어야한다는 제약 조건)를 유지하면서 시퀀스의 모든 고유 요소를 찾을 수 있습니다. filter를 통과 한 클로저 내부에서, 우리는 클로저 외부에서 정의 된 변수(seen)를 참조하여 클로저의 여러 반복에 대해 상태를 유지합니다.


# **Ranges**

- 값의 하한값과 상한값으로 정의되는 값의 간격

- 두 개의 범위 연산자를 사용하여 범위를 만듭니다

  - ..<   :  상한을 포함하지 않는 경우 
  - …    :  두 범위를 모두 포함하는 경우

  ```swift
  let singleDigitNumbers = 0..<10
  let lowercaseLetters = Character("a")...Character("z")
  ```

- Sequence 또는 Collection인것 처럼 보이지만 **둘 다 아닙니다**.

- 라이브러리에는 **네 가지 범위 유형**이 있습니다. 다음과 같이 2x2 행렬로 분류 할 수 있습니다.

  |                                          | Half-open range | Closed range         |
  | ---------------------------------------- | --------------- | -------------------- |
  | Elements are Comparable                  | Range           | ClosedRange          |
  | Elements are Strideable(with integer steps) | CountableRange  | CountableClosedRange |

  표의 column(Half-open range , Closed range)은 위에서 본 두 범위 연산자에 해당하며 [Countable]Range (반 열림) 또는 [Countable]CosedRange (닫힘)를 각각 만듭니다.

  - **Half-open range**는 **empty interval** 을 만들 수 있습니다. (lower와 upper가 같은경우)
  - **Closed range**는 **최대 값을 포함** 할 수 있습니다. Half-open range에는 항상 범위에서 가장 높은 값보다 더 큰 표현 가능한 값이 하나 이상 필요합니다.

- elements are comparable : 비교값이 같은 type이어야 함 type…type????????????????

- strideable : range 사용할 수 없는 date같은 것을 range가능하게 사용 할 수 있을 듯????????????

- **카운트 가능한 범위의 유효 범위**는 정수 및 포인터 유형을 포함하지만 **부동 소수점 유형은 포함하지 않습니다**. 유형의 Stride에 대한 정수 제약 때문입니다. 연속적인 부동 소수점 값을 반복 할 필요가 있다면 **stride (from : to :)**와 **stride (from : through : by)** 함수를 사용하여 그러한 시퀀스를 생성 할 수 있습니다.

  - 일부 범위에서는 반복 할 수 있지만 다른 범위에서는 반복 할 수 없습니다. 예를 들어 위에서 정의한 Character 값의 범위는 시퀀스가 아니므로이 방법은 작동하지 않습니다.

    ```swift
    let lowercaseLetters = Character("a")...Character("z")
    for char in lowercaseLetters{
      
    }
    //Error:  Type 'ClosedRange<Character>' does not conform to protocol 'Sequence'
    ```

  - 정수 범위는 집계 가능한 범위이므로 컬렉션입니다.

    ```swift
    let singleDigitNumbers = 0..<10
    singleDigitNumbers.map{ $0 * $0 }
    //[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
    ```

  - 표준 라이브러리에는 현재 Countable Range, CountableRange 및 CountableClosedRange에 대해 별도의 유형이 있어야합니다. 이상적으로, 이들은 뚜렷한 유형이 아니라 포괄적 인 매개 변수가 필수 제약 조건을 충족시키는 조건에서 컬렉션 준수를 추가하는 Range 및 ClosedRange의 확장입니다.

