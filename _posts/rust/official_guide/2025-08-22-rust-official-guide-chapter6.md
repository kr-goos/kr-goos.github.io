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
- 각 배리언트는 **다른 타입**과 **다른 양(amount)**의 연관된 데이터를 가질 수 있음
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
    - 이 값은 `m.call()` 이 실행될 떄 call 메서드 안에서 `self` 가 됨


