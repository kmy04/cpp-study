# cpp-study

## 접근 제한자

| 접근 위치              | `public` | `protected` | `private` |
| ------------------ | -------- | ----------- | --------- |
| 클래스 내부             | ✅ 가능     | ✅ 가능        | ✅ 가능      |
| **파생 클래스(자식 클래스)** | ✅ 가능     | ✅ 가능        | ❌ 불가능     |
| 외부(일반 함수, main 등)  | ✅ 가능     | ❌ 불가능       | ❌ 불가능     |


🔓 `public` (공개 접근)

+ 어디서든 접근이 가능하다

+ 예: 해당 클래스의 객체를 사용하는 모든 코드에서 멤버에 접근할 수 있다.


🔓 `protected` (보호된 접근)

+ **자기 자신**과 **파생 클래스(자식 클래스) 내부**에서만 접근 가능

+ 클래스 외(main이나 일반 함수 등)에서는 접근 불가능

🔒 `private` (비공개 접근)

+ 같은 클래스 내부에서만 접근 가능하다.

+ 외부(즉, 클래스 밖에 있는 함수나 다른 클래스 등)에서는 접근할 수 없다.

**"외부"의 범위는 어디까지인가?**

+ 외부란 다음을 포함한다:

  1. 클래스의 멤버 함수가 아닌 일반 함수
 
  2. 다른 클래스의 멤버 함수
 
  3. main 함수를 포함한 프로그램 전체에서 클래스 밖에 있는 코드
 
### ✅ **예외**

+ friend 함수나 friend 클래스로 지정된 경우는 예외적으로 private 멤버에 접근할 수 있습니다.

+ 같은 클래스 내부에서라면 private 멤버 간에 자유롭게 접근할 수 있습니다.

### ✅ **핵심 개념: “접근 권한”은 “누가” 접근하느냐에 따라 달라진다**

**상속을 받았다는 것은:**

+ **자식 클래스 내부**에서 부모 클래스의 `protected` 멤버에 **접근**할 권한이 생긴 것

+ 즉, **자식 객체가 "스스로" `protected`멤버에 접근하는 코드를 작성할 수 있다는 것**

### 🚫 **“외부 코드(main 함수)”는 여전히 protected 멤버에 접근할 수 없습니다**

```cpp
#include <iostream>

class Parent
{
protected:
    int _secretData;
public:
    Parent(/* args */);
    ~Parent();
};

Parent::Parent(/* args */)
{
}

Parent::~Parent()
{
}

class Child : public Parent
{
public:
    Child(/* args */) {}
    ~Child() {}
};


int main() {
    Child child;

    child._secretData = 1;
}
```

여기서 child._secretData는 **main 함수(=외부)** 가 `child`객체의 `_secretData`멤버에 접근하려는 시도이다.

그리고 `_secretData`는 `protected`멤버 이므로, **외부에서는 접근할 수 없다.**

### 🔍 “자식이 상속받았다는 것” vs “외부에서 자식 객체를 사용할 수 있다는 것”의 차이

| 상황                             | 접근 가능 여부 | 설명                       |
| ------------------------------ | -------- | ------------------------ |
| `Child` 클래스 **내부**에서 `a` 멤버 사용 | ✅ 가능     | 자식 클래스니까 접근 허용           |
| \*\*main 함수(외부)\*\*에서 `a.a` 사용 | ❌ 불가     | 외부는 `protected` 멤버 접근 불가 |


## 캡슐화

### ✅ 개념 요약

  데이터와 이를 처리하는 함수를 하나의 클래스로 묶고, 내부 세부 구현은 감추는 것
### ✅ 핵심: "정보 은닉"

```cpp
class BankAccount {
private:
    double balance; // 외부에 숨김

public:
    void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    void withdraw(double amount) {
        if (amount <= balance) balance -= amount;
    }

    double getBalance() const {
        return balance;
    }
};
```

### 🔍 깊은 의미

+ `balance`는 **직접 접근 불가** -> 잘못된 상태(음수 잔액 등)를 차단

+ 인터페이스 (`deposit`, `withdraw`)를 통해서만 상태 변화 가능

+ 즉, **객체의 일관성과 무결성 보장**

### 🎯 실제 설계에서의 힘

+ 내부 구현이 바뀌어도, 외부는 **함수만 알고 있으면 사용 가능** -> 유연한 유지 보수

+ API 제공 시, **클래스 사용자에게 최소한의 정보만 공개** 가능

+ **보안**, **버그 방지**, **코드 관리**의 핵심 축

### 🧩 왜 필요한가? (캡슐화의 목적)

| 목적       | 설명                              |
| -------- | ------------------------------- |
| ✅ 정보 은닉  | 내부 구현을 숨겨 잘못된 접근이나 오용을 막음       |
| ✅ 일관성 유지 | 객체의 상태가 항상 유효하도록 제한함            |
| ✅ 변경에 유연 | 내부 구현을 바꿔도 외부는 영향 없음 (인터페이스 고정) |
| ✅ 책임 분리  | “무엇을 할 수 있는가”와 “어떻게 할 것인가”를 분리  |

### 4. 예시로 이해하기

**❌ 캡슐화 없이 작성한 코드**
```cpp
class User {
public:
    std::string name;
    int age;
};

int main() {
    User u;
    u.age = -999; // ❌ 잘못된 나이
}
```
문제점: 외부에서 객체의 상태를 무제한으로 조작 가능 → 데이터 무결성 붕괴

**✅ 캡슐화 적용**

```cpp
class User {
private:
    std::string name;
    int age;

public:
    void setAge(int a) {
        if (a >= 0 && a <= 150)
            age = a;
    }

    int getAge() const {
        return age;
    }
};
```

+ 외부는 오직 setAge() / getAge()로만 접근 가능

+ 객체가 스스로 “**내 상태를 보호하고, 유효성 검사도 내가 한다**” 는 구조

**🛠️ 내부 구현 변경의 유연성**
캡슐화가 잘 되어 있으면, 내부 구조를 바꾸더라도 외부 인터페이스는 그대로 유지할 수 있다.

```cpp
// 바뀌기 전
class Point {
private:
    int x, y;
public:
    void setX(int val) { x = val; }
    int getX() const { return x; }
};
```
```cpp
// 내부 구현만 바꿈
class Point {
private:
    std::pair<int, int> coords;
public:
    void setX(int val) { coords.first = val; }
    int getX() const { return coords.first; }
};
```

+ 내부 구조는 바뀌었지만, 외부에서 쓰는 방식은 동일

  -> 코드 변경의 영향도를 줄이고, 유지보수가 쉬워짐

### 🧠 객체지향 설계 원칙과의 연결

**✅ SRP (단일 책임 원칙)**

+ 객체는 자신의 **상태를 스스로 관리**해야 하며,

+ 캡슐화는 그 책임을 객체 내부로 국한시킴

**✅ OCP (개방-폐쇄 원칙)**

+ 내부를 바꿔도 외부가 안 바뀌면 확장에 유리

### 🎓 요약: 캡슐화는 단순히 ‘숨기는 것’이 아니다

| 오해                    | 사실                                      |
| --------------------- | --------------------------------------- |
| 캡슐화는 `private` 쓰는 거지? | `private`는 도구일 뿐, **역할 분리와 제어권 부여**가 본질 |
| 캡슐화는 보안이야?            | 보안도 되지만 **구조적 안정성과 일관성 보장**이 더 중요한 목표   |

### ✅ 결론

캡슐화는 객체가 "**자기 상태는 내가 관리하고, 외부는 필요한 기능만 써라**"라고 주장하는 구조이다.

그 결과로 생기는 장점은:

+ **코드 안정성**

+ **수정 용이성**

+ **모듈화**
+ 
  + **모듈화(Modularization)** 는 복잡한 프로그램을 작은 단위(모듈)로 나누어 설계하고 구현하는 방법이다.
 
  + 리모컨을 생각하면 이핵하기 쉬운데 버튼이 서로 상호 의존하는 관계가 되면 버튼 하나가 고장 났을 때 다른 버튼들도 같이 고장나는 문제가 발생할 수 있기에 **독립성**을 갖춰야하고
 
  + 여러 기능을이나 역할을 담당하게 되면 볼륨을 올리고 싶은데 전원이 종료되는 원치 않는 상황이 발생하는 문제가 생길 수 있기 때문에 **책임 분리**를 갖춰야한다.
 
  + 이렇게 생각하면 모듈화가 왜 필요한가에 대해서 간단하게 이해할 수 있을 것이다.

+ **테스트 편의성**


## 상속

**상속은 기존 클래스(부모, 또는 기반 클래스)의 속성과 기능을 새로운 클래스(자식, 또는 파생클래스)가 물려받는 것이다.

### 1. 기본 구조

```cpp
class Parent {
public:
    void speak() {
        std::cout << "I am Parent\n";
    }
};

class Child : public Parent {
    // Parent의 speak()를 자동으로 물려받음
};
```

+ `Child`는 `Parent`의 `speak()` 메서드를 아무것도 안 해도 사용할 수 있다.

+ `Child`는 `Parent`의 모든 **멤버 변수/함수 중 접근 가능한 것들**을 상속받는다.

### 2. 상속의 이점

| 장점       | 설명                              |
| -------- | ------------------------------- |
| 코드 재사용성  | 부모 클래스의 코드를 재사용해 중복 줄임          |
| 계층 구조 표현 | "A는 B의 일종이다"라는 관계 표현 (`is-a`)   |
| 다형성과 연계  | 부모 타입으로 자식 객체를 다룰 수 있음 (다형성 가능) |

**🧠 개념 요약: is-a 관계란?**

**자식 클래스는 부모 클래스의 한 종류다**라는 의미이다.

즉, 어떤 클래스 A가 B를 상속한다면
-> A는 B의 모든 특성과 기능을 가진 **B의 특수화 버전**이라는 뜻이다.

**✅ 예제 1: 동물 예시**

```cpp
class Animal {
public:
    void breathe() { std::cout << "Breathing\n"; }
};

class Dog : public Animal {
public:
    void bark() { std::cout << "Bark!\n"; }
};
```

+ `Dog`는 `Animal`을 상속받았으므로,

+ "Dog **is-a** Animal" → `개는 동물이다` → 의미가 맞음

+ 따라서 이 상속 구조는 적절함

**❌ 예제 2: 잘못된 상속 (is-a 관계가 아님)**

```cpp
class Engine { };
class Car : public Engine { }; // 잘못된 구조
```

+ "Car is-a Engine" → 자동차는 엔진이다 ❌ (말이 안 됨)

+ 실제 관계는 "Car has-a Engine" → 자동차는 엔진을 가지고 있다

**📌 이런 경우에는 상속이 아니라 포함(composition) 을 사용해야 합니다.**
```cpp
class Car {
private:
    Engine engine; // 포함 관계
};
```

| 관계    | 의미           | 예시              | 코드 표현                           |
| ----- | ------------ | --------------- | ------------------------------- |
| is-a  | A는 B의 일종이다   | 개는 동물이다         | `class Dog : public Animal`     |
| has-a | A는 B를 가지고 있다 | 자동차는 엔진을 가지고 있다 | `class Car { Engine engine; };` |

**✅ 결론**

+ 상속을 사용할 때는 반드시 is-a 관계가 성립하는지 **논리적으로 따져야 한다**.

+ 말이 안 되는 상속 구조는 설계를 망치고 유지 보수를 어렵게 만든다.

+ 논리적으로 맞으면 → 상속 (is-a)

+ 소유 관계라면 → 포함 (has-a)

### 3. 상속의 종류 (접근 지정자 기준)

```cpp
class Child : public Parent  // 일반적인 상속
class Child : protected Parent
class Child : private Parent
```
| 형식             | Parent 멤버가 자식에서 어떻게 보이는가                  |
| -------------- | ----------------------------------------- |
| `public` 상속    | public → public, protected → protected    |
| `protected` 상속 | public → protected, protected → protected |
| `private` 상속   | public/protected → private                |

※ private 멤버는 어떤 방식으로 상속해도 **자식이 접근 불가** (단, 부모 함수 통해 간접적으로 접근 가능)

**📌 세 가지 상속 방식의 핵심 차이**
| 부모 멤버의 접근 지정자 | public 상속 | protected 상속 | private 상속 |
| ------------- | --------- | ------------ | ---------- |
| `public`      | public    | protected    | private    |
| `protected`   | protected | protected    | private    |
| `private`     | 접근 불가     | 접근 불가        | 접근 불가      |

+ **public 상속**: 부모의 인터페이스를 외부에 그대로 노출

+ **protected 상속**: 부모의 인터페이스를 자식 안에서는 사용하지만, 외부엔 숨김

+ **private 상속**: 부모의 인터페이스를 자식 외부와 손자에게 모두 숨김

**🧠 정리**
| 상속 방식         | 자식 클래스 내부 | 외부 코드                   | 손자 클래스                   |
| ------------- | --------- | ----------------------- | ------------------------ |
| **public**    | 접근 가능     | 접근 가능 (`public` 그대로 유지) | 접근 가능                    |
| **protected** | 접근 가능     | 접근 불가                   | 접근 가능 (`protected`로 유지됨) |
| **private**   | 접근 가능     | 접근 불가                   | ❌ 접근 불가 (상속이 끊김)         |

**부모에서 `private`으로 선언된 멤버 함수, 변수들은 자식에서 접근 불가능 그 외에 `public`, `protected`는 접근 가능**

**상속에 접근 제한자는 자식 클래스에 적용되는 것이 아닌 자식 클래스 외부에 대한 접근 제한 레벨이라고 보면 된다.**

**🔚 결론**
+ **public 상속**: 부모의 인터페이스를 그대로 노출하고 싶을 때 (가장 일반적)

+ **protected 상속**: 외부에는 숨기되, 자식/손자들끼리만 공유하고 싶을 때

+ **private 상속**: 상속은 하되 자식을 제외한 모두에게 숨기고 싶을 때

+ 부모 클래스 내부에서 정의된 접근 제한자는 상속받는 자식에게도 영향을 주지만, 상속을 할 때 지정하는 접근 제한자는 자식에게 영향을 주지 않는다.

### 🔁 4. 함수 오버라이딩 (재정의)

자식 클래스가 부모의 함수를 다르게 구현하고 싶을 때 사용한다.

```cpp
class Parent {
public:
    virtual void speak() {
        std::cout << "Parent speaks\n";
    }
};

class Child : public Parent {
public:
    void speak() override {
        std::cout << "Child speaks\n";
    }
};
```

+ `virtual`은 오버라이딩이 가능하게 한다.

+ `override`는 오버라이딩임을 명시한다. (실수 방지용)\

`override`키워드가 하는 일은 무엇인가?

**📌 예시**

🔸 실수한 오버라이딩 (override 없음)

```cpp
class Base {
public:
    virtual void print() const { std::cout << "Base\n"; }
};

class Derived : public Base {
public:
    void print() { std::cout << "Derived\n"; } // ❌ const 누락 → override 실패
};
```

이 경우, `print()`는 새로운 함수가 되어버린다.
즉, 내가 원한건 오버라이딩인데 오버라이딩이 아니게 된다.

🔹 override 사용

```cpp
class Derived : public Base {
public:
    void print() override { std::cout << "Derived\n"; } // ❌ 컴파일 에러: const 누락
};
```
`override` 덕분에 컴파일 타임에 실수를 잡아낸다.

#### 여기서 `final` 키워드가 추가적으로 등장해야되는데 `final`키워드는 **상속 계층에서"이 이상은 확장 금지"**라는 의미를 갖는다

간단한 예시:

```cpp
class Base {
public:
    virtual void foo();   // 자식이 override 가능
    virtual void bar();   // 자식이 override 가능
};

class Intermediate : public Base {
public:
    void foo() override final;  // ✅ 여기서 완성, 이후 자식에서 변경 불가
    void bar() override;        // 계속 오버라이드 가능
};
```

+ `foo()`함수는 여기서 한 번만 더 오버라이드하고 **그 이후에는 변경되지 않기를 원할**  수 있다.

+ 즉, **"이 가상 함수 체인은 여기서 끝나야 한다"**는 것을 명시하는 것이다.

+ 그 이후에 `foo*()`함수를 오버라이드 하려고 하면 컴파일 에러가 난다.

참고로 클래스에도 붙여서 사용이 가능하다

```cpp
class Parent { };
class Child final : public Parent { };
```

+ `child`는 `parent`를 상속받았지만 `child`는 **다른 클래스에 자신을 상속시킬 수 없다**는 의미이다.

### 5. 생성자와 소멸자

+ 부모 클래스의 생성자는 자식 클래스의 생성자보다 먼저 호출된다.

+ 소멸자는 자식 클래스의 소멸자가 먼저 호출되고 그 이후에 부모 클래스의 소멸자가 호출된다.

```cpp
class Parent {
public:
    Parent() { std::cout << "Parent constructor\n"; }
    ~Parent() { std::cout << "Parent destructor\n"; }
};

class Child : public Parent {
public:
    Child() { std::cout << "Child constructor\n"; }
    ~Child() { std::cout << "Child destructor\n"; }
};
```

출력: 

```
Parent constructor
Child constructor
Child destructor
Parent destructor
```

### ✅ 요약

+ **상속은 "코드를 물려주는" 기능이다.**

+ 부모의 public/protected멤버를 자식이 사용할 수 있다. (private도 간접적으로 사용 가능)

+ 함수 오버라이딩, 다형성 등과 함께 사용하면 좋다

+ 생성자/소멸자 호출 순서 주의

+ 접근 지정자에 따라 상속 구조가 크게 달라진다.

## 다형성 (Polymorphism)

### ✅ 개념 요약

+ **하나의 인터페이스로 다양한 객체를 동일하게 다룰 수 있게 하는 능력**

+ 즉, **같은 함수 호출이 다른 방식으로 동작** 하게 하는 것.

다형성은 주로 두 가지 형태로 나뉜다.

1. 컴파일 타임 다형성 (정적 다형성, Static Polymorphism)

2. 런타임 다형성 (동적 다형성, Dynamic Polymorphism)

### 🔑 1. 컴파일 타임 다형성 (정적 다형성)

컴파일 타임 다형성은 **오버로딩(Overloading)** 과 **템플릿(Template)** 을 통해 이루어진다.

이 방식에서는 **어떤 메서드를 호출할지 컴파일 시점에 결정**되기 때문에, 코드 싫애 전에 이미 호출할 함수나 메서드가 결정된다.

#### 🔸 메서드 오버로딩 (Method Overloading)

같은 이름의 함수를 **매개변수의 개수나 타입에 따라 다르게 정의**하여 사용하는 방식이다.

```cpp
class Printer {
public:
    void print(int n) {
        std::cout << "Printing integer: " << n << std::endl;
    }

    void print(double d) {
        std::cout << "Printing double: " << d << std::endl;
    }
};

int main() {
    Printer p;
    p.print(5);      // print(int n) 호출
    p.print(3.14);   // print(double d) 호출
}
```
여기서 print 함수는 매개변수 타입에 따라 **어떤 버전의 함수를 사용할지 컴파일 시점**에 결정된다.

#### 🔸 템플릿 (Template)

템플릿은 **다양한 타입에 대해 동일한 작업을 수행할 수 있는 코드**를 작성하는 방법이다.

이것도 **컴파일 타임에 타입이 결정**되므로 컴파일 타임 다형성에 속한다.

```cpp
template <typename T>
void print(T value) {
    std::cout << value << std::endl;
}

int main() {
    print(42);     // int 타입
    print(3.14);   // double 타입
    print("Hello"); // const char* 타입
}
```

`print` 함수는 호출될 때마다 매개변수 타입에 맞게 컴파일 시점에 **적절한 함수**가 생성된다.

### 🔑 2. 런타임 다형성 (동적 다형성)

**상속과 가상함수(virtual functions)** 를 통해 이루어진다. 이 방식에서는 **어떤 메서드가 호출될지 실행 시점에 결정**된다.

런타임 다형성은 **다형성의 핵심**으로, **상속을 통해 공통된 인터페이스를 정의하고, 자식 클래스에서 이를 오버라이딩**하여 구현을 변경할 수 있다. 주로 **부모 클래스의 포인터 또는 참조를 사용해 자식 객체를 다룰 때** 사용된다.

#### 🔸 가상 함수 (Virtual Function)

`virtual`키워드를 사용하여 부모 클래스에서 정의한 함수가 자식 클래스에서 **다르게 동작하도록 허용**할 수 있다.

```cpp
class Shape {
public:
    virtual void draw() const { std::cout << "Drawing shape" << std::endl; }
    virtual ~Shape() {}  // 가상 소멸자
};

class Circle : public Shape {
public:
    void draw() const override { std::cout << "Drawing circle" << std::endl; }
};

class Square : public Shape {
public:
    void draw() const override { std::cout << "Drawing square" << std::endl; }
};

int main() {
    Shape* shape1 = new Circle();
    Shape* shape2 = new Square();

    shape1->draw(); // "Drawing circle"
    shape2->draw(); // "Drawing square"

    delete shape1;
    delete shape2;
}
```

+ `draw` 함수는 `Shape` 클래스에서 정의되어 있지만, **`Circle`** 과 **`Square`** 클래스에서 **각기 다른 방식으로 오버라이딩**된다.

+ **부모 클래스인 `Shape`의 포인터를 사용해도, 실제 객체의 타입에 맞는 `draw` 함수**가 호출됩니다. 이 부분이 런타임 다형성의 핵심이다.


#### 🔸 가상 소멸자 (Virtual Destructor)

가상 함수의 개념은 소멸자에도 적용된다.

소멸자가 가상으로 선언되지 않으면, 부모 클래스의 포인터를 사용해 객체를 삭제할 때 **자식 클래스의 소멸자가 호출되지 않고** 메모리 누수가 발생할 수 있다.

```cpp
class Base {
public:
    virtual ~Base() { std::cout << "Base Destructor\n"; }
};

class Derived : public Base {
public:
    ~Derived() { std::cout << "Derived Destructor\n"; }
};

int main() {
    Base* ptr = new Derived();
    delete ptr;  // Derived 소멸자와 Base 소멸자 모두 호출
}
```

여기서 `Base` 클래스의 소멸자는 가상 소멸자(`virtual ~Base()`)로 정의되어 있기 때문에, `Derived` **객체가 삭제될 때** `Derived` **소멸자와** `Base` **소멸자**가 모두 호출된다.

#### 🔑 다형성의 장점

1. **유연성**: 한 종류의 객체를 다룰 때 여러 방법으로 다룰 수 있게 해준다.

2. **확장성**: 새로운 클래스가 추가도리 때, 기존 코드를 수정할 필요 없이 **추가적인 기능을 쉽게 확장**할 수 있다.

3. **재사용성**: 이미 정의된 부모 클래스와 인터페이스를 활용하여, **새로운 구현을 쉽게 작성**할 수 있다.


#### ✅ 요약

+ **정적 다형성**은 **컴파일 타임**에 결정되는 다형성으로, **오버로딩**과 **템플릿**을 통해 구현됩

+ **동적 다형성**은 **런타임**에 결정되는 다형성으로, **가상 함수**와 **상속**을 사용하여 구현됩니다. 이 방식은 **부모 클래스의 포인터를 사용해 자식 객체를 다룰 수 있게** 해준다.

### 🎯 핵심 개념: 런타임에 결정된다는 것의 의미

#### 📌 컴파일 타임 결정
컴파일 타임에는 컴파일러가 **정적인 타입**만 알 수 있다. 즉, `Shape* ptr` 이라는 코드가 있을 때, 컴파일러는 "이건 `Shape` 타입의 포인터야" 라는 사실만 알고 있고, **이 포인터가 실제로 어떤 객체(Circle, Square 등)를 가리킬지는 실행 중에야 비로소 알게 된다.**

즉, 다음과 같은 코드에서:

```cpp
Shape* ptr;
if (user_input == "circle") {
    ptr = new Circle();
} else {
    ptr = new Square();
}
ptr->draw();  // ??? 어떤 draw?
```

+ 컴파일 시점에는 `ptr`이 `Circle`인지 `Square`인지 **알 수 없다**.

+ 그래서 **virtual 함수 테이블(vtable)** 이라는 메커니즘을 통해,

+ 실제로 가리키는 객체의 타입에 따라 **런타임에 적절한** `draw()` **함수가 호출**되는 것이다.

#### 📌 다시 정리: 런타임에 결정된다는 것은?

+ 객체가 **어떤 클래스의 인스턴스인지**는 실행 중에야 알 수 있다.

+ 따라서 가상 함수는 **vtable을 이용해 런타임에 어떤 함수가 호출될지 결정**한다.

+ 이것이 다형성의 핵심이고 이를 통해 **하나의 포인터로 여러 타입의 객체를 유연하게 제어**할 수 있다.

## vtable vptr

#### ✅ vtable (virtual table, 가상 함수 테이블)
+ **클래스 단위**로 존재한다.

+ 해당 클래스에서 정의된 **가상 함수들의 주소를 저장**한 테이블이다.

+ 가상 함수가 하나라도 있는 클래스는 컴파일 시에 **vtable이 생성**된다.

+ 오버라이딩된 경우, 자식 클래스의 vtable에는 **오버라이딩된 함수의 주소**가 들어간다.

#### ✅ vptr (virtual pointer, 가상 함수 포인터)

+ **객체마다 존재**한다.

+ 객체가 속한 클래스의 **vtable을 가르키는 포인터**이다.

+ 컴파일 시점에 컴파일러가 객체 내부에 자동으로 삽입한다.

+ `vptr`의 값은 생성자에서 초기화된다.

+ 객체를 통해 가상 함수를 호출할 때, 내부적으로 vptr을 따라가 vtable을 보고 해당 함수의 주소 호출한다.

#### 🔹작동 방식 예제

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void foo() { cout << "Base::foo" << endl; }
    virtual void bar() { cout << "Base::bar" << endl; }
};

class Derived : public Base {
public:
    void foo() override { cout << "Derived::foo" << endl; }
};
```

+ `Base`의 vtable:

  ```cpp
  [ &Base::foo, &Base::bar ]
  ```

+ `Derived`의 vtable

  ```cpp
  [ &Derived::foo, &Base::bar ]
  ```

#### 🔸 vtable을 직접 불러 보기

#### 🔸 중요한 전제: 이것은 비표준 방식이다

  이 접근법은 **표준 c++에서 보장되지 않는다.**

  + vptr가 **객체의 가장 앞**에 존재한다는 건 대부분의 컴파일러에서 맞지만,

  + 이는 **컴파일러 ABI(Application Binary Interface)에 따라 달라질 수 있는 구현 디테일**이다.

```cpp
#include <iostream>
typedef void(*FuncPtr)(void*);  // 함수 포인터 타입 정의

int main() {
    Derived d;
    Base* b = &d;

    FuncPtr* vtable = *(FuncPtr**)b;  // vptr을 역참조하여 vtable 얻기
    vtable[0](b);  // 첫 번째 가상 함수 호출 (Derived::foo)
    vtable[1](b);  // 두 번째 가상 함수 호출 (Base::bar)

    return 0;
}
```
+ `Dervide`객체와 `Base`객체는 위에 예제를 가져와서 테스트

+ typedef void(\*FuncPtr)(void\*); 이 코드는 함수 포인터 타입의 정의를 하는 부분이다.

+ `void*`타입의 인자를 받고 반환값이 없는 함수에 대한 정의이다.

+ FuncPtr* vtable = *(FuncPtr**)b; 이 코드는 b의 vtable을 얻는 코드이다.

+ `FuncPtr*`는 함수 포인터들의 **배열**처럼 취급될 수 있는 포인터이다.

+ `FuncPtr**`는 **2중 배열**이 아닌 함수 포인터 배열의 **주소**이다. 즉, **이중 포인터**이다.

+ 가상 함수 테이블 (`vtable`)은 함수 포인터들의 배열처럼 되어 있다.

+ 즉, `FuncPtr* vtable` -> 함수 포인터 배열의 첫 번째 원소 주소

예:
```cpp
vtable[0] == &Derived::foo
vtable[1] == &Base::bar
```

+ `FuncPtr**`: `vtable`의 **주소**를 의미한다. 즉, `vptr`가 가키는 것이 `FuncPtr*`이므로

+ `FuncPtr**`는 "**vptr를 가리키는 포인터**"라고 보면 된다.

+ 그렇다면 *(FuncPtr**)를 하면 vtable이 아닌 vptr가 나와야하는 것 아닌가? 라는 의문이 들 수 있다.

+ `(FuncPtr**)b` → 객체의 시작 주소를 vptr의 주소처럼 간주

+ `*`을 하면 → vptr의 값, 즉 vtable의 주소가 나옴으로 이해해야 한다.

+ 즉,`vptr`는 `vtable`의 시작 주소라고 보면된다.

+ 컴파일러는 `b->foo()`를 호출할 때:

  ```cpp
  (*(b->vptr)[0])(b);
  ```
  처럼 컴파일한다. (간단화한 형태)

즉, 가상함수를 호출할 때는:

1. 객체의 시작에서 `vptr`를 읽고,

2. 그 `vptr`가 가리키는 `vtable`배열에서

3. 인덱스를 따라 함수 포인터를 가져와 호출한다.


#### ✅ 핵심 요약

| 표현              | 의미                               |
| --------------- | -------------------------------- |
| `FuncPtr`       | 함수 하나의 포인터 (예: `void(*)(void*)`) |
| `FuncPtr*`      | 함수 포인터들의 배열 (vtable)             |
| `FuncPtr**`     | vtable 주소를 저장한 포인터 = vptr의 타입처럼  |
| `(FuncPtr**)b`  | 객체 `b`를 "vptr을 가리키는 포인터"처럼 간주    |
| `*(FuncPtr**)b` | `vptr`을 역참조해서 `vtable` 주소 얻기     |
| `vtable[0](b)`  | 첫 번째 가상 함수 호출                    |


  

