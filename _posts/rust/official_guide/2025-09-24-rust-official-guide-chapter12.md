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
- main이 커지기 시작하여 여러 책임과 기능들이 main 에 조직화될 때 이 바이너리 프로그램의 별도 관심사를 나누기 위한 가이드라인 단계
    - 프로그램을 main.rs와 lib.rs로 분리하고 프로그램 로직을 lib.rs로 옮김
    - 커맨드 라인 파싱 로직이 작은 동안에는 main.rs에 남을 수 있음
    - 커맨드 라인 파싱 로직이 복잡해지기 시작하면, main.rs로부터 추출하여 lib.rs로 옮김
- 위 과정을 거친 후 main 함수에 남아 있는 책임 소재는 다음으로 한정
    - 인수를 가지고 커맨드 라인 파싱 로직 호출
    - 그 밖의 설정
    - lib.rs의 run 함수 호출
    - run이 에러를 반환할 때 에러 처리하기
- 즉, 위 패턴은 main.rs는 프로그램 실행을, lib.rs는 당면한 작업의 모든 로직 처리를 담당하여 관심사 분리

#### 인수 파서 추출
```rust
fn parse_config(args: &[String]) -> (&str, &str) {
    let query = &args[1];
    let file_path = &args[2];

    (query, filepath)
}
```
- main 으로 부터 커맨드라인 파싱 로직을 담당하는 parse_config 함수 추출 (main은 더 이상 커맨드 라인 인수와 변수들이 어떻게 대응되는지 결정할 책임이 없음)

#### 설정값 묶기
```rust
struct Config {
    query: String,
    file_path: String,
}

fn parse_config(args: &[String]) -> Config {
    let query = args[1].clone();
    let file_path = args[2].clone();

    Config { query, filepath }
}
```
- 기존 튜플로 묶어진 2개의 반환 값은 명확한 의미를 전달해주지 못하므로, 두 값을 하나의 구조체에 넣고 구조체 필드에 각각 의미가 있는 이름을 부여하여 이 값들이 어떻게 연관이 되어있고 이 값들의 목적은 무엇인지 더 쉽게 이해하도록 변경
- `clone` 메서드는 데이터의 전체 복사본을 만들어 Config 인스턴스가 소유할 수 있게 해주는데, 이는 문자열 데이터에 대한 참조자를 저장하는 것이 비해 더 많은 시간과 메모리를 소비하지만, 값의 복제는 참조자의 라이프타임을 관리할 필요가 없어지기 때문에 약간의 성능을 포기하고 단순함을 얻는 것은 가치 있는 절충안

#### Config를 위한 생성자 만들기
```rust
impl Config {
    fn new(args: &[String]) -> Config {
        let query = args[1].clone();
        let file_path = args[2].clone();

        Config { query, filepath }
    }
}
```
- `Config` 구조체가 들어오면서 `parse_config` 함수의 목적이 `Config` 인스턴스를 생성하는 것이 되었으므로, Config 구조체와 연관된 `new` 라는 이름의 함수로 변경하는 것이 더 자연스러움


### 에러 처리 수정