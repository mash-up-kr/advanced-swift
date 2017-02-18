# Arryas

### Arrays and Mutability

* `Array`의 특징

  * 배열은 모두 같은 타입의 요소(element)를 가짐
  * 배열의 요소 간에는 순서가 있음
  * 각 요소에 index를 통해 무작위로 접근(random access)할 수 있음

* `Array`를 포함해 swift 표준 라이브러리의 모든 collection 타입은 value type 이다.

  * `let`으로 선언하면 배열의 요소를 수정하거나 추가/삭제할 수 없다.

    * ```swift
      let immutableFibs = [0, 1, 1, 2, 3, 5]
      immutableFibs.append(8) // compile error!
      ```

  * `var`로 선언하면 배열의 요소를 수정하거나 추가/삭제할 수 있다.

    * ```swift
      let mutableFibs = [0, 1, 1, 2, 3, 5]
      mutableFibs.append(8) // available!
      ```

  * 다른 변수에 할당하거나 함수의 인자로 넘기는 경우 배열의 모든 내용이 복사된다.

    * ```swift
      var x = [1, 2, 3]
      var y = x
      y.append(4)
      y // [1, 2, 3, 4]
      x // [1, 2, 3]
      ```

    * 복사가 많이 일어나면 성능에 문제가될 가능성다. 하지만 swift 표준 라이브러리는 copy-on-write로 구현되어 있기 때문에  `y`의 요소가 수정될 때 실제 메모리 복사가 일어난다.

* `Foundation`의 `NSArray`와 `NSMutableArray`

  * `NSArray`
    * 배열의 요소 수정 불가능
  * `NSMutableArray`
    * 배열의 요소 수정 가능

### Arrays and Optionals

* 배열의 유용한 기능

  * `isEmpty`

  * `count`

  * Subscripting을 통해 특정 index에 직접 접근

    * ```swift
      fibs[3]
      ```

    * 배열의 길이를 벗어나는 index로 배열의 요소에 접근하는 경우 에러가 난다. 따라서 index로 직접 접근하는 경우는 드물다.

      * 배열의 모든 요소를 순회하고 싶은 경우

        * ```swift
          for x in array
          ```

      * 배열의 첫 번째 요소를 제외한 모든 요소를 순회하고 싶은 경우

        * ```swift
          for x in array.dropFirst()
          ```

      * 배열의 마지막 5개 요소를 제외한 모든 요소를 순회하고 싶은 경우

        * ```swift
          for x in array.dropLast(5)
          ```

      * 배열의 모든 요소를 index와 함께 순회하고 싶은 경우

        * ```swift
          for (index, element) in collection.enumerated()
          ```

      * 특정 요소의 index를 찾고 싶은 경우

        * ```swift
          if let index = array.index { someMatchingLogic($0) }
          ```

      * 배열의 모든 요소를 변환하고 싶은 경우

        * ```swift
          array.map { someTransformation($0) }
          ```

      * 특정 조건과 일치하는 배열의 요소를 얻고 싶은 경우

        * ```swift
          array.filter { someCriteria($0) }
          ```

    * Swift 3에서 전통적인 스타일의 for 문을 없앤 것에서 배열에 접근할 때 index로 직접 접근하는 경우를 지양함을 할 수 있다.

  * 빈 배열에 대해서 `first`, `last`, `popLast`는 `nil` 값을 반환

  * 이에 반해 빈 배열에 대해 `removeLast`를 호출하면 에러가 난다.

### Transforming Arrays

###### Map

* `map`은 배열의 모든 요소를 변환할 때 사용한다.

* 함수형 프로그래밍을 적용한 것이다.

* `map`의 장점

  * 더 짧다.
  * 더 깨끗하다.
  * 무엇을 하는지 명확하게 나타낸다.

* `map`함수는 다음과 같이 구현할 수 있다.

  * ```swift
    extension Array {
      func map<T>(_ transform: (Element) -> T) -> [T] {
        var result: [T] = []
        result.reserveCapacity(count)
        for x in self {
          result.append(transform(x))
        }
        return result
      }
    }
    ```

  * 실제로는 [`Sequence`의 extenstion](https://github.com/apple/swift/blob/master/stdlib/public/core/Sequence.swift#L807-L825)으로 구현되어 있다.

###### Parameterizing Behavior with Functions ***

* `map`과 같은 함수는 인자로 특정 행위(함수)를 받는다.
* 표준 라이브러리에서 제공하는 고차 함수 인자의 예
  * `map`과 `flatMap` : 요소를 어떻게 변환할지?
  * `filter` : 요소가 포함될지?
  * `reduce` : 요소를 누적 값에 어떻게 누적시킬지?
  * `sequence` : 다음 요소가 무엇이 되어야할지
  * `forEach` : 요소로 어떤 동작을 할지?
  * `sort`, `lexicographicallyPrecedes`, `partition` : 두 개의 요소의 순서가 어떻게 되냐?
  * `index`, `first`, `contains` : 이 요소와 일치하냐?
  * `min`, `max` : 두 요소 중 최소값, 최대값은 무엇인가?
  * `elementsEqual`, `starts` : 두 요소가 같냐?
  * `split` : 이 요소가 seperator냐?
* ​
* 기타 짜볼 수 있는 유용한 함수
  * `accumulate`
  * `all(matching:)`, `none(matching:)`
  * `coutn(where:)`
  * `indices(where:)`
  * `prefix(while:)`
  * `drop(while:)`

###### Mutation and Stateful Closures ***

* `map`을 사용할 때 side effect를 수행하면 안된다.

  * ```swift
    array.map { item in
      table.insert(item)
    }
    ```

  * array를 변환(`map`)하면서 table을 변경(`mutation`)하고 있다.

  * 이 경우에는 `map`보다 `forEach`가 적절하다.

###### Filter

* 하나의 배열에 특정 조건에 맞는 요소들만 포함하는 배열을 새로 만든다.

  * ```swift
    nums.filter { num in num % 2 == 0 }
    ```

  * ```swift
    nums.filter { $0 % 2 == 0 }
    ```

* `map`과 `filter`를 조합해서 계산 결과를 위한 중간 배열 없이 많은 연산을 쉽게할 수 있다.

  * ```swift
    (1..<10).map { $0 * $0 }.filter { $0 % 2 == 0 }
    ```

* `filter`를 아래와 같이 쓰고 있다면 `contains`로 사용하는게 성능이 더 좋다.

  * ```swift
    0 < bigArray.filter { someCondition }.count
    ```

  * ```swift
    bigArray.contains { someCondition }
    ```

  * 두 가지 이유에서 성능이 더 좋다.

    * `filter`에 비해 `contains`는 새로운 배열을 만들지 않는다.
    * `filter`에 비해 `contains`는 조건에 맞는 요소를 찾았다면 모든 요소를 순회하지 않는다. (exits early)

  * 배열의 모든 요소가 특정 조건과 일치하기를 바란다면 아래와 같이 `all(matching:)`함수를 구현할 수 있다.

    * ```swift
      extension Sequence {
        public func all(matching predicate: (Iterator.Element) -> Bool) -> Bool {
          return !contains { !predicate($0) }
        }
      }

      let evenNums = nums.filter { $0 % 2 == 0 }
      evenNums.all { $0 % 2 == 0 } // true
      ```

###### Reduce

* 배열의 모든 요소를 하나의 값으로 조합한다.

  * ```swift
    var total = 0
    for num in fibs {
      total = total + num
    }
    total // 12
    ```

  * ```swift
    let total = fibs.reduce(0) { total, num in total + num }
    ```

* 연산자인 `+` 또한 함수이기 때문에 다음과 같이 줄여서 사용할 수 있다.

  * ```swift
    let total = fibs.reduce(0, +)
    ```

* `reduce`의 결과값의 타입은 배열 요소의 타입과 같을 필요가 없다.

  * ```swift
    fibs.reduce("") { str, num in str + "\(num) " } // 0 1 1 2 3 5
    ```

* ***

###### A Flattening Map ***

* ​

###### Iteration using forEach

* `for`와 거의 동일하게 동작

  * ```swift
    for element in [1, 2, 3] {
      print(element)
    }
    ```

  * ```swift
    [1, 2, 3].forEach { element in
      print(element)
    }
    ```

* ​

### Array Types

###### Slices

* Subscript를 통해 단일 요소가 아닌 특정 범위의 요소에 대해 접근할 수 있다.

  * ```swift
    let slice = fibs[1..<fibs.endIndex]
    slice // [1, 1, 2, 3, 5]
    type(of: slice) // ArraySlice<Int>
    ```

* `ArraySlice`는 배열의 `view`이다.

* ​

###### Bridging ***

* ​

# Dictionaries

### Mutation

### Some Useful Dictionary Extenstions

### Hashable Requirement

# Sets

### Set Algebra

### Index Sets and Character Sets

### Using Sets Inside Closures

# Ranges

