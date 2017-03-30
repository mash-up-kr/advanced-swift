# Indices

- index : collection내의 위치를 나타냄

- 모든 collection은 **startIndex**와 **endIndex**를 가짐

  - startIndex : collection의 첫 element를 가리킴
  - endIndex : collection의 **마지막 element의 뒤에 오는** index
    - 따라서 endIndex는 **subscripting에 사용하기 적합하지 않음**
    - 인덱스 범위 (someIndex .. <endIndex)를 구성하거나 다른 인덱스를 비교할 수 있다 (예 : 루프의 중단 조건 (someIndex <endIndex 인 동안).

- collection의 index에 대한 유일한 요구 사항은 ***index가 정의 된 순서를 갖는다는 것***을 나타내는 또 다른 방법 인 ***Comparable이어야한다***

  - Dictionary의 경우, key는 index가 아니다(주어진 키 다음에 어떤 index가 오는지 말해줄 수 없으므로)

- As a result, DictionaryIndex is an opaque value that points to a position in the
  dictionary’s internal storage buffer. It really is just a wrapper for a single Int offset, but
  that’s an implementation detail of no interest to users of the collection. (In fact, the
  reality is somewhat more complex because dictionaries that get passed to or returned
  from Objective-C APIs use an NSDictionary as their backing store for efficient bridging,
  and the index type for those dictionaries is different. But you get the idea.)

  - **index로 Dictionary를 subscripting**하면 ***optional value를 return하지 않지만***

  - **key를 이용해 subscripting** 하면 ***optional value를 return*** 한다.

    ```swift
    struct Dictionary {
      subscript(key: Key) -> Value?
    }

    protocol Collection {
      subscript(position: Index) -> Element { get }
    }
    ```

    - dictionary의 element 타입은 tuple type.

  ​			
  ​		

  ## Index Invavlidation

  - collection이 변경되면 index가 invalid될 수 있다.

    - index는 유효하지만 다른 element를 처리하는 경우(element를 앞에 추가하는 경우)
    - index가 유효하지 않은 경우(첫 번째 element를 제거하면 마지막 index였던 index가 invalid된다.)
    - Dictionary는 key-value쌍이 추가되어도 index는 stable하다.
      - dictionary가 너무 커져서 reallocation을 유발하기 전까진
        (이는 버퍼의 크기를 조정해야만 요소가 삽입되어 모든 요소를 다시 해시해야하므로 사전의 저장소 버퍼에있는 요소의 위치가 변경되지 않기 때문.)

  - An index should be a dumb value that only stores the minimal amount of information
    required to describe an element’s position. In particular, indices shouldn’t keep a
    reference to their collection, if at all possible. Similarly, a collection usually can’t
    distinguish one of its “own” indices from one that came from another collection of the
    same type. Again, this is trivially evident for arrays. Of course, you can use an integer
    index that was derived from one array to index another:(인덱스는 요소의 위치를 설명하는 데 필요한 최소한의 정보 만 저장하는 벙어리 값이어야합니다. 특히, 가능하다면 지표는 그들의 컬렉션에 대한 참조를 유지하지 않아야한다. 마찬가지로 콜렉션은 일반적으로 "자체"색인 중 하나를 동일한 유형의 다른 모음에서 가져온 색인과 구별 할 수 없습니다. 다시 말하지만, 이것은 배열에 대해 명백하게 드러납니다. 물론 한 배열에서 파생 된 정수 인덱스를 사용하여 다른 인덱스를 인덱싱 할 수 있습니다.)

    ```swift
    let numbers = [1,2,3,4]
    let squares = numbers.map { $0 * $0 }
    let numbersIndex = numbers.index(of: 4)! // 3
    squares[numbersIndex] //16
    ```

    ​

​			
​		

	## Advancing Indices

- Swift 3은 컬렉션을 위해 인덱스 통과가 처리되는 방식을 크게 변경했습니다. 인덱스를 전방 또는 후방으로 전진시키는 (즉, 주어진 인덱스로부터 새로운 인덱스를 유도하는) 작업은 이제 콜렉션의 책임이며, 스위프트 2까지 인덱스는 스스로 전진 할 수 있습니다. 다음 색인으로 이동하기 위해 someIndex.successor ()를 작성한 곳에서 대신 collection.index (after : someIndex)를 작성할 수 있습니다.
- 이러한 변경은 ***성능***때문이다. 다른 index로부터 index를 도출하는 것은, 종종 collection의 내부에 대한 정보를 필요로한다는 것을 알게되었습니다. index를 전진시키는 것이 간단한 추가 연산 인 배열의 경우는 아닙니다. 예를 들어 문자열 색인은 실제 문자 데이터를 검사해야합니다. 문자는 Swift에서 크기가 다양하기 때문입니다.























​	