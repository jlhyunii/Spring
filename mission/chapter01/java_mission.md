## 1. 자바의 접근제어자

### public > protected > default > private

- **public** : 모든 클래스에서 접근 가능.
- **protected** : 같은 패키지 안의 모든 클래스와, 다른 패키지의 자식 클래스에서 접근 가능.
- **default** : 같은 패키지 안의 클래스에서만 접근 가능. ( = 접근제어자를 생략한 경우)
- **private** : 같은 클래스 안에 있는 멤버들만 접근 가능.

### 접근제어자의 사용 목적

- **캡슐화** : 클래스의 내부 구현 세부사항을 숨기고 외부에서 직접 접근하지 못하도록 한다.  >> 안정성, 유지보수성<br>

````java
public class Car {
    private String brand;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }
}
````

- **데이터 보호** : 멤버 변수에 대한 접근을 제한함으로써 데이터의 무결성과 안정성을 보호할 수 있다.

````java
public class BankAccount {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
    }
}
````

- **코드 재사용** : 상속 관계에서 부모 클래스의 메소드와 변수를 자식 클래스에서 재사용할 수 있다.

<br>

## 2. 클래스와 인터페이스

### 클래스

클래스는 객체를 생성하기 위한 설계도로, 객체의 속성과 메소드를 정의한다.

- **속성** : 객체의 상태.<br>
&emsp; ex) Car 클래스의 속성 : color, model, year 등
- **메소드** : 객체가 수행할 수 있는 작업.<br>
&emsp; ex) Car 클래스의 메소드 : drive() 등

<br>

### 인터페이스

인터페이스는 클래스가 구현해야 하는 메소드의 집합을 정의한다.<br>
인터페이스 자체는 구현을 가지지 않고 메소드의 시그니처만을 포함한다.

<br>

### 클래스 vs 인터페이스

- **구현** : 클래스는 구현을 포함하지만, 인터페이스는 구현을 포함하지 않는다.
- **상속** : 클래스는 단일 상속을 지원하고, 인터페이스는 다중 상속을 지원한다.
- **필드** : 클래스는 필드를 가질 수 있지만, 인터페이스는 기본적으로 상수 제외 필드를 가질 수 없다.
- **접근제어자** : 클래스는 모두 가능하지만, 인터페이스는 기본적으로 모든 메소드가 public이며 나머지는 불가능하다.

<br>

## 3. 상속

상속은 한 클래스의 속성과 메소드를 다른 클래스가 물려받는 기능이다.

<br>

### 상속의 종류

- **단일 상속** : 한 클래스가 하나의 부모 클래스를 상속받는 경우.
- **다중 상속** : 한 클래스가 여러 부모 클래스를 상속받는 경우. (지원되지 않는 경우 존재)
- **다단계 상속** : 한 클래스가 다른 클래스를 상속받고, 그 클래스가 다시 다른 클래스를 상속받는 경우.

<br>

### 상속의 장점

- **코드 재사용** : 공통된 기능을 부모 클래스에서 정의하고 자식 클래스에서 재사용할 수 있다.
- **유지보수 용이** : 공통된 기능을 부모 클래스에서 수정하면 자식 클래스에도 자동으로 반영된다.
- **다형성** : 상속을 통해 부모 클래스의 타입으로 자식 클래스의 객체를 사용할 수 있다.

<br>

### 상속 예제 코드

````java
public class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public void eat() {
        System.out.println(name + " is eating");
    }
}

public class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
    
    public void bark() {
        System.out.println(name + " is barking");
    }
}
````

<br>

### 면접 질문

Q : 클래스와 인터페이스의 차이점은 뭘까요?
