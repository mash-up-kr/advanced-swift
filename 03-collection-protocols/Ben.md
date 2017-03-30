
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

 