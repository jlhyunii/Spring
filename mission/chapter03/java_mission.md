## 1. 제네릭

### 클래스, 메서드 타입을 내부에서 지정하는 것이 아닌 사용자가 외부에서 지정하는 기법.

#### _"즉, 클래스를 정의할 때 구체적인 타입을 적지 않고 변수 형태로 적는 것이다. 타입은 객체를 생성할 때 기재한다."_

<br>

```java
ArrayList<String> list = new ArrayList<>();
```

- **<>** 로 제네릭을 표현할 수 있다.
- 괄호 안에 타입명을 기재한다.
- 위의 경우, 리스트 클래스 자료형의 타입은 **String**으로 지정되어 문자열 데이터만 리스트에 저장할 수 있는 것이 가능하다.

<br>

#### 제네릭을 사용해야 하는 이유

- 잘못된 타입이 사용될 수 있는 문제를 컴파일 과정에서 제기하는 것이 가능하다.
- 특정 타입에 의존하지 않고 여러 타입을 지원하는 코드 작성이 가능하다. >> 코드의 **재사용성** 극대화
- 코드의 가독성이 좋으며 유지보수가 쉽다.

<br>

#### 제네릭 타입 매개변수

- 다이아몬드 연산자
- 괄호 안에 식별자 기호를 지정함으로써 파라미터화를 하는 것이 가능하다.

```java
List<T>  // T - 타입 매개변수
ArrayList<String> list = new ArrayList<>(); // String - 매개변수화된 타입
```

<br>

#### 예시

```java
class A<T> {
    private T id;
    public T getId() {
        return id;
    }
    public void setId(T id) {
        this.id = id;
    }
}

public static void main(String[] args) {
    A<String> a1 = new A<>();
    A<Integer> a2 = new A<>();

    a1.setId("jihyun");
    a2.setId(22);

    System.out.println(a1.getId());
    System.out.println(a2.getId());
}
```

<br>

## 2.래퍼 클래스

### 기본 자료형의 값을 감싸 객체형태로 만든 것이 wrapper 클래스이다.

자바에서 제공하는 자료형은 크게 2가지로 구분할 수 있다.
1. **기본 자료형** : byte, short, int, long, float, double, char, boolean
2. **참조형 자료형** : 배열, class 등

_**즉, 기본 자료형을 참조형 자료형처럼 사용하기 위한 클래스이다.**_

<br>

#### wrapper 클래스 종류

```
byte -> Byte
short -> Short
int -> Integer
long -> Long
double -> Double
char -> Character
float -> Float
boolean -> Boolean
```

<br>

#### wrapper 클래스를 사용하는 이유

- 제네릭
- 기본 자료형의 값을 문자열로 변환 혹은 반대의 경우

<br>

#### 기본 자료형에서 wrapper 클래스 자료형으로 변경하고 싶은 경우?

**boxing** : 박싱한다.

```java
int primaryInt = 20;
Integer wrapperInt1 = Integer.valueOf(20);
```
- boxing 하는 방법 : wrapper class.valueOf(값) 

<br>

#### wrapper 클래스 자료형에서 기본 자료형으로 변경하고 싶은 경우?

**unboxing** : 언박싱한다.

```java
int primaryInt = 20;
primaryInt = wrapperInt.intValue();
```
- unboxing 하는 방법 : wrapper class객체.기본타입Value() 호출

<br>

#### 기본 자료형과 wrapper 클래스 연산은 가능할까?

기본 자료형은 언박싱 되어있고, wrapper 클래스 객체는 박싱 되어있기 때문에 마치 하나로 통일하여 연산해야할 것 같지만,
변환없이 자동으로 연산이 가능하다.

- autoBoxing : 자동 박싱
- autoUnboxing : 자동 언박싱

<br>

## 3. 옵셔널

### null인 값을 참조해도 NullPointerException이 발생하지 않도록 값을 래퍼로 감싸주는 타입이다.

<br>

```java
Optional<T> findById(ID id);
---
Member member = memberRepository.findById(memberId)
                                .orElseThrow(MemberNotFoundException::new)
```

- 반환값이 Optional 타입이기 때문에 원하는 member를 찾지 못했을 경우, MemberNotFoundException을 발생시켜 NullPointerException을 미리 방지한다.

<br>

#### Optional 사용 방법
```
1. empty() : 비어있는 Optional 객체를 생성.
2. of() : 전달된 값으로 새로운 Optional 객체를 생성.
3. ofNullable() : 비어있을 수도 있고, 아닐 수도 있는 Optional 객체를 생성.
4. equals() : Optional 객체의 값을 비교.
5. filter() : Optional 객체의 값을 조건에 따라 필터링.
6. map() : Optional 객체 내부의 값을 변환하여 결과를 새로운 Optional 객체로 반환.
7. flatMap() : 중첩된 Optional.
8. get() : Optional 객체의 값을 가져옴.
9. isEmpty() : Optional 객체가 비어있는지 확인.
10. isPresent() : Optional 객체가 있는지 확인.
11. ifPresent() : Optional 객체가 있다면 내부 연산을 실행.
12. ifPresentOrElse() : Optional 객체가 있다면 내부 연산을 실행, 없다면 또 다른 내부 연산을 실행.
13. or() : Optional 객체가 비어있다면 다른 Optional 객체를 반환.
14. orElse() : Optional 객체가 비어있다면 전달된 기본값 other를 반환.
15. orElseGet() : Optional 객체가 비어있다면 내부 함수를 실행하여 생성된 기본값을 반환.
16. orElseThrow() : Optional 객체가 비어있다면 Exception을 발생.
17. stream() : Optional 객체의 값을 Stream으로 변환.
18. toString() : Optional의 내부값을 String 문자열로 바꿔 변환.
19. hashCode() : Optional 객체의 HashCode를 반환.
```

<br>

## 4. 와일드카드

### 제네릭을 사용할 시 타입 파라미터가 상속 관계인 여러 타입의 제네릭을 허용하기 위해 사용한다.

즉, 제네릭 타입의 유연성을 위해 와일드카드가 필요하다.

<br>

#### 공변

- 상속 관계가 유지되는 경우.
- 클래스 A가 클래스 B의 하위 클래스일 때, 제네릭 타입 A도 제네릭 타입 B의 하위 타입이 되는 경우이다.

<br>

#### 반공변

- 상속 관계가 뒤집히는 경우.
- 클래스 A가 클래스 B의 하위 클래스일 때, 제네릭 타입 A는 제네릭 타입 B의 상위 타입이 되는 경우이다.

<br>

#### 불공변

- 상속 관계를 유지하지 않는 경우.
- 상위 클래스와 하위 클래스가 있을 때, 제네릭 타입 간에는 상속 관계가 성립하지 않는 경우이다.

<br>

#### 제네릭은 이 중에서 불공변에 해당한다. 타입이 다르면 아예 다른 객체임을 의미한다.<br> 따라서 전달받은 그 자체로만 캐스팅이 가능하다. >> 함수를 하나 더 정의해야하는 번거로움 발생

**_"따라서 와일드카드가 필요하다!"_**

<br>

#### 와일드카드의 장점

- 제네릭 타입을 하나만 받지 않고 여러 타입의 제네릭을 받을 수 있다.

<br>

### 불공변인 제네릭을 공변, 반공변화할 수 있을까?

#### 1. extends를 통한 공변화

```java
static void print(List <? extends vehicle> list)
```

- vehicle 포함 vehicle 하위 클래스를 타입 파라미터로 지정 가능하다.

<br>

#### 2. super를 통한 반공변화

```java
static void print(List <? super vehicle> output)
```

- 최소 vehicle 클래스를 타입 파라미터로 지정해야 한다.
- 즉, 상위 클래스를 허용한다는 의미이다.

<br>

#### <?>

- 이는 어떤 타입을 지정하는지 알 수 없다.
- 따라서, list에 넣을 수 없다.
- 추가적으로 null만 가능하다.

<br>

### QnA

Q. 제네릭은 공변/반공변/불공변 중 어디에 해당하며, 그로 인한 문제점이 무엇인가요?

A. 제네릭은 불공변. 만약 List<car>와 List<subway>가 있다면, 둘의 타입 파라미터는
다르기에 해당 클래스 두 개는 아예 다른 객체가 된다. 그러면 전달받은 그 자체로만 캐스팅이
가능하므로 함수를 하나 더 정의해주어야 한다는 번거로움이 발생한다. 따라서 제네릭의 유연성을
위하여 와일드카드가 필요하다.mi