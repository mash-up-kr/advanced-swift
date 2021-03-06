# Advanced Swift page 70 ~
## Indicies
1. startIndex는 First Element의 Index를 가리키지만 endIndex는 Last Element 다음의 인덱스를 가리킨다. 예를들어 배열의 count가 5라면 startIndex는 0, endIndex는 5 -> 따라서 실제 사용시에는 Range를 활용하여 (startIndex..<endIndex) 다음과 같이 이용하면 된다.
2. Array에서는 Integer를 인덱스로 활용하며 우리의 FIFOQueue도 동일하다. 또한, Integer를 활용한 인덱스는 굉장히 직관적이다 그러나 그것이 단 하나의 옵션이 될 수는 없다. 
3. Collection의 인덱스가 되기 위한 유일한 조건은 Comparable 이다. 다른 말로 하면 인덱스가 order를 정의할 수 있냐의 문제이다.
4. 딕셔너리를 살펴보면 그것의 key 값이 인덱스가 될 수 있는 후보이다. key는 딕셔너리 내부에 있는 값의 주소를 가리키는 것으로 사용될 수 있으나, 결과적으로 key는 인덱스가 될 수 없다. -> 왜나하면 주어진 key로는 다음에 올 index가 무엇인지 알 수가 없기 때문이다. 
5. 또한, 인덱스를 subscripting 하는 것은 hashing이나 detour 없는 직접적인 Element 접근을 기대한다.
6. 딕셔너리에서 Key를 통해 가져오는 것은 Optional을 반환할 수 있다, 그러나 index를 통해서 Element를 불러올 경우에는 Non-Optional을 반환한다. -> 왜냐하면, index를 잘못 불러오는 것은 프로그래머의 책임이라고 판단하기 때문이다. 

### Index Invalidation

1. 인덱스는 컬렉션이 조작될 경우 invaild 상태가 될 수 있다. 
2. Invalidation은 인덱스는 vaild지만 다른 Element를 가리키고 있거나 인덱스가 더 이상 vaild 하지 않고 이것을 사용해 collection에 접근할 경우 문제를 발생시킬 수 있는 것을 의미한다. 
3. Array의 경우를 생각해보면 되는데 Array의 첫 번째 배열을 삭제하면 마지막 요소를 가리키는 인덱스는 invaild 상태가 된다. 반면에 그보다 작은 인덱스들은 vaild 상태로 남아있지만, 가리키는 Element가 바뀌게 된다. 
4. 딕셔너리 인덱스는 새로운 key-value 쌍이 계속해서 추가되어 딕셔너리 공간의 재할당이 발생하더라도 안정적인 상태로 남아있게 된다. -> 이것은 딕셔너리 스토리지 버퍼 내의 요소의 위치가 변하지 않기 때문이다. ( 딕셔너리에서 요소를 삭제하는 것은 인덱스를 무력화시킨다. )
5. 인덱스는 요소의 위치를 묘사하는 최소한의 정보만 담고있는 값이어야만 한다. 
6. 가끔 인덱스는 그 자신의 컬렉션을 가리키지 않을 때도 있다.  -> 컬렉션이 자신의 인덱스인지 아닌지 구분할 수가 없기 때문이다. -> 좋은 아이디어는 아니다. -> 만약 다른 컬렉션의 인덱스를 사용하는 것을 빈 String에 사용할 경우 프로그램은 오류로 인해 종료되기마련이다. 왜냐하면, out of bounds이기 때문이다. 

### Advancing Indicies 

1. Swift3는 컬렉션을 위한 인덱스 순회에서 주요한 변화를 소개하였다.  -> 인덱스를 forward 혹은 backward 하는 작업의 책임은 컬렉션에 있다. 그러나, Swift2 까지 인덱스는 그 자체로 advance 되어야했다. 
2. 스위프트는 퍼포먼스를 위해서 이러한 변화를 감행했다. 

### A Linked List

1. integer 인덱스를 가지고 있지 않는 컬렉션의 예제를 살펴보자. -> a singly linked list의 구현을 살펴보자. -> 이것을 위해서는 우리는 자료 구조를 indirect enum과 같은 것을 사용하여 다른 방법으로 먼저 증명해야한다. 
2. linked list의 노드는 두개의 것 중 하나이다. -> 다음 노드의 값과 주소, 또는 리스트의 마지막을 가리키는 노드이다. 
3. 여기서 indirect 키워드의 사용은 컴파일러는 이 값을 주소로써 다뤄야한다는 것을 가리킨다. 
4. 스위프트의 enum은 value 타입이다. -> 이것은 변수가 값의 위치를 가리키는 주소를 가지고 있다기 보다 그들이 그들의 값을 변수에 직접적으로 가지고 있다는 것을 의미한다. 
5. 이는 많은 장점을 보유하고 있다, struct와 class 챕터에서 볼 예정이다. -> 하지만 이것은 그들이 그들 스스로 주소를 포함할 수 없다는 것을 의미하기도 한다. 
6. indirect 키워드는 enum case가 주소와 그 자체의 주소르 보유할 수 있도록 만들어준다.
7. List는 immutable 해서 영구적으로 보존된다 -> 그렇다면 어떻게 List에서 조작가능한 메서드를 만들 수 있을까? -> List 자체를 변형하는 것이 아니라 해당 List 를 담은 변수를 조작하는 것이다. -> 이것은 변수와 값의 차이를 보여주게 된다. 
8. 리스트의 노드들은 값이다 -> 따라서 그들은 변할 수가 없다. 
9. 반면에, 변수들은 그들이 가지고 있는 값을 변화시킬 수 있다. -> 그것은 무작위의 노드 혹은 값을 가리키는 간접적인 주소의 값을 가지게 할 수 있다. -> 그러나 바뀌는 것이 그것의 실제 값은 아니다 -> 단지 가리키는 노드를 바꾸는 것 뿐이다.
10. 당신의 변수를 var이 아닌 let으로 선언해도 상관 없다. -> 그 변수는 constant가 되겠지만 let은 변수에 해당할 뿐이다, 값이 아니라. -> 값은 정의에 의해 상수이다. 
11. Swift는 ARC를 사용하기 때문에 메모리가 필요가 없을 경우, 다시 말해 아무도 그 변수를 참조하지 않을 경우 해제한다. 

### Conforming List to Sequence 

1. list 변수들은 list 내부를 순회할 수 있기 때문에, 이것은 List가 Sequence를 conform할 수 있다는 것을 의미한다. -> List 는 안정적이지 않은 그 순회 상태를 가지고 있는 안정적이지 않은 Sequence이다. 
2. 우리는 IteratorProtocol과 Sequence의 conformance를 추가할 수 있다, next() 메서드를 추가함으로서.
3. 이것은 protocol extension의 힘으로써, 우리는 List를 여러개의 Standard Library Function과 함께 사용할 수 있다는 것을 의미한다.

### Conforming List to Collection

1. Collection을 conform하는 List를 만들어볼 차례인데, 우리는 List의 index type을 결정해야한다. 
2. 최고의 인덱스 타입은 주로 Collection 공간의 오프셋을 나타내는 간단한 integer type이다. -> 그러나 그것은 이번 경우에는 연속되는 공간이 존재하는 것이 아니라서 정상적으로 동작하지 않는다. -> 따라서, integer 기반의 인덱스는 접근할 경우에 startIndex부터 계속 찾아야하기 때문에 O(n)의 시간 복잡도를 가진다.
3. 그러나, Collection을 정의하고 있는 문서에서는 O(1)을 요구한다. -> 왜냐하면 많은 Collection 연산자들이 O(1)에 근거하고 있기 때문이다.
4. 결과적으로 우리의 인덱스는 List를 직접적으로 가져올 수 있어야한다. -> 이것은 퍼포먼스의 문제가 아니라 리스트가 변경 불가능하면 copy-on-write 최적화 기법을 사용하지 못하기 때문이다. 
5. 우리는 이미 List를 iterator를 사용해 직접적으로 사용해봤기 때문에, 같은 방식으로 사용해야할 것 같고 인덱스로써 enum을 사용해야할 것 같다. -> 그러나 이것은 문제를 야기시키는데, 예를들어, 인덱스와 컬렉션이 `==` 에 대해서 다른 구현 방식을 가져야한다. 
 - 인덱스의 경우 같은 리스트 내에서 같은 위치인지 알아야한다. -> 이것은 Equatable을 conform할 필요가 없다. 
 - 반면에, collection은 두 리스트가 같은 요소들을 가지고 있는지 비교해야하기 때문에 Equatable을 conform해야한다. 
6. 













