---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 9장 요약"
description: 에러처리
author: 김우석
date: 2025-09-01 07:00:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---

- 러스트에는 예외 처리 기능이 없음
- 러스트 에러의 종류
    - **복구 가능한(recoverable) 에러**
        - `Result<T, E>` 타입
    - **복구 불가능한(unrecoverable) 에러**
        - 프로그램을 종료하는 panic! 매크로

## panic!으로 복구 불가능한 에러 처리하기
- 패닉 : 프로그램이 더 이상 정상적으로 실행될 수 없는 **심각한 오류 상황**에서 발생
	- 실패 메시지를 출력
	- 되감기(unwind)
		- 러스트가 패닉을 발생시킨 함수로부터 스택을 거꾸로 훑어가면서 데이터를 청소한다는 의미
		- 데이터 정리 작업 없이 즉각 종료되는 그만두기(aborting)을 하고 싶다면 아래와 같이 설정
			- Cargo.toml
				- ```toml
				  [profile.XXX] // XXX 는 dev 또는 release 가 될 수 있음
				  panic = 'abort'
				  ```
	- 스택을 청소
	- 프로세스 종료
- 패닉을 일으키는 두 가지 방법
	- 패닉을 일으킬 동작을 하는 것 (예: 배열 끝 부분을 넘어선 접근)
	- `panic!` 매크로를 명시적으로 호출

### panic! 백트레이스 이용하기

- **백트레이스**란?
	- 패닉(panic) 발생 시 어떤 경로로 함수들이 호출되다가 에러가 발생했는지 보여주는 호출 스택 기록을 의미
	- 즉, "에러가 어디서 발생했는지", "그 지점까지 어떤 경로를 거쳤는지" 알 수 있는 디버깅 도구
- 백트레이스 출력하기
	- `RUST_BACKTRACE=1 cargo run` 와 같이 환경변수 `RUST_BACKTRACE` 를 켜주면 스택 추적이 자세히 나옴


## Result로 복구 가능한 에러 처리하기
- 복구 가능한에러는 `Result` 열거형으로 표현
	- `Result<T, E>` 타입
		- 배리언트
			- `Ok(T)`: 성공 시 값 반환
			- `Err(E)`: 실패 시 에러 반환
            - 제네릭 타입이라서, 성공값 타입(`T`) 와 오류 타입(`E`)을 원하는 타입으로 정할 수 있음
        - 처리 방식
            - `match` 구문으로 분기 처리
                - `Ok` : 성공에 대한 처리
                - `Err` : 에러에 대한 처리

### 서로 다른 에러에 대해 매칭하기

#### 다른 종류의 에러를 다른 방식으로 처리하기
- `File::Open` 이 반환하는 `Err` 배리언트 값의 타입은 `io::Error` 구조체
    - `io::Error` 구조체는 kind 메서드를 지원 (`io::ErrorKind`를 얻을 수 있음)
        - `io::ErrorKind` 는 io 연산으로부터 발생할 수 있는 다양한 종류의 에러를 나타내는 배리언트가 있는 열거형
        - 즉, `error.kind()` 에 대한 내부 매칭을 할 수 있음
            - ```rust
              let f = File::open("hello.txt");
              let f = match f {
                  Ok(file) => file,
                  Err(error) => match error.kind() {} // 파일이 존재하지 않을때에 대한 처리
              ```
- 위와 같이 `match` 표현식은 매우 용하지만 매우 원시적이어서 너무 많은 `match` 가 생길 수 있음

#### 에러 발생 시 패닉을 위한 숏컷: unwrap과 expect
- `match` 의 사용은 충분히 잘 작동하지만, 코드가 장황하기도 하고 의도를 항상 잘 전달하는 것은 아님, 따라서 `Result<T,E>` 는 다양한 특정 작업을 수행하기 위한 **도우미 메서드** 들을 제공
    - `match` 구문과 비슷한 구현을 한 숏컷 메서드
        - `unwrap()`
            - `Result` 값이 Ok 배리언트라면, 값을 반환
            - `Result` 값이 Err 배리언트라면 `panic!` 매크로를 호출로 기본 메시지 출력
        - `expect()` ( **권장** )
            - `Result` 값이 Ok 배리언트라면, 값을 반환
            - `Result` 값이 Err 배리언트라면 `panic!` 매크로를 호출로 매개변수로 전달한 에러 메시지 출력



