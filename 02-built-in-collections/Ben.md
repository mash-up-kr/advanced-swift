# Built-In Collections
 - Swift는 시퀀스와 컬렉션에 강조점을 둠
 - Swift Collection 모델은 다른 언어보다 확장성 면에서 뛰어남 (다시 복잡하긴 함)

 ## Arrays
 ### Arrays and Mutability
 - Array는 순서가 있는 elements(동일 타입)들의 컨테이너 이며, 각각의 element에 접근할 수 있음

```Swift
var mutableFibs = [0, 1, 1, 2, 3, 5]
mutableFibs.append(8)
mutableFibs.append(contentsOf: [13, 21])
```
 - 기존 배열을 다른 변수에 할당하면 배열 내용이 복사됨 (참조 x)
 - 함수에 배열을 전달할때도 마찬가지로 복사
```Swift
var x = [1, 2, 3]
var y = x
y.append(4)
// x: [1, 2, 3]
// y: [1, 2, 3, 4]
```
 - NSArray와는 같다고 생각하면 안됨(보통 NS Foundation에서 배열을 변경하려면 NSMutableArray를 사용해야함)
 - NSMutableArray를 NSArray에 할당할때 NSMutableArray에 새로운 값이 추가되면 할당한 NSArray에도 추가됨
```Swift
 let a = NSMutableArray(array: [1, 2, 3])

// b의 값을 변화시키기 싫음
let b: NSArray = a

// 하지만 a를 통해서 값이 변함
a.insert(4, at: 3)
// b: [1, 2, 3, 4]
```
 - 위 문제를 해결하기 위해서 복사본을 만들어서 해결할 수 있음
```Swift
let c = NSMutableArray(array: [1, 2, 3])
let d = c.copy() as! NSArray

c.insert(4, at: 3)
// d: [1, 2, 3]
```
 - Swift에서는 `var`키워드와 `let`키워드를 분리하여 mutability를 표시함
 - `var` : mutability
 - `let` : not-mutability (절대 배열이 변경되지 않을 것을 보장)
 - copy-on-write : 필요할 때만 데이터가 복사되도록 하는 기법
     - 첫번째 예제에서 `y.append`가 호출되기 전까지는 x, y가 내부 저장 공간을 공유
     - `y.append` 호출 후 저장 공간이 분리됨
    
 ### Arrays and Optionals
 - Swift에서 배열에 접근할때 인덱스를 통해(array[3]) 접근할 수 있음 -> 잠정적 크래쉬
 - 배열에 접근할 때 유용한 API 모음
     - 전체 요소를 순회할때 : `for x in array`
     - 첫번째 요소를 제외하고 순회할때 : `for x in array.dropFirst()`
     - 뒤에서 n번째 요소를 제외하고 순회할때 : `for x in array.dropLast(n)`
     - 인덱스와 요소와 함께 순회할때 : `for (num, element) in array.enumerated()`
     - 특정 요소의 인덱스를 알아낼때 : `if let idx = array.index { someLogin($0) }`
     - 배열 요소를 변환시키고 싶을때 : `array.map { someTransformLogin($0) }`
     - 특정 조건에 만족하는 요소만 뽑아내고 싶을때 : `array.filter { someFilterLogin($0) }`
 - 첨자를 이용해서 접근하는 것은 유용할때도 있지만 나쁜 습관임
 - Array타입의 `first`, `last` 프로퍼티는 배열이 비여있을경우 nil 리턴
 - `first` == `isEmpty ? nil : self[0]`
 - `removeLast` -> 빈 배열에서 접근시 크래쉬
 - `popLast` -> 비여있는지 여부를 먼저 판단후 있을경우 마지막 요소 삭제 후 반환, 없을경우 nil 리턴 

 ## Transforming Arrays
 ### Map
 - 일반적으로 배열의 모든 요소에 대해 변환을 수행할때 반복문을 사용함
```Swift
let fibs = [0, 1, 1, 2, 3, 5]
var squared = [Int]()
for fib in fibs {
   squared.append(fib * fib)
}
```
 - Swift는 `map`함수를 통해서도 모든 요소를 변환시킬 수 있음
```Swift
let squares = fibs.map { fib in fib * fib }
```
 - `map`함수의 장점
     - 오류가 발생할 여지가 적음
     - 명확함 (요소의 변환이 일어난다는 것을 알 수 있음)
     - 변환된 값을 할당할 변수는 `let`으로 설정해도 됨 -> 안전
 - `map`함수의 구현은 어렵지 않음
 - Swift에서는 배열은 Sequence의 확장임
```Swift
extension Array {

    func map<T>(_ transform: (Element) -> T) -> [T] {
        var result: [T] = []
        result.reserveCapacity(count)
        for x in self {
            result.append(transform(x))
        }
        return result
    }
}
```

### Parameterizing Behavior with Functions
 - `map`과 같은 함수가 유용한 이유 : 각 요소가 어떻게 변환 할 것인지에 대한 논리를 클로져로 커스텀할 수 있기 때문
 - 클로져를 사용할 수 있는 함수 모음
     - `map`, `flatMap` : 어떻게 요소를 변환시킬지
     - `filter` : 어떤 요소만 포함시킬지
     - `reduce` : 어떻게 요소들을 합칠지
     - `sequence` : 시퀀스의 다음 요소는 무엇일지
     - `forEach` : 요소와 함께 수행할 사이드 이펙트는??
     - `sort`, `lexicographicallyPrecedes`, `partition` : 다음의 2 요소의 순서를 어떻게 할지
     - `index`, `first`, `contains` : 이 요소와 매칭되는지
     - `min`, `max` : 2개의 요소에서 최소값과 최대값이 무엇인지
     - `elementsEqual`, `starts` : 2개의 요소가 동일한지
     - `split` : 이 요소가 구분자 인지
 - 위 함수들은 배열이 어떤 로직을 수행하지 함수 이름을 통해 대체함
 - 표준 라이브러리에는 없지만 정의 사용 할 수 있는 함수 모음
     - `accumulate` : 요소들의 조합(reduce 함수와 비슷하지만 임시 조합의 배열로 반환)
     - `all(matching:)`, `none(matching:)` : 요소들에서 특정 값이 전체 또는 하나도 없는 특정 기준에 만족하는지)
     - `count(where:)` : 특정 조건에 만족하는 요소의 갯수(filter와 유사하지만 배열을 만들지는 않음)
     - `indices(where:)` : 특정 조건에 만족하는 인덱스 배열을 반환(index(where:) 함수와 비슷하지만 첫번째 요소를 찾았을때 멈추지 않음)
     - `prefix(while:)` : 특정 요소가 filter 조건에서 true되면, 나머지를 요소의 배열을 반환(filter와 비슷하지만, 일찍 탈출할 수 있고, 느슨하게 계산되는 시퀀스에서 유용함)
     - `drop(while:)` : 특정 요소가 filter 조건에 만족하기 전까지 요소를 배열에 추가, 만족시 멈춤(prefix(where:) 함수와 비슷하지만 방향이 다름)
 - 새로운 함수를 만들때, 패턴을 찾아서 적용하면 쉬움
```Swift
let names = ["Paula", "Elena", "Zoe"]
var lastNameEndingInA: String?
for name in names.reversed() where name.hasSuffix("a") {
    lastNameEndingInA = name
    break
}
// lastNameEndingInA : Elena
```
 - 클로져를 사용해서 for 루프 부분을 추상화 할 수 있음
```Swift
extension Sequence {
    
    func last(where predicate: (Iterator.Element) -> Bool) -> Iterator.Element? {
        for element in reversed() where predicate(element) {
            return element
        }
        return nil
    }
}

let match = names.last {
    $0.hasSuffix("a")
}
// match : Elena
```

### Mutation and Stateful Closures
 - 배열을 반복 할 때 `map`을 사용해서 빠질 수 있는 함정이 있음(ex: 요소를 배열에 삽입)
 ```Swift
 array.map { item in
    table.insert(item)
 }
 ```
 - 위 경우에는 for 루프를 도는 것이 명백함 (`forEach` 함수가 `map`보다는 적합하지만 자체 문제점을 가지고 있음)
 - Side Effects를 수행한다는 것은 의도적으로 클로져의 local state를 제공하는 것과는 다른데, 이것은 특히 유용한 기술이고, 클로져를 만드는 것임
 - 즉, 범위를 벗어나는 변수를 캡쳐하고 변형 할 수 있는 기능을 제공함으로 `high-order function`과 결합하면 강력한 도구가된다.
 - 위에 설명한 `accumulate`함수를 `map`과 `stateful closure`로 구현 될 수 있음
```Swift
extension Array {
    
    func accumulate<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) -> Result) -> [Result] {
        var running = initialResult
        return map { next in
            running = nextPartialResult(running, next)
            return running
        }
    }
}

[1, 2, 3, 4].accumulate(0, +)
[1, 2, 3, 4].accumulate(0) {
    return $0 + $1
}
// [1, 3, 6, 10]
```
 - 위와 같이 사용하면, 실행중인 값을 저장하는 임시 변수를 만든후 map을 사용하여 진행중인 값의 배열을 만듬

 ### Filter
 - 특정 조건과 일치하는 요소 만 포함하는 새로운 배열을 만드는 것
```Swift
extension Array {
    
    func filter(_ isIncluded: (Element) -> Bool) -> [Element] {
        var result: [Element] = []
        for x in self where isIncluded(x) {
            result.append(x)
        }
        return result
    }
}
```
 - 특정 경우에 `filter`함수를 사용하는 것 보다 `contains(where:)`함수를 사용하는 것이 성능에 더 좋다.
```Swift
bigArray.filter { someCondition }.count > 0 // 새로운 배열을 만들기 때문에 비용이듬
bigArray.contains { someCondition }
```
 - `contains`의 경우 첫 번째 요소와 일치하는 즉시 조기에 종료됨
 - 간혹, 모든요소가 조건에 만족하는지 판단할때 다음과 같은 로직을 수행함 (가독성이 안좋은 코드)
```Swift
!sequence.contains { !condition }
```
 - 위 코드는 `all(predicate:)`함수를 새로 만들어서 사용하면 편함
```Swift
extension Sequence {
    func all(matching predicated: (Iterator.Element) -> Bool) -> Bool {
        return !contains { !predicated($0) }
    }
}

let number = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let evenNum = number.filter { $0 % 2 == 0 }
let isAllEvenNum = evenNum.all { $0 % 2 == 0 }
// isAllEvenNum : true
```

### Reduce
 - 배열의 모든 요소를 결합하여 새 값을 만들때 사용됨
```Swift
extension Array {
    func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) -> Result) -> Result {
        var result = initialResult
        for x in self {
            result = nextPartialResult(result, x)
        }
        return result
    }
}
```
 - `reduce`메소드는 초기값과 중간값과 요소를 결합하여 함수의 두 부분을 추상화함
```Swift
let sum =  bs.reduce(0) { total, num in total + num } // 12
```
 - `reduce`만을 사용해서 `map`, `filter` 만들기
```Swift
extension Array {
    
    func map2<T>(_ transform: (Element) -> T) -> [T] {
        return reduce([]) {
            $0 + [transform($1)]
        }
    }
    
    func filter2(_ isIncluded: (Element) -> Bool) -> [Element] {
        return reduce([]) {
            isIncluded($1) ? $0 + [$1] : $0
        }
    }
}

```
 - 하지만 위 구현은 O(n2) 이기 때문에 배열의 길이가 길어지면 성능상에 이슈가 있음

 ### A Flattening Map
 - 예시 : 특정 마크타운 파일에 있는 link들을 추출하여 배열을 만듬
```Swift
func extractLinks(markdownFile: String) -> [URL] { ... }

let markdownFiles: [String] = // ...
let nestedLinks = markdownFiles.map(extractLinks) 
let links = nestedLinks.joined()
 ```
 - 위 코드의 경우 `map`을 수행하여 2차원 배열을 가져오고 `joined`을 통해 1차원 배열로 합침
 - `flatMap`함수는 위 2가지의 행동을 한 스텝으로 줄임 (`markdownFiles̪. atMap(extractLinks)`) 코드는 단일 배열로 반환됨
```Swift
extension Array {
    
    func flatMap<T>(_ transform: (Element) -> [T]) -> [T] {
        var result: [T] = []
        for x in self {
            result.append(contentsOf: transform(x))
        }
        return result
    }
}

let suits = ["♠", "♥", "♣", "♦"]
let ranks = ["J","Q","K","A"]
let result = suits.flatMap { suit in
    ranks.map { rank in
        (suit, rank)
    }
}
// result : 튜플로 반환 ("♠", "J"), ("♠", "Q") ...
``` 

### Iteration using forEach
 - `map`, `filter`, `reduce`, `flatMap`과 달리 `forEach` 함수는 아무 것도 반환하지 않음
 - 루플 를 기계적으로 교환하는 역할을 함
```Swift
for element in [1, 2, 3] {
    print(element)
}

[1, 2, 3].forEach { element in
    print(element)
}
```
 - `forEach` 함수는 크게 이점이 없어보이지만, 클로져 표현식 대신, 함수 이름을 전달하면 명확하고 간결한 코드를 만들어 낼 수 있음
```Swift
theViews.forEach(view.addSubview)
``` 
 - for 루프와의 차이점은 for 루프는 return문 혹은  break로 빠져나올 수 있지만, `forEach`함수는 모든 Iteration을 다 돌아야함
```Swift
extension Array where Element: Equatable {
    
    func index(of element: Element) -> Int? {
        for idx in self.indices where self[idx] == element {
            return idx
        }
        return nil
    }
}
```
 - 위 코드를 `forEach`로 구현하려면 where 절을 직접 사용할 수 없기 때문에 `filter`함수를 이용해서 구현해야함 (아래 코드는 잘못된 코드임)
 ```Swift
 extension Array where Element: Equatable {
    
    func index_forEach(of element: Element) -> Int? {
        self.indices.filter { idx in
            self[idx] == element
        }.forEach { idx in
            return idx
        }
        return nil
    }
}
 ```
  - `forEach` 클로저 내부의 return은 외부 함수를 벗어나지 않음 (클로저 자체에서만 반환됨)
  - 따라서 위 코드는 `idx`가 반환되지 않음
```Swift
(1..<10).forEach { num in
    print(num)
    if num > 2 {
        return
    }
}
```
 - 마찬가지로 위의 코드에서 return은 루프를 종료하는것이 아님
 - 따라서, for 루프와 `forEach` 함수를 상황에 알맞게 사용하는 것이 좋음(즉, `forEach` 남발하지 말자)

 ## Array Type
 ### Slice
 - 배열에 첨자로 접근하는 것 이외에도 요소의 범위로 접근할 수 있음
 - 예시 : 배열에 첫번째 요소를 제외한 모든 요소를 가져올때
```Swift
let nums = [1, 2, 3, 4, 5]
let slice = nums[1..<nums.endIndex]
// slice : [2, 3, 4, 5]
// type(of: slice) : ArraySlice<Int>.Type
```
 - 여기에서 slice의 타입은 Array가 아닌 ArraySlice<Int> 임
 - ArraySlice는 배열에 대한 뷰라고 보면됨
 - slice를 사용하면 배열을 복사 할 필요가 없음
 - ArraySlice 유형에는 배열과 동일한 메소드가 정의되어 있어 마치 배열 인 것 처럼 사용할 수도 있음

 ## Bridging
  - Swift array는 Objective-C에 연결할 수 있음
  - Objective-C id 유형은 `Any` 타입으로 Swift로 가져올 수 있음
  - 즉, Swift배열은 이제 NSArray에 연결할 수 있음

 ## Dictionaries
  - 딕셔너리는 key, value를 갖은 자료형임(키는 중복될 수 없음)
  - 딕셔너리에서 값을 찾는데 소요되는 시간 : O(1), 배열에서 값을 찾는데 소요되는 시간 : O(n)
  - 배열과 다르게 딕셔너리는 정렬되지 않음
```Swift
enum Setting {
    case text(String)
    case int(Int)
    case bool(Bool)
}

let defaultSetting: [String: Setting] = [
    "Airplan Mode": .bool(true),
    "Name": .text("My iPhone")
]

defaultSetting["Name"] // Optional(Setting.text("My iPhone"))
```
 - key를 subscript 문법으로 value에 접근시, 딕셔너리는 항상 Optional을 리턴함 (키가 존재하지 않거나, 해당 키의 value가 nil일때)
 - 반면에, 배열은 잘못된 인덱스를 갖고 subscript에 접근시 크래쉬

### Mutation
 - 배열과 마찬기지로 `let`키워드로 딕셔너리 선언시 immutable함
 - 딕셔너리에서 value를 삭제시 서브스크립트를 사용해 값을 `nil`로 세팅하거나 또는 `removeValue(forKey:)`함수 사용
 - 시스템 함수 `removeValue(forKey:)`, `updateValue(_:forKey:)`사용시 변경 이전의 값이 반환됨
```Swift
var localizedSettings = defaultSetting
localizedSettings["Name"] = .text("Mein iPhone")
localizedSettings["Do Not Disturb"] = .bool(true)

let oldName = localizedSettings.updateValue(.text("ll mio iPhone"), forKey: "Name")
localizedSettings["Name"] // Optional(Setting.text("ll mio iPhone"))
oldName // Optional(Setting.text("Mein iPhone"))
```

### Some Useful Dictionary Extensions
 - 1. Merge 함수 : 딕셔너리에 다른 딕셔너리를 합병시킴
```Swift
extension Dictionary {
    mutating func merge<S>(_ other: S)
        where S: Sequence, S.Iterator.Element == (key: Key, value: Value) {
            for (k, v) in other {
                self[k] = v
            }
    }
}

var settings = defaultSetting
let overriddenSettings: [String: Setting] = ["Name": .text("Jane's iPhone")]
settings.merge(overriddenSettings)
settings //["Name": {text "Jane's iPhone"}, "Airplan Mode": {bool true}]
``` 
 - 2. 편리한 init : 배열을 딕셔너리로 만들기 (개념은.. 빈 딕셔너리를 만들고 `merge` 함수 사용)
```Swift
extension Dictionary {
    init<S: Sequence>(_ sequence: S)
        where S.Iterator.Element == (key: Key, value: Value) {
            self = [:]
            self.merge(sequence)
    }
}

let defaultAlarms = (1..<5).map { (key: "Alarm \($0)", value: false) }
let alarmsDictionary = Dictionary(defaultAlarms)
// ["Alarm 1": false, "Alarm 4": false, "Alarm 2": false, "Alarm 3": false]
```
 - 3. mapValue 함수 : 딕셔너리에 구조는 그대로있고 값만 수정
```Swift
extension Dictionary {
    func mapValue<NewValue>(transform: (Value) -> NewValue) -> [Key: NewValue] {
        return Dictionary<Key, NewValue>(map { key, value in
            return (key, transform(value))
        })
    }
}

let settingsAsStrings = defaultSetting.mapValue { setting -> String in
    switch setting {
    case .text(let text): return text
    case .int(let number): return String(number)
    case .bool(let value): return String(value)
    }
}
settingsAsStrings
// ["Name": "My iPhone", "Airplan Mode": "true"]
```

### Hashable Requirement
 - 딕셔너리는 hash table임
 - `hashValue`라는 키를 기반으로 딕셔너리는 각각의 키를 배열 저장소에 저장한다.
 - 따라서, 커스텀 타입의 키를 만들기 위해서는 반듣이 `Hashable`프로토콜을 준수해야함
 - `hashValue`를 설정해야되고, `Hashable`프로토콜은 `Equatable`프로토콜을 상속받았음으로 `==` 함수를 구현해야함
 - 여기서, 중요한 점은 `==`함수에서 두 인스턴스는 반드시 같은 `hashValue`를 갖고있어야한다.
 - 역의 경우 다르다. 같은 `hashValue`를 갖고있는 두 인스턴스는 반드시 동일하게 비교되는 것은 아님??
 - 많은 hash가능 유형(String 등)은 본질적으로 무한 카니널리티를 가지고 있지만, 유한 해시 값은 유한 한 수의 해시 값을 가지고 있다는 점을 고려하면 의미가 있음
 - 중복된 hash 값은 잠재적으로 Dictionary가 붕괴할 것이란걸 짐착할 수 있음
 - 그렇지만, 좋은 hash function은 딕셔너리의 퍼포먼스적인 특징을 지키기 위해서 최소한의 붕괴만을 야기한다.
 - 두번째 특징은 좋은 hash function은 빠르다.
```Swift
struct Person {
    var name: String
    var zipCode: Int
    var birthDay: Date
}

extension Person: Equatable {
    static func ==(lhs: Person, rhs: Person) -> Bool {
        return lhs.name == rhs.name && lhs.zipCode == rhs.zipCode && lhs.birthDay == rhs.birthDay
    }
}

extension Person: Hashable {
    var hashValue: Int {
        return name.hashValue ^ zipCode.hashValue ^ birthDay.hashValue
    }
}
``` 
 - 마지막으로 딕셔너리의 Key로써 value semantics(ex. mutable objects)를 사용하면 안됨

## Set
 - Set은 정렬되지 않는 element (각각의 element는 오직 하나만 존재함)의 collection임
 - Key가 없는 딕셔너리와 비슷함 (O(1), Set의 요소들은 hashable를 준수해야함)
 - 정렬 순수가 중요하지 않거나, 중복해서 값이 들어갈 필요가 없을때 Array보다 Set을 이용하는 것이 좋음

### Set Algebra
 - Set은 수학에서의 집합임
```Swift
// 차집합
let iPods: Set = ["iPod touch", "iPod nano", "iPod mini", "iPod shuf e", "iPod Classic"]
let discountinuedIPods: Set = ["iPod mini", "iPod Classic"]
let currentIPods = iPods.subtracting(discountinuedIPods)
currentIPods
// ["iPod nano", "iPod shuf e", "iPod touch"]

// 교집합
let touchscreen: Set = ["iPhone", "iPad", "iPod touch", "iPod nano"]
let iPodsWithTouch = iPods.intersection(touchscreen)
iPodsWithTouch
// ["iPod touch", "iPod nano"]

// 합집합
var discontinued: Set = ["iBook", "Powerbook", "Power Mac"]
discontinued.formUnion(discountinuedIPods)
discontinued
// ["iBook", "iPod mini", "Powerbook", "Power Mac", "iPod Classic"]
```

### Index Sets and Character Sets
 - Set은 스탠다르 라이브러리에서 `SetAlgebra`프로토콜을 준수하는 유일한 타입임
 - 또한, `SetAlgebra`프로토콜은 `IndexSet`, `CharacterSet`타입 에서도 준수됨
 - `IndexSet`
     - 양의 정수를 표현함.
     - `Set<Int>`를 사용할 수 있지만 메모리 효율적인 측면에서 `IndexSet`가 좋다. (범위를 내부적으로 저장하기 때문)
```Swift
var indices = IndexSet()
indices.insert(integersIn: 1..<5)
indices.insert(integersIn: 11..<15)
let evenIndices = indices.filter { $0 % 2 == 0 }
evenIndices //[2, 4, 12, 14]
```
 - `CharacterSet`
     - 유니코드 문자열들을 저장할때 효율적임

 ## Using Sets Inside Closures
  - unique 함수 : 내부적으로 Set을 이용하여 중복을 없앰
```Swift
extension Sequence where Iterator.Element: Hashable {
    func unique() -> [Iterator.Element] {
        var seen: Set<Iterator.Element> = []
        return filter {
            if seen.contains($0) {
                return false
            } else {
                seen.insert($0)
                return true
            }
        }
    }
}

[1,2,3,12,1,3,4,5,6,4,6].unique() // [1, 2, 3, 12, 4, 5, 6]
```

## Ranges