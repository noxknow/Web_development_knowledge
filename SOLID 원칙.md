# SOLID 원칙

## 1. SRP: 단일 책임 원칙

### SRP(Single Responsibility Principle)

> "한 클래스는 하나의 책임만 가져야 한다."
> 

하나의 책임이란 말은 문맥과 상황에 따른 모호한 말이다. 중요한 기준은 **'변경'**이다.

변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것이라 말할 수 있다.

SRP 원칙을 적용하면 다른 클래스들이 서로 영향을 미치는 연쇄작용을 줄일 수 있다.

즉, 응집도(cohesion)는 높이고 결합도(coupling)은 낮출 수 있다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/0e659059-e622-4df7-b96e-e87539b1d78d)

위의 남자 클래스에 SRP를 적용한다고 가정해보자.

다음과 같이 책임을 분배하여 클래스를 분리할 수 있을 것이다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/270957bb-9f1f-41d6-8fca-d8b218d70e93)

이렇게 책임을 적절하게 분배함으로써 코드의 가독성 향상, 유지보수 용이라는 이점을 가질 수 있다.

---

## 2. OCP: 개방 폐쇄 원칙

### OCP(Open Closed Priciple)

> "소프트웨어 요소는 확장에는 열려있으나 변경에는 닫혀 있어야 한다."
> 

이는 요구사항의 변경이나 추가사항의 발생하더라도, 기존 구성요소는 수정이 일어나지 말아야하며 쉽게 확장이 가능하여 재사용할 수 있어야 한다는 뜻이다.

로버트 마틴은 OCP는 관리가 용이하고 재사용 가능한 코드를 만드는 기반이며, OCP를 가능케 하는 중요한 메커니즘은 추상화(Abstraction)와 다형성(Polymorphism)이라고 설명한다.

OCP는 객체지향의 장점을 극대화하는 아주 중요한 원리이다.

### **OCP 원칙 위반 예제와 수정하기**

다음 Animal 클래스가 있고, Animal 타입을 받아 고양이 혹은 개면 각각 동물의 소리에 맞춰 출력하는 HelloAnimal 클래스가 있다.

메인 메소드에서 cat과 dog 동물 객체를 만들고 HelloAnimal 클래스의 ~~hello()~~ 메소드를 통해 실행해보면 오류없이 잘 동작됨을 확인 할 수 있다.

```java
class Animal {
	String type;
    
    Animal(String type) {
    	this.type = type;
    }
}

// 동물 타입을 받아 각 동물에 맞춰 울음소리를 내게 하는 클래스 모듈
class HelloAnimal {
    void hello(Animal animal) {
        if(animal.type.equals("Cat")) {
            System.out.println("냐옹");
        } else if(animal.type.equals("Dog")) {
            System.out.println("멍멍");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        HelloAnimal hello = new HelloAnimal();
        
        Animal cat = new Animal("Cat");
        Animal dog = new Animal("Dog");

        hello.hello(cat); // 냐옹
        hello.hello(dog); // 멍멍
    }
}
```

동작 자체는 문제가 없지만 문제는 **기능 추가** 이다.

만일 '고양이'와 '개' 외에 '양'이나 '사자'를 추가하게 된다면 어떻게 될까?

당연히 HelloAnimal 클래스를 수정해주어야 한다. 각 객체의 필드 변수에 맞게 if문을 분기하여 구성해줘야 한다.

```java
class HelloAnimal {
	// 기능을 확장하기 위해서는 클래스 내부 구성을 일일히 수정해야 하는 번거로움이 생긴다.
    void hello(Animal animal) {
        if (animal.type.equals("Cat")) {
            System.out.println("냐옹");
        } else if (animal.type.equals("Dog")) {
            System.out.println("멍멍");
        } else if (animal.type.equals("Sheep")) {
            System.out.println("메에에");
        } else if (animal.type.equals("Lion")) {
            System.out.println("어흥");
        }
        // ...
    }
}
```

이런식으로 코드를 구성한다면, 동물이 추가될때마다 계속 코드를 일일히 변경해줘야 하는 번거로운 작업이 생기게 된다.

이는 처음부터 설계서부터 잘못되었기 때문에 일어나는 현상이다.

따라서 처음에 OCP 설계 원칙에 따라 적절한 추상화 클래스를 구성하고 이를 상속하여 확장시키는 관계로 구성하면 변경에는 닫히고(closed) 추가에는 열려있는(opened) 프로그램을 만들수 있다.

이때 어떤식으로 OCP 대로 추상화 설계를 할 것인가에 대해서는 다음 규칙대로 이행하면 된다.

1. 먼저 변경(확장)될 것과 변하지 않을 것을 엄격히 구분한다.
2. 이 두 모듈이 만나는 지점에 추상화(추상클래스 or 인터페이스)를 정의한다.
3. 구현체에 의존하기보다 정의한 추상화에 의존하도록 코드를 작성 한다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/d0955cde-87d6-45ff-a8ad-9386dee22cb6)

```java
// 추상화
abstract class Animal {
    abstract void speak();
}

class Cat extends Animal { // 상속
    void speak() {
        System.out.println("냐옹");
    }
}

class Dog extends Animal { // 상속
    void speak() {
        System.out.println("멍멍");
    }
}

// 추상클래스를 상속만 하면 메소드 강제 구현 규칙으로 규격화만 하면 확장에 제한 없다 (opened)
class Sheep extends Animal {
    void speak() {
        System.out.println("매에에");
    }
}

class Lion extends Animal {
    void speak() {
        System.out.println("어흥");
    }
}

// 기능 확장으로 인한 클래스가 추가되어도, 더이상 수정할 필요가 없어진다 (closed)
class HelloAnimal {
    void hello(Animal animal) {
        animal.speak();
    }
}

public class Main {
    public static void main(String[] args) {
        HelloAnimal hello = new HelloAnimal();

        Animal cat = new Cat();
        Animal dog = new Dog();

        Animal sheep = new Sheep();
        Animal lion = new Lion();

        hello.hello(cat); // 냐옹
        hello.hello(dog); // 멍멍
        hello.hello(sheep); // 매에에
        hello.hello(lion); // 어흥
    }
}
```

이처럼 클래스를 설계할 때 변할 부분과 변하지 않을 부분을 명확히 구분해야한다.

변할 수 있는 부분은 추상화하여 상속하는 클래스가 의존할 수 있게 코드를 작성한다.

---

## 3. LSP: 리스코프 치환 원칙

### LSP(Listov Substitution Priciple)

> "서브 타입은 언제나 자신의 기반 타입으로 교체할 수 있어야 한다."
> 

프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다. 이는 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것을 말하며, 다형성을 지원하기 위한 원칙이다. 인터페이스를 구현한 구현체는 믿고 사용하려면 이 원칙이 필요하다.

상속을 잘 활용하고 있다면, 이미 LSP는 잘 구현하고 있는 것이다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/c0ac219e-b3d2-486a-a145-490613bf16df)

- LSP를 잘 구현한 사례 : 박쥐는 포유류의 역할을 할 수 있다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/40e8b898-a173-4096-ab37-90b26d4aef08)

- LSP 위반 사례 : 딸은 아버지의 역할을 할 수 없다.

쉬운 예시를 하나 더 들자면, 자동차 인터페이스의 엑셀은 앞으로 가게 하는 기능인데 이를 뒤로 가게 구현하면 LSP를 위반한 것이다. 느리더라도 앞으로 가게 구현해야 한다.

---

## 4. **ISP: 인터페이스 분리 원칙**

### **ISP(Interface Segregation Principle)**

> "클라이언트는 자신이 사용하지 않는 메소드에 의존 관계를 맺으면 안 된다."
> 

이는 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다는 원칙이다.

다시말해, 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다는 뜻으로, 하나의 큰 인터페이스를 상속 받기 보다는 인터페이스를 구체적이고 작은 단위들로 분리시켜 꼭 필요한 인터페이스만 상속하자는 의미이다.

SRP가 클래스의 단일책임을 강조했다면 ISP는 인터페이스의 단일책임을 강조한다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/a678f028-00a3-453c-9237-59ad21eb80a2)

위의 SRP 예제에서 남자 클래스를 단일 책임을 갖는 클래스들로 나누었다. 이것은 너무 많은 클래스 구현을 불러 온다.

ISP를 적용하면 다양한 역할을 인터페이스로 만들고, 남자 클래스는 그 인터페이스를 구현한 클래스로 만들 수 있다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/2f4380f7-3698-4f63-83da-a2ad7398218a)

---

## 5. DIP: 의존관계 역전 원칙

### DIP(Dependency Inversion Principle)

> "추상화된 것은 구체적인 것에 의존하면 안된다. 구체적인 것이 추상화된 것에 의존해야 한다."
> 

쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻이다. 구현체에 의존하게 되면 변경이 아주 어려워진다.

즉, 인터페이스를 적극적으로 활용하라는 의미이다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/34942a6f-cb19-4de5-81eb-631ca0294641)

자동차는 스노우타이어에 의존하고 있다.

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/9c486f4c-0c60-4df4-bf90-e4a8959b8c74)

해당 관계를 타이어 인터페이스로 역전 시킨다. 이것이 의존성 주입(DI)이다.

상위클래스일 수록, 인터페이스일 수록, 추상 클래스일 수록 변하지 않을 가능성이 크다. 하위클래스나 구체클래스가 아닌 더 추상적인 것에 의존하라는 것이 의존 역전 원칙이다.

### ✔ 의존성 주입(Dependency Injection, DI): 외부에서 두 객체 간의 관계를 결정하고 주입하는 것.

cf) https://bnzn2426.tistory.com/125
