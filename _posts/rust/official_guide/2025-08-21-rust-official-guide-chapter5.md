---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 5장 요약"
description: 구조체로 연관된 데이터 구조화하기
author: 김우석
date: 2025-08-21 08:00:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---

## 구조체 정의 및 인스턴스화
**구조체(struct)** 는 여러 값을 묶고 이름을 지어서 의미 있는 묶음을 정의하는 데 사용합니다.

- 구성 요소들은 각각 다른 타입이 될 수 있습니다.
- 각각의 구성 요소에 이름을 붙일 수 있습니다.
- 특정 요소에 접근할 때 순서에 의존할 필요가 사라져 튜플보다 유연하게 사용할 수 있습니다.
- ```rust
  struct User {
      active: bool,
      username: String,
      email: String,
      sign_in_count: u64,
  }
  ```
    - 구조체 정의는 `struct` 키워드와 해당 구조체에 지어줄 이름을 입력합니다. (구조체 명은 묶을 데이터의 의미에 맞도록 지어줘야합니다.)
    - 중괄호 안에서는 **필드(field)** 라고 부르는 각 구성 요소의 이름 및 타입을 정의합니다.
- 정의한 구조체를 사용하려면 해당 구조체의 각 필드에 대한 구체적인 값을 정하여 구조체의 **인스턴스(instance)** 를 생성해야합니다.
    - 구조체의 이름을 적고, 중괄호를 열고, 그 안에 필드의 이름(key)과 해당 필드에 저장할 값(value)을 **키:값** 쌍의 형태로 추가해야합니다.
    - 필드의 순서는 구조체를 정의했을 때와 동일하지 않아도 됩니다.
    - ```rust
      let user1 = Uesr {
          active: true,
          username: String::from("someusername123"),
          email: String::from("someone@example.com"),
          sign_in_count: 1,
      }
      ```
- 구조체 내의 특정 값은 점(`.`) 표기법으로 얻어올 수 있습니다.
    - user1의 username 을 얻어오려면 `user1.username` 처럼 사용합니다.
- 가변 인스턴스의 특정 필드의 값을 변경할 수 있습니다.
    - ```rust
      fn main() {
          let mut user1 = Uesr {
              active: true,
              username: String::from("someusername123"),
              email: String::from("someone@example.com"),
              sign_in_count: 1,
          }

          user1.email = String::from("anotheremail@example.com");
      }
      ```
      - 일부 필드만 가변으로 만들 수 없어 가변성은 해당 **인스턴스 전체**가 지니게 됩니다.
- 함수의 마지막 표현식에 구조체의 새 인스턴스를 생성하는 표현식을 써서 해당 인스턴스를 **암묵적으로 반환**할 수 있습니다.
    - ```rust
      // User 인스턴스를 반환하는 함수
      fn build_user(email: String, username: String) -> User {
          User {
              active: true,
              username: username,
              email: email,
              sign_in_count: 1,
          }
      }
      ```

### 필드 초기화 축약법 사용하기
위 예제의 build_user 함수처럼 변수명과 구조체의 필드명이 같을땐, **필드 초기화 축약법(field init shorthand)**을 사용해서 더 적은 타이핑으로 같은 기능을 구현할 수 있습니다.

```rust
// User 인스턴스를 반환하는 함수
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}
```

- `username: username` 과 같은 부분을 변수명과 필드명이 같다는 점을 이용해 `username`로만 작성한 모습입니다.


### 기존 인스턴스를 이용해 새 인스턴스를 만들 때 구조체 업데이트 문법 사용하기
다른 인스턴스에서 대부분의 값을 유지한 채로 몇 개의 값만 바꿔 새로운 인스턴스를 생성하게 되는 경우가 간혹 있습니다. 이때 유용한게 **구조체 업데이트 문법(struct udpate syntax)** 입니다.

```rust
// let user2 = Uesr {
//     active: user1.active,
//     username: user1.username,
//     email: String::from("another@example.com"),
//     sign_in_count: user1.sign_in_count,
// }

// 위와 동일
let user2 = Uesr {
    email: String::from("another@example.com")
    ..user1
}

```
- `email` 값이 다른 `user2` 인스턴스를 생성하지만 `username`, `active`, `sign_in_count` 는 `user1`의 필드와 같은 값을 갖습니다.
- `user1` 의 값들과 동일한 값들로 나머지를 채우려면 ..user1를 **제일 끝**에 적어야합니다.
    - 다른 필드들은 구조체의 정의 내에 있는 필드들의 순서와 상관없이 임의 순서로 적을 수 있습니다.
- 구조체 업데이트 문법은 대입과 마찬가지로 `=`을 이용합니다.
    - 이 구문은 **데이터를 이동**시킵니다.
- `user2` 를 생성한 후 `user1` 은 더이상 사용할 수 없습니다.
    - `user1` 의 `username` 필드의 String이 `user2`로 이동하기 때문입니다.
    - `user2` 에 `email`과 `username`의 String 모두를 제공하고 `user1`에서는 `active`와 `sign_in_count`값만 사용한다면, `user2`를 만든 이후에도 `user1`은 **유효**합니다.
        - Copy 가능한 필드(`active`, `sign_in_count`)는 복사 → `user1`에서도 여전히 **사용 가능**합니다.
        - Move 되는 필드(`username`)는 user2로 소유권 이전 → `user1.username`은 더 이상 **사용 불가**합니다.


### 명명된 필드 없는 튜플 구조체를 사용하여 다른 타입 만들기
**튜플 구조체(tuple struct)** 는 구조체 자체에는 이름을 지어 의미를 주지만, 이를 구성하는 필드에는 이름을 붙이지 않고 타입만 적어 넣은 형태입니다.
튜플 구조체는 튜플 전체에 이름을 지어주거나 특정 튜플을 다른 튜플과 구분하고는 싶은데, 그렇다고 각 필드명을 일일이 정해 일반적인 구조체 형태로 만들면 너무장황하거나 불필요한 경우에 유용합니다.

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```
- 튜플 구조체의 정의는 일반적인 구조체처럼 `struct` 키워드와 구조체명으로 시작되나, 그 뒤에 타입들로 이루어진 튜플이 따라옵니다.
- `black`, `origin`은 서로 다른 튜플 구조체의 인스턴스이므로, 타입이 서로 다릅니다.
    - 구조체 내 필드 구성이 같더라도 각각의 구조체는 **별도의 타입**입니다.
    - `Color`타입을 매개변수로 받는 함수에 `Point` 타입을 인수로 넘겨주는 건 불가능합니다.
- 튜플 구조체는 이름 대신 위치(index)로 구분되는 필드만 있습니다.
    - `black.0`, `black.1`, `black.2` 와 같이 `.`과 `인덱스`로 개별값에 접근합니다.
- 여러 부분으로 해체할 수 있습니다.
    - ```rust
      let Color(r, g, b) = black;
      println!("red = {r}, green = {g}, blue = {b}");
      ```

### 필드가 없는 유사 유닛 구조체
필드가 아예 없는 구조체를 정의한 것을 **유사 유닛 구조체(unit-like struct)** 라고 지칭하며, 유사 유닛 구조체는 어떤 타입에 대해 트레이트를 구현하고 싶지만 타입 내부에 어떤 데이터를 저장할 필요는 없을 경우 유용합니다.

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

- `AlwaysEqual` 을 정의하기 위해서 struct 키워드 뒤에 이름을 쓰고 바로 세미콜론을 붙였습니다.
- `subject` 변수에 `AlwaysEqual` 인스턴스를 만들어 넣어줍니다.
- `struct` 의 정의에서도, 인스턴스를 만들어 넣을 때도 정의한 이름 뒤에 괄호나 중괄호를 필요로 하지 않습니다.


## 구조체를 사용한 예제 프로그램
어떨 때 구조체를 사용하면 좋을 지 이해해보기 위해 사각형 넓이를 계산하는 프로그램을 작성해보겠습니다.

`cargo new rectangles`

```rust
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!("The area of the rectangle is {} square pixels.", area(width1, height1));
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```

`cargo run`

```bash
$ cargo run
    Blocking waiting for file lock on package cache
   Compiling rectangles v0.1.0 (C:\projects\rust_learn\rectangles)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 21.61s
     Running `target\debug\rectangles.exe`
The area of the rectangle is 1500 square pixels.
```

위 코드는 각 치수의 값으로 area 함수를 호출하여 사각형의 면적을 성공적으로 계산하지만, 몇가지 작업을 더하여 **코드를 더 명료하고 읽기 쉽게** 만들 수 있습니다.

area 함수의 시그니처를 보면 하나의 사각형의 면적을 계산하는 것을 가정하고 있지만 두 개의 매개변수를 받고있으며, 이 두 값이 서로 연관되어 있다는 것을 명확하게 표현하는 부분은 찾아볼 수 없습니다.


### 튜플로 리팩터링하기

```rust
fn main() {
    let rect1 = (30, 50);

    println!("The area of the rectangle is {} square pixels.", area(rect1));
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```
- 장점
    - 코드가 더욱 짜임새 있어집니다.
    - 인수를 하나만 넘기면 됩니다.
- 단점
    - 튜플의 특성으로 각 요소에 이름이 없어 값에 인덱스로 접근해야합니다.
    - 값에 인덱스로 접근하기 때문에 계산식이 불명확해 보입니다.
- ```text
  다행히 넓이를 계산할 땐 어떤 값이 너비이고 어떤 값이 높이인지 구분하지 못해도 별 문제가 없습니다.
  하지만 만들어야 할 프로그램이 화면에 사각형을 그리는 프로그램이라면 너비 width가 튜플 인덱스 0에 위치하고 높이 height 는 튜플 인덱스 1에 위치한다는 걸 꼭 기억하고 있어야 할 겁니다.
  이처럼 코드 내에서 데이터의 의미를 전달하지 못하므로 에러가 발생하기 더 쉬워집니다.
  ```


### 구조체로 리팩터링하여 코드에 더 많은 의미를 담기

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("The area of the rectangle is {} square pixels.", area(&rect1));
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

- `area` 함수의 매개변수는 이제 `rectangle` 하나뿐입니다.
- `area` 함수의 매개변수를 **불변 참조자 타입**으로 정의
    - 매개변수의 타입은 **불변 참조자 타입**으로 정하여 소유권을 빌려오기만 하도록 만들었습니다.
    - 구조체의 소유권을 가져와버리면 `main` 함수에서 `area` 함수 호출 이후에 `rect1`을 더 사용할 수 없습니다.
- `area` 함수는 `Rectangle` 인스턴스의 `width`, `height` 필드에 접근합니다. 
    - 빌린 구조체 인스턴스의 필드에 접근하는 것은 필드값을 이동시키지 않습니다(**구조체의 대여를 자주 보게되는 이유**임을 기억해야 합니다.)
- 구조체 정의를 통해 `width`, `height`가 서로 연관된 값이라는 것도 알 수 있습니다.
- 튜플처럼 `.인덱스` 접근 대신 서술적인 필드명을 제공하여 코드의 의미가 더욱 분명해지고 가독성이 높아집니다.


### 트레이트 파생으로 유용한 기능 추가하기
프로그램을 디버깅하는 동안 `Rectangle` 인스턴스 내 모든 필드값을 출력해서 확인하는 예시입니다.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {}", rect1);
}
```

`cargo run`

```bash
error[E0277]: `Rectangle` doesn't implement 
```

`println!` 매크로에는 여러 출력 형식을 사용할 수 있습니다. 그리고 기본 형식인 `{}`로 지정할 땐 Display라는 최종 사용자를 위한 출력 형식을 사용하는데 여태 사용했던 기본 타입들은 Display가 기본적으로 구현되어 있었습니다. 하지만 구조체의 경우 중간중간 쉼표를 사용하거나, 중괄호를 출력하거나, 필드 일부를 생략하는 등 여러 상황에 우리가 원하는 걸 임의로 예쌍해서 제공하려 들지 않기 때문에, 구조체에는 `println!` 및 `{}`(자리표시자)와 함께 사용하기 위한 **Display 구현체가 기본 제공되지 않습니다.**

```
= help: the trait `std::fmt::Display` is not implemented for `Rectangle` 
= note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
```

`{}`대신 `{:?}`를 사용하도록 변경해봅시다. `{}` 내에 `:?`를 추가하는 건 `println!`에 **Debug 라는 출력 형식을 사용하고 싶다고 전달하는 것**과 같습니다.

`cargo run`

```bash
error[E0277]: `Rectangle` doesn't implement `Debug`
```

다시 컴파일 하면 여전히 에러가 발생합니다.

```bash
= help: the trait `Debug` is not implemented for `Rectangle`
= note: add `#[derive(Debug)]` to `Rectangle` or manually `impl Debug for Rectangle`
```

러스트는 디버깅 정보를 출력하는 기능을 **자체적으로 가지고 있습니다.** 하지만 우리가 만든 구조체에 해당 기능을 적용하려면 명시적 동의가 필요하므로, 구조체 정의 이전에 `#[derive(Debug)]` 외부 속성(outer attribute)을 작성해 주어야 합니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {:?}", rect1);
}
```

`cargo run`

```bash
rect1 is Rectangle { width: 30, height: 50 }
```
프로그램을 실행해보면 더 이상 에러가 발생하지 않고 인스턴스 내 모든 필드 값을 보여줍니다. 필드가 더 많은 구조체라면 이보다 더 읽기 편한 형태가 필요할텐데 그럴 땐 `println!` 문자열 내에 `{:?}` 대신 `{:#?}` 를 사용합니다.

```bash
rect1 is Rectangle {
    width: 30,
    height: 50,
}
```

Debug 포맷을 사용하여 값을 출력하는 그 밖의 방법은 dbg! 매크로를 사용하는 것인데, 이는 **표현식의 소유권을 가져와서** 코드에서 dbg! 매크로를 호출한 파일 및 라인 번호를 결괏값과 함께 출력하고 다시 소유권을 반환합니다.

```text
[NOTE] dbg! 매크로의 호출은 표준 에러 콘솔 스트림(stderr)에 출력 되는데, 이는 표준 출력 콘솔 스트림(stdout)에 출력하는 println! 과는 상반됩니다.
```

아래는 `rect`의 전체 구조체의 필드값 뿐만 아니라 `width` 필드에 대입되는 값에 관심이 있는 경우에 대한 예시입니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
```

- `dbg!`매크로의 특징
    - `dbg!`는 표현식을 인자로 받고, 그 값을 `stderr`로 찍은 뒤 **원래 값(소유권)을 그대로 반환**합니다.
    - 즉, dbg!(expr)은 expr과 똑같이 쓸 수 있습니다. (단, 콘솔 출력이 추가될 뿐)
- `width: dbg!(30 * scale)`
    - `30 * scale`은 단순히 `60`이라는 표현식
    - `dbg!(30 * scale)`은 stderr에 `src/main.rs:10: 30 * scale = 60` 같은 로그를 찍고, 값 `60`을 그대로 반환합니다.
    - 따라서 `width`에는 `60`이 들어감 → `dbg!` 안 썼을 때와 똑같습니다.
- `dbg!(&rect1)`
    - 만약 `dbg!(rect1)`을 해버리면?
        - `rect1`은 소유권이 dbg! 매크로로 넘어갑니다.
        - 즉, `dbg!`가 `rect1`을 받아서 찍고, 다시 돌려주지만 그 순간 `rect1`은 `dbg!`로 **“이동(move)”** 되어버려서, 이후 `rect1`을 쓸 수 없게 됩니다.
    - `&rect1`은 소유권이 아니라 빌림(borrow) 이므로 `dbg!`가 찍은 다음에도 `rect1`은 여전히 main 함수 안에서 사용할 수 있습니다.


`cargo run`

```rust
[src\main.rs:10:16] 30 * scale = 60
[src\main.rs:14:5] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```
- 현재 디버깅 중인 `30 * scale` 표현식이 있는 src\main.rs의 10 행에서 나온 것이며 그 결과 값이 `60` 임을 알 수 있습니다.
- src\main.rs의 14번째 라인에 있는 `dbg!` 호출은 `Rectangle` 구조체인 `&rect1`의 결과를 출력합니다.
    - 이 출력 결과는 Rectangle 타입에 대한 보기 좋은 Debug 포맷을 이용합니다.

```text
위와 같이 dbg! 매크로는 코드가 어떤 일을 하고 있는지 알아볼 때 매우 유용할 수 있습니다.
```

## 메서드 문법
- `fn` 함수명으로 선언합니다.
- 매개변수와 반환값을 가집니다.
- 구조체 콘텍스트에 정의됩니다.
- 첫 번째 매개변수로 `self`, `&self`, `&mut self` 중 하나를 명시적으로 적어야 합니다.
    - `self` 매개변수 : 메서드를 호출하고 있는 구조체의 인스턴스를 나타냅니다.
- `인스턴스.메서드명(인수)` 와 같은 형태로 메서드를 호출할 수 있습니다.

### 메서드 정의하기
기존의 `Rectangle` 매개변수를 갖던 `area` 함수를 Rectangle 구조체의 메서드로 변경해 보겠습니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("The area of the rectangle is {} square pixels.", rect1.area());
}
```

- `Rectangle`의 콘텍스트에 메서드 정의
    - `Rectangle`에 대한 `impl`(implementation) 블록을 만듭니다.
        - `impl` 블록 내의 모든 것은 `Rectangle` 타입과 연관됩니다.
    - `area`시그니처에서는 `rectangle: &Rectangle` 대신 `&self` 를 사용합니다.
        - `&self` 는 `self: &Self` 의 축약형입니다.
        - `&self`는 이 메서드가 `Self` 타입의 인스턴스를 **빌려온다(`&`)** 는 것을 나타냅니다.
            - 여기서 `&self`를 선택한 이유는 기존 함수 버전에서 &Rectangle을 사용했던 것 처럼 소유권을 가져오는 것도, 데이터를 쓰는 것도 아닌, 데이터를 읽기만 하기 때문입니다.
        -  메서드는 다른 매개변수가 그런 것 처럼 `self`의 소유권을 가져올 수도, 지금처럼 `self`를 불변으로 빌려올 수도, 가변으로 빌려올 수도 있습니다.
            - 메서드에서 작업 중 호출한 인스턴스를 변경하고 싶다면 `&mut self` 를 사용할 수 있습니다.
            - `self` 라고만 작성하여 인스턴스의 소유권을 가져오도록 만드는 일은 거의 없습니다. (보통 해당 메서드가 `self`를 다른 무언가로 변환하고 그 이후에는 원본 인스턴스의 사용을 막고자 할 때 사용합니다.)
- 함수 대신 메서드를 사용하는 주된 이유
    - 메서드 구문을 제공하고 모든 메서드 시그니처 내에서 타입을 반복할 필요가 없습니다.
    - 코드를 더 조직적으로 만들기 위해서입니다.
    - 향후 어떠한 구조체의 기능과 관련된 코드를 라이브러리 곳곳에서 찾아내야 하는 것 보다, 하나의 impl 블록 내에서 이 타입의 인스턴스로 할 수 있는 모든 것들을 모아두는 것이 더 효율적입니다.

#### 메서드의 추가 특징

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn width(&self) -> u32 {
        self.width > 0
    }
}

```

- 구조체의 필드 이름과 동일한 메서드 이름을 만들 수 있습니다.
    - `rect1.width` 처럼 괄호가 없으면 width 필드를 의미합니다.
    - `rect1.width()` 처럼 괄호가 있으면 width 메서드를 의미합니다.
- 필드와 동일한 이름의 메서드는 보통 `게터(getter)` 메서드라고 부릅니다.
    - `getter` 는 해당 필드의 값을 얻어오는 것 말고는 아무 것도 하지 않는 경우가 대부분입니다.
    - 러스트는 구조체 필드에 대한 게터를 자동으로 만들지 않습니다.
- C 나 C++ 처럼 메서드 호출 연산자는 없나요?
    - 러스트에는 **자동 참조 및 역참조(automatic referencing and deferencing)** 라는 기능이 있고, 메서드 호출에 이 기능이 포함되어 있기 때문에 C 나 C++ 에서 사용되는 `->` 연산자는 제공하지 않습니다.
        - C 나 C++ 에서 사용되는 `->` 는 포인터가 가리키는 구조체(또는 클래스) 멤버(필드 or 메서드)에 접근할 때 쓰는 연산자입니다.

### 더 많은 매개변수를 가진 메서드

`Rectangle` 인스턴스를 받아서, `self` 사각형(첫 번째 Rectangle) 면적 내에 두 번쨰 사각형 `Rectangle` 인스턴스가 완전히 들어갈 수 있다면 true 를 반환하고, 못들어가면 false 를 반환하는 메서드를 생성합니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };

    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```
- `rect2` 는 너비와 높이 둘 다 `rect1` 보다 작지만, `rect3`는 `rect1` 보다 너비가 넓으므로 출력은 다음과 같습니다.
    - ```bash
      Can rect1 hold rect2? true
      Can rect1 hold rect3? false
      ```
- `can_hold` 메서드의 매개변수는 `Rectangle` 을 **불변 참조자**로 받습니다.
    - 매개변수의 인스턴스를 읽을 수만 있으면 되기 때문에 가변으로 빌려올 필요가 없습니다.
    - 또한 소유권을 `main` 에 남겨두지 않을 이유도 없기 때문에 논리적으로 불변 참조자가 가장 적합합니다.

### 연관 함수
`impl` 블록 내에 구현된 모든 함수는 `impl` 뒤에 나오는 타입과 모두 연관되어 있기 때문에 **연관 함수(associate function)**라고 부릅니다.

- 메서드에서 해당 타입의 인스턴스가 필요하지 않다면 self 를 첫 매개변수로 갖지 않는 연관함수를 정의할 수도 있습니다. (예를 들어 `String::from`)
- Rust에는 언어 차원에서 "생성자"라는 문법이 존재하지 않고, 대신 관례적으로 `new`라는 이름의 연관 함수(associated function) 를 정의해서 생성자처럼 사용하기도 합니다.
- 생성자의 또 다른 예시로, `Rectangle`로 정사각형을 만들 때 사용하는 연관 함수 square를 만들어 보겠습니다.
    - ```rust
      impl Rectangle {
          fn square(size: u32) -> Self {
              Self {
                  width: size,
                  height: size,
              }
          }
      }
      ```
        - 반환 타입 및 함수 본문의 `Self` 키워드는 `impl` 키워드 뒤에 적혀 있는 타입의 별칭으로서, 여기서는 `Rectangle` 이 됩니다.
        - 연관 함수를 호출할 땐 `let sq = Rectangle::square(3);` 처럼 구조체 명에 `::` 구문을 붙여서 호출합니다. (연관 함수는 구조체의 네임스페이스 안에 있기 때문)
    

### 여러 개의 impl 블록
각 구조체는 여러 개의 `impl` 블록을 가질 수 있습니다.

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}


impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```