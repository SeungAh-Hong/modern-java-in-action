> 👀 **들어가기 전에**

## Behavior Parameterization (동작 파라미터화)란?

: 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록

- 이 코드 블록은 나중에 프로그램에서 호출하며, 실행이 나중으로 미뤄짐<br>
  (나중에 실행될 메서드의 인수로 코드 블록을 전달할 수 있음)
- 결국 코드 블록에 따라 메서드의 동작이 파라미터화됨

#### 동작 파라미터화의 실생활 예시
- 룸메이트에게 이메일로 '우체국에 가서, 이 고객 번호를 사용하고, 관리자에게 이야기한 다음, 소포를 가져와라'라는 동작을 담은 내용을 전달한다.
- 그러면 룸메이트는 이메일을 확인한 후, 해당 내용을 인수로 받아 수행한다.

#### Q. 동작 파라미터화가 필요한 이유?
- 사용자 요구사항은 항상 바뀌기 때문!
- 변화하는 요구사항은 SW 엔지니어링에서 피할 수 없는 문제!

#### Q. 동작 파라미터화를 이용하면 장점은?
- **변화되는 요구사항**에 **엔지니어링적 비용을 가장 최소화하며 대응**할 수 있다.
- **새로 추가한 기능도 쉽게 구현**할 수 있다.
- 장기적인 관점에서 **유지 보수가 쉽다**.

<br>

# 2.1 변화하는 요구사항에 대응하기

## 변화하는 요구사항에 유연하게 대응할 수 있는 코드란?

다음은 사과 색을 정의하는 enum이다.
```java
enum Color { RED, GREEN }
```

<br>

### **요구사항 1**
: **녹색 사과만 찾아주세요.**

```java
public static List filterGreenApples(List<Apple> inventory) {
	List<Apple> result = new ArrayList<>(); // 사과 누적 리스트
	for (Apple apple : inventory) {
		if (GREEN.equals(apple.getColor()) { //  녹색 사과만 선택 
			result.add(apple);
		}
	} 
	return result; 
}
```

<br>

### **요구사항 2**
: **녹색 사과 + 어두운 녹색 사과, 빨간 사과, 어두운 빨간색 사과를 모두 찾아주세요.**

- 단순한 방법 : 메서드 코드를 복사해, if문의 조건만 수정한다.
  - 코드의 반복 발생 (DRY, Don't Repeat Yourself 원칙 위배)<br>
  - 더욱 다양한 변화되는 요구사항에는 적절히 대응 불가

- 이럴 때 적용하면 좋은 규칙!
  > ***`💡 Tip`***<br>
  > ***거의 비슷한 코드가 반복 존재한다면, 그 코드를 추상화한다.***

<br>

- 개선된 방법 : 색을 파라미터화할 수 있도록 메서드에 파라미터를 추가한다.<br>
	```java
	public static List filterApplesByColor(List<Apple> inventory, Color color) {
		List<Apple> result = new ArrayList<>();
		for (Apple apple: inventory) { 
			if (apple.getColor().equals(color)) {
				result.add(apple);
			}
		}
		return result; 
	}
	```
  사용 방법
	```java
	List<Apple> greenApples = filterApplesByColor(inventory, GREEN); 
	List<Apple> redApples = filterApplesByColor(inventory, RED);
	```

<br>


### **요구사항 3**
: **초록색 사과도 따로 골라두고, 무게가 150g 이상인 사과도 따로 모두 찾아주세요.**

- 단순한 방법 : 무게 정보 파라미터로 필터링하는 메서드 한 개 추가하기
  ```java
  public static List filterApplesByWeight(List inventory, int weight) { 
    List<Apple> result = new ArrayList<>();
    for (Apple apple: inventory) {
      if (apple.getWeight() > weight) {
        result.add(apple);
      }
    }
    return result;
  }
  ```
  - 코드의 반복 발생
  - 성능 저하
  - 반복을 줄이기 위해서는, 한 줄이 아니라 메서드 전체 구현을 고쳐야 함! 즉, 엔지니어링적으로 비싼 대가를 치러야 함

<br>

- **개선 방법 1** (비추천) : 모든 속성을 메서드 파라미터로 추가하기
  - 단, 어떤 기준으로 사과를 필터링할지 구분하는 또 다른 방법 필요
  - 색이나 무게 중 어떤 것을 기준으로 필터링할지 가리키는 플래그 또 추가 필요

<br>

- **개선 방법 1-1** (비추천) : 여러 속성을 filter라는 메서드로 합치기
  ```java
  public static List filterApples(List<Apple> inventory, Color color, 
                int weight, boolean flag) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple： inventory) {
      if ((flag && apple.getColor().equals(color)) ||     // 색이나 무게 선택 방법
        (!flag && apple.getWeight() > weight)) {        // 바람직 X
        result.add(apple);
      }
    }
    return result;
  }
  ```
  **사용 방법**
  ```java
  List<Apple> greenApples = filterApples(inventory, GREEN, 0, true); 
  List<Apple> heavyApples = filterApples(inventory, null, 150, false);
  ```
  - 형편 없는 코드
    - `true`/`false`의 불명확한 사용
  - 추가적인 요구사항에 유연하게 대응 불가
    - 여러 중복된 `filterApplesby...()`메서드를 만들어야 함
    - 혹은 모든 것을 처리하는 거대한 하나의 필터 메서드를 구현 해야 함

<br>

> 💡 ***To Be Continued...***<br>
> *위에는 조건들이 나름 잘 정의되어 있었지만, 문제가 잘 정의되어 있지 않은 상황이라면?<br>
> => 곤란해짐!*<br>
> *👉🏻 `filterApples()`에다가 분류 기준을 동작파라미터화를 이용해서 넣어보면 어떨까?`*
