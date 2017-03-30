# Collection Protocols

## Sequences

- makeIterator()
```
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    func makeIterator() -> Iterator
}
```

### Iterators

- sequences 는 iterator를 생성함으로써 element에 접근한다.
- iterator는 한값을 정해서 이를 순서에 맞게 진행함
- IteratorProtocol 프로토콜에 정의되어있는 유일한 메소드는 next()뿐임.
- next()는 다음으로 넘어가거나 sequence가 없으면 nil을 리턴함

- 연관된 element 타입은 iterator가 생성한 값의 타입이 될 수 있다.
- ex. String.CharacterView의 iterator element 타입은 Character임.

- 자신만의 커스텀 sequences 타입을 실행할때 iterator를 신경써야함


```
struct ConstantIterator: IteratorProtocol { 
    mutating func next() -> Int? {
        return 1 
    }
}
```
next()함수가 mutating으로 선언 되었다.
iterator는 변할 수 있어야 하기 때문에 mutating이 필요하다.

ex. 
```
struct FibsIterator: IteratorProtocol { var state = (0, 1)
    mutating func next() -> Int? {
        let upcomingNumber = state.0 
        state = (state.1, state.0 + state.1)
        return upcomingNumber  
    }
}
```
피보나치 수열을 이다. 위의 경우에는 무한하게 next()를 생성하므로 overflow된다.

### Conforming to Sequence

유한한 순서를 생성하는 iterator
```
struct Pre xIterator: IteratorProtocol { 
    let string: String
    var offset: String.Index

    init(string: String) { 
        self.string = string
        offset = string.startIndex
    }

    mutating func next() -> String? {
        guard offset < string.endIndex else { return nil } offset = string.index(after: offset)
        return string[string.startIndex..<offset] 
        //start와 offset사이의 서브스트링을 반환
    } 
}
```

```
struct Pre xSequence: Sequence { 
    let string: String

    func makeIterator() -> Pre xIterator { 
        return Pre xIterator(string: string)
    }
}
```

```
for pre x in Pre xSequence(string: "Hello") { 
    print(pre x)
}
```

### Iterators and Value Semantics

- iterator도 모두 값이라서 복사되면 각각으로 작용함.

```
let seq = stride(from: 0, to: 10, by: 1) 
var i1 = seq.makeIterator()
i1.next() // Optional(0)
i1.next() // Optional(1)

vari2=i1

i1.next() // Optional(2) 
i1.next() // Optional(3) 
i2.next() // Optional(2) 
i2.next() // Optional(3)
```


- 값 형태가 아니게 쓰는 iterator도 있음
- AnyIterator
- 다른 iterator를 래핑함. 그래서 기본 iterator의 구체적인 유형을 지움.
```
var i3 = AnyIterator(i1) 
vari4=

i3.next() // Optional(4) 
i4.next() // Optional(5) 
i3.next() // Optional(6) 
i3.next() // Optional(7)
```
이 상황에서 값이 아닌 주소만 복사됨.

- 위와 같은 상황은 흔치 않음.
- loop 안에서 한번만 사용하고 쓴뒤에 날리는 것이 좋다.
- 공유하는 것은 좋지 않다.

### Function-Based Iterators and Sequences

- custom struct를 사용하는 iterator는 값 의미를 가지고 AnyIterator를 사용할 때는 그렇지 않다.

- AnySequence는 함수를 취하는 이니셜아니저를 생성하기 때문에 반복자를 생성하는 것이 더 쉽다.

```
let  bsSequence = AnySequence( bsIterator) Array( bsSequence.pre x(10)) // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

- 다른 대안책은 sequence 함수를 사용하는 것이다.
```
let randomNumbers = sequence( rst: 100) { (previous: UInt32 in 
    let newValue = arc4random_uniform(previous)
    guard newValue > 0 else {
        return nil
    }
    return newValue 
}
Array(randomNumbers) // [100, 91, 36, 13, 2, 1]
```

## Infinete Sequences

## Unstable Sequences

- 시퀀스는 배열이나 리스트 같은 고전 컬렌션 데이터 구조에는 제한이 없다.

- 피보나치 시퀀스는 다른 보통의 시퀀스와 같이 생성되지는않는다.

```
let standardIn = AnySequence { 
    return AnyIterator {
        readLine() 
    }
}
```
- 시퀀스를 확장해서 이렇게 쓸 수 있다.

```
let numberedStdIn = standardIn.enumerated()

for (i, line) in numberedStdIn { 
    print("\(i+1): \(line)")
}
```
- enumerated 메소드는 원래 시퀀스요소와 증가하는 수를 짝으로하는 시퀀스를 새로운 시퀀스로 감싼다.


## The Relationship Between Sequences and Iterators

- 안정적인 시퀀스는 for루프에 의해 변경되면 안된다.
- 모든 이터레이터는 불안정적인 시퀀스로 여겨질 수 잇다.
- conformance를 선언함으로써 이터레이터를 시퀀스로 바꿀 수 있다.

## Subsequences

```
protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    associatedtype SubSequence
    // ...
}   
```

- SubSequence는 원래 시퀀스를 나눌 때 return 타입으로 사용된다.
> prefix, suffix - 처음이나 마지막 n 요소들
> dropFirst, dropLast - 처음이나 마지막 n요소들이 지워진 곳의 서브시퀀스
> split - 특정 separator요소들에 시퀀스를 없애고 서브시퀀스의 배열을 리턴하는 것


# Collections

- Collection protocol은 Sequeuce위 위에 지어지며 Sequence로부터 메소드를 상속받는다.
- standard 라이브러리에는 큐가 없다. 연속적인 메모리에 배열이 저장되기 때문이다.

## Designing a Protocol for Queues

```
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

## A Queue Implementation

```
/// An ef cient variable-size FIFO queue of elements of type `Element`
struct FIFOQueue<Element>: Queue {  
    fileprivate var left: [Element] = []  
    fileprivate var right: [Element] = []

    /// Add an element to the back of the queue.
    /// - Complexity: O(1).
    mutating func enqueue(_ newElement: Element) {
        right.append(newElement) 
    }

    /// Removes front of the queue.
    /// Returns `nil` in case of an empty queue. 
    /// - Complexity: Amortized O(1). 
    mutating func dequeue() -> Element? {
        if left.isEmpty {
            left = right.reversed() 
            right.removeAll()
        }
        return left.popLast() 
    }
}
```
- 두개의 스택을 사용해서 큐를 구현



