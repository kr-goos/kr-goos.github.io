---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 5장 요약"
description: CHAPTER 5 구조체로 연관된 데이터 구조화하기
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

위 코드는 각 치수의 값으로 area 함수를 호출하여 사각형의 면적을 성공적으로 계싼하지만, 몇가지 작업을 더하여 **코드를 더 명료하고 읽기 쉽게** 만들 수 있습니다.

area 함수의 시그니처를 보면 하나의 사각형의 면적을 계산하는 것을 가저하고 있지만 두 개의 매개변수를 받고있으며, 이 두 값이 서로 연관되어 있다는 것을 명확하게 표현하는 부분은 찾아볼 수 없습니다.


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

튜플을 사용함으로써 더 짜임새 있고 인수도 단 하나만 넘기면 된다는 장점이 있지만, 튜플의 각 요소에 이름이 없는 튜플의 특성 때문에 값을 인덱스로 접근해야 해서 계산식이 불명확해졌습니다.

다행히 넓이를 계싼할 땐 어떤 값이 너비이고 어떤 값이 높이인지 구분하지 못해도 별 문제가 없습니다.
하지만 만들어야 할 프로그램이 화면에 사각형을 그리는 프로그램이라면 너비 width가 튜플 인덱스 0에 위치하고 높이 height 는 튜플 인덱스 1에 위치한다는 걸 꼭 기억하고 있어야 할 겁니다.
이처럼 코드 내에서 데이터의 의미를 전달하지 못하므로 에러가 발생하기 더 쉬워집니다.


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

`Rectangle` 이라는 구조체를 정의하고, 중괄호 안에 `width`, `height` 필드를 `u32` 타입으로 정의했습니다.

`area` 함수의 매개변수는 이제 rectangle 하나뿐입니다. 단, 구조체의 소유권을 가져와버리면 main 함수에서 area 함수 호출 이후에 rect1을 더 사용할 수 없으므로, rectangle 매개변수의 타입은 **불변 참조자 타입**으로 정하여 소유권을 빌려오기만 하도록 만들었습니다.

`area` 함수는 `Rectangle` 인스턴스의 `width`, `height` 필드에 접근합니다. (빌린 구조체 인스턴스의 필드에 접근하는 것은 **필드값을 이동시키지 않으며**, 이것이 **구조체의 대여를 자주 보게되는 이유**임을 기억해야 합니다.)

구조체 정의를 통해 `width`, `height`가 서로 연관된 값이라는 것도 알 수 있고, `.인덱스` 접근 대신 서술적인 필드명을 제공하여 코드의 의미가 더욱 분명해지고 가독성이 높아집니다.

### 트레이트 파생으로 유용한 기능 추가하기