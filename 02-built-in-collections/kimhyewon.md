# Built - In Collections

## Arrays

#### Arrays and Mutability

-  같은 type의 elements를 가지는 container

-  have value semantic

  ​

- let

  - let 으로 선언된 array를 수정할경우 compile error 가 발생한다.

  - let fibs = [0, 1, 1, 2, 3, 5] : let 으로 선언되어 append 함수 사용 불가, will never change

  - reference will never change


    ​

- var

  - array 를 변화할 수 있게 하기 위해선 var 로 선언

  - ```swift
    var mutableFibs = [0, 1, 1, 2, 3, 5]
    Now we can easily append a single element or a sequence of elements:
    mutableFibs.append(8) mutableFibs.append(contentsOf: [13, 21]) mutableFibs // [0, 1, 1, 2, 3, 5, 8, 13, 21]
    ```



- 존재하는 array 를 할당할 경우 값을 복사한다.

  -  NSArray has no mutating methods 

  - ```swift
    var x = [1, 2, 3]
    var y = x // copy of x
    y.append(4)
    y // [1, 2, 3, 4] 
    x // [1, 2, 3]
    ```

  - 함수로 array를 전달할때도 마찬가지로 값이 복사된다.

    ​

- ​

  - to mutate an array, you need an NSMutableArray

  - ```swift
    let a = NSMutableArray(array: [1,2,3]) // I don't want to be able to mutate b
    let b: NSArray = a
    // But it can still be mutated - via a
    a.insert(4, at: 3) 
    b // ( 1, 2, 3, 4 )
    ```

  - 만약 I don't want to be able to mutate b 

  - ```swift
    let c = NSMutableArray(array: [1,2,3])
    let d = c.copy() as! NSArray 
    c.insert(4, at: 3)
    d // ( 1, 2, 3 )
    ```



-  In Swift, instead of needing two types, there’s just one, and mutability is defined by declaring with var instead of let. But there’s no reference sharing — when you declare asecond array with let, you’re guaranteed it’ll never change.


  ​			

#### Arrays and Optionals

-  array는 subscripting 을 통해 index로 element에 직접 접근하는것이 가능하다.

- subscripting를 통해 요소를 가져 오기 전에 색인이 범위 내에 있는지 확인해야한다.

  ​

- array 반복

  - ```swift
    let array = [1,2,3,4,5,6,7,8,9]
    for x in array {
      print(x) //1,2,3,4,5,6,7,8,9
    }			
    ```

-   첫번째 element를 제외한 array 반복

  - ```swift
    let array = [1,2,3,4,5,6,7,8,9]
    for x in array.dropFirst() {
      print(x) //2,3,4,5,6,7,8,9
    }
    ```

-  뒤의 5개 element를 제외한 array 반복

  - ```swift
    let array = [1,2,3,4,5,6,7,8,9]
    for x in array.dropLast(5) {
        print(x) //1,2,3,4
    }
    ```

- index 와 함께 array 반복 

  - ```swift
    for (n, element) in array.enumerated() {
        print("n:\(n) element:\(element)")
    }
    //n:0 element:1 n:1 element:2 n:2 element:3 n:3 element:4 n:4 element:5 n:5 element:6 n:6 element:7 n:7 element:8 n:8 element:9
    ```

- Want to find the location of a specific element

  - ```swift
    if let idx = array.index { someMatchingLogic($0) }
    ```

  - Returns the position immediately after the given index.

  - ```swift
        /// - Parameter i: A valid index of the collection. `i` must be less than
        ///   `endIndex`.
        /// - Returns: The index value immediately after `i`.
        public func index(after i: Int) -> Int
    ```

  - Returns the position immediately before the given index.

  - ```swift
        /// - Parameter i: A valid index of the collection. `i` must be greater than
        ///   `startIndex`.
        /// - Returns: The index value immediately before `i`.
        public func index(before i: Int) -> Int
    ```

  - Returns the first index where the specified value appears in the collection.

  - ```swift
        /// After using `index(of:)` to find the position of a particular element in
        /// a collection, you can use it to access the element by subscripting. This
        /// example shows how you can modify one of the names in an array of
        /// students.
        ///
        ///     var students = ["Ben", "Ivy", "Jordell", "Maxime"]
        ///     if let i = students.index(of: "Maxime") {
        ///         students[i] = "Max"
        ///     }
        ///     print(students)
        ///     // Prints "["Ben", "Ivy", "Jordell", "Max"]"
        ///
        /// - Parameter element: An element to search for in the collection.
        /// - Returns: The first index where `element` is found. If `element` is not
        ///   found in the collection, returns `nil`.
        ///
        /// - SeeAlso: `index(where:)`
        public func index(of element: Element) -> Int?
    ```

  -  Returns an index that is the specified distance from the given index.

    ```swift
    public func index(_ i: Int, offsetBy n: Int) -> Int

    let numbers = [10, 20, 30, 40, 50]
    let i = numbers.index(numbers.startIndex, offsetBy: 4)
    print(numbers[i]) // Prints "50"

        /// - Parameters:
        ///   - i: A valid index of the array.
        ///   - n: The distance to offset `i`.
        /// - Returns: An index offset by `n` from the index `i`. If `n` is positive,
        ///   this is the same value as the result of `n` calls to `index(after:)`.
        ///   If `n` is negative, this is the same value as the result of `-n` calls
        ///   to `index(before:)`.
        ///
        /// - Complexity: O(1)

    ```

  - Returns an index that is the specified distance from the given index, unless that distance is beyond a given limiting index.

    ```swift
    public func index(_ i: Int, offsetBy n: Int, limitedBy limit: Int) -> Int?
       
    let numbers = [10, 20, 30, 40, 50]
    let i = numbers.index(numbers.startIndex, offsetBy: 4, limitedBy: numbers.endIndex)
    print(numbers[i!]) // Prints "50"
       
    let j = numbers.index(numbers.startIndex,offsetBy: 10, limitedBy: numbers.endIndex)
    print(j) //nil
        
        /// - Parameters:
        ///   - i: A valid index of the array.
        ///   - n: The distance to offset `i`.
        ///   - limit: A valid index of the collection to use as a limit. If `n > 0`,
        ///     `limit` has no effect if it is less than `i`. Likewise, if `n < 0`,
        ///     `limit` has no effect if it is greater than `i`.
        /// - Returns: An index offset by `n` from the index `i`, unless that index
        ///   would be beyond `limit` in the direction of movement. In that case,
        ///   the method returns `nil`.
        ///
        /// - SeeAlso: `index(_:offsetBy:)`, `formIndex(_:offsetBy:limitedBy:)`
        /// - Complexity: O(1)
    ```

- array의 모든값을 변화시켜 array를 만들 때

  - ```swift
    array.map { someTransformation($0) }
    ```

- 특별한 조건에 맞는 element만을 원할 때

  - ```swift
    array.filter { someCriteria($0) }
    ```



#### Transforming Arrays

##### Map

-  transform every value in an array
- a function is going to be applied to every element, returning a new array of the transformed elements. -> 모든 element 값를 변화시켜 새로운 array를 만든다

```swift
let square = array.map { n in n * n }

let milesToPoint = ["p1":12.0, "p2":50.9, "p3":70.0]
let kmToPoint = milesToPoint.map { name, miles in miles * 1.6 }
kmToPoint
```



- 장점

1. shorter
2. 에러가 생길 여지가 적다
3. 명확하다


- 맵은 호출마다 달라지지 않는 상용구를 항상 변화하는 기능, 즉 정확히 어떻게 각 요소를 변환 할 것인지에 대한 논리로부터 분리합니다. 이 기능은 호출자가 제공하는 매개 변수인 변환 함수를 통해 수행됩니다.

##### parameterizing Behavior with Functions

- 이 매개 변수화 동작 패턴은 표준 라이브러리에서 찾을 수 있습니다. 호출자가 주요 단계를 사용자 정의 할 수 있도록 클로저를 사용하는 12 가지 이상의 개별 함수가 있다.

  - map and flatMap - 어떻게 element를 변환 시킬 것인가

  - filter - element가 포함되어야 하는가

  - reduce - 어떻게 element를 집계값으로 만들 수 있는가 

  - sequence - 시퀀스의 다음element는 무엇인가

  - forEach - 수행하면서 각 element에 어떤 영향이 있는가

  - sort, lexicographicallyPrecedes, and partition - 두 가지 element가 어느 순서로 오게 되는가

    - sort : 오름차순 정렬

    - 나머지 : 내림차순 정렬

      ```swift
      class People {
          var name: String
          var age: Int
          
          init (name: String, age: Int) {
              self.name = name
              self.age = age
          }
      }

      var peoples = [People]()
      var people = People(name: "hye", age: 23)
      peoples.append(people)
      people = People(name: "ryan", age: 26)
      peoples.append(people)
      people = People(name: "jw", age: 18)
      peoples.append(people)

      peoples.sort { $0.age < $1.age }
      for i in peoples {
          print(i.age) //18  23  26
      }

      peoples.sort { $0.name.uppercased() < $1.name.uppercased() }
      for i in peoples {
          print(i.name) //hye jw ryan
      }
      ```

  - index, first, contains - 이 element가 일치 하는가

    -  contains : can take a value to check for, so long as the elements are equatable

    - ```swift
      peoples.contains { $0.age > 25 } //false true
      peoples.contains { $0.age > 20 } //true
      ```

  - min and max - 두 element중 어느것이 min/max 인가

  - elementsEqual and starts - 두 element가 동일한가

  - split - 이 element가 seporator인가

  ​

- 비슷한 기능을하는 다른 기능들도 있지만 행동을 지정하기 위해 클로저를 사용하지만 표준 라이브러리에는 없습니다. 자신을 쉽게 정의 할 수 있습니다.

  - accumulate - 요소를 실행 값의 배열로 결합 (reduce와 같지만 각 임시 조합의 배열 반환) 요소를 실행 값의 배열로 결합 (reduce와 같지만 각 임시 조합의 배열 반환)
  - all(matching:) - 시퀀스의 모든 요소가 기준과 일치하는지 테스트합니다 (contains 포함하여 빌드 할 수 있으며 일부는 신중하게 배치됩니다)
  - count(where:)  
  - indices(where:) - 기준 (similarto index (where :))과 일치하는 인덱스 목록을 반환하지만 첫 번째 인덱스에서는 멈추지 않습니다.
  - prefix(while:) - true를 반환하는 동안 필터 element를 필터링 한 다음 나머지를 삭제합니다 (필터와 유사하지만 초기 이탈이 있으며 무한 또는 지연 계산 시퀀스에 유용함)
  - drop(while:) - 술어가 true가 될 때까지 요소를 놓은 다음 나머지 부분을 반환합니다 (pre x (while :)와 비슷하지만 반비례를 반환 함)

##### 

##### Mutation and Stateful Closures

배열을 반복 할 때 map을 사용하여 부작용을 수행 할 수 있습니다 (예 : 요소를 조회 테이블에 삽입). 우리는 이것을 권장하지 않습니다. 다음을 살펴보십시오.

```swift
array.map { item in table.insert(item) }
```

배열의 변형처럼 보이는 구조에서 부작용 (조회 테이블의 변이)을 숨 깁니다. 위와 같은 것을 본다면 map과 같은 함수 대신에 for 루프를 사용하는 것이 명백합니다. forEach 메소드는이 경우 맵보다 더 적합하지만 자체 문제점이 있습니다. 우리는 각각에 대해 살펴볼 것입니다.

부작용을 수행하는 것은 의도적으로 클로저 로컬 상태를 제공하는 것과는 다른데, 이는 특히 유용한 기술이며 클로저를 만드는 것입니다. 즉, 범위를 벗어나는 변수를 캡처하고 변형 할 수있는 함수입니다. 따라서 고차 함수와 결합 할 때 강력한 도구입니다. 예를 들어 위에서 설명한 누적 함수는 다음과 같이 map과 stateful closure로 구현 될 수 있습니다.

```swift
extension Array {
func accumulate<Result>(_ initialResult: Result,
_ nextPartialResult: (Result, Element) -> Result) -> [Result] {
var running = initialResult return map { next in
running = nextPartialResult(running, next)
return running }
} }
```

이렇게하면 실행중인 값을 저장하는 임시 변수를 만든 다음 map을 사용하여 진행중인 값의 배열을 만듭니다.

```swift
[1,2,3,4].accumulate(0, +) // [1, 3, 6, 10]
```

이 코드는 map이 시퀀스를 따라 순차적으로 변환을 수행한다고 가정합니다. 위의 map의 경우에는 그렇지 않습니다. 그러나 시퀀스를 순서가 바뀔 수있는 구현이있을 수 있습니다 (예 : 요소의 동시 변환을 수행하는 경우). map의 공식 표준 라이브러리 버전은 안전 베팅처럼 보이지만 순서를 순서대로 변환하는지 여부를 지정하지 않습니다.



##### Filter

- 매우 일반적인 작업은 배열을 가져 와서 특정 조건과 일치하는 요소 만 포함하는 새로운 배열을 만드는 것이다

```swift
let filter = array.filter { n in n > 3 }
```



- 매우 짧은 closure의 경우이 값을보다 쉽게 읽을 수 있다.
- 클로져가 더 복잡하다면, 이전에했던 것처럼 인수를 명시 적으로 명시하는 것이 더 좋다
- 클로저가 한 줄에 깔끔하게 들어 맞으면 약식 인수 이름이 적합

```swift
nums.filter { num in num % 2 == 0 } // [2, 4, 6, 8, 10]
(1..<10).filter { $0 % 2 == 0 } // [2, 4, 6, 8, 10]
```



- map과 fiter를 결합함으로써, 우리는 하나의 중간 배열을 도입 할 필요없이 배열에 많은 연산을 쉽게 작성할 수 있으며, 결과 코드는 더 짧아지고 읽기 쉽습니다. 예를 들어, 100 이하의 모든 정사각형을 찾기 위해 0 .. <10 범위를 정사각형으로 매핑 한 다음 모든 홀수를 필터링 할 수 있다.

```swift
(1..<10).map { $0 * $0 }.filter { $0 % 2 == 0 } // [4, 16, 36, 64]
```



- ```swift
  bigArray.contains { someCondition }
  ```

  -  요소를 계산하기 위해 필터링 된 요소의 전체 배열을 작성하지 않고 첫 번째 요소와 일치하는 즉시 조기에 종료
  - 일반적으로 모든 결과를 원할 경우에만 필터를 사용하십시오.

  ​

##### Reduce

- combine all elements into a new value

- ```swift
  var total = 0
  for num in  bs { total = total + num
  }
  total // 12
  ```

- ```swift
  let sum =  bs.reduce(0) { total, num in total + num } // 12
  ```

- ```swift
  bs.reduce(0, +) // 12
  ```

- ```swift
  let total1 = array.reduce(0) { i, sum in sum + i }
  total1 //21
  let total2 = array.reduce(0, +)
  total2 //21
  let total3 = nestedItems.map { $0.reduce(0, +) } //21

  let word = ["abc", "def", "ghi"]
  let text = word.reduce("", +)
  text //"abcdefghi"
  let text2 = word.reduce("total:") { total, word in "\(total), \(word)" }
  text2 //"total:, abc, def, ghi"
  ```

- 여기서 일어나는 일은 언제든지 combine 함수를 통해 변형되거나 포함 된 요소를 이전 요소에 추가하여 새로운 배열을 만드는 것입니다. 이것은 두 가지 구현이 모두 O (n)가 아닌 O (n2)임을 의미



##### A Flattening Map

```swift
let items: [Int?] = [1, 2, nil, 4, 5, nil, 8]

let mapItems = items.map { $0 }
mapItems //[{some 1},{some 2},nil,{some 4},{some 5},nil,{some 8}]
let flatMapItems = items.flatMap { $0 }
flatMapItems //[1,2,4,5,8]
```

```swift
let nestedItems = [ [1, 2, 3], [4, 5, 6] ]
nestedItems //[[1,2,3],[4,5,6]]
let flattenNested = nestedItems.flatMap { $0 }
flattenNested //[1,2,3,4,5,6]
let onlyEven = nestedItems.flatMap { intArray in intArray.filter { $0 % 2 == 0 } }
onlyEven // [2,4,6]
```



##### Iteration using forEach

- 이는 for 루프와 거의 동일하게 작동
- 전달 된 함수는 시퀀스의 각 요소에 대해 한 번 실행
- map와 달리 forEach는 아무 것도 반환하지 않습니다



- ```swift
  for e in [1,2,3] {
      print(e)
  }

  [1,2,3].forEach { e in print(e) }
  ```

  ​

- 클로저 표현식 대신 forEach에 함수 이름을 전달하면 명확하고 간결한 코드로 이어질 수 있습니다

  - ```swift
    Views.forEach (view.addSubview)
    ```



- for 루프와 forEach 사이에는 약간의 차이점

  - if a for loop has a return statement in it, rewriting it with forEach can significantly
    change the code’s behavior

  - ```swift
    extension Array where Element: Equatable { func index(of element: Element) -> Int? {
        for idx in self.indices where self[idx] == element { return idx
        }
        return nil
        }
    }

    extension Array where Element: Equatable {
        func index_foreach(of element: Element) -> Int? {
            self.indices.filter { idx in self[idx] == element
                }.forEach { idx in return idx
            }
            return nil
        } 
    }

    (1..<10).forEach { number in print(number) 
        if number > 2 { return }
    } //1 2 3 4 5 6 7 8 9 
    ```

    - forEach 클로저 내부의 리턴은 외부 함수를 벗어나지 않습니다. 클로저 자체에서만 반환됩니다.

      ​

#### Array Types

##### slices



##### bridging


