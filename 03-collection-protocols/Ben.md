
# Collection Protocols

## Sequences
Sequence은 Array, Dictionary, Set과 같은 Collection 타입의 기반이 되는 프로토콜로 동일 타입의 values를 iterate 할 수 있는 일련의 값이다.

Sequence를 순회하는 가장 간단한 방법은 for loop를 통해서 사용할 수 있다.
```Swift
for element in someSequence {
    doSomething(with: element)
}
```

Sequence 프로토콜을 conform하는 것은 매우 심플하다. `makeItertator()`함수만 구현해주면 된다.

```Swift
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    func makeIterator() -> Itertator
}
```

### Ierators
Sequence는 iterator를 만듬으로써 요소의 접근을 제공한다. Iterator는 Sequence의 값을 한번에 하나씩 생성하고 Sequence를 통해 추적 할 때 반복 상태를 추적한다.
IteratorProtocol에 선언된 메소드는 오직 `next()`일 뿐이다. `next()`함수는 각 subsequence 호출에서 sequence의 다음 요소를 반환하며, sequence가 마지막에 다달으면 nil을 반환한다.

```Swift
protcol IteratorProtocol {
    associatedtype Element
    mutating func next() -> Element?
}
```

위 코드에서 assoricatedtype으로 정의된 Element는 iterator가 제공하는 값의 타입이다.
iterator를 주로 커스텀 시퀀스 타입을 만들거나, (드믈게) iterators 직접적으로 만들어서 접근한다. 

```Swift
var iterator = someSequence.makeIterator()
while let element = iterator.next() {
    doSometing(with: element)
}
```

Iterator는 절대로 순회시 reversed되거나 reset되지 않는다.
`next()`함수는 mutating function으로 선언되어 있다. 필수적으로 mutating 되야되는 것은 아니지만 대부분 유용한 iterator는 시퀀스의 포지션을 계속해서 트랙킹하기 위해 mutable state를 필요로한다.

피보나치 수열의 예)
```Swift
struct FibsIterator: IteratorProtocol {
    var state = (0, 1)
    mutating func next() -> Int? {
        let upcomingNumber = state.0
        state = (state.1, state.0 + state.1)
        return upcomingNumber
    }
}
```

### Conforming to Sequence

무한 시퀀스가 아닌 한정된 시퀀스를 만들어보자.
**PrefixIterator** struct는 문자열의 처음 부터 시작해서 String의 slice를 증가시켜 마지막에 도달할때 까지 문자열을 반환하는 기능을 제공한다.

```Swift
struct PrefixIterator: IteratorProtocol {
    let string: String
    var offset: String.Index
    
    init(string: String) {
        self.string = string
        offset = string.startIndex
    }
    
    mutating func next() -> String? {
        guard offset < string.endIndex else {
            return nil
        }
        offset = string.index(after: offset)
        return string[string.startIndex..<offset]
    }
}
```

**PrefixIterator**를 만들었으니 **PrefixSequence** 만드는 것은 간단하다. 

```Swift
struct PrefixSequence: Sequence {
    let string: String
    
    func makeIterator() -> PrefixIterator {
        return PrefixIterator(string: string)
    }
}
```

커스텀 시퀀스를 만들었으니 테스트를 해보자.

```Swift
for prefix in PrefixSequence(string: "Apple") {
    print(prefix)
}
/*
A
Ap
App
Appl
Apple
*/

PrefixSequence(string: "Apple").map { $0.uppercased() }
/*
["A", "AP", "APP", "APPL", "APPLE"]
*/
```

FibsIterator도 prefix 기능을 추가하여 다음과 같이 수정할 수 있다.
```Swift
struct FibsIterator: IteratorProtocol {
    var state = (0, 1)
    var initial = 0
    let prefix: Int
    
    init(prefix: Int) {
        self.prefix = prefix
    }
    
    mutating func next() -> Int? {
        guard initial < prefix else {
            return nil
        }
        initial += 1
        let upcomingNumber = state.0
        state = (state.1, state.0 + state.1)
        return upcomingNumber
    }
}

struct FibsSequence: Sequence {
    let prefix: Int
    
    func makeIterator() -> FibsIterator {
        return FibsIterator(prefix: prefix)
    }
}

for i in FibsSequence(prefix: 10) {
    print(i)
}
```

### Iterators and Value Semantics
대부분의 Itertator는 값 타입이기 때문에 참조가 아닌 복사된다. 하지만, **AnyIterator**의 경우 다른 Iterator로 감싸져 있다. 그리고, Base Iterator의 값 타입 속성을 지워버린다.

```Swift
let seq = stride(from: 0, to: 10, by: 1)
var i1 = seq.makeIterator()
i1.next() // 0
i1.next() // 1

var i2 = i1

i1.next() // 2
i1.next() // 3
i2.next() // 2
i2.next() // 3

var i3 = AnyIterator(i1)
var i4 = i3

i3.next() // 4
i4.next() // 5
i3.next() // 6
i4.next() // 7
```

### Function-Based Iterators and Sequences
**AnyIterator** 타입은 next의 구현을 클로져로 받는 생성자를 갖고 있다. 즉, 커스텀 시퀀스, Iterator타입을 만들지 않고 구현이 가능하다.

```Swift
func fibsIterator() -> AnyIterator<Int> {
    var state = (0, 1)
    return AnyIterator {
        let upcomingNumber = state.0
        state = (state.1, state.0 + state.1)
        return upcomingNumber
    }
}
```

위 코드를 보면 state를 내부적으로 capturing하여 매 iterate가 될때 마다 값을 변경한다.

Sequence도 `sequence(first: next:)` 함수를 통해 만들 수 있다.

```Swift
let randomNumbers = sequence(first: 100) { (previous: UInt32) in
    let newValue = arc4random_uniform(previous)
    guard newValue > 0 else {
        return nil
    }
    return newValue
}

Array(randomNumbers)

let fibSequence2 = sequence(state: (0, 1)) { (state: inout(Int, Int)) -> Int? in
    let upcomingNumber = state.0
    state = (state.1, state.0 + state.1)
    return upcomingNumber
}

Array(fibSequence2.prefix(10))
```

> sequence(first:next:), sequence(state:next:) 함수의 반환형이 UnfoldSequence다. 이 용어는 함수형 프로그래밍에서 비롯되며 동일한 작업을 종종 unfold라는 용어로 불려진다. 시퀀스는 reduce(프로그래밍 언어에서 종종 fold라고 불러지는)의 대응 물이다. reduce가 시퀀스를 단일 반환 값으로 reduces(또는 fold)할 경우 시퀀스는 단일 값을 unfolds하여 시퀀스를 생성함 (무슨말이지)

### Unstable Sequence
Sequence는 특정 타입에 제한을 두지 않기 때문에 네트워크 스트림, 디스크 파일, UI Events 스트림 등등 많은 데이터가 Sequence의 역할을 할 수 있다.
그러나 elemet를 한번 이상 반복 할 때는 모든 동작이 배열처럼 작동하는 것은 아니다.

네크워트 패킷 스트림과 같은 Sequence는 순회에 의해 소비된다. 다른 iteration을 수행해도 동일한 값을 다시 생성하지 않는다. (하지만 둘 다 유효한 시퀀스 이다.) Document에 의하면 시퀀스는 여러 순회에 대한 보장을 하지 않는다는게 명확히 표시되어 있다.

하지만, Collection 프로토콜을 conform하게 되면 시퀀스는 안정적이다. 하지만 역은 성립하지 않는다.

### Subsequences
Sequence는 **Subsequence**라는 이름으로 assicoated type이 또 하나 존재한다.

```Swift
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    associatedtype SubSequence
}
```

**Subsequence**는 오리지널 sequence의 slices를 반환한다.
 - **prefix** and **suffix** -> take the first or last n elements
 - **dropFirst** and **dropLast** -> return subsequences where the first or last n elements have been moved
 - **split** -> break up the sequence at the specified separator elements and return an array of subsequences

 

 ## Collections
 Collection은 위에서 본거 처럼 stable sequence를 보장한다. 그래서 여러번 순회를 하더라도 값이 파괴되지 않는다. 
 추가적으로, Collection의 value는 subscript를 사용하여 인덱스로 접근이 가능하다. Subscript indices는 종종 Int지만 Indices는 불명확한 값(ex. dictionaries, strings)로도 사용된다. Collection의 indices는 시작과 끝을 가지고 한정된 범위에서 변함이 없기 때문에 Sequence와는 다르게 무한하지 않다.

 Collection 프로토콜은 Sequence프로코톨을 기반으로 한다. Sequence로 부터 함수를 상속받는다. 거기에 Collection은 특정 포지션이나 stable iterator를 지원하는 추가적인 메소드를 제공한다.

 만약, Collection의 모든 기능이 필요하지 않더라도, Sequence 대신에 Collection을 사용하는 것이 좋다. (사용자들에게 한정되고 multi-pass iteration을 보장시켜주는 것이 좋기 때문에)

 또한, Collection 프로토콜을 conform하는 것이 까다롭지만 (포지션을 정하는 것) Swift team은 **protocol for multi-pass sequence**를 피하는 것을 제안한다.

 ### Designing a Protocol for Queue

 ```Swift
 /// A type that can `enqueue` and `dequeue` elements.
protocol Queue {
    
    /// The type of elements held in `self`.
    associatedtype Element
    
    /// Enqueue `element` to `self`.
    mutating func enqueue(_ newElement: Element)
    
    /// Dequeue an element from `self`.
    mutating func dequeue() -> Element?
}
 ```

 위의 Queue프로토콜을 살펴보자. 몇가지 특징이 있다.
  - 시간 복잡도가 없다 : Queue 프로토콜을 상속받는 쪽에서 사용하는 방식에 따라 바뀔 수 있다.
  - peek 함수가 없다 : Queue 프로토콜은 network call이나 operating system에서 사용될 수 있기 때문에, 오직 pop만 존재한다.
  - FIFO or LIFO? : Queue 프로토콜을 상속받는 쪽에서 사용하는 방식에 따라 바뀔 수 있다. (ex. enqueue를 Array의 `append`함수로 구현하게 되면 FIFO)

  ### A Queue Implementation

  ```Swift
  /// An efficient variable-size FIFO queue of element of type `Element`
struct FIFOQueue<Element>: Queue {
    
    fileprivate var left: [Element] = []
    fileprivate var right: [Element] = []
    
    /// Add an element to the back of the queue
    /// - Complexity: O(1)
    mutating func enqueue(_ newElement: Element) {
        right.append(newElement)
    }
    
    /// Removes front of the queue
    /// Returns `nil` in case of an empty queue.
    /// - Complexity: Amortized O(1)
    mutating func dequeue() -> Element? {
        if left.isEmpty {
            left = right.reversed()
            right.removeAll()
        }
        return left.popLast()
    }
}
  ```

  두개의 스택을 이용하여 큐를 구현한 예제다.
  여기에서 `dequeue`함수를 보면 `right.reversed()` 구문으로 시간 복잡도가 O(n)이 될수도 있지만 comment에는 Amortized O(1)로 표시되어 있다. 

  ### Comforming to Collection
  이제, FIFOQueue를 Collection 프로토콜에 conform하게 만들어보자.
  그전에, Collection 프로토콜 구조를 살펴보자. (4개의 associatedtype이나 존재한다;;)

  ```Swift

protocol Collection: Indexable, Sequence {
    
    associatedtype Iterator: IteratorProtocol = IndexingIterator<Self>
    associatedtype SubSequence: IndexableBase, Sequence = Slice<Self>
    associatedtype IndexDistance: SignedInteger = Int
    associatedtype Indices: IndexableBase, Sequence = DefaultIndices<Self>
    
    var first: Iterator.Element? { get }
    var indices: Indices { get }
    var isEmpty: Bool { get }
    var count: IndexDistance { get }
    
    func makeIterator() -> Iterator
    func prefix(through position: Index) -> SubSequence
    func prefix(upTo end: Index) -> SubSequence
    func suffix(from start: Index) -> SubSequence
    func distance(from start: Index, to end: Index) -> IndexDistance
    func index(_ i: Index, offsetBy n: IndexDistance) -> Index
    func index(_ i: Index, offsetBy n: IndexDistance, limitBy limit: Index) -> Index?
    
    subscript(position: Index) -> Iterator.Element { get }
    subscript(bounds: Range<Index>) -> SubSequence { get }
}
  ```

  위의 Collection Protocol 구조를 보면 위에서 얘기한거 처람 4개의 associatedtype이 존재하지만 다행이도(?) 4개 모두 default가 있을므로 구현해주지 않아도 된다. 또한, function, property, subscript에도 동일하게 기본구현(default implementation)을 지원함으로 구현해주지 안하도 된다.

  문서를 보면 Collections 프로토콜을 conform 할 때 추가해 줘야 구현해줘야 하는 값들의 구조(이상적)는 다음과 같다.

  ```Swift
  protocol Collection: Indexable, Sequence {
    
    /// A type that represents a position in the collection. 
    associatedtype Index: Comparable
    
    /// The position of the  rst element in a nonempty collection.
    var startIndex: Index { get }
    
    /// The collection's "past the end" position---that is, the position one /// greater than the last valid subscript argument.
    var endIndex: Index { get }
    
    /// Returns the position immediately after the given index.
    func index(after i: Index) -> Index
    
    /// Accesses the element at the speci ed position. 
    subscript(position: Index) -> Element { get }
}
  ```

  이제 FIFOQueue를 Collection 프로토콜을 따르게 수정해보자.

  ```Swift
  extension FIFOQueue: Collection {
    
    var startIndex: Int { return 0 }
    var endIndex: Int { return left.count + right.count }
    
    func index(after i: Int) -> Int {
        precondition(i < endIndex)
        return i + 1
    }
    
    subscript(position: Int) -> Element {
        precondition((0..<endIndex).contains(position), "Index out of bounds")
        if position < left.endIndex {
            return left[left.count - position - 1]
        } else {
            return right[position - left.count]
        }
    }
}
  ```

  Collection 프로토콜의 기능을 구현해주었으므로 사용예는 다음과 같다.

  ```Swift
  var q = FIFOQueue<String>()

for x in ["1", "2", "foo", "3"] {
    q.enqueue(x)
}

for s in q{
    print(s, terminator: " ")
}//12foo3

var a = Array(q) // ["1", "2", "foo", "3"] 
a.append(contentsOf: q[2...3]) // ()

q.map { $0.uppercased() } // ["1", "2", "FOO", "3"] 
q.flatMap { Int($0) } // [1, 2, 3]

q.filter { $0.characters.count > 1 } // ["foo"]
q.sorted() // ["1", "2", "3", "foo"] 
q.joined(separator: " ") // 1 2 foo 3

q.isEmpty // false 
q.count // 4
q.first // Optional("1")
  ```

  ### Conforming to ExpressibleByArrayLiteral
  만든 커스텀 타입이 Collection Protocol을 conform 하게되면 **ExpressibleByArrayLiteral** 프로토콜도 conform하는 것이 좋다.

  ```Swift
  extension FIFOQueue: ExpressibleByArrayLiteral {
    
    public init(arrayLiteral elements: Element...) {
        self.init(left: elements.reversed(), right: [])
    }
}
  ```
  위의 구현으로 우리는 빈 Queue가 아닌 left 사이드에 버퍼를 두어 Queue를 생성 할 수 있다.

  ```
  let queue: FIFOQueue = [1,2,3] // FIFOQueue<Int>(left: [3, 2, 1], right: [])
  ```

  ### Associated Types
  Collection 프로토콜은 4개의 associatedtype이 존재하지만, conform 할 때 **Index** associatedtype 이것만 구현해주었다 (심지어 타입추론으로 생략이 가능하다). 나머지는 기본구현으로 구현하지 않아도 되었었다.

  다른 associatedtype을 사용하지 않더라도 내부 구현은 알아둘 필요가 있다. 
  
  - **Iterator** : 위에서 봤듯이 Sequence로 부터 상속된다. default iterator type은 **IndexingIterator<Self>**다. 이것은 매우 간단한 struct다. 기본구현을 살펴보자.

  ```Swift
  struct IndexingIterator<Elements: IndexableBase>: IteratorProtocol, Sequence {
    
    private let _element: Elements
    private var _position: Elements.Index
    
    init(_ elements: Elements) {
        self._element = elements
        self._position = elements.startIndex
    }
    
    mutating func next() -> Elements._Element? {
        guard _position < _element.endIndex else {
            return nil
        }
        let element = _element[_position]
        _element.formIndex(after: &_position)
        return element
    }
}
  ```

  - **SubSequence** : 마찬기지로 **Sequence**로 부터 상속된다. 기본구현은 **Slice<Self>**다.

  - **IndexDistance** : 부호가 있는 정수로, 두개의 indices 사이에서 단계별 number을 표현한다. 기본구현은 **Int**다. (별 일 없으면 수정 할 필요 없음)

  - **Indices** : return 타입은 Collection의 **indices** 프로퍼티다. 오름차순으로 정렬된 base collection의 subscripting을 위한 유효한 첨자를 표현한다. 따라서 **endIndex**는 포함되지 않는다 (저명하게 subscripting에서 사용 할 수 없기 때문) 반환형의 타입은 Range<Int>에서 DefaultIndices<Self>로 수정되었다.

  ```Swift
  extension FIFOQueue: Collection { ...

      typealias Indices = CountableRange<Int> 
      
      var indices: CountableRange<Int> {
          return startIndex..<endIndex 
      }
    }
  ```
