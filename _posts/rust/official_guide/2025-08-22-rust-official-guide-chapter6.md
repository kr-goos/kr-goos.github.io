---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 6장 요약"
description: CHAPTER 6 열거형과 패턴 매칭
author: 김우석
date: 2025-08-22 20:30:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---

**열거형(enumerateion, 줄여서 enum)** 은 하나의 타입이 가질 수 잇는 배리언트(variant)들을 열거함으로써 타입을 정의할 수 있도록 합니다.

## 열거형 정의하기
열거형은 어떤 값이 여러 개의 가능한 값의 집합 중 하나라는 것을 나타내는 방법을 제공

### 열거형 값

```rust
enum IpAddrKind {
    V4,
    V6
}
```
- enum (열거형)
    - 하나의 타입을 정의하는 틀 (`IpAddrKind`)
- variant (배리언트)
    - enum 이 가질 수 있는 구체적인 값 (`V4`, `V6`)

```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```
- 열거형을 정의할 때의 식별자로 네임스페이스가 만들어져서 각 배리언트 앞에 이중콜론(::) 을 붙여야 함

```rust
fn route(ip_kind: IpAddrKind) {}
```
- `IpAddrKind` 타입을 인수로 받는 함수를 정의

```rust
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```
- 배리언트 중 하나를 사용해서 함수를 호출

```rust
struct IpAddr {
    kind: IpAddrKind,
    address: String,
}
```
- `kind`와 `address`의 값을 함께 사용하기 위해 구조체를 사용
    - 배리언트에 해당하는 연관된 값을 갖게됨

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));
let loopback = IpAddr::V6(String::from("::1"));
```
- 구조체 대신 열거형 배리언트에 데이터를 직접 넣는 방식을 사용
    - 열거형의 각 배리언트에 직접 데이터를 붙임으로 써 구조체 사용을 줄일 수 있음
    - 열거형의 동작에 대한 다른 세부사항을 살펴보기가 좀 더 쉬워짐
    - 열거형을 정의하게 되면 **생성자 함수가 자동적으로 정의**됨
        - `IpAddr::V4(String::from("127.0.0.1"))` 와 같이 String 인수를 입력받아서 IpAddr 타입의 인스턴스를 만들 수 있음

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```
- 각 배리언트는 **다른 타입**과 **다른 양(amount)** 의 연관된 데이터를 가질 수 있음
    - 열거형 배리언트에는 어떤 종류의 데이터라도 넣을 수 있음
        - 문자열, 숫자, 구조체, 열거형 ...

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```
- `Message` 열거형에는 다른 데이터 타입을 갖는 네 개의 배리언트가 있음
    - `Quit` 은 연관된 데이터가 전혀 없음
    - `Move` 는 구조체처럼 이름이 있는 필드를 갖음
    - `Write` 는 하나의 `String`을 가짐
    - `ChangeColor` 는 세 개의 `i32` 를 가짐

```rust
struct QuitMessage;
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String);
struct ChangeColorMessage(i32, i32, i32);
```
- 각기 다른 타입을 갖는 여러 개의 구조체를 사용한다면, 이 메시지 중 어떤 한가지를 인수로 받는 함수를 정의하기가 힘듦

```rust
impl Message {
    fn call(&self) {
        // 메서드 본문이 여기 정의될 것입니다.
    }
}

let m = Message:Write(String::from("hello"));
m.call();
```
- 열거형에도 `impl` 을 사용해서 메서드를 정의할 수 있음
- 메서드 본문에서는 `self`를 사용하여 호출한 열거형 값을 가져올 수 있음
- 변수 `m`은 `Message:Write(String::from("hello"))` 값을 갖게 됨
    - 이 값은 `m.call()` 이 실행될 때 call 메서드 안에서 `self` 가 됨


### Option 열거형이 널값보다 좋은 점들
`Option`은 표준 라이브러리에 정의되어 있으며 값이 있거나 없을 수 있는 상황을 표현하는 열거형(enum)

- 러스트는 Null 개념이 존재하지 않음
    - 따라서 값의 **존재** 혹은 **부재**의 개념을 표현할 수 있어야 하기 때문에 `Option` 열거형이 사용됨
        - 널을 너무 많이 사용하는 문제를 제한하고, 러스트 코드의 안정성을 높이기 위해 의도된 러스트의 디자인 결정 사항
    - 대부분의 언어에서는 존재와 부재의 개념을 `not null` 또는 `null` 로 표현함

```rust
enum Option<T> { // T는 제네릭으로 어떤 타입도 담을 수 있음
    Some(T), // 값이 있을 때
    None,    // 값이 없을 때
}
```

- 러스트에서 기본으로 임포트하는 목록인 프렐루드에 포함되어 있음
    - 배리언트들 또한 프렐루드에 포함 (따라서 배리언트 앞에 `Option::` 을 사용하지 않아도 됨)
- `<T>`는 제네릭을 의미하며 현재는 어떤 타입의 데이터도 담을 수 있음을 의미하는 것으로 이해하고 추후 설명
    - `T`의 자리에 구체적인 타입을 집어넣으면 `Option<T>` 타입을 모두 다른 타입으로 만듦
        - ```rust
          let some_number = Some(5); // Option<i32> 로 추론
          let some_char = Some('e'); // Option<char> 로 추론
          let absent_number: Option<i32> = None; 
          ```
            - `Some`배리언트 내에 어떤 값을 명시했기 때문에 러스트는 이 타입들을 추론할 수 있음
            - `absent_number`는 `Option`타입을 명시하도록 해야함
                - `None` 값만 봐서는 동반되는 Some 배리언트가 어떤 타입의 값을 가질 지 컴파일러가 추론할 수 없기 때문
                - 타입 명시로 인해 `Some` 값을 얻게 되면 값이 존재한다는 것과 해당 값이 `Some` 내에 있다는 것을 알 수 있음

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
``` 

- `Option<T>` 와 `T`가 다른 타입이기 때문에, 컴파일러는 `Option<T>`값을 명백하게 유효한 값처럼 사용하지 못하도록 함
    - `Option<i8>` 에 `i8` 을 더하려 하므로 컴파일되지 않음

```bash
error[E0277]: cannot add `Option<i8>` to `i8`
 --> src/main.rs:5:17
  |
5 |     let sum = x + y;
  |                 ^ no implementation for `i8 + Option<i8>`
  |
  = help: the trait `Add<Option<i8>>` is not implemented for `i8`
```
- 위 에러 메시지는 러스트가 `Option<i8>`과 `i8` 을 어떻게 더해야 할지 모름을 의미
    - 두 타입이 다르기 때문
- `Option<i8>` 타입은 값이 있을지 없을지 확인이 필요하며, 컴파일러는 값을 사용하기 전에 이런 경우가 처리되었는지 확인함
    - 즉 `Option<i8>` 을 `i8` 로 변환해야 함
- 이런 방식은 널로 인해 발생하는 가장 흔한 문제인, 실제로는 널인데 널이 아니라고 가정하는 상황을 발견한데 도움이 됨
    - 널이 아닌 값을 갖는다는 가정을 놓치는 경우에 대한 위험 요소가 제거되면, 코드에 더 확신을 가질 수 있음
- 널일 수 있는 값을 사용하기 위해서는 명시적으로 값의 타입을 `Option<T>`로 만들고, 값을 사용할 때 명시적으로 널인 경우에 대해 처리

## match 제어 흐름 구조
러스트는 **match** 라고 불리는 매우 강력한 제어 흐름 연산자를 가지고 있으며 이는 일련의 패턴에 대해 어떤 값을 비교한 뒤 어떤 패턴에 매칭되었는지를 바탕으로 코드를 수행하도록 해줌

- 패턴
    - 리터럴값
    - 변수명
    - 와일드 카드 등
- 패턴의 표현성
    - 컴파일러는 모든 가능한 경우가 처리되는지 검사

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

- `match` 뒤에는 표현식이 오며 위 경우에는 `coin`값이 사용됨
    - `coin`의 타입은 `Coin` 열거형
- `match` 갈래
    - 하나의 갈래는 패턴과 코드 두 부분으로 이루어짐
        - `=>` 전에 사용된 부분이 **패턴**
        - `=>` 는 **패턴과 실행되는 코드를 구분해주는 연산자**
        - `=>` 후에 사용된 부분이 **실행되는 코드 또는 그냥 값**
    - 각 갈래는 `,` 로 구분됨
- `match` 표현식이 실행될 때, 결괏값을 각 갈래의 패턴에 대해서 순차적으로 비교
    - 패턴이 그 값과 매칭되면 그 패턴과 연관된 코드가 실행
    - 패턴이 그 값과 매칭되지 않으면, 다음 갈래로 실행을 계속함
- 각 갈래와 연관된 코드는 표현식이고, 이 매칭 갈래에서의 표현식의 결과로서 생기는 값은 전체 `match` 표현식에 대해 반환되는 값

```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```
- 각 갈래가 그냥 값을 반환하는 경우 코드 블록을 사용할 필요가 없음
- 갈래 내에서 여러 줄의 코드를 실행시키고 싶다면 중괄호를 사용하고, 쉼표는 옵션이 됨

### 값을 바인딩하는 패턴
매치 갈래는 패턴과 매칭된 값들의 일부분을 바인딩할 수 있으며 열거형의 배리언트로 부터 어떤 값들을 추출할 수 있는 방법을 제공

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // --생략--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

- 열거형의 배리언트로 부터 어떤 값들을 추출할 수 있는 방법
    - Quarter 배리언트는 내부에 `UsState` 값을 들고 있도록 구성

```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
```
    

