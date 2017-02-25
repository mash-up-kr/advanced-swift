# Advanced Swift _2 ( Built-In Collections )
## Array
- 일반적으로 예상치 못한 배열의 변경을 방지하기 위해서 let 선언을 통해서 배열을 구성하는게 올바르다.
- Swift의 Collection Type은 항상 Reference copy를 하지 않고 값을 복사한다.
```Swift
var x = [1,2,3] 
var y = x y.append(4)
y // [1, 2, 3, 4] 
x // [1, 2, 3]
```

- Foundation의 NSArray의 경우 조작 메서드를 보유하고 있지 않고 조작을 위해선 NSMutableArray를 사용해야한다. 그러나 NSArray가 항상 조작되지 않는다고 보장되어지진 않는다.
```Swift
let a = NSMutableArray(array: [1,2,3])
let b: NSArray = a
a.insert(4, at: 3) b // ( 1, 2, 3, 4 )
``` 

b는 NSArray이지만 NSMutableArray의 Reference 값을 할당 받음으로써 간접적으로 조작이 됨.

- Swift에서는 위와 달리 Reference sharing이 없기 때문에 `let`으로 선언할 경우 변하지 않는 변수임이 보장된다.

## Arrays and Optionals
- Swift는 인덱스 연산과 전통적인 방식의 C-Style 루프를 권장하지 않는다.
- 인덱스 연산을 사용하는 상황이 만들어져 철저한 계산에 의해 올바른 값을 얻어내었다고 확신하여 force-unwrapping을 할 수 있다. 하지만 force-unwrapping이 습관화되다보면 결국 실수하게 되며 원하지 않는 값을 unwrapping 하게 되어 문제를 일으킨다. 

## Transforming Arrays
### Map 

- 아래와 같은 정수 배열의 연산에 관련된 코드가 있다.
```Swift
var squared: [Int] = [] 
for fib in fibs {
squared.append( fib * fib) }
squared // [0, 1, 1, 4, 9, 25]
```

- Swift에는 함수형 프로그래밍에서 쓰이던 `map` 메서드가 있는데 이를 활용하면 훨씬 간결한 코드를 작성할 수 있다.
```Swift
let squares =  bs.map {  b in  b *  b }
squares // [0, 1, 1, 4, 9, 25]
```

- 위 코드에는 세 가지 정도의 장점이 있는데
1. 짧다. ( 에러가 발생할 수 있는 확률이 적다. )
2. 깔끔하다. > 일단 사용할 수 있는 모든 곳에 `map`을 사용하라 > 이유인 즉슨  `map`을 보면 무슨 역할을 하는지 바로 알 수 있다.
3. 결과 값이 `let` 변수로 선언할 수 있다. > 변수가 추후에 수정하지 않아도 됨.

### Parameterizing Behavior with Functions

- Swift Standard Library 전체에서 parameterizing의 흔적을 찾아볼 수 있다.
- 아래 함수들의 목적은 코드의 필요없는 부분을 없애기 위해서이다. ( 예를 들면, 새로운 array의 생성이나 반복문 등 )

- `map` , `flatMap` -> 요소들을 어떻게 변환시킬 건지?
- `filter` -> 요소가 포함되어야하는지?
- `reduce` -> 어떻게 요소들을 하나의 값으로 만들지?
- `sequnce` -> 시퀀스 다음에는 어떤 요소가 와야하는지?
- `forEach` -> 요소에 적용해야할 조건(?)은 무엇인지?
- `sort`, `lexicographicallyPrecedes`, `partition` -> 두 요소가 어떤식으로 와야하는지?
- `index`, `first`, `contains` -> 요소와 매칭이 되는지?
- `min`, `max`  -> 두 요소중 어떤 것이 크고/작은지?
- `elementsEqual` , `starts` -> 두 요소가 같은지?
- `split` -> 요소가 separator인지?

- 아래와 같이 배열의 마지막 부터 접미사를 탐색하는 코드를 Extension을 활용해 좀 더 간단하게 변환할 수 있다.
```Swift
let names = ["Paula", "Elena", "Zoe"]
var lastNameEndingInA: String?
for name in names.reversed() where name.hasSuffix x("a") {
	lastNameEndingInA = name
	break
}
lastNameEndingInA // Optional("Elena")
```

```Swift
extension Sequence {
func last(where predicate: (Iterator.Element) -> Bool) -> Iterator.Element? {
	for element in reversed() where predicate(element) { 
		return element
	}
	return nil
	} 
}
```

- Extension을 활용하여 만든  `last` 를 사용하면 동일한 결과를 얻어낸다.
```Swift
let match = names.last { $0.hasSuffix("a") }
match // Optional("Elena")
```

`last`를 활용하였더니 반복문을 사용하였던 예시보다 훨씬 읽기 편한 코드로 변하게 되었다. `for loop` 가 간단하더라도 `last`와 같은 함수를 활용하는 것이 에러가 발생할 확률을 현저히 줄여준다. 

- 함수를 활용하는 방식은 `guard`와도 적절하게 활용된다. 
```Swift
guard let match = someSequence.last(where: { $0.passesTest() }) 
	else { return }
// Do something with match
```

### Mutation and Stateful Closures

- `map`을 활용하다보면 특정 테이블에 요소들을 집어넣는 등의 특정 활동(?)을 할 수 있는데 추천하지 않는 방법이다.

```Swift
array.map { item in
	table.insert(item)
}
```

위와 같은 상황에서는 `map`이 무슨 활동을 하려는지 명확하지 않기 때문에 `map` 보단 `for loop`가 더 적절한 경우이다.

- Performing side effects is different than deliberately giving the closure *local state*, which is a particularly useful technique, and it’s what makes closures — functions that can capture and mutate variables outside their scope

```Swift
extension Array {
	func accumulate<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) -> Result) -> [Result] {
	var running = initialResult return map { next in
		running = nextPartialResult(running, next)
		return running 
		}
	} 
}
```

```Swift
[1, 2, 3, 4].accumulate(0, +) // [1, 3, 6, 10]
```

### Filter

```Swift
nums.filter { num in num % 2 == 0 } // [2, 4, 6, 8, 10]
```

- We can use Swift’s shorthand notation for arguments of a closures expression to make this even shorter. Instead of naming the num argument.

```Swift
nums.filter { $0 % 2 == 0 } // [2, 4, 6, 8, 10]
```

- By combining `map` and `filter`, we can easily write a lot of operations on arrays without having to introduce a single intermediate array, and the resulting code will become shorter and easier to read.

```Swift
(1..<10).map {$0 * $0}.filter {$0 % 2 == 0} // [4, 16, 36, 64]
``` 

- Don’t use `filter` like this.

```Swift
bigArray.filter { someCondition }.count > 0 // (X)
bigArray.contains { someCondition } // (O)
```

filter creates a brand new array and processes every element in the array. But this is unnecessary. This code only needs to check if one element matches — in which case, `contains(where:)` will do the job:

## Dictionaries
- `Dictionary` 는 키에 일치하는 값으로 이루어진 자료형이다.
* 중복 key는 지원되지 않는다.
* `Array` 와는 다르게 `Dictionary` 는 정렬되어 있지 않다. 

```Swift
enum Setting {
	case text(String),
	case int(int),
	case bool(Bool)
}

let defaultSetting: [String:Setting] = [
	"Airplane Mode": .bool(true),
	"Name": .text("My iPhone"),
]

defaultSetting["Name"] // Optional(Setting.text("My iPhone")
``` 

- `Dictionary` 는 항상 옵셔널 값을 반환한다.
* 특정 키에 대한 값이 존재하지 않을 경우 `nil` 을 반환한다.
* `Array` 와 다르게 `out-of-bounds` 문제를 발생시키지 않는다.
- `Array` 와 다르게 특정 키에 대한 값의 존재가 다른 키에 대한 값의 존재를 알려주지 않는다. 

### Mutation

- `Array` 와 동일하게 `let` 으로 선언된 `Dictionary` 는 조작되지 않는다.
* `var` 을 통해서 조작할 수 있는 `Dictionary` 값을 선언할 수 있다. 
* `Dictionary` 의 값을 제거하기 위해서는 `subscripting` 을 이용하여 `nil` 값을 세팅해주거나 `removeValue(forKey:)` 를 호출해주면 된다. 
* `removeValue(forKey:)` 의 경우 삭제하려는 키의 값이 존재하지 않는 경우 `nil` 을 반환하고 존재할 경우 삭제된 값이 반환된다.
* 조작이 불가능한 `Dictionary` 를 조작하고 싶을 경우 다른 `Dictionary` 로 `copy` 를 하면된다.

```Swift
var localizedSettings = defaultSettings
localizedSettings["Name"] = .text("Mein iPhone")
localizedSettings[“Do Not Disturb”] = .bool(true)
```

- 다시 한 번 살펴보면, `defaultSettings` 의 값은 변화하지 않는다. 
* `updateValue(_: forKey:)` 로도 새로운 값으로 업데이트 할 수 있으며 이 메서드는 이전의 값을 리턴한다.

```Swift
let oldName = localizedSettings
	.updateValue(.text("il mio iPhone"), forKey: "Name")
localizedSettings["Name"] // Optional(Setting.text("il mio iPhone"))
oldName // Optional(Setting.text("Mein iPhone"))
```

### Some Useful Dictionary Extensions

- `Dictionary` 의 기본 요소들은 유지한채 다른 `Dictionary` 의 요소를 가져오기 위한 메서드가 `Standard Libarary` 에는 구현되어 있지 않으므로 `Extension`을 활용하여 구현한다.

```Swift
extension Dictionary {	mutating func merge<S>(_ other: S)		where S: Sequence, S.Iterator.Element == (key: Key, value: Value) 
{ 
		for (k, v) in other {			self[k] = v 
		}	} 
}
```

- 구현된 `merge`를 실제 코드에 사용하면 다음과 같다. 
```Swift
var settings = defaultSettingslet overriddenSettings: [String:Setting] = ["Name": .text("Jane's iPhone")] settings.merge(overriddenSettings)settings// ["Name": Setting.text("Jane\'s iPhone"), "Airplane Mode": Setting.bool(true)]
```

- 다른 흥미로운 `Extension` 은 키, 값이 쌍으로 이루어진 순열로부터 `Dictionary` 를 만드는 것이다.  
* `Standard Library` 는 배열에서 이와 비슷한 생성자를 제공하고 굉장히 자주 쓰인다. 

```Swift
extension Dictionary {
	init<S: Sequence>(_ sequence: S)
		where S.Iterator.Element == (key: Key, value: Value) {
			self = [:]
			self.merge(sequence)
		}
}
```

```Swift
let defaultAlarms = (1..<5).map { (key: "Alarm \($0)", value: false) }
let alarmsDictionary = Dictionary(defaultAlarms)
```

- 세 번째로 유용한 `Extension` 은 `Dictionary` 의 값에 대하여 `map` 을 적용시키는 것이다.
* `Dictionary` 는 순열이고, 이미 `Array` 를 만들어내는 `map` 메서드가 존재하지만, 때때로 `Dictionary` 의 틀은 유지한 채로 값만 변화시키고 싶은 경우가 있다. 
* 따라서, `mapValues` 메서드는 표준 `map` 메서드를 호출하여 변화된 값으로 이루어진 `Array` 를 구성한 뒤 새로운 생성자를 통해서 다시  `Dictionary` 를 만들 것이다.

```Swift
extension Dictionary  {
	func mapValues<NewValue>(transform: (Value) -> NewValue) -> [Key: NewValue] {
		return Dictionary<Key, NewValue>(map { (key, value) in
			return (key, transform(value))
		})
	}
}
```

```Swift
let settingsAsStrings = settings.mapValue { setting -> String in 
	switch setting {
	case .text(let text): return text
	case .int(let number): return String(number)
	case .bool(let value): return String(value)
	}
}
settingsAsStrings // ["Name": "Jane\'s iPhone", "Airpleane Mode": "true"]
```

### Hashable Requirement

- `Dictionary` 는 `key` 의 `hashValue` 를 기반으로한 공간 배열에 각각의 `key` 의 위치를 할당한다.
* 만약 커스텀 타입을 `Dictionary` 의 `key` 로 사용하고 싶다면 `Hashable` 프로토콜을 반드시 구현하여야 한다. 
* 두 개의 인스턴스가 같다고 가정할 때, `hashValue` 는 반드시 같다. 하지만 그 역은 성립하지 않는다.
* 중복되는 `hash values` 는 잠재적으로 `Dictionary` 가 충돌을 컨트롤 할 수 있음을 의미한다.

## Sets
- `Set` 은 특정 규칙이 존재하지 않고 한 번씩만 등장하는 요소들의 집합이다. 
* `Set` 은 `Value` 없이 `Key` 만 존재하는 `Dictionary` 라고 생각해야만한다. 
* `Dictionary` 처럼 `Set` 은 `hash table` 로 구성되어 있고 비슷한 퍼포먼스 특징과 요구사항을 지니고 있다.
- `Set` 의 `membership` 값을 테스트하는 것은 상수 시간이 되고 요소들은 반드시 `Hashable` 해야만 한다, `Dictionary` 의 `Key` 처럼.

- `Set` 의 활용
	1. `membership` 에 대한 효율적인 테스트를 하기 위해서는 `Array` 보다 `Set` 을 활용하면 좋다.
	2. 일정 규칙이 존재하지 않아도 되는 경우에 활용하면 좋다.
	3. 중복이 없음을 보장하여햐하는 경우에 좋다.

```Swift
let naturals: Set = [1, 2, 3, 2]
naturals // [2, 3, 1]
naturals.contains(3) // true
naturals.contains(0) // false
```	
 
- 숫자 2의 경우 `Set` 에서 단 한 번만 등장한다, 중복은 절대 삽입되지 않는다.
- 모든 `Collection Type` 에서 보았듯이 `Set` 에도 기본적인 연산들이 지원된다. 예를 들면, `iterate`, `for loop`, `map`, `filter`…

### Set Algebra

- 이름에도 포함하고 있듯이 `Set` 은 수학적인 `집합` 과 많은 연관성을 가지고 있다. 
* `집합` 에서 사용되는 모든 연산자들을 지원하고 있다. 
* 집합 연산 중  `subtract` 가 지원된다.

```Swift
let iPods: Set = ["iPod touch", "iPod nano", "iPod mini", "iPod shuffle", "iPod Classic"]
let discountinuedIPod: Set = ["iPod mini", "iPod Classic"]
let currentIpods = iPods.subtracting(discontinuedIPods)
// ["iPod shuffle", "iPod nano", "iPod touch"]
```

- 또한, 두 `Sets` 의  `intersection`  또한 지원된다. 

```Swift
let touchScreen: Set = ["iPhone", "iPad", "iPod touch", "iPod nano"]
let iPodsWithTouch = iPods.intersection(touchscreen)
// ["iPod touch", "iPod nano"]
```

- 우리는 `union` 도 사용할 수 있다.

```Swift
var discontinued: Set = ["iBook", "Powerbook", "Power Mac"]
discontinued.formUnion(discontinuedIPods)
discontinued // ["iBook", "iPod mini", "Powerbook", "Power Mac", "iPod Classic"]
```

### Index Sets and Character Sets

- `Set`은 `Standard Library` 에서 유일하게 `SetAlgebra` 를 구현할 수 있는 데이터 타입이지만, `Foundation` 의 `indexSet` 과 `characterSet` 에도 적용될 수 있다. 

#### Index Set
- `indexSet` 은 양의 정수  값 들의 집합이고 이것은 물론 `Set<Int>` 로 구현할 수 있다.
* 그러나, `indexSet` 이 좀 더 공간을 효율적으로 구성할 수 있는데, 내부적으로 범위의 리스트를 사용하기 때문이다. 

#### Character Set
- `Character Set` 은 유니코드 문자들의 집합을 좀 더 효율적으로 저장하기 위한 데이터 타입이다.
- 주로 문자열에 특정 문자 값이 포함되어 있는지 체크할 경우에 자주 사용한다.

### Using Sets Inside Closures

- `Dictionary` 와 `Sets` 은 함수 안에서 굉장히 편리하게 사용할 수 있는 데이터 타입들이다.

## Range
- `Range` 는 값의 구간이다.
* `Range` 는 두 가지 연산자를 통해서 만들어질 수 있는데 `..<` 와 `…` 이다. 

```Swift
// 0 to 9, 10 is not included
let singleDigitNumbers = 0..<10

// "z" is included
let lowercaseLetters = Character("a")...Character("z")
```

- `Range` 는 `sequence` 와 `collection` 모두에 적합해보이지만, 놀랍게도 둘 다 아니다. 
* 4가지의 `Range Type`이 `Standard Library` 에 정의되어 있다.





