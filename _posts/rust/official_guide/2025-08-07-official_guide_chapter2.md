---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 2장 요약"
description: CHAPTER 2 추리 게임
author: 김우석
date: 2025-08-07 08:00:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---

## 새 프로젝트 준비
1장에서 생성했던 디렉터리인 projects로 이동하고 아래와 같이 카고를 이용하여 새 프로젝트를 생성하고, 작업 디렉터리를 변경
```bash
cargo new guessing_game
cd guessing_game
```

`cargo run` 명령문을 이용하여 프로그램을 컴파일하고 실행해 보기
```
$ cargo run
   Compiling guessing_game v0.1.0 (C:\projects\rust_learn\guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.82s
     Running `target\debug\guessing_game.exe`
Hello, world!
```

**이처럼 run 명령어는 프로젝트를 빠르게 반복해서 실행해야할 때 유용**

## 추릿값 처리하기

```rust
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

### 코드 분석
```rust
use std::io;
```
- 사용자의 입력을 받기 위해서는 io 입출력 라이브러리를 스코프로 가져와야 함 (main.rs 에서 사용할 수 있도록 한다는 의미)
    - io 라이브러리는 std라고 불리는 표준 라이브러리에 있음
        - **io 라이브러리**는 사용자의 입력을 받는 것을 포함하여 io와 관련된 기능들을 제공
- 프렐루드(prelude)
    - Rust가 모든 Rust 프로그램에 자동으로 가져오는 항목들의 목록
        - 위 코드에서는 String 타입을 사용하고 있지만 어디에도 use std::string::String; 같은 구문이 없음
        - 위 예시와 같이 자주 쓰이는 항목들은 Rust의 프렐루드에 포함되어서 자동으로 가져와짐
    - 만약 위 코드에서 처럼 원하는 타입이 프렐루드에 없다면 use 문을 활용하여 명시적으로 해당 타입을 가져와야 함

```rust
fn main() {}
```
- main 함수는 프로그램의 진입점(entrypoint)

```rust
    println!("Guess the number!");

    println!("Please input your guess.");
```
- println!은 문자열을 화면에 출력하는 매크로

```rust
let mut guess = String::new();
```
- 사용자의 입력값을 저장할 변수(variable)를 생성
    - `let` : 변수를 선언하는 키워드
    - `mut` : 변경 가능(mutable)한 변수
        - `mut` 가 없으면 값을 바꾸려고 할 때 컴파일 에러가 발생
            - `cannot assign twice to immutable variable`
            - Rust에서는 기본적으로 불변(immutable) 변수로 생성이되며, 한 번 정한 값은 바꿀 수 없음
    - `=` : 바인딩(bind)를 의미
        - `let x = 5` 가 있다면 좌변의 변수에 우변의 값을 바인딩
    - `String` : 문자열 타입
    - `::` : 연관 함수(associated function)를 의미
        - `String::new()` 와 같이 이 **타입과 관련된 함수를 호출**할때 사용
            - 따라서, new 함수를 통해 String 인스턴스를 만들고 그것을 초기값으로 사용한다는 의미 (비어 있는 새 문자열을 생성)
            - `String`은 표준 라이브러리에서 제공하는 확장 가능한(growable) UTF-8 인코딩의 문자열 타입

```rust
io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");
```
- 프로그램 첫 번째 라인에 `use std::io;`를 이용하여 표준 라이브러리의 입출력 기능을 가져왔고, io모듈의 연관 함수인 `stdin`을 호출하는 과정
    - `stdin` : 터미널의 표준 입력의 핸들을 나타내는 타입인 `std::io::Stdin`의 인스턴스를 돌려줌
- `.read_line(&mut guess)` : 사용자로부터 한 줄을 입력받아(줄 끝의 개행 문자`\n` 까지 포함하여 읽음) 주어진 변수에 추가하고 `Result<T, E>` 를 반환
    - `&mut guess` : `&mut` 은 가변 참조를 말하며, guess 는 문자열 변수를 의미
        - 메서드가 문자열의 내용물을 바꿀 수 있기 때문에 이 문자열 인수는 **가변** 이어야 함
        - `&` : 코드의 여러 부분에서 데이터를 여러 번 메모리로 복사하지 않고 접근하기 위한 방법을 제공하는 **참조자(reference)** 임을 나타냄 (현재 챕터에서는 참조자가 변수처럼 기본적으로 불변이라는 것만 알고 넘어가시면 됩니다.)
    - `Result<T, E>` : 결과가 성공일 수도 있고, 실패일 수도 있음을 나타내는 열거형(enumerate)
        ```rust
        enum Result<T, E> {
            Ok(T),
            Err(E),
        }
        ```
        - `enum` 은 하나의 타입이 여러 가지 형태 중 하나를 가질 수 있도록 정의할때 사용되며 enum에 정의된 상태값을 **배리언트(variant)** 라고 한다.
            ```rust
            enum Color {
                Red,
                Green,
                Blue,
            }
            ```
            - Color는 **열거형 타입**
            - Red, Green, Blue는 타입이 가질 수 있는 **형태(값)**
            - 위 예제에서 Red, Green, Blue는 모두 Color enum의 variant를 의미
                - Color::Red
                - Color::Green
                - Color::Blue
- `.expect("Failed to read line")` : Result 의 메서드로, Result 인스턴스가 Err일 경우 expect 메서드는 프로그램 작동을 멈추고 except에 인수로 넘겼던 메시지를 출력함
    - except를 호출하지 않는다면, 컴파일은 되지만 경고가 발생
    ```
    warning: unused `Result` that must be used
    --> src\main.rs:10:5
    |
    10 | /     io::stdin()
    11 | |         .read_line(&mut guess);
    | |______________________________^
    |
    = note: this `Result` may be an `Err` variant, which should be handled
    = note: `#[warn(unused_must_use)]` on by default
    ```

```rust
println!("You guessed: {guess}");
```
- `{guess}` : 사용자가 입력한 값을 담고 있는 문자열을 출력하며, `{}`는 자리표시자(placeholer)를 의미

```rust
let x = 5;
let y = 10;
println!("x = {x} and y + 2 = {}", y + 2); // x = 5 and y + 2 = 12 를 출력
```

- 어떤 표현식의 결괏값을 출력할 때는 빈 중괄호를 형식 문자열에 위치시키고, 그 뒤에 쉼표로 구분된 표현식들을 나열하여 각 중괄호에 순차적으로 출력하도록 할 수 있음

### 테스트
- 키보드로부터 입력받은 다음 그 값을 출력하는지 확인
    ```bash
    $ cargo run
    Compiling guessing_game v0.1.0 (C:\projects\rust_learn\guessing_game)
        Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.62s
        Running `target\debug\guessing_game.exe`
    Guess the number!
    Please input your guess.
    5 # 사용자 입력
    You guessed: 5
    ```


## 비밀번호 생성하기



## 정리한 내용 이외의 중요한 내용
- 크레이트
    - 바이너리 크레이트 : 실행 가능한 프로그램을 만드는 크레이트
    - 라이브러리 크레이트 : 자체적으로 실행될 수는 없고 다른 프로그램에서 사용되기 위한 용도의 크레이트