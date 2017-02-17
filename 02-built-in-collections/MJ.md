#Collection Type

## **Array**##

-    같은 타입의 값을 정렬된 형태로 저장

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
     ```
     ​

     ### **Array Iterate**

     ```swift
     for x in array {
       //모든 index iterate
     }


     for x in array.dropFirst() {
       //index == 0 제외한 iterate
     }


     for x in array.dropLast(5){
       //뒤에서 5개 제외한 iterate.
       //array의 lastindex보다 큰 값이 들어가는 경우 nothing happen
     }


     for (index, element) in array.enumerated() {
       //index와 element에 각각 접근 가능
     }


     //조건에 부합하는 val의 index 반환. 없으면 nil반환
     array.index({(val: T) in return 조건})
     //즉 특정한 element의 index 찾음



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




     //조건에 부합하는 element들만 가져옴
     array.filter({val: T} in return 조건)




     //오름차순 정리
     array.sort()

     array.sort { $0.age < $1.age }
     people.contains { $0.age < 18 }
     people.sort { $0.name.uppercased() < $1.name.uppercased() 



     //array가 otherArray보다 사전적으로 앞에 오는지 검사(return Bool)
     array.lexicographicallyPrecedes(otherArray)

     //컬렉션의 요소 순서를 재정렬하고 주어진 조건자를 요소 간 비교로 사용하여 피벗 인덱스를 반환합니다.
     var numbers = [50, 30, 60, 50, 80, 10, 40, 30]
     numbers.partition { a, b in a > b}	// 2
     numbers //[60, 80, 50, 50, 30, 10, 40, 30]


     //두 sequence가 같은 순서의 elements를 갖는지 검사(return Bool)
     array.elementsEqual(otherArray)
     //시퀀스의 초기 요소가 다른 시퀀스의 요소와 같은지 여부를 나타내는 부울 값을 반환합니다.
     array.starts(with: otherArray)

     [1,2,3,4,5].elementsEqual([1,2,3])	//false
     [1,2,3].elementsEqual([1,2,3,4,5])	//false
     [1,2,3,4,5].starts(with: [1,2,3])	//true
     [1,2,3].starts(with: [1,2,3,4,5])	//false


     //separator로 배열을 나눈다
     [1,2,3,4,5].split(separator: 3)		//[[1,2],[4,5]]


     [1,2,3,4,5].forEach({(val: Int) in 로직})	


     ** 이 모든 기능의 목표는 새로운 배열의 생성, 소스 데이터에 대한 for 루프 등과 같이 코드의 중요하지 않은 부분이 혼란스럽게되는 것을 없애는 것. **
     ```

-    **NSArray**는 immutable한 Obj-c의 class => **reference type**

     - mutable한 NSArray를 사용하려면 NSMutableArray 사용




### **Mutation And Stateful Closures ?????**



### **Filter**

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
  - 일반적으로 모든 결과를 원할 경우에만 필터를 사용.



### **Reduce**

- 컨테이너 내부의 콘텐츠를 하나로 합쳐주는 기능

- 초기 값 (이 경우 0)과 중간 값 (total)과 요소(num)를 결합하는 함수의 두 부분을 추상화함.

  ```swift
  let numbers = [1,2,3,4,5]
  numbers.reduce(0, {(result: Int, nextValue: Int) in result + nextValue})	//15

  //연산자도 함수이므로 
  numbers.reduce(0,+)	//15
  ```

- **출력 유형은 요소의 유형과 같을 필요 없다**

  ```swift
  numbers.reduce(""){ str, num in "\(str)" + "\(num)"}	//12345
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

### **Map**

- 배열, 딕셔너리, 세트, 옵셔널 등에서 사용 가능

  - Sequence, Collection 프로토콜을 따르는 타입과 옵셔널은 모두 map 사용 가능

- 기존 컨테이너의 값은 변경되지 않고 새로운 컨테이너가 생성되어 반환

- **기존 데이터를 변형하는데 많이 사용**

- 다중 스레드 환경일 때 대상 컨테이너의 값이 스레드에서 변경되는 시점에 다른 스레드에서도 동시에 변경되려고 할 때 예측하지 못한 결과가 발생하는 부작용 방지

  ​

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



  //Optional
  let optionalInt : Int? = 3
  let resultInt : Int? = optionalInt.map({(unwrappedInt: Int?) in
      guard let unWrappedInt = unwrappedInt else { return 0 }
          return unWrappedInt * 2
  })
  ```



### **FlatMap**

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



### **forEach**

- for와 거의 동일

- void

- for보다 더 적은 메모리 사용

- 속도는 for가 더 빠름

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
    if number > 2 { return }	//루프가 끝나지 않고 다시 돌아옴
  }
  ```



### **ArraySlices**

- 배열조각

  ```swift
  let slice = numbers[1..<numbers.endIndex]
  type(of: slice)	//ArraySlice<Int>
  ```

-  배열에 대한 뷰

- 배열 복사할 필요 없음

- 배열처럼 사용 가능(배열과 동일한 메서드 가짐)

- 배열로 변환 가능

  ```swift
  Array(slice)
  ```

  ​

### **Bridging**

- 스위프트 Array는 Objective-C에 연결할 수 있음 
- NSArray는 객체 만 저장할 수 있기 때문에 Swift 배열의 요소를 연결할 수 있도록 AnyObject로 변환 할 수 있어야 했다.
- 이로 인해 클래스 인스턴스와 Objective-C 클래스에 자동 브리징을 지원하는 소수의 값 유형 (예 : Int, Bool 및 String)에 대한 브리징이 제한되었다.
- 이 제한 사항은 Swift 3에서는 더 이상 존재하지 않는다. Objective-C id 유형은 AnyObject 대신 **Any**로 Swift로 가져 오므로 Swift 배열은 이제 NSArray에 연결할 수 있다.



