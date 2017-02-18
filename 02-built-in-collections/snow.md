2. Built-In Collections
=================

*Arrays*
-----------
## Arrays and Mutablility
- `var` vs `let`
- Arrays는 struct이므로 value type
- `NSMutableArray` vs `NSArray`

## Arrays and Optionals*
`let x = [2, 4, 6, 8, 10, 12, 14]`라고 가정
- Want to iterate over the array? [2, 4, 6, 8, 10, 12, 14] - `for x in array`
- Want to iterate over all but the first element of an array? [4, 6, 8, 10, 12, 14] (첫번째꺼빼고 나머지): `for x in dropFirst`
- Want to iterate over all but the last 5 elements? [2, 4] (뒤에서 5개빼고 나머지): `for x in array.dropLast(5)`
- Want to number all the elements in an array? `for (num, element) in collection.enumerated()`
- Want to find the location of a specific element? 2 `if let idx = array.index { $0 % 3 == 0 }`
- Want to transform all the elements in an array? [4, 8, 12, 16, 20, 24, 28] `array.map { $0 * 2 }`
- Want to fetch only the elements matching a specific criterion? [6, 12] `array. filter { $0 % 3 == 0 }`

## Tansforming Arrays*
- *Map*
- 모든 요소를 돌며 변환
```swift
var squared: [Int] = []
for fib in fibs {
squared.append(fib*fib)
}
squared

// is same
let squares =  fibs.map {  fib in  fib * fib }
squares 
```
```swift
extension Array {
func map<T>(_transform: (Element) -> T) -> [T] {
var result: [T] = []
result.reserveCapacity(count) //?
for x in self {
result.append(transform(x))
}
return result
}
}
```

## Parameterizing Behavior with Functions
- how to transform an element: `map` and `flatMap`
- should an element be included?: `filter`
- how to fold an element into an aggregate value: `reduce`
- what should the next element of the sequence be?: `sequence`
- what side effect to perform with an element: `forEach`
- in what order should two elements come?
: `sort, lexicographicallyPrecedes` and `partition`
- does this element match?: `index`, `first` and `contains`
- which is the min/max of two elements?: `min` and `max`
- are two elements equivalent?: `elementsEqual` and `starts`
- is this element a separator?: `split`

- `accumulate`: combine elements into an array of running values (like reduce, but returning an array of each interim combination)
- `all(matching:)` and `none(matching:)`: test if all or no elements in a sequence match a criterion (can be built with contains, with some carefully placed negation)
- `count(where:)`: count the number of elements that match (similar to filter, but without constructing an array)
- `indices(where:)` —return a list of indices matching a criteria (similar to index(where:), but doesn’t stop on the first one)
- pre x(while:)—filterelementswhileapredicatereturnstrue,thendroptherest (similar to  lter, but with an early exit, and useful for infinite or lazily computed sequences)
- drop(while:)—dropelementsuntilthepredicateceasestobetrue,andthen return the rest (similar to pre x(while:), but this returns the inverse)

## Mutation and Stateful Closures
- 배열을 반복할때 `side effects` (e.g. inserting the elements into some lookup table) 를 사용하면 안된다.

```swift
array.map { item in table.insert(item) } 
```
- `map` 보다는 `forEach` 가 적절


## Filter
-  조건에 만족하는 요소들만 포함하는 새로운 배열을 만듬
```swift
extension Array {
func filter(_ isIncluded: (Element) -> Bool) -> [Element] {
var result: [Element] = []
for x in self where isincluded(x) {
result.append(x)
}
return result
}
}
```

- [...].filter {...}.count > 0을 contains로 표현가능
- 아래로 표현하면 새로운 배열을 만들지 않고 해당 조건을 만족하는 요소를 만날시 바로 종료
```swift
bigArray.filter { someCondition }.count > 0 // someCondition을 만족하는 요소가 있냐
bigArray.contains { someCondition }
```

- 아래 코드는 모든 요소가 해당 조건을 만족하는지 검사
```swift
extension Sequence { // Sequence?
public func all(matching predicate: (Iterator.Element) -> Bool) -> Bool {
return !contains { !predicate($0) }
}
}
let evenNums = nums.filter { $0 % 2 == 0 }
evenNums.all { $0 % 2 == 0 } // true
```

## Reduce
- 배열의 모든 요소를 한 값으로 조합
```swift
for num in fibs {
total = total + num
}
total //12

// is same
let sum1 = fibs.reduce(0) { total, num in total + num }
let sum2 = fibs.reduce(0) { $0 + $1 }
let sum3 = fibs.reduce(0, +)
```

```swift
extension Array {
func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Elment) -> Result) -> Result {
var result = initialResult
for x in self {
result = nextPartialResult(result, x)
}
return result
} 
}
```

## A Flattening Map
- map vs flatMap
- map과 다르게 flatMap 옵션널과 nil이 넘어오지 않는다
```swift
let items: [Int?] = [1, 2, nil, 4, 5, nil, 7]

let mapItems = items.map { $0 }
mapItems // [{Some 1}, {Some 2}, nil, {Some 4}, {Some 5}, nil, {Some 7}]

let flatMapItems = items.flatMap { $0 }
flatMapItems // [1, 2, 4, 5, 7]
```

- 아래 예제는두 배열을 서로 join하면서 map
```swift
extension Array {
func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Elment) -> Result) -> Result {
var result = initialResult
for x in self {
result = nextPartialResult(result, x)
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
```

## Iteration using forEach
- for 루프와 거의 동일하게 작동
- 전달 된 함수는 시퀀스의 각 요소에 대해 한 번 실행
- map, filiter와 다르게 forEach는 아무 것도 반환하지 않음
- 단일 함수 호출 인 경우 편리

- *for loop vs for each*
- for loop: 모든 Iteration를 돌지 않을 수도 있음 (return, break에 의해)
- for each: 모든 Iteration를 돔
```swift
for element in [1,2,3] {
print(element)
}
// is same
[1,2,3].forEach { element in 
print(element)
}
```

- 아래 코드는 잘못된 코드, forEach문에 직접적으로 `where` 절을 쓸수 없다
```swift
extension Array where Element: Equatable {
func index(of element: Element) -> Int? {
for idx in self.indices where self[idx] == element { //indices?
return idx
}
return nil
}
}
```

- 아래 코드도 잘못된 코드, `forEach` 는 반환하지 않음,  closure 자신만 리턴
```swift
extension Array where Element: Equatable {
func index_foreach(of element: Element) -> Int? {
self.indices.filter {
self[idx] == element
}.forEach { idx in
return idx
}
return nil
}
}
```

- 잘못된 코드, 1~9사이의 모든 숫자 출력, closure를 리턴
```swift
(1..<10).forEach { number in 
print(number)
if number > 2 { return }
}
```

*Array Types*
-----------

## Slices
- 범위로 요소 범위에 액세스가능
- `ArraySlice`는 배열의 `view`이다.
```swift
let slice = fibs[1..<fibs.endlIndex]
slice // [1, 1, 2, 3, 5]
type(of: slice) // ArraySlice<Int>
```
## Bridging
- Swift Array 는 Objective-c로 전환 가능
- NSArray는 객체이기 때문에 `AnyObject` 타입으로 Swift로 변환 가능




