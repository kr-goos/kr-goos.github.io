---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 12장 요약"
description: I/O 프로젝트: 커맨드 라인 프로그램 만들기
author: 김우석
date: 2025-09-24 18:00:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---


고전적인 커맨드 라인 검색 도구인 `grep`을 직접 구현한 버전을 만들기 위한 준비
```bash
cargo new minigrep
```

## 커맨드 라인 인수 받기
- 첫 과제 : 두 개의 커맨드 라인 인수(파일 경로, 문자열)를 받기 

### 인수 읽기
```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    dbg!(args);
}
```

```bash
$ cargo run -- rust official
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.03s
     Running `target\debug\minigrep.exe rust official`
[src\main.rs:5:5] args = [
    "target\\debug\\minigrep.exe",
    "rust",
    "official",
]
```
- 커맨드 라인 인수로 전달된 값들을 읽을 수 있도록 하기 위해서는 표준 라이브러리가 제공하는 `std::env::args` 함수를 사용 (`args` 함수는 커맨드 라인 인수의 반복자를 반환)
- 반복자는 일련의 값들을 생성하고, 반복자의 `collect` 메서드를 호출하여 반복자가 생성하는 모든 요소를 담고있는 벡터 같은 컬렉션으로 바뀔 수 있음

### 인수들을 변수에 저장하기
```rust
    let args: Vec<String> = env::args().collect();
    
    let query = &args[1];
    let file_path = &args[2];
```

```bash
$ cargo run -- test sample.txt
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.03s
     Running `target\debug\minigrep.exe test sample.txt`
Searching for test
In file sample.txt
```
- `args[0]` 에는 바이너리 파일의 이름이 들어가므로, `--` 뒤에 나열한 테스트 바이너리에게 전달한 인수는 인덱스 1부터 시작하며 이를 추출하여 변수에 저장 (인수 : 파일 경로, 문자열 )

## 파일 읽기
```rust
use std::fs;

let contents = fs::read_to_string(file_path).expect("Should have been able to read the file");
```
- `use` 구문을 사용하여 `fs` 를 가져오고 인수로 받은 `file_path`를 `fs::read_to_string`에 넘겨 파일을 열고, 파일 내용물의 `std::io::Result<String>`을 반환

## 모듈성과 에러 처리 향상을 위한 리팩터링
1. main 함수가 현재 인수 파싱과, 파일을 읽는 작업을 수행하는데 이는 단일 책임의 원칙 위배하며 기능을 나누어 각각의 함수가 하나의 작업에 대한 책임만 지도록 수정
2. main 의 기능이 많아지면서 필요한 변수들이 너무 많이 쌓이는데 이는 변수의 목적을 추적하기 어렵게 하므로 설정 변수들을 하나의 구조체로 모아 목적을 분명히 하는 것이 좋음
3. 파일 읽기 실패 시 에러 메시지 출력을 위해서 `expect` 를 사용해 불필요한 메시지만 출력하여 사용자에게 어떠한 정보도 제공하지 못하고 있음
4. 서로 다른 에러를 처리하기 위해 `expect`를 남발하지 말고, 모든 에러 처리 코드를 한 곳에 모아두어 최종 사용자에게 의미 있는 메시지를 출력

### 바이너리 프로젝트에 대한 관심사 분리


