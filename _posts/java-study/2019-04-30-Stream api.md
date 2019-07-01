---

title: "[java-study] java8 스트림"

excerpt: "2019. 04. 30. 자바8의 변화 - Stream 스터디"

search: true

categories:

- java_study

tags:

- java
- stream
---
## 스트림 API

> 스트림이란?

: 데이터를 추상화하여 다루어 다양한 방식으로 저장된 데이터를 읽고 쓰기 위한 방법

- List, 배열의 iterator나 반복문 사용의 코드 길이가 매우 길고 재사용 힘듦 => 해결책



 **특징**

1. 외부 반복을 통해 작업하는 컬렉션과는 달리 내부 반복을 통해 작업을 수행함
   - 내부 반복이라는 것은 반복문을 메서드의 내부에 숨길 수 있다는 것을 의미

2. 재사용이 가능한 컬렉션과는 달리 단 한 번만 사용할 수 있음.

3. 원본 데이터를 변경하지 않음.

4. 스트림의 연산은 필터-맵 기반의 API를 사용하여 지연연산을 통해 성능을 최적화함.


5. parallelStream() 메소드를 통한 손쉬운 병렬 처리를 지원함. 

   - 여러쓰레드가 처리해주니 병렬스트림이 항상 성능면에서 유리해보일 수 있지만 애플리케이션에서 사용하는 쓰레드가 많거나 스트림의 요소 수가 많지 않다면 오히려 쓰레드를 사용하는데 드는 오버헤드가 더 클 수도 있다

부정적인 입장 : <https://homoefficio.github.io/2016/06/26/for-loop-%EB%A5%BC-Stream-forEach-%EB%A1%9C-%EB%B0%94%EA%BE%B8%EC%A7%80-%EB%A7%90%EC%95%84%EC%95%BC-%ED%95%A0-3%EA%B0%80%EC%A7%80-%EC%9D%B4%EC%9C%A0/>

> 동작 흐름

데이터 소스 => 중개연산(필터) - 스트림의 변환 => 중개 연산(맵) - 스트림의 사용=> 최종 연산



---

> 스트림 생성

1. 컬렉션

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(4);
list.add(3);
list.add(2);
list.add(1);
```

```java
Stream<Integer> stream = list.stream();
stream.forEach(System.out::println); 
```

forEach() => 스트림의 요소를 하나씩 소모하면서 순차적 접근 (원본 데이터는 그대로 유지)



2. 배열

```java
String[] arr = new String[]{"하나", "둘", "셋", "넷"};
Stream<String> stream1 = Arrays.stream(arr);
stream1.forEach(e->System.out.print(e+" ")); //하나 둘 셋 넷

Stream<String> stream2 = Arrays.stream(arr, 1, 3); //subString
stream2.forEach(e -> System.out.print(e+" ")); //둘 셋
```

-> 컬렉션과 차이 배열의 특정 부분만 이용할 수 있음.

Int, long 타입이 있는 Stream은 Stream 앞에 타입을 적어준다 ex) IntStream, LongStream...



3. 가변 매개변수

```java 
Stream<Double> stream = Stream.of(4.2, 2.5, 3.1, 1.9);
stream.forEach(System.out::println);
```

of() 메소드를 사용하여 가변 매개변수를 전달받아 스트림 생성 가능



4. 지정된 범위의 연속된 정수

range(), rangeClosed()

- range() : 시작 정수를 포함하지만, 명시된 마지막 정수는 포함하지 않는 스트림
- rangeClosed() : 작 정수뿐만 아니라 명시된 마지막 정수까지도 포함하는 스트림을 생성

```java
IntStream stream1 = IntStream.range(1, 4);
stream1.forEach(e -> System.out.print(e + " ")); //1부터 4이전까지 1 2 3

IntStream stream2 = IntStream.rangeClosed(1, 4);
stream2.forEach(e -> System.out.print(e + " ")); //1부터 4까지 1 2 3 4
```



5. 람다 표현식

- iterate() : 시드로 명시된 값을 람다 표현식에 사용하여 반환된 값을 다시 시드로 사용하는 방식으로 무한 스트림을 생성
  - 이해하기 어렵 -> 예제

```java
IntStream stream = Stream.iterate(2, n -> n+2);//2,4,6,...
```

```java
Stream.iterate(0, n -> n + 1)
                .limit(10) //조건
                .forEach(x -> System.out.println(x)); //0,1,2,...,9
```

- generate()

```java
Stream<String> stream = Stream.generate(() -> "Echo").limit(5);
stream.forEach(System.out::println); //echo 5번 
```



6. 빈 스트림

```java
Stream<Object> stream = Stream.empty();
System.out.println(stream.count()); // 스트림의 요소의 총 개수를 출력함.
```

-> 말그대로 빈 스트림 

---

> 스트림의 중개 연산

1. 스트림 필터링

- distinct() : 스트림에서 중복 요소 제거 (equal() 기준)
- filter() : 말 그대로 필터링

```java
IntStream stream1 = IntStream.of(7, 5, 5, 2, 1, 2, 3, 5, 4, 6);
IntStream stream2 = IntStream.of(7, 5, 5, 2, 1, 2, 3, 5, 4, 6);
// 스트림에서 중복된 요소를 제거함.
stream1.distinct().forEach(e -> System.out.print(e + " "));
stream2.filter(n -> n % 2 != 0).forEach(e -> System.out.print(e + " "));
//홀수만
```



2. 스트림 변환

- map() : 반환값들로 이루워진 스트림 

```java
Stream<String> stream =Stream.of("Hello", "HI", "Bye", "good");
stream.map(s->s.length()).forEach(system.out::println);
```



- flatMap() : 스트림들을 하나의 스트림으로 합쳐서 하나의 새로운 스트림

```java
String[] arr = {"I study hard", "You study JAVA", "I am hungry"};
        
        Stream<String> stream = Arrays.stream(arr);
        stream.flatMap(s -> Stream.of(s.split(" "))).forEach(System.out::println); 
```

=> 단어별 출력



3. 스트림 제한

- skip() : 해당 변수를 첫번째 요소로 지정함.
- limit(개수) : 개수만큼 출력

```java
IntStream stream1 = IntStream.range(0,10);
IntStream stream2 = IntStream.range(0,10);
IntStream stream3 = IntStream.range(0,10);

stream1.skip(4).forEach(n -> System.out.print(n + " "));//4 5 6 7 8 9 
stream2.limit(5).forEach(n -> System.out.print(n + " "));//0 1 2 3 4
stream3.skip(3).limit(5).forEach(n -> System.out.print(n + " "));// 3 4 5 6 7
```



4. 스트림 정렬

정렬은 배열과 동일 sorted();

- 역 정렬시

  ```java
  stream.sorted(Camparator.reverseOrder())
  ```



5. 스트림 연산 결과 확인

peek()

- foreach 는 스트림을 소비해서 다음 연산이 불가능하고 peek 는 스트림을 소비하지 않아서 다음 연산이 가능함

```java
Stream.of("one", "two", "three", "four")
                .filter(e -> e.length() > 3)
                .peek(e -> System.out.println("Filtered value: " + e))
                .map(String::toUpperCase)
                .peek(e -> System.out.println("Mapped value: " + e))
                .collect(Collectors.toList());
```

Filtered value: three
Mapped value: THREE
Filtered value: four
Mapped value: FOUR

---

> 스트림 최종 연산	

1. 요소의 출력 : forEach()

2. 요소의 소모 : reduce()

```java
List<Integer> ages = new ArrayList<Integer>();
ages.add(1);ages.add(2);ages.add(3);//1,2,3
System.out.println(ages.stream().reduce((b,c) -> b+c).get());//1+2+3=6
```

3. 요소의 검색 : findFirst(), findAny()

4. 요소의 검사 : anyMatch(), allMatch(), noneMatch()

   - anyMatch() : 해당 스트림의 일부 요소가 특정 조건을 만족할 경우에 true를 반환함.

   - allMatch() : 해당 스트림의 모든 요소가 특정 조건을 만족할 경우에 true를 반환함.

   - noneMatch() : 해당 스트림의 모든 요소가 특정 조건을 만족하지 않을 경우에 true를 반환함.

5. 요소의 통계 : count(), min(), max()

```java
IntStream stream = IntStream.of(30,90,70,10);
System.out.pritnln(stream.count());
```

3. 요소의 연산 : sum(), average()

4. 요소의 수집 : collect()

   자세한 내용 : <http://tcpschool.com/java/java_stream_terminal>



---

참고자료

예제

<https://www.baeldung.com/java-streams-peek-api>

<https://jeong-pro.tistory.com/165>

개념 <http://tcpschool.com/>

추가설명

<http://iloveulhj.github.io/posts/java/java-stream-api.html>

