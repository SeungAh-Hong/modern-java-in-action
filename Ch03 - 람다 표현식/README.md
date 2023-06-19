## 담당자

- 3.1 람다란 무엇인가 : 어정윤
- 3.2 어디에, 어떻게 람다를 사용할까 : 김대현
- 3.3 람다 활용 - 실행 어라운드 패턴 : 홍승아
- 3.4 함수형 인터페이스 사용 : 이홍섭
- 3.5 형식 검사, 형식 추론, 제약 : 박상욱
- 3.6 메서드 참조 : 송민진
- 3.7 람다, 메서드 참조 활용하기 : 박상욱
- 3.8 람다 표현식을 조합할 수 있는 유용한 메서드 : 김대현
## 진행 날짜
- 2023년 4월 13일 (목요일)

## 통합 정리
# 3.1. 람다란 무엇인가?

람다 표현식이란 메소드로 전달할 수 있는 익명 함수를 단순화한 것이다.

### 람다의 특징

1. 익명

   보통의 메소드와 달리 이름이 없으므로 **익명**이라 표현

2. 함수

   람다는 메소드처럼 특정 클래스에 종속되지 않으므로 함수라고 부름

   (메소드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트 포함)

3. 전달

   람다 표현식을 메소드 인수로 전달하거나 변수로 저장 가능

4. 간결성

   익명 클래스처럼 많은 자질구레한 코드 구현할 필요 없음

### 람다 표현식이 중요한 이유

- 람다를 이용해 간결한 방식으로 코드 전달 가능

ex) 커스텀 Comparator 객체

| 기존 코드

```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
    public int compare(Apple apple1, Apple apple2) {
        return apple1.getWeight().compareTo(apple2.getWeight());
    }
};
```

| 람다 표현식 사용

```java
Comparator<Apple> byWeight = (Apple apple1, Apple apple2) -> apple1.getWeight().compareTo(apple2.getWeight());
```

람다 표현식을 이용하면 compare 메소드의 바디를 직접 전달하는 것처럼 코드를 전달할 수 있다.

### 람다 표현식 구성

1. 파라미터 리스트

   Comparator의 compare 메소드 파라미터(사과 두 개)
   
2. 화살표

   화살표(->)는 람다의 파라미터 리스트와 바디를 구분

3. 람다 바디

   두 사과의 무게 비교(람다의 반환값에 해당)

### 람다 예제

1. 불리언 표현식

   ```java
   (List<String> list) -> list.isEmpty()
   ```

2. 객체 생성

   ```java
   () -> new Apple(10)
   ```

3. 객체에서 소비

   ```java
   (Apple apple) -> {
        System.out.println(apple.getWeight());
   }
   ```

4. 객체에서 선택/추출

   ```java
   (String s) -> s.length()
   ```

5. 두 값을 조합

   ```java
   (int a, int b) -> a * b
   ```

6. 두 객체 비교

   ```java
   (Apple apple1, Apple apple2) -> apple1.getWeight().compareTo(apple2.getWeight())
   ```
# 3.2 어디에, 어떻게 람다를 사용할까?

함수형 인터페이스 문맥에서 사용할 수 있다.

Functional Interface

## 3.2.1 함수형 인터페이스

정확히 하나의 추상 메서드를 지정하는 인터페이스

(디폴트 메서드들은 많아도 상관 없음.)

``` java
public interface Comparator<T> {
	int compare(T o1, T o2);
}
```

### 퀴즈 3-2

``` java
public interface Adder {
	int add(int a, double b);
}

public interface SmartAdder extends Addr {
	int add(double a, double b);
}

public interface Nothing {

}
```

Adder만이 함수형 인터페이스이다. SmartAdder 같은 경우는 오버로딩된 add 함수 2개가 존재하기 때문에 함수형 인터페이스가 아니다.

함수형 인터페이스에 람다나 익명클래스를 전달하면 컴파일러가 자동으로 해당 함수형 인터페이스의 인스턴스로 변환해준다.

```java
Runnable r1 = () -> System.out.println("Hello world 1");

Runnable r2 = new Runnable() {
	public void run(){
		System.out.println("Hello world 2");
	}
};

public static void process(Runnable r){
	r.run();
}

process(r1);
process(r2);
process(() -> System.out.println("Hello World 3"));

```


## 3.3.2 함수 디스크립터

함수 디스크립터: 무엇을 설명하고자 하는지 잘 모르겠다;;

함수 시그니처: 함수의 인풋과 아웃풋의 타입을 설명해 놓은 표현식.

함수형 인터페이스의 시그니처와 람다식의 시그니처가 일치해야 한다는 것을 강조한다.

### 람다와 메소드 호출

람다식에서 한개의 void를 리턴하는 메서드 호출은 중괄호를 생략할 수 있다.

### 20장, 21장

람다를 함수형 인터페이스로 받지 말고, 람다 고유의 타입을 추가하는 것은 어떨까?

언어 설계 상 어렵고 복잡해지기 때문에 개발자들이 그러지 않기로 결정함.

### 퀴즈 3-3
``` java
Predicate<Apple> p = (Apple p) ->a.getWeight();
```

위 코드는 어디가 잘못되었을까?

뒤에 있는 람다식은 Apple을 인자로 받고 Int를 리턴하는 함수 시그니처를 가지고 있다. 

반면 Predicate p는 true / false 를 리턴하는 Apple -> boolean 시그니처를 가지고 있어 서로의 시그니처가 불일치한다.

### @FunctionalInterface

어떠한 인터페이스에 해당 어노테이션을 선언했는데 실제 구현은 함수형 인터페이스가 아닐 경우 컴파일 에러를 발생시켜 개발자의 실수를 막는다. 또한 코드를 읽는 이에게 하여금 해당 인터페이스의 사용 방법을 직관적으로 알려준다. 
# 3.3 람다 활용 - 실행 어라운드 패턴

자원처리(DB 파일처리 등)에 사용하는 순환 패턴은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루어진다.<br>
설정-작업-정리 형식의 코드를 **실행 어라운드 패턴**(execute around pattern)이라고 부른다.

자바 7에서는 try-with-resource 구문을 사용한다. 이를 사용하면 자원을 명시적으로 닫을 필요가 없으므로 간결한 코드를 구현하는 데 도움을 준다.

- 예제: 파일에서 한 행을 읽는 코드

```java
public String processFile() throws IOException {
  try ( BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
    return br.readLine(); // 실제 작업
  }
}
```

try 괄호 안에 Open, Close할 자원을 명시하고 중괄호 안에 Process 로직을 넣는다.

## 3.3.1. 1단계: 동작 파라미터화를 기억하라

위 코드는 요구사항 변경에 대응하지 못한다. processFile의 동작을 파라미터화하면 기존의 설정, 정리 과정을 재사용하고 메서드만 다른 동작을 수행하도록 할 수 있다.

함수형 인터페이스에 람다를 사용하기 위해, BufferReader를 인수로 받아서 String을 반환하는 람다를 구현해보자.

- 한 번에 두 행 출력하는 코드

```java
String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

## 3.3.2. 2단계: 함수형 인터페이스를 이용해서 동작 전달

함수형 인터페이스 자리에 람다를 사용할 수 있다. (람다는 파라미터로 ‘코드’를 넘기는 기술로, ‘코드’를 담을 ‘그릇’이 필요한데 함수형 인터페이스가 그 역할을 한다.)

따라서 BufferedReader → String과 IOException을 throw할 수 있는 시그니처와 일치하는 함수형 인터페이스 BufferedReaderProcessor를 생성한다.

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
  String process(BufferedReader b) throws IOException; *// 추상 메서드 1개 -> process라는 이름으로 BufferedReader 객체를 입력 받고 String을 반환*
}
```

정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
  // ...
}
```

## 3.3.3. 3단계: 동작 실행

이제 BufferedReaderProcessor에 정의된 process 메서드의 시그니처(BufferedReader -> String)와 일치하는 람다를 전달할 수 있다.

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리된다. 

따라서 processFile 바디 내에서 BufferedReaderProcessor 객체의 process를 호출할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
  try (BufferedReader br = new BufferedReader(new fileReader("data.txt"))) {
    return p.process(br); // BufferedReader 객체 처리
  }
}
```

## 3.3.4. 4단계: 람다 전달

이제 람다를 이용해서 다양한 동작을 processFile 메서드로 전달할 수 있다.

```java
String oneLine = processFile((BufferedReader br) -> br.readline());
String twoLines = processFile((BufferedReader br) -> br.readline() + br.readline());
```

이렇듯 언제든 변화 가능한 process 코드를 파라미터로 분리하여 언제든 대응 가능하도록 구성한 패턴이 실행 어라운드 패턴이다. 그리고 실행 어라운드 패턴에서 코드를 넘길 때 사용하는 기술이 람다(Lambda)이다.

## 정리
![](picture3-3.png)
#  3.4 함수형 인터페이스 사용
- 함수형 인터페이스의 추상 메서드 시그니처를 함수 디스크립터 (function descriptor)라고 한다.
- 다양한 람다 표현식을 사용하기 위해서는 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다.
- 자바 8에서는 `java.util.function` 패키지로 여러 가지 새로운 함수형 인터페이스를 제공한다.
## 3.4.1 Predicate
- Predicate<T> 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 boolean을 반환한다.
- 우리가 만들었던 인터페이스와 같은 형태인데 따로 정의할 필요 없이 바로 사용할 수 있다는 점이 특징이다.
- 즉 제네릭 T 형식의 객체를 사용하는 boolean 표현식이 필요한 상황에서 Predicate 인터페이스를 사용할 수 있다.
  
아래는 String 객체를 인수로 받는 람다를 정의할 수 있다.
```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}

public <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> results = new ArrayList<>();
    for (T t : list) {
        if (p.test(t)) {
            results.add(t);
        }
    }
    return results;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```
## 3.4.2 Consumer
- Consumer<T> 인터페이스는 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의한다.
- 제네릭 T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있다.
- 예를들어 Integer 리스트를 인수로 받아서 각 항목에 어떤 동작을 수행하는 forEach 메서드를 정의할 때 활용할 수 있다.

아래는 forEach와 람다를 이용해서 리스트의 모든 항목을 출력하는 예제이다.
```java

@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}

public <T> void forEach(List<T> list, Consumer<T> c) {
    for (T t : list) {
        c.accept(t);
    }
}
forEach(Arrays.asList(1,2,3,4,5), (Integer i) -> System.out.println(interface));
```
## 3.4.3 Function
- Function<T,R> 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의한다.
- 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있다.
  - ex) 사과의 무게 정보를 추출하거나 문자열을 길이와 매핑

아래는 String 리스트를 인수로 받아 각 String의 길이를 포함하는 Integer 리스트로 변환하는 map 메서드를 정의하는 코드이다.
```java

@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}

public <T, R> List<R> map(List<T> list, Function<T, R> f) {
    List<R> results = new ArrayList<>();
    for (T t : list) {
      results.add(f.apply(t));
    }
    return results;
}

//[7,2,6]
List<Integer> l = map(
        Arrays.asList("lambdas","in","action"),
        (String s) -> s.length() // Function의 apply 메서드를 구현하는 람다
);
```
### 기본형 특화
- 자바의 모든 형식은 참조형(Byte,Integer,Object,List) 아니면 기본형(int,double,char,byte..)에 해당한다.
- 하지만 제네릭 내부 구현 때문에 제네릭 파라미터에는 **참조형**만 사용할 수 있다.
- 자바에서는 `기본형 -> 참조형`으로 변환하는 기능을 제공하는데, 이 기능을 박싱 이라고 한다.
  - 반대로 `참조형 -> 기본형` 으로 변환하는 동작을 언방식 이라고 한다.
  - 박싱과 언박싱을 자동으로 처리하는 오토박싱 기능도 지원한다.
  - 하지만 이러한 변환 과정에는 비용이 들기 때문에 자바 8에서는 오토바 박싱을 피할 수 있도록 특별한 버전의 함수형 인터페이스도 제공한다.

아래 코드는 `IntPredicate`는 1000이라는 값을 박싱하지 않지만, `Predicate<Integer>`는 1000이라는 값을 Integer 객체로 반환하는 코드이다.
```java
public interface IntPredicate {
    boolean test(T t);
}
IntPredicate evenNumbers = (int i) -> i % 2 == 0;
evenNumbers.test(1000) // 참 (박싱 없음)

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
oddNumbers.test(1000); // 거짓 (박싱)
```
# 3.5 형식 검사, 형식 추론, 제약 

## 3.5.1 형식 검사

람다가 사용되는 콘텍스트를 이용해서 람다의 형식(Type)을 추론할 수 있다.
어떤 콘텍스트(문맥, 예를 들면 람다가 전달될 메서드 파라미터나 람다가 할당되는 변수 등)에서
기대되는 람다 표현식의 형식을 **대상 형식**이라고 부른다.

```java
List<Apple> heavierThan150g = 
filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```

다음과 같은 순서로 형식 확인 과정이 진행된다.

	1. filter 메서드의 선언을 확인한다.
	2. filter 메서드는 두 번째 파라미터로 Predicate<Apple> 형식(대상 형식)을 기대한다.
	3. Predicate<Apple>은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스다.
	4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사한다.
	5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 한다.

---

	Tip. 함수 디스크립터
	보통 자바의 람다 표현식의 사용 용도를 두 가지로 정의하는데, 하나는 람다 함수로서의 용도 그리고
	다른 하나는 람다 표현식 문법으로 표현한 것을 의미한다고 보면 된다.
	그 중 함수 디스크럽터는 단어 그대로 함수가 어떤 입력 값을 받고 어떤 반환 값을 주는지에 대한 설명을
	람다 표현식 문법으로 표현한 것을 의미한다.

	예를 들어, () -> void 는 파라미터 리스트가 없으며, void를 반환하는 함수를 의미하는 디스크럽터이며,
	(int, int) -> double은 두 개의 int 파라미터로 받아 double형 자료를 반환하는 함수를 설명한다.
	즉, 추상 메서드가 어떤 역할을 하는지 간략하게 설명한 것이다.

---

![스크린샷 2023-04-11 오후 6 06 34](https://user-images.githubusercontent.com/96435200/231112686-36da7757-ad7e-4423-855e-0645bf5582e6.png)

``` java
	1. 람다가 사용된 컨텍스트는 무엇인가? 우선 filter의 정의를 확인하자.
	
	 public <T> List<T> filter(List<T> list, Predicate<T> p) { -- filter 메서드
		List<T> results = new ArrayList<>();
		for(T t: list) {
			if(p.test(t)) {
					result.add(t);
			}
			return results;
		} 
	 }

	2. 대상 형식은 Predicate<Apple>이다. (T는 Apple로 대치된다!)
	   
	3. Predicate<Apple> 인터페이스의 추상 메서드는 무엇인가?
	   @FunctionalInterface
	   public interface Predicate<T> { -- Predicate 인터페이스
	        boolean  test(T t);
	   }

	4. Apple을 인수로 받아 boolean을 반환하는 test 메서드다!
	5. 함수 디스크립터는 Apple -> boolean이므로 람다의 시그니처와 일치한다!
		람다도 Apple을 인수로 받아 boolean을 반환하므로 코드 형식 검사가 성공적으로 완료된다.
		
```

![스크린샷 2023-04-11 오후 6 20 18](https://user-images.githubusercontent.com/96435200/231119284-7da6ac2e-bd62-4da0-9e97-69cb669d98e8.png)

* 람다 표현식이 예외를 던질 수 있다면 추상 메서드도 같은 예외를 던질 수 있도록 throws로 선언해야 한다.


---

## 3.5.2 같은 람다, 다른 함수형 인터페이스

대상 형식이라는 특징 때문에 같은 람다 표현식이라도 호환되는 추상 메서드를 가진 다른 함수형 인터페이스로 사용될 수 있다.

예를 들어 이전에 살펴본 Callable과 PrivilegedAction 인터페이스는 인수를 받지 않고 제네릭 형식 T를 반환하는 함수를 정의한다.

```java
	Callable<Integer> c = () -> 42;
	PrivilegedAction<Integer> p = () -> 42;

	 @FunctionalInterface
	 public interface Callable<V> {
		V call() throws Exception; -- 인수를 받지않고 제네릭 형식을 반환한다.
	 }

	public interface PrivilegedAction<T> {
		T run(); -- 역시 인수를 받지 않고 제네릭 형식을 반환한다.
	}
  
  
  	Tip. 다이아몬드 연산자
	주어진 클래스 인스턴스 표현식을 두 개 이상의 다양한 콘텍스트에 사용할 수 있다.
	이때 인스턴스 표현식의 형식 인수는 콘텍스트에 의해 추론된다.
	List<String> listOfStrings = new ArrayList<>(); -- String 타입으로 추론된다.
	Map<String, Integer> mapOfContext = new HashMap<>(); -- String, Integer 타입으로 추론된다.
  
  
  	Tip. 특별한 void 호환 규칙
	람다의 바디에 일반 표현식이 있으면 void를 반환하는 함수 디스크립터와 호환된다.
	물론 파라미터 리스트도 호환되어야 한다.

	// Predicate 는 boolean 반환값을 갖는다.
	Predicate<String> p = s -> list.add(s);
	// Consumer는 void 반환값을 갖는다.
	Consumer<String> b = s -> list.add(s);

	public interface List<E> extends Collections<E> {
			boolean add(E e);
	}

	@FunctionalInterface
	public interface Predeicate<T> {
			boolean test(T t);
	}

	@FunctionalInterface
	public interface Consumer<T> {
			void accept(T t); -- Consumer의 콘텍스트는 return으로 void를 기대하지만 boolean을 반환
											-- 유효한 코드
	}
	
```
  
  <img width="350" alt="스크린샷 2023-04-11 오후 7 57 54" src="https://user-images.githubusercontent.com/96435200/231140469-1f376ffa-776d-40e0-b599-cbe7ed1d6605.png">
  결과 : bool = true;
  
  할당문 콘텍스트, 메서드 호출 콘텍스트(파라미터, 반환값), 형변환 콘텍스트 등으로 표현식의 형식을 추론할 수 있다.
  
  ---
  
  ## 퀴즈 3-4 형식 검사 문제. 다음 코드를 컴파일할 수 없는 이유는?
  
```java
Object o = () -> {System.out.println("Tricky example");}

지금 람다 표현식의 콘텍스트는 Object 이다.
하지만 Object 는 클래스이지 함수형 인터페이스가 아니다.
따라서 () -> void 형식의 함수 디스크립터를 갖는 Runnable로 대상 형식을 바꿔서 문제를 해결하는 방법이 있다.
또한 람다 표현식을 명시적으로 대상 형식을 제공하는 Runnable로 캐스팅해서 문제를 해결할 수도 있다.
```

<img width="345" alt="스크린샷 2023-04-11 오후 8 11 32" src="https://user-images.githubusercontent.com/96435200/231143159-8499796e-dc68-49e3-af54-c3b0065cac33.png">

결과 : Tricky example

<img width="335" alt="스크린샷 2023-04-11 오후 8 20 55" src="https://user-images.githubusercontent.com/96435200/231145919-4f86d184-546f-4f26-a806-9df5bc5635a8.png">
두번째 캐스팅 방법은 같은 함수형 디스크립터를 가진 두 함수형 인터페이스를 갖는 메소드를 오버로딩할 때 이와 같은 기법을 활용할 수 있다.<br>
즉, 어떤 메소드의 시그니처가 사용되어야 하는지를 명시적으로 구분하도록 람다를 캐스트할 수 있다.<br>

```
예를 들어 execute(() -> {}) 라는 람다 표현식이 있다면 Runnable과 Action의 함수 디스크립터가
같으므로 누구를 가리키는지가 명확하지 않다.
```

<img width="397" alt="스크린샷 2023-04-11 오후 8 45 49" src="https://user-images.githubusercontent.com/96435200/231153311-613a7b4c-9eac-4484-9666-6c96f1117695.png">

```
하지만 다음처럼 캐스트 형식을 확실히 지정해주면 누구를 호출할 것인지가 명확해진다.
```
<img width="251" alt="스크린샷 2023-04-11 오후 8 47 04" src="https://user-images.githubusercontent.com/96435200/231153535-0229791a-725c-4bc8-81e7-d590e0b0779d.png">


---

## 3.5.3 형식 추론

우리 코드를 좀 더 단순화할 수 있는 방법이 있다.<br>
자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)를 이용해서<br>
람다 표현식과 관련된 함수형 인터페이스를 추론한다.<br>

즉, 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다.<br>
결과적으로 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략 할 수 있다.<br>

<img width="466" alt="스크린샷 2023-04-11 오후 9 18 57" src="https://user-images.githubusercontent.com/96435200/231160296-10920bdf-67df-4c8d-9518-04e403a83046.png">

<img width="467" alt="스크린샷 2023-04-11 오후 9 19 10" src="https://user-images.githubusercontent.com/96435200/231160544-ac753076-52df-4f8c-befc-9393fb4538fa.png">

상황에 따라 명시적으로 형식을 포함하는 것이 좋을 때도 있고 형식을 배제하는 것이 가독성을 향상시킬 때도 있다.<br>
어떤 방법이 좋은지 정해진 규칙은 없다.<br>
개발자가 스스로 어떤 코드가 가독성을 향상 시킬수 있는지 결정해야햔다.<br>

```
Tip. 람다에 형식 추론 대상 파라미터가 하나 뿐일때는 해당 파라미터명을 감싸는 괄호도 생략할 수 있다.
```
---

## 3.5.4 지역 변수 사용
지금까지 살펴본 모든 람다 표현식은 인수를 자신의 바디 안에서만 사용했다.<br>
하지만 람다 표현식에서는 익명 함수가 하는 것처럼 **자유 변수**(파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있다.<br>
이와 같은 동작을 **람다 캡처링**이라고 부른다.

<img width="416" alt="스크린샷 2023-04-11 오후 9 27 32" src="https://user-images.githubusercontent.com/96435200/231162152-ae4322b4-43ac-4bfd-bf7b-bf9e6c0c3fee.png">

하지만 자유 변수에도 약간의 제약이 있다.<br>
람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처할 수 있지만 그러려면 지역 변수는 명시적으로 final로 선언되어 있어야 하거나<br>
실직적으로 final로 선언된 변수와 똑같이 사용되어야 한다.<br>
즉, 람다 표현식은 한 번만 할당할 수 있는 지역 변수를 캡처할 수 있다.<br>

<img width="412" alt="스크린샷 2023-04-11 오후 9 30 06" src="https://user-images.githubusercontent.com/96435200/231162702-92ba6dde-53ed-4f1f-b87a-b0774324aecf.png">

```
예를 들어 다음 예제는 portNumber에 값을 두 번 할당하므로 컴파일할 수 없는 코드다.
```
<img width="699" alt="스크린샷 2023-04-12 오전 9 14 05" src="https://user-images.githubusercontent.com/96435200/231315304-1ddd9c6d-e564-4d2f-82d7-71c25a6b0f72.png">
---

## 지역 변수의 제약

왜 지역 변수에 이런 제약이 필요할까?<br>
우선 내부적으로 인스턴스 변수와 지역 변수는 저장되는 위치가 다르다.<br>
인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 저장된다.<br>
람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당ㄹ한 스레드가 사라져서 <br>
변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있다.<br>
따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다.<br>
따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴다.
  
# Method Reference (메서드 참조)


## 메서드 참조란?

- Java 8의 새로운 기능
- 특정 메서드만을 호출하는 람다 표현식의 축약형
- 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다.

- 때로는 람다 표현식보다 더 가독성이 좋으며 자연스러울 수 있다.
	- 람다 표현식
		```java
		inventory.sort((Apple a1, Apple a2) ->
						a1,getWeight().compareTo(a2.getWeight()));
		```
	
	- 메서드 참조 적용 후
		```java
		inventory.sort(comparing(Apple::getWeight));
		```

> 📌 **언제 유용한가?** <br>
> - 람다가 "이 메서드를 직접 호출해!"라고 명령할 경우 → 메서드명을 직접 참조하는 것이 편리함 <br>
> - 이 때 명시적으로 메서드명을 참조할 수 있음 → **가독성 향상!**

<br>

## 활용 방법
**구분자 `::` 붙이기** (괄호 필요 X)
- `Apple::getWeight` : Apple 클래스에 정의된 `getWeight`의 메서드 참조<br>
   (= 람다표현식 `(Apple a) -> a.getWeight()`)

<br>

## 예제
- **예시 1**
	- 람다 표현식
	```java
	() -> Thread.currentThread().dumpStack()
	```
	
	- 메서드 참조 적용 후
	```java
	Thread.currentThread()::dumpStack
	```

- **예시 2**
	- 람다 표현식
	```java
	(str, i) -> str.substring(i)
	```
	
	- 메서드 참조 적용 후
	```java
	String::substring
	```

- **예시 3**
	- 람다 표현식
	```java
	(String s) -> System.out.printIn(s) (String s) System.out::printin
	-> this.isValidName(s)
	```
	
	- 메서드 참조 적용 후
	```java
	System.out::printin
	this::isValidName
	```

> 💡 **참고**<br>
> 즉, 메서드 참조는<br>
> - 새로운 기능 X<br>
> - 하나의 메서드를 참조하는 람다를 더 편리하게 표현할 수 있는 문법!<br>
> - 같은 기능을 더 간결하게 구현할 수 있다!

<br>

## 람다 표현식 -> 메서드 참조

### 대표 예제

> **배경 지식**
> - `List`의 `sort` 메서드 : 인수로 Comparator을 기대한다.
> - `Comparator` : `(T, T) -> int`라는 함수 디스크립터를 갖는다.
> - `String` 클래스 : `compareToIgnoreCase`라는 메서드를 갖는다.

**람다 표현식**
```java
List<String> str = Arrays.asList("a","b","A","B");
str.sort((s1, s2) -> s1.compareToIgnoreCase(s2));
```

**메서드 참조로 변경**
```java
List<String> str = Arrays.asList("a","b","A","B");
str.sort(String::compareToIgnoreCase)；
```

<br>

>💡 **알고 가기**
> - 컴파일러는 **메서드 참조**가 **주어진 함수형 인터페이스**와 호환하는지 확인한다. (like 람다 표현식의 형식을 검사하던 방식)
> - 따라서, 메서드 참조는 콘텍스트 형식과 일치해야 한다.


<br>

## 메서드 참조 방법

1. **정적 메서드 참조**
    - `Integer::parseInt`
    
2. **다양한 형식의 인스턴스 메서드 참조**
    - `String::length`
    - 메서드 참조를 이용해서 람다 표현식의 파라미터로 전달할 때 사용

3. **기존 객체의 인스턴스 메서드 참조**
    - `expensiveTransaction::getValue` 
       (Transaction 객체를 할당받은 expensiveTransaction 지역변수가 있고, 
        Transaction 객체에는 getValue 메서드가 있음)
    -  현존하는 외부 객체의 메서드를 호출할 때 사용
    - 비공개 헬퍼 메서드를 정의한 상황에서 유용하게 활용 가능
   ```java
   private boolean isValidName(String string) {
	   return Character.isUpperCase(string.charAt(0));}
   ```
       
	```java
	filter(words, this::isValidName)
	```
<br>

![image](https://user-images.githubusercontent.com/100582309/231714945-614e8c1b-093f-4889-95a1-7a9e722d85c5.png){:width="30%" height="30%"}

<br>

### 예제 1 - ①번 방법
: 정적 메서드 참조

**람다 표현식**
```java
ToIntFunction<String> stringToInt = (String s) -> Integer.parselnt(s);
```

**메서드 참조로 변경**
```java
Function<String, Integer> stringToInteger = Integer::parselnt;
```

<br>

### 예제 2 - ②번 방법
: 다양한 형식의 인스턴스 메서드 참조

**람다 표현식**
```java
BiPredicate<List<String>, String> contains = (list, element) -> list.contains(element);
```

**메서드 참조로 변경**
```java
BiPredicate<List<String>, String> contains = List::contains;
```

<br>

### 예제 3 - ③번 방법
: 기존 객체의 인스턴스 메서드 참조 (비공개 헬퍼 메서드에 유용)

**람다 표현식**
```java
Predicate<String> startsWithNumber = (String string) -> this.startsWithNumber(string);
```

**메서드 참조로 변경**
```java
Predicate<String> startsWithNumber = this::startsWithNumber
```

<br>

## 생성자 참조

`new` 키워드를 이용해서 기존 생성자의 참조를 만들 수 있다. 
(정적 메소드의 참조를 만드는 방법과 비슷!)

- **case 1** : 기본 생성자 참조
	```java
	Supplier<Apple> c1 = Apple::new;
	Apple a1 = c1.get();          // Supplier의 get 메서드를 호출해서 새로운 Apple 객체를 만들 수 있음
	```

	> 참고 : 람다 표현식 방식
	> ```java
	> Supplier c1 = () -> new Apple();        // 디폴트 생성자를 가진 Apple 생성
	> Apple 0000000000
	> a1 = c1.get();
	> ```

<br>

- **case 2** : 인수가 있는 생성자 참조
	```java
	Function<Integer, Apple> c2 = Apple::new;    // Apple(Integer weight)의 생성자 참조!
	Apple a2 = c2.apply(110);
	```

<br>

- **case 2-1** : 여러 무게들을 담은 리스트를 활용하여, 다양한 무게의 사과 리스트 생성하기
	```java
	List weights = Arrays.asList(7, 3, 4, 10);
	List<Apple> apples = map(weights, Apple::new);
	public List<Apple> map(List<Integer> list, Function<Integer, Apple> f) {
		List<Apple> result = new ArrayList<>();
		for(Integer i : list) {
			result.add(f.apply(i));
		}
		return result;
	}
	```

<br>

- **case 3** : 2개의 인수를 갖는 생성자 참조
  - `BiFunction` 인터페이스와 같은 시그니처 -> `BiFunction` 활용
	```java
	BiFunction<Color, Integer, Apple> c3 = Apple::new;
	Apple a3 = c3.apply(GREEN, 110);
	```

<br>

- **case 3-1** : String으로 과일 종류, Integer로 무게를 줬을 때, 해당 종류의 과일을 생성하기
	```java
	static Map<String, Function<Integer, Fruit>> map = new HashMap<>();
	static {
		map.put("apple", Apple::new);
		map.put("orange", Orange::new);
	}
	```

	```java
	public static Fruit giveMeFruit(String fruitType, Integer weight) {
		return map.get(fruitType.toLowerCase())
				  .apply(weight);
	}
	```

<br>

- **case 4** : 인수가 3개 이상인 생성자 참조는?<br>
  현재 이런 시그니처를 갖는 함수형 인터페이스는 제공되지 않음 -> 직접 다음과 같은 함수형 인터페이스를 만들어야 한다.
	```java
	public interface TriFunction<T, U, V, R> {
		R apply(T t, U u, V v);
	}
	```

	```JAVA
	TriFunction<Integer, Integer, Integer, Color> colorFactory = Color::new;
	```

## 3.7.1 1단계 : 코드전달

처음에 다룬 사과 리스트를 다양한 정렬 기법으로 정렬하는 문제로 돌아가보자

List API에서 sort 메서드를 제공하므로 직접 정렬 메서드를 구현할 필요는 없다.
sort 메서드는 다음과 같은 시그니처를 갖는다.

```java
void sort(Comparator<? super E> c)
```

sort 메서드는 Comparator 객체를 인수로 받아 두 사과를 비교한다.

```java
@FunctionalInterface
public interface Comparator<T> {
	int compare(T o1, o2);
}
```

객체 안에 동작을 포함시키는 방식으로 다양한 전략을 전달할 수 있다.
즉, 이제 'sort의 동작은 파라미터화 되었다'라고 말할 수 있다.

<img width="476" alt="스크린샷 2023-04-12 오후 5 34 14" src="https://user-images.githubusercontent.com/96435200/231401000-1ae9562f-cab0-4cc8-8647-c1f1d38badf0.png">


즉, sort에 전달된 compare의 정렬 전략에 따라 sort의 동작이 달라질 것이다.

```java
inventory.sort(new AppleComparator());
```

---

## 3.7.2 2단계 : 익명 클래스 사용

한 번만 사용할 Comparator를 위 코드처럼 구현하는 것보다는 익명 클래스를 이용하는 것이 좋다.

<img width="348" alt="스크린샷 2023-04-12 오후 5 40 10" src="https://user-images.githubusercontent.com/96435200/231402352-9a034aa9-6cf3-47fe-aa8d-08383cbd0ca1.png">


---

### 3.7.3 3단계 : 람다 표현식 사용

한 번만 사용할 익명 클래스를 만든 건 좋았으나 코드가 장황하다.
함수형 인터페이스를 기대하는 곳 어디에서나 람다 표현식을 사용할 수 있으니 사용해보자.

<img width="322" alt="스크린샷 2023-04-12 오후 5 49 37" src="https://user-images.githubusercontent.com/96435200/231404807-652ea1eb-6e4d-4a89-a15c-63b40195ce4a.png">


자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해서 람다의 파라미터 형식을 추론한다.
사진의 두 번째 코드처럼 더 줄일 수 있다.

이 코드의 가독성을 더 향상시킬 수 없을까?
Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는
정적 메서드 comparing을 포함한다.

<img width="633" alt="스크린샷 2023-04-12 오후 6 01 50" src="https://user-images.githubusercontent.com/96435200/231408415-30a809c4-976b-414d-a52c-d63d87a87d21.png">

```java
Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
```

이제 코드를 다음처럼 간소화할 수 있다.
```java
Comparator<Apple> c = Comparator.comparing(apple -> apple.getWeight());
```


---


## 3.7.4 4단계 : 메서드 참조 사용

메서드 참조를 이용하면 람다 표현식의 인수를 더 깔끔하게 전달할 수 있으니
메서드 참조를 이용해서 코드를 조금 더 간소해보자.

```java
(java.util.Comparator.comparaing은 정적으로 임포트했다고 가정)
inventory.sort(comparing(Apple::getWeight));
```

단지 코드만 짧아진 것이 아니라 코드의 의미도 명확해졌다.
즉, 코드 자체로 'Apple을 weight별로 비교해서 inventory를 sort하라'는 의미를 전달할 수 있다.

# 3.8 람다 표현식을 조합할 수 있는 유용한 메서드

간단한 여러개의 람다 표현식을 조함해서 복잡한 람다 표현식을 만들 수 있다.

자바에서는 함수끼리의 연산을 돕는 디폴트 메서드를 통해 구현한다.

>특정 상황에서 범용적으로 사용되는 디폴트 메서드들이 미리 구현되어 있는 경우가 99퍼센트이니 외우면 된다.

간단하게 자주 쓰이는

- Comparator
- Predicate
- Pipe, Compose

를 살펴본다.

### 3.8.1  Comparator 조합

### 역정렬

Comparator.comparing 에 reversed 라는 default method가 있다.

해당 메서드를 콜하면, 정렬 순서를 바꾼 함수 인터페이스의 인스턴스를 생성한다.

즉, 

``` java
return a1.getWeight().compareTo(a2.getWeight())

return a2.getWeight().compareTo(a1.getWeight())
```

위 대신 아래를 사용한 함수 인터페이스 구현체를 전달하여 역순으로 정렬한다.

### thenComparing

정렬 조건을 추가하고 싶다면 thenComparing을 통해 간단히 추가할 수 있다.

```java
inventory.sort(comparing(Apple:getWeight)
			  .reversed()
			  .thenComparing(Apple::getCountry));
```

## 3.8.2 Predicate 조합

- negate
- and
- or

3가지 메서드를 제공한다.

주의: 순서는 왼쪽에서 오른쪽으로 연결된다.

A || B && C 라면 A || (B && C) 로 해석되는 통념과 달리
a.or(b).and(c)는 (A || B) && C 로 해석된다.

### 3.8.3 Function 조합

함수형 언어에서 자주 쓰이는 andThen (pipe)와 compose가 디폴트 메서드로 제공된다.

예를 들어 가상의 함수형 언어에서 
f, g, h 함수가 있을때

1. composeExample(x) -> f . g . h x 를 실행하면 f(g(h(x))) 가 실행된다. 이것이 compose 이다.
2. PipeExample(x) -> f | g | h . x 를 하면 h(g(f(x))) 가 실행된다. 이것이 pipe이다. 

> unix sh에서 쓰이는 파이프도 이러한 함수 컴포지션의 일종이라고 보면 된다.

자바에서는

```java

Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = x -> x / 4;

Function<Integer, Integer> composeExample = f.compose(g.compose(h));
Function<Integer, Integer> pipeExample = f.andThen(g).andThen(h);
```

로 표현한다.

### 둘 다 존재하는 이유

나름의 이유가 있다.

1. 전통적으로 함수형 언어에 둘 다 존재한다.
2. 자바에서 사용하기 훨씬 편하다.

https://stackoverflow.com/questions/43849066/java-8-functions-compose-and-andthen

기존 Function A에서 새로운 Function B를 붙일 때는 A.andThen(B)가 편하고 직관적이다.

하지만 Function이 아닌 기존 자바 메서드 C 다음에 동작 Function D를 추가하고 싶은 거라면

(Function<U, V> D).andThen(C) 를 해야만 한다.

그럴 경우 C.compose(D)가 훨씬 편하다.
