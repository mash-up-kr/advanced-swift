# Built-In Collections

elements의 **Collections**는 프로그래밍 언어에 있어 가장 중요한 데이타 타입이다.

이번 챕터에서 우리는 주요 컬렉션 타입에 대해 살펴보고 이를 효율적으로 다루는 방법에 초점을 맞출 것이다.


## Arrays

### Arrays and Mutablily

**Arrays**는 Swift에서 가장 흔한 컬렉션이다. 한 집합은 각 element에 무작위로 접근하는 같은 종류의 요소들의 container이다.

```
let fibs = [0, 1, 1, 2, 3, 5] 
// 집힙은 let으로 선언하면 constant이기 떄문에 변경불가능.

var mutableFibs = [0, 1, 1, 2, 3, 5]
//변경가능
mutableFibs.append(8)
mutableFibs.append(contentsOf:[13,21])
mutableFibs
```

var과 let사이를 구별하는데는 많은 이점이 있다.
let으로 선언된 변수는 결코 변하지 않는 다는 것을 쉽게 확인할 수 있다. 그러나 이는 value semantic일 경우이다.
class instance(i.e. a reference type)에 let변수는 reference가 결코 바뀌지 않다는 것을 의미하지 않는다.
reference는 바뀌지 않지만 참조하는 변수는 바뀔수 있다.

Arrays는 변수의 의미를 가지고 있다. 그래서 array를 다른 변수에 넣어도 값이 복사가 되어 복사한 변수가 변해도 복사된 변수에는 영향을 미치지 않는다.

NSArray는 바뀌지 않는 메소드이다. array를 바꾸기 위해서는 NSMutableArray가 필요하다.
그러나 NSArray reference는 array값 자체가 변하지 않는다는 것을 의미하지는 않는다.

## Arrays and Optionals

- **array 수만큼 반복** : for x in array

- **첫번째 빼고 array 반복** : for x in array.dropFirst()

- **5번째 빼고 array 반복** : for x in array.dropLast(5)

- **array에 있는 elements의 수만큼 반복** : for(num, element) in collection.enumberated()


## Transforming Arrays

### Map

값을 배열로 바꾸는 경우가 많이 있다. 이를 편히 쓰기 위해 **map**을 제공한다.

```
let squares = fibs.map {
    fib in fib * fib
}
squares // [0,1,1,4,9,25]
```

**map**에 익숙해지면 간결하고, 명확해지며, 잡음이 사라진다.

또한, **map**은 element를 변경할 필요도 없기 때문에 상수로 선언하여도 사용할 수 있다. 


### Parameterizing Behavior with Functions

- map flatMap
- filter
- reduce
- sequence
- forEach
- sort, lexicographicallyPrecedes partition
- index firsst contains
- min max
- elementsEqual starts
- split

이외의 standard library에 없는 있으면 좋은 함수들이 여러 개 있다.
이들을 만들어서 샤용하면 실수를 할 확률이 적고 가독성도 높아진다.


### Mutation and Stateful Closures

```
array.map { item in
    table.insert(item)
}
``` 
이는 부작용을 낳는다. map보다 for문이나 forEach를 쓰는것이 더 적절하다. 

closure local state를 통해 유용하게 사용할 수 있다. 아래는 이를 이용한 accumulate함수이다.

```
extension Array {
    func accumulate<Result>(_initialResult: Result,
    _nextPartialResult: (Result, Element) -> Result) -> [Result]
    {
        var running = _initialResult
        return map { next in
            running = nextPartialResult(running, next)
            return running
        }
    }
}
```
이는 변수를 만들어서 값을 저장한 뒤 map을 이용하여 배열을 만든다. 아래와 같이 사용할 수 있다.

```
[1,2,3,4].accumulate(0, +) //[1,3,6,10]
```

### filter

어떤 상태와 맞는 요소를 포함하는 배열을 생성하는 operation이다.

```
nums.filter{num in num % 2 == 0}
```

```
(1..<10).map { $0 * $0}.filter{$0 % 2 == 0} //[4, 16, 36, 64]
```

### Reduce

모든 element를 결합하여 새로운 값을 원할때 이렇게 사용한다.

```
var total =0

for num in fibs {
    total = total + num
}
total //12
```
이를 아래와 같이 사용할 수 있다.

```
let sum = fibs. reduce(0){ total, num in total + num} //12
```
0 : 초기값
total : 결합하는 중간 변수
num : element

```
fibs.reduce(0,+) //12
```

### A Flattening Map

다른 종류의 두 배열을 합쳐 준다.

```
let num = [1, 2]

let alpha = ["a", "b", "c"]

let result = num.flatMap { num in
    alpha.map { alpha in
        (num, alpha)
    }
}
//[(.0 1, .1 "a"), (.0 1, .1 "b"), (.0 1, .1 "c"), (.0 2, .1 "a"), (.0 2, .1 "b"), (.0 2, .1 "c")]
```

### Iteration using forEach

for 루프와 매우 유사하다. 각 element를 순서대로 한번씩 실행하고 return하지 않는다.
하지만 for 문과 forEach는 미묘하게 다르다.

```
extension Array where Element: Equatable {
    func index(of element: Element) -> Int? {
        for idx in self.indices where self[idx] == element {
            return idx
        }
        return nil
    }
}
```
이는 where가 forEach에 그대로 쓰일수 없기 때문에 아래와 같이 사용해야 한다.

```
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

forEach 안에 있는 return은 function 값을 return하는 것이 아니라 closure그 자체를 return 한다.


## Array Types

### Slices

```
let slice =  fibs[1..< bs.endIndex]
slice//[1,1,2,3,5]
type(of: slice) // ArraySlice<Int>
```

ArraySlice는 배열의 view이다.
마치 진짜 배열인 것처럼 보여진다.
배열의 일부분을 나타낼 때 사용한다.
실제로 배열을 복사할 필요는 없으며 배열과 같이 사용하면 된다.

### Bridging

Swift의 배열은 Objective-C로 넘어갈 수 있다. 
NSArray는 객체로 유지할 수 있기 때문에 AnyObjet와 양립할 수 잇어야 한다.
변수 타입도 Objective-C로 복사될 수 있다.


