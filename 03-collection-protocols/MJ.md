# **Sequences**

- 계층 구조의 최 하단에 위치

- 값을 반복 할 수있는 동일한 유형의 일련의 값. 

- sequence를 traverse하는 가장 일반적인 방법은 for loop

  ```swift
  for element in someSequence {
    doSomething(with: element)
  }
  ```

- Sequence의 정의로부터 반복자에 대해 배울 수있는 유일한 것은 IteratorProtocol을 준수하는 유형이라는 것

  ```swift
  protocol Sequence {
    associatedtype Iterator: IteratorProtocol
    func makeIterator() -> Iterator
  }
  ```

  ​

  ​

  ## Iterators

  - Sequence는 iterator(반복자)를 생성하여 element에 대한 access를 제공.

  - iterator는 sequence의 값을 한 번에 하나씩 생성하고 sequence를 통과 할 때 반복 상태를 추적

  - **IteratorProtocol 프로토콜에 정의 된 유일한 메소드는 next ()**

  - next ()는 각 후속 호출에서 sequence의 다음 요소를 반환해야하며, sequence가 고갈되면 nil을 반환.

    ```swift
    protocol IteratorProtocol {
      associatedtype Element
      mutating func next() -> Element?
    }
    ```

  - associated Element type은 iterator가 생성하는 값의 유형을 지정

    - 예를 들어, String.CharacterView의 iterator의 요소 유형은 Character.

  - extension에 의해 iterator는 sequence의 요소 유형도 정의

  - Element가 IteratorProtocol의 assocatedtype이라는 사실 때문에 메서드 시그니처 또는 Sequence의 제네릭 제약에서 Iterator.Element에 대한 참조가 자주 나타나는 이유?????????????????????????????

  ​

  - 일반적으로 custom sequence type을 구현할 때는 iterator 만 신경쓰면 된다. 그 외에도 iteerator를 직접 사용할 필요는 거의 없다. 

    - Why? for loop는 sequence를 통과하는 관용적 방법이기 때문. 사실 이것은 for loop가 수행하는 것. 컴파일러는 sequence에 대한 새로운 iterator를 생성하고 iterator에서 nil이 반환 될 때까지 반복적으로 호출한다. 최 상단에 있는 for loop는 다음 코드에 대한 축약임.

      ```swift
      for element in someSequence {
        doSomething(with: element)
      }

      //이 녀석의 축약
      var iterator = someSequence.makeIterator()
      while let element = iterator.next() {
        doSomething(with: element)
      }
      ```

  - iterator는 단일 통과 구문이다. 그들은 한 방향으로만 진행할 수 있고 결코 반대로 가거나 reset 할 수 없다. 

  - 대부분의 iterator는 유한한 수의 요소를 생성하고 결국 next ()에서 nil을 반환하지만 무한한 시리즈를 뱉어내는 것을 멈추게하는 요소는 없다. 실제로, 상상할 수있는 가장 단순한 iterator (즉각적으로 nil을 반환하는) 는 같은 값을 반복해서 반환하는 iterator이다.

  ​

  ```swift
  struct ConstantIterator: IteratorProtocol {
    typealias Element = Int
    mutating func next() -> Int? {
      return 1
    }
  }

  struct ConstantIterator: IteratorProtocol { 
    mutating func next() -> Int? {
      return 1 
    }
  }
  ```

  - Element에 대한 명시적인 typalias는 선택 사항이다 
    - 그러나 더 큰 프로토콜에서, 특히 문서화의 목적으로는 유용. 
    - 이를 생략하면 컴파일러는 next ()의 반환 유형에서 구체적인 유형의 Element를 추론한다.
  - next () 메서드는 mutating으로 선언된다. 위의 단순한 예제에서는 iterator가 변경 가능한 상태가 아니기 때문에 엄격하게 필요하지 않지만, 실제적으로 iterator는 본질적으로 stateful하다. 거의 모든 유용한 iterator는 순서에서 그 위치를 추적하기 위해 가변 상태를 요구한다. ????????????????????????????????

  ​

  ```swift
  //우리는 ConstantIterator의 새로운 인스턴스를 생성하고 생성 된 시퀀스를 while 루프에서 반복하여 무한한 스트림을 출력 할 수 있다.
  var iterator = ConstantIterator()
  while let x = iterator.next() {
    print(x)
  }
  ```

  ​

  - FibsIterator는 피보나치 sequence를 생성한다. 

    - 다가오는 두 개의 숫자를 저장하여 sequenc의 현재 위치를 추적한다. 

    - 다음 메서드는 첫 번째 숫자를 반환하고 다음 호출의 상태를 업데이트한다. 

    - 이 iterator는 "무한"스트림을 생성한다. 

    - 정수 오버플로에 도달 할 때까지 숫자를 계속 생성하고 프로그램이 충돌합니다.

      ```swift
      struct FibsIterator: IteratorProtocol {
        var state = (0,1)
        mutating func next() -> Int? {
          let upcomingNumber = state.0
          state = (state.1, state.0 + state.1)
          return upcomingNumber
        }
      }
      ```

      ​

      ​

  ## Conforming to Sequence

  - 유한한 sequence를 생성하는 iterator의 예는, 문자열의 모든 접두어 (문자열 자체 포함)를 생성하는 하단의 PrefixIterator이다. 문자열의 시작 부분부터 시작하여 next를 호출 할 때마다 끝에 도달 할 때까지 한 문자 씩 반환하는 문자열 조각을 증가시킨다.

    ```swift
    struct PrefixIterator: IteratorProtocol {
      let string: String
      var offset: String.Index
      
      init(string: String){
        self.string = string
        offset = string.startIndex
      }
      
      mutating func next() -> String? {
        guard offset < string.endIndex else { return nil } //글자가 없는 경우 return nil
        offset = string.index(after: offset)
        return string[string.startIndex..<offset]
        //startIndex와 오프셋 사이의 하위 문자열을 반환하는 슬라이스 작업
      }
    }
    ```

    ​

  - PrefixIterator를 사용하면, 수반되는 PrefixSequence 형식을 쉽게 정의 할 수 있다. 

    - 컴파일러가 makeIterator 메서드의 반환 유형을 추론 할 수 있으므로 관련 반복자 유형을 명시적으로 지정하지 않아도 된다.

      ```swift
      struct PrefixSequence: Sequence {
        let string: String
        
        func makeIterator() -> PrefixIterator {
          return PrefixIterator(string: string)
        }
      }


      //사용하기

      for prefix in PrefixSequence(string: "Hello"){
        print(prefix)
      }
      /* 
      H
      He 
      Hel 
      Hell 
      Hello
      */

      PrefixSequence(string: "Hello").map { $0.uppercased() }
      // ["H", "HE", "HEL", "HELL", "HELLO"]
      ```

    ​

  - 피보나치 수열도 해보기

    ```swift
    struct FibsIterator: IteratorProtocol {
      var state: (Int,Int)
      var times: Int = 0
      var endIndex: Int
      
      init(state: (Int, Int), times: Int = 0, endIndex: Int) {
        self.state = state
        self.times = times
        self.endIndex = endIndex
      }
      
      mutating func next() -> Int? {
        guard times < endIndex else { return nil }
        let upcomingNumber = state.0
        state = (state.1, state.0 + state.1)
        self.times += 1
        return upcomingNumber
      }
    }

    struct FibsSequence: Sequence {
      let val: (Int,Int)
      let endIndex: Int
      
      func makeIterator() -> FibsIterator {
        return FibsIterator(state: val, endIndex: endIndex)
      }
    }

    for value in FibsSequence(val: (0,1), endIndex: 3) {
      print(value)
    }
    ```

  ​

  ​

  ## Iterator and Value Semantics

  - 지금까지 살펴본 iterator는 모두 copy by value이다. 복사본을 만들면 iterator의 전체 상태가 복사되고 두 인스턴스는 서로 독립적으로 동작한다. 
  - 표준 라이브러리의 대부분의 iterator는 copy by value이지만 예외도 있다.













