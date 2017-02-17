# Advanced Swift _2 ( Built-In Collections )
## Array
- 일반적으로 예상치 못한 배열의 변경을 방지하기 위해서 let 선언을 통해서 배열을 구성하는게 올바르다.
- Swift의 Collection Type은 항상 Reference copy를 하지 않고 값을 복사한다.
```Swift
var x = [1,2,3] 
var y = x y.append(4)y // [1, 2, 3, 4] 
x // [1, 2, 3]
```

- Foundation의 NSArray의 경우 조작 메서드를 보유하고 있지 않고 조작을 위해선 NSMutableArray를 사용해야한다. 그러나 NSArray가 항상 조작되지 않는다고 보장되어지진 않는다.
```Swift
let a = NSMutableArray(array: [1,2,3])let b: NSArray = aa.insert(4, at: 3) b // ( 1, 2, 3, 4 )
``` * b는 NSArray이지만 NSMutableArray의 Reference 값을 할당 받음으로써 간접적으로 조작이 됨.

- Swift에서는 위와 달리 Reference sharing이 없기 때문에 `let`으로 선언할 경우 변하지 않는 변수임이 보장된다.

## Arrays and Optionals
- Swift는 인덱스 연산과 전통적인 방식의 C-Style 루프를 권장하지 않는다.
- 인덱스 연산을 사용하는 상황이 만들어져 철저한 계산에 의해 올바른 값을 얻어내었다고 확신하여 force-unwrapping을 할 수 있다. 하지만 force-unwrapping이 습관화되다보면 결국 실수하게 되며 원하지 않는 값을 unwrapping 하게 되어 문제를 일으킨다. 

## Transforming Arrays
### Map 

- 아래와 같은 정수 배열의 연산에 관련된 코드가 있다.
```Swift
var squared: [Int] = [] 
for fib in fibs {squared.append( fib * fib) }squared // [0, 1, 1, 4, 9, 25]
```

- Swift에는 함수형 프로그래밍에서 쓰이던 `map` 메서드가 있는데 이를 활용하면 훨씬 간결한 코드를 작성할 수 있다.
```Swift
let squares =  bs.map {  b in  b *  b }squares // [0, 1, 1, 4, 9, 25]
```

- 위 코드에는 세 가지 정도의 장점이 있는데
1. 짧다. ( 에러가 발생할 수 있는 확률이 적다. )
2. 깔끔하다. > 일단 사용할 수 있는 모든 곳에 `map`을 사용하라 >이유인 즉슨  `map`을 보면 무슨 역할을 하는지 바로 알 수 있다.
3. 결과 값이 `let` 변수로 선언할 수 있다. > 변수가 추후에 수정하지 않아도 됨.

### Parameterizing Behavior with Functions

- Swift Standard Library 전체에서 parameterizing의 흔적을 찾아볼 수 있다.
- 아래 함수들의 목적은 코드의 필요없는 부분을 없애기 위해서이다. ( 예를 들면, 새로운 array의 생성이나 반복문 등 )
`map` , `flatMap` -> 요소들을 어떻게 변환시킬 건지?
`filter` -> 요소가 포함되어야하는지?
`reduce` -> 어떻게 요소들을 하나의 값으로 만들지?
`sequnce` -> 시퀀스 다음에는 어떤 요소가 와야하는지?
`forEach` -> 요소에 적용해야할 조건(?)은 무엇인지?
`sort`, `lexicographicallyPrecedes`, `partition` -> 두 요소가 어떤식으로 와야하는지?
`index`, `first`, `contains` -> 요소와 매칭이 되는지?
`min`, `max`  -> 두 요소중 어떤 것이 크고/작은지?
`elementsEqual` , `starts` -> 두 요소가 같은지?
`split` -> 요소가 separator인지?

- 아래와 같이 배열의 마지막 부터 접미사를 탐색하는 코드를 Extension을 활용해 좀 더 간단하게 변환할 수 있다.
```Swift
let names = ["Paula", "Elena", "Zoe"]var lastNameEndingInA: String?for name in names.reversed() where name.hasSuffix x("a") {	lastNameEndingInA = name	break}lastNameEndingInA // Optional("Elena")
```

```Swift
extension Sequence {func last(where predicate: (Iterator.Element) -> Bool) -> Iterator.Element? {	for element in reversed() where predicate(element) { 
		return element	}	return nil	} 
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
	else { return }// Do something with match
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
extension Array {	func accumulate<Result>(_ initialResult: Result,		_ nextPartialResult: (Result, Element) -> Result) -> [Result] {	var running = initialResult return map { next in		running = nextPartialResult(running, next)		return running 
		}	} 
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
``` * More readable

- By combining `map` and `filter`, we can easily write a lot of operations on arrays without having to introduce a single intermediate array, and the resulting code will become shorter and easier to read.

```Swift
(1..<10).map {$0 * $0}.filter {$0 % 2 == 0} // [4, 16, 36, 64]
``` 

- Don’t use `filter` like this.

```Swift
bigArray.filter { someCondition }.count > 0 // (X)
bigArray.contains { someCondition } // (O)
```

filter creates a brand new array and processes every element in the array. But this is unnecessary. This code only needs to check if one element matches — in which case, contains(where:) will do the job:






