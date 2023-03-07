Java 스타일 가이드

# 1. 변수 명명 규칙

# 2. 함수 명명 규칙

# 3. 문법

## 3.0. Java 기초
## 3.0.1. OOP 특징
1. 캡슐화
2. 추상화 
3. 상속
4. 다형성

## 3.0.2. OOP 설계 5원칙 SOLID
- 객체 지향 설계시 지켜야할 원칙
1. SRP(Single Responsibility Principle): 단일 책임 원칙
2. OCP(Open Closed Priciple): 개방 폐쇄 원칙
3. LSP(Listov Substitution Priciple): 리스코프 치환 원칙
4. ISP(Interface Segregation Principle): 인터페이스 분리 원칙
5. DIP(Dependency Inversion Principle): 의존 역전 원칙

## 3.0.3. 타입
### 3.0.3.1 Call by Value
- 기본 타입
    - byte, int, char, boolean ...
    - 변수의 메모리 위치에 바로 값을 가진다.

### 3.0.3.2 Call by Reference
- Array, Object
- Copy
    - shallow copy
    - deep copy

### 3.0.3.3 String
- Immutable
- StringBuffer
    - 수정이 많은 String 사용시, 지향

### 3.0.3.4 연산된 값
- getter, setter
- predicate: boolean 값을 반환하는 함수

## 3.0.4 접근자
- public: 접근 제한이 없는
- private: 클래스 내에서만 접근 가능한
- protected: 동일한 패키지 또는 파생클래스에서만 접근 가능한
- static: 고정된
    - 객체 생성 없이 사용할 수 있는 필드와 메소드를 생성하고자
    - 객체 참조 없이 바로 사용
- final: 최종적인
    - 값이 저장되면 최종적인 값이 되므로, 수정이 불가능
- static final: 상수 (고정된 최종)
- default: 인터페이스에서, 로직을 포함한 메소드 선언 가능한 (기본적인)

## 3.0.5 OOP
- 인터페이스: 객체의 명세
    - 동작의 추상화: 객체의 외부에서 쓰일 메서드들을 모두 작성
    - default 동작: default 접근자로 작성하는 default 로직
    - 상수
- 클래스: 객체 생성 틀 
    - 구현하는 인터페이스의 메서드(public)
    - 클래스 동작을 위한 내부 메서드(private)
    - 필드(field)
- 인스턴스: 힙에 올라가는 객체


## 3.1 동작 파라미터
- 구성
    1. 호출 로직 (2번을 호출, 3번을 인자로 사용)
    2. 동작 호출 형식 (함수 디스크립터)
    3. 동작 파라미터 (함수 인터페이스와 구현 vs 람다)

### 3.1.0 메서드
#### 3.1.0.1 메서드 시그니처
- 디자인된 메서드 구조
    - **메서드 명**과 **파라미터 리스트**로 구성

#### 3.1.0.2 메서드 오버로딩
- 같은 메서드 명칭이나, 파라미터 리스트가 달라, 
    - 메서드 시그니처가 다른 메서드들
    - 하나의 클래스는 2개의 같은 메서드 시그니처를 가질 수 없다.
    - 메서드 시그니처는 **리턴 타입을 포함하지 않는다.**
```java
public int square(int x, int y) {
    ///
}
public double square(double x, double y) {
    ///
}
```

### 3.1.1 함수형 인터페이스와 구현 클래스
#### 3.1.1.1 추상적 조건으로 필터링
- 전략 패턴
    - 런타임 단계에 논리 구현을 선택
- 추상적 필터링
    - 논리 로직과
    - 세부 조건을 분리 

```java
// # predicate 인터페이스 (3.함수형 인터페이스)
public interface ApplePredicate{
    boolean test(Apple apple);
}
// ## 무게 필터링 구현 (3-1.구현된 함수)
public Class AppleWeightPredicate implements ApplePredicate{
    public boolean test(Apple apple){
        return apple.getWeight() > 150;
    }
}
// ## 색 필터링 구현 (3-2.구현된 함수)
public Class AppleColorPredicate implements ApplePredicate{
    public boolean test(Apple apple){
        return GREEN.equals(apple.getColor);
    }
}

// # 추상화된 필터링 함수 (2.호출되는 함수)
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p){
    List<Apple> result = new ArrayList<>();
    for(Apple apple: inventory){
        if(p.test(apple)){      // 추상화된 p를 통한 필터링
            result.add(apple);
        }
    }
    return result;
}

// # 전략 선택을 통한 필터링 (1.호출 로직)
List<Apple> greenApples = filterApples(inventory, new AppleColorPredicate());
```
- 함수형 인터페이스
    - 오직 하나의 추상 메서드를 갖는 인터페이스
    - Ex) 자바 API: Comparator, Runnable, Callabel, PrivilegedAction


#### 3.1.1.2 익명 클래스
- 이름 없는 클래스
- 지역 클래스
- 클래스 선언과 인스턴스화를 동시에 진행
- 단순한 일회성 동작시
    - 람다 추천
```java
List<Apple> greenApples = filterApples(inventory, new ApplePredicate(){
    public boolean test(Apple apple){
        return GREEN.equals(apple.getColor);
    }
});
```


### 3.1.2 람다
- 익명 함수를 단순화한 것
- 함수형 인터페이스의 추상 메서드를 직접 구현함으로써,
    - 람다의 전체 표현식을 함수형 인터페이스를 구현한 클래스의 인스턴스로 취급
```java
List<Apple> greenApples = filterApples(inventory, (Apple apple) -> GREEN.equals(apple.getColor()));
```


### 3.1.3 제네릭 Generic
- 잘못된 타입의 인입을 컴파일 단계에서 방지한다.
- 클래스 외부에서 타입을 지정하기 때문에, 타입 체크와 변환이 필요 없다.
- 코드의 재사용성이 높다.
```java
// 제네릭를 이용한, 추상화 (3.함수형 인터페이스)
public interface Predicate<T>{
    boolean test(T t);
}

// 제네릭을 이용한, 필터 (2.호출되는 함수)
public static <T> List<T> filter(List<T> list, Predicate<T> p){
    List<T> result = new ArrayList<>();
    for(T e: list){
        if(p.test(e)){
            result.add(e);
        }
    }
    return result;
}

// T = Apple (1.호출 로직, 3-1.구현된 람다)
List<Apple> greenApples = filter(inventory, (Apple apple) -> GREEN.equals(apple.getColor()));
// T = Integer (1.호출 로직, 3-2.구현된 람다)
List<Integer> evenNumbers = filter(numbers, (Integer i) -> i % 2 == 0);
```


### 3.1.4 함수 디스크립터
- 람다 표현식의 시그니처를 서술해주는 메서드
```java
// # 함수 디스크립터에 해당 (2.호출되는 함수)
// r의 메서드의 명칭(run), 파라미터 리스트(없음), 반환값(void)을 설명해주고 있다.
public void process(Runnable r){
    r.run();
}

// # 로직과, 람다 표현식 (1.호출 로직, 3.람다)
// 함수형 인터페이스를 구현한 클래스의 인스턴스로써 사용되고 있다.
process(() -> System.out.println("run!"));
```
#### 3.1.4.1 기본 제공 함수형 인터페이스 (자바8)
- `Predicate<T>`
    - `T -> boolean`
    - 참/거짓 단정자
- `Consumer<T>`
    - `T -> void`
    - 소비자
- `Function<T, R>`
    - `T -> R`
    - 기능자
- `Supplier<T>`
    - `() -> T`
    - 제공자
- `UnaryOperator<T>`
    - `T -> T`
    - 단항 연산자
- `BinaryOperator<T>`
    - `(T, T) -> T`
    - 이중 연산자

- `BiPredicate<T, U>`
    - `(T, U) -> boolean`
    - 이중 단정자
- `BiConsumer<T, U>`
    - `(T, U) -> void`
    - 이중 소비자
- `BiFunction<T, U, R>`
    - `(T, U) -> R`
    - 이중 기능자


### 3.1.5 메서드 참조 `::`
- 특정 메서드만 호출하는 기능
```java
// java.util.Comparator.comparing 활용
inventory.sort(comparing(Apple::getWeight)); // 메서드 참조
```
1. 정적 메서드 참조
    - 비교
        - 람다: `(args) -> ClassName.staticMethods(args)`
        - 메서드 참조: `ClassName::staticMethod`
    - ex) `Integer::parseInt`
2. 다양한 형식의 인스턴스 메서드 참조
    - 비교
        - 람다: `(arg0, rest) -> arg0.instanceMethod(rest)`
        - 메서드 참조: `ClassName::instanceMethod(rest)` 
            - 단, ClassName은 arg0의 ClassName 형식
    - ex) `String::length`
3. 기존 객체의 인스턴스 메서드 참조
    - 비교
        - 람다: `(args) -> expr.instanceMethod(args)`
        - 메서드 참조: `expr::instanceMethod(args)`
    - ex) Transaction이라는 클래스가 있을때, 생성된 expensiveTransaction
        - `expensiveTransaction::getValue`
#### 3.1.5.2 생성자 참조
```java
// # 1. Supplier: () -> Apple 시그니처의 생성자가 있다고 할 때,
// ## 1-0. 람다 방식
Supplier<Apple> c10 = () -> new Apple();
Apple a10 = c10.get();

// ## 1-1. 참조 방식
Supplier<Apple> c11 = Apple::new;
Apple a11 = c11.get(); // Supplier의 get을 통해 Apple 객체 생성이 가능하다.

// # 2. Function: Apple(Integer weight) 시그니처의 생성자가 있다고 할 때,
// ## 2-0. 람다 방식
Function<Integer, Apple> c20 = (weight) -> new Apple(weight);
Apple a20 = c20.apply(110);

// ## 2-1. 참조 방식
Function<Integer, Apple> c21 = Apple::new;
Apple a21 = c21.apply(110);

// # 3. 사용 방법
List<Integer> weights = Arrays.asList(7,3,4,10);
List<Apple> apples = map(weights, Apple::new);

public List<Apple> map(List<Integer> list, Function<Integer, Apple> f){
    List<Apple> result = new ArrayList<>();
    for(Integer i: list){
        result.add(f.apply(i));
    }
    return result;
}
```

## 3.2 컬렉션

### 3.2.1 상속 관계
- Collection
    - List
        - LinkedList
        - Stack
        - Vector
        - ArrayList
    - Set
        - HashSet
        - SortedSet
            - TreeSet
- Map
    - Hashtable
    - HashMap
    - SortedMap
        - TreeMap

#### 3.2.1.1 List
##### 3.2.1.1.1 Stack
- 메서드
    - push(): 데이터 삽입
    - pop(): 데이터 추출
    - peek(): 데이터 조회
    - search(): Stack으로부터 데이터 검색
##### 3.2.1.1.2 Vector
- 동기화 보장
- 지양
##### 3.2.1.1.3 ArrayList
- 동기화 보장하지 않는다
- 메서드
    - add(), get(), toArray(), contains(), size()
#### 3.2.1.2 Set
- 집합
- 순서가 없다
- 중복되지 않는다
##### 3.2.1.2.1 HashSet
- 메서드
    - add(), next(), remove(), contains(), size()
#### 3.2.1.3 Map
- key value 쌍의 리스트
- key는 중복되지 않는다
- 메서드
    - put(), get()
##### 3.2.1.3.1 Hashtable
- 동기화 보장
- 지양
##### 3.2.1.3.2 HashMap
- 동기화 보장하지 않는다

### 3.2.2 Sorted
- Set
    - SortedSet
        - TreeSet
- Map
    - SortedMap
        - TreeMap

#### 3.2.2.1 Comparator
- sort시, Comparator를 구현하여, 정렬의 방식을 지정할 수 있다.
    - 오름차순, return 값의 양수가 후순위
- `compare(T o1, T o2)`
```java
// 제공되는 함수 인터페이스
public interface Comparator<T>{
    default int compare(T o1, T o2){
        return Integer.compare(o1.hashCode(), o2.hashCode());
    };
}

// 익명 클래스 방식
Arrays.sort(classList, new Comparator<ClassName>() {
    @Override
    public int compare(ClassName o1, ClassName o2) {
        return Integer.compare(o1.score, o2.score);
    }
});

// 람다 방식
Arrays.sort(classList, (ClassName o1, ClassName o2) -> Integer.compare(o1.score, o2.score));
```
- [Spec: java.util > Interface Comparator<T>](docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#method.summary)


### 3.2.3 Comparable
- Comparable을 구현하여, 우위 방식을 구현할 수 있다.
- `compareTo(T o)`
```java
public class ClassName implements Comparable<Type> { 
    private score;

    @Override
    public int compareTo(ClassName another) {
        return this.score - another.score;
    }
}
```
- [Spec: java.lang > Interface Comparable<T>](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html#method.summary)



## 3.3 스트림
- 데이터 처리 연산을 지원하는 소스에서 추출된 연속된 요소
- 특징
    - 연속된 요소
    - 소스
    - 데이터 처리 연산
        - 컬렉션의 주제는 자료구조의 복잡성과 데이터
        - 스트림의 주제는 표현 계산식과 연산
    - 파이프라이닝
        - laziness, short-circuiting 최적화를 얻을 수 있다.
    - 내부 반복
        - 반복자를 사용하는 컬렉션과 달리, 내부 반복을 지원한다.
        - 외부 반복에서는 병렬성을 스스로 관리해야한다.
- 장점
    - 선언형: 간결, 가독성 증대
    - 조립성: 유연성 증대
        - 고수준 빌딩 블록으로 이루어져, 특정 스레딩 모델에 제한되지 않고, 자유롭게 사용 가능하다.
    - 병렬화: 성능 향상
        - 소프트웨어 공학적으로(내부적으로) 멀티코어 아키텍처를 최대한 활용 가능
- 종류
    - 순차 `stream()`
    - 병렬 `parallelStream()`
        - 멀티스레드로 구현하지 않고, 병렬로 데이터 처리 가능
- 연산
    - filter
        - return을 boolean 값으로, 해당 element 필터링 여부 결정
    - sorted
        - comparator를 받아, compare
    - map
        - return 값으로 element들을 대체
    - limit
        - 반환 갯수 제한
    - collect
        - 집계하여 새로운 자료구조로 반환

```java
// # stream 파이프라인 처리
List<String> lowCaloricDishesName = menu.stream()
    .filter(d->d.getCalories() < 400>)
    .sorted(comparing(Dish::getCalories))
    .map(Dish::getName)
    .collect(toList());

// # parallelStream 병렬 처리
List<String> lowCaloricDishesName = menu.parallelStream()
    .filter(d->d.getCalories() < 400>)
    .sorted(comparing(Dish::getCalories))
    .map(Dish::getName)
    .collect(toList());
```



- 옵셔널 체이닝



