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

여기서 `final` 키워드가 추가적으로 등장해야되는데 `final`키워드는 **상속 계층에서"이 이상은 확장 금지"**라는 의미를 갖는다

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

## vtable vptr

## 다형성

## 캡슐화
