## Conforming to ExpressibleByArrayLiteral

이와 같은 컬렉션을 구현할 때 ExpressibleByArrayLiteral도 구현하는 것이 좋습니다. 
이렇게하면 사용자가 친숙한 [value1, value2, etc] 구문을 사용하여 대기열을 만들 수 있습니다. 
다음과 같이 쉽게 할 수 있습니다.

```swift
extension FIFOQueue: ExpressibleByArrayLiteral { 
	public init(arrayLiteral elements: Element...) { 
		self.init(left: elements.reversed(), right: [])
	}
}
```

우리의 큐 로직의 경우, 요소를 반대로하여 왼손 버퍼에서 사용할 준비가되어 있습니다. 
물론 요소를 오른쪽 버퍼에 복사 할 수는 있지만 요소를 복사하는 것이므로 나중에 역순으로 복사 할 필요가 없도록 역순으로 복사하는 것이 더 효율적입니다. 
이제 리터럴에서 대기열을 쉽게 만들 수 있습니다.

```swift
let queue: FIFOQueue = [1,2,3] // FIFOQueue<Int>(left: [3, 2, 1], right: [])
```

Swift에서 리터럴과 유형의 차이점을 강조하는 것이 중요합니다. 여기서 [1,2,3]은 배열이 아닙니다. 오히려 그것은 "배열 리터럴"입니다. ExpressibleByArrayLiteral을 준수하는 모든 유형을 만드는 데 사용할 수있는 무언가입니다. 이 특정 리터럴에는 다른 리터럴 (정수 리터럴)이 포함되어 있으며 ExpressibleByIntegerLiteral을 준수하는 모든 유형을 만들 수 있습니다.

이러한 리터럴에는 리터럴을 사용할 때 명시 적 유형을 지정하지 않으면 Swift에서 가정하는 "기본"유형이 있습니다. 따라서 배열 리터럴은 기본적으로 Array, 정수 리터럴 기본값은 Int이고, 부동 소수점 리터럴은 기본적으로 Double이며, 문자열 리터럴의 기본값은 String입니다. 그러나 이것은 사용자가 지정하지 않은 경우에만 발생합니다. 예를 들어 위에 선언 된 대기열은 정수형 대기열이지만 다른 정수 유형의 대기열 일 수 있습니다.

```swift
let byteQueue: FIFOQueue<UInt8> = [1,2,3] // FIFOQueue<UInt8>(left: [3, 2, 1], right: [])
```

종종 리터럴 유형은 컨텍스트에서 유추 할 수 있습니다. 예를 들어, 함수가 리터럴에서 생성 할 수있는 유형을 사용하는 경우 다음과 같이 표시됩니다.

```swift
func takesSetOfFloats( oats: Set<Float>) { 
//...
}
takesSetOfFloats( oats: [1,2,3])
```

이 리터럴은 Array <Int>가 아니라 Set <Float>로 해석됩니다.







## Associated Types

Collection은 관련 유형 중 하나를 제외한 모든 유형에 대해 기본값을 제공합니다. 프로토콜을 채택하는 유형은 색인 유형 만 지정하면됩니다. 다른 관련 유형을 많이 신경 쓸 필요는 없지만, 각각의 유형을 더 잘 이해하기 위해 각각을 간략하게 살펴 보는 것이 좋습니다. 하나씩 차례로 살펴 보겠습니다.
반복자. Sequence에서 상속됩니다. [이미 시퀀스에 대한 논의에서 반복자를 자세히 살펴 보았습니다](/MJ - Sequences). 컬렉션의 기본 반복기 유형은 IndexingIterator <Self>입니다. 이 컬렉션은 컬렉션을 래핑하고 컬렉션의 고유 인덱스를 사용하여 각 요소를 단계별로 처리하는 간단한 구조체입니다. 구현은 간단합니다.



/////////////////////////////////////////////////////////////////////////////

subsequence - generic
slice - type

































