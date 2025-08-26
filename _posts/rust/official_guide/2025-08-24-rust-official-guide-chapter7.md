---
title: "[RUST] 러스트 프로그래밍 공식 가이드(제2판) 7장 요약"
description: CHAPTER 7 커져가는 프로젝트를 패키지, 크레이트, 모듈로 관리하기
author: 김우석
date: 2025-08-24 19:00:00 +0900
categories: [Rust, OfficialGuide]
tags: [Rust]
image:
  path: /assets/img/posts/rust/rust-logo.svg
---

러스트에는 코드 조직화에 필요한 기능이 여럿 있습니다. 어떤 세부 정보를 외부에 노출할지, 비공개로 둘지, 프로그램의 스코프 내 어떤 이름이 있는지 등 다양합니다. 이를 통틀어 **모듈 시스템(module system)**이라 하며, 다음 기능들이 포함됩니다.

- **패키지** : 크레이트를 빌드하고, 테스트하고, 공유하는데 사용하는 카고 기능
- **크레이트** : 라이브러리나 실행 가능한 모듈로 구성된 트리 구조
- **모듈 및 use** : 구조, 스코프를 제어하고, 조직 세부 경로를 감추는 데 사용
- **경로** : 구조체, 함수, 모듈 등의 이름을 지정

## 패키지와 크레이트
- **크레이트** : 러스트가 한 번의 컴파일 시에 고려하는 가장 작은 코드 단위
    - **바이너리 크레이트(binary crate)** : 커맨드 라인 프로그램이나 서버 처럼 실행 가능한 실행 파일로 컴파일할 수 있는 프로그램
        - `main` 함수를 포함하고 있어야 함
    - **라이브러리 크레이트(library crate)** : 여러 프로젝트에서 공용될 의도로 만들어진 기능들이 정의되어 있음
        - `main` 함수를 포함하고 있지 않음
        - 실행 파일 형태로 컴파일되지 않음
- **크레이트 루트** : 러스트 컴파일러가 컴파일을 시작하는 소스파일이고, 크레이트 모듈을 구성함
- **패키지** : 일련의 기능을 제공하는 하나 이상의 크레이트로 구성된 번들
    - 크레이트들을 빌드하는 법이 설명된 `Cargo.toml` 파일이 포함되어 있음
    - 패키지에는 여러 개의 바이너리 크레이트가 원하는 만큼 포함될 수 있음
    - 라이브러리 크레이트는 하나만 넣을 수 있음
    - 적어도 하나 이상의 크레이트가 포함되어야 하며, 이는 라이브러리든 바이너리든 상관 없음

### 모듈, 경로, use, pub 키워드가 컴파일러에서 작동하는 방법
- 크레이트 루트부터 시작
    - 크레이트를 컴파일 할 때 컴파일러는 먼저 크레이트 루트 파일을 봄
        - 라이브러리 크레이트의 경우 `src/lib.rs`
        - 바이너리 크레이트의 경우 `src/main.rs`
- 모듈 선언
    - 크레이트 루트 파일에는 새로운 모듈을 선언할 수 있음
    - `mod garden;` 이라는 코드로 garden 모듈을 선언할 수 있으며, 컴파일러는 아래의 장소에서 이 모듈의 코드가 있는지 살펴봄
        - `mod garden` 뒤에 세미콜론 대신 중괄호를 써서 안쪽에 코드를 적은 인라인
        - `src/garden.rs` 파일 안
        - `src/garden/mod.rs` 파일 안
- 서브 모듈 선언
    - 크레이트 루트가 아닌 다른 파일에서는 서브모듈을 선언할 수 있음
    - 예를들면 `src/garden.rs` 안에 `mod vegetables;` 를 선언할 수 있음
    - 컴파일러는 부모 모듈 이름의 디렉터리 안쪽에 위치한 아래의 장소들에서 이 서브모듈의 코드가 있는지 살펴 봄
        - `mod vegetables` 뒤에 세미콜론 대신 중괄호를 써서 안쪽에 코드를 적은 인라인
        - `src/garden/vegetables.rs` 파일 안
        - `src/garden/vegetables/mod.rs` 파일 안
- 모듈 내 코드로의 경로
    - 일단 모듈이 크레이트의 일부로서 구성되면, 공개 규칙이 허용하는 한도 내에서라면 해당 코드의 경로를 사용하여 동일한 크레이트의 어디에서든 이 모듈의 코드를 참조할 수 있게 됨
    - 예를들면, `garden vegetables` 모듈 안에 있는 `Asparagus` 타입은 `crate::garden::vegetables::Asparagus`로 쓸 수 있음
- 비공개 vs 공개
    - 모듈 내의 코드는 기본적으로 부모 모듈에게 비공개(private)
    - 모듈을 공개(public)로 만들려면, `mod` 대신 `pub mod` 를 사용하여 선언
    - 공개 모듈의 아이템들을 공개로 하려면 마찬가지로 그 선언 앞에 `pub`를 사용
- `use` 키워드
    - 어떤 스코프 내에서 use 키워드는 긴 경로의 반복을 줄이기 위한 어떤 아이템으로의 단축경로를 만들어줌
    - `create::garden::vegetables::Asparagus`를 참조할 수 있는 모든스코프에서 `use create::garden::vegetables::Asparagus;`로 단축 경로로 만들 수 있음
        - 단축경로 사용 이후 스코프에서 이 타입을 사용하려면 `Asparagus` 만 작성해주면 됨


## 모듈을 정의하여 스코프 및 공개 여부 제어하기
- **모듈(module)** : 크레이트의 코드를 읽기 쉽고 재사용하기도 쉽게끔 구조화를 할 수 있게 해줌
    - `mod` 키워드와 모듈이름을 지정하여 모듈을 정의
    - 모듈의 본문은 중괄호로 감싸져 있음
    - 모듈 안에 다른 모듈 및 구조체, 열거형, 상수, 트레이트,함수 등의 아이템 정의를 넣을 수 있음
    - 모듈 내의 코드는 기본적으로 비공개
    - 모듈은 아이템의 **공개 여부(privacy)**를 제어하도록 해줌
        - 비공개 아이템은 외부에서 사용이 허용되지 않음
        - 모듈과 모듈 내 아이템을 선택적으로 공개할 수 있음
    - 모듈 트리
        - Rust의 모든 크레이트는 트리 구조로 모듈들이 연결됨
        - 트리 최상단(root)은 크레이트 루트 파일(`main.rs` 또는 `lib.rs`)
        - 모듈 트리 구조
            - 전체 모듈 최상위에 crate 라는 모듈이 암묵적으로 위치
                - ```bash
                  crate (암묵적으로 존재)
                  |- network
                  |     |- client
                  |            |-request
                  |-  utils
                  ```
                - `crate::` 는 현재 크레이트의 루트부터 시작하는 절대 경로를 나타냄


## 경로를 사용하여 모듈 트리의 아이템 참조하기
러스트 **모듈 트리에서 아이템을 찾는 방법**은 파일 시스템에서 경로를 사용하는 방법과 동일

- **절대 경로(absolute path)** : 크레이트 루트로부터 시작되는 전체 경로
    - 외부 크레이트로부터의 코드에 대해서는 해당 크레이트 이름으로 절대 경로가 시작됨
        - Cargo.toml
            - ```toml
              [dependencies]
              rand = "0.8"
              ```
        - 코드
            - ```rust
              fn main() {
                  // 절대 경로: rand crate 루트부터 시작
                  let mut rng = rand::thread_rng();
                  let n: u32 = rand::Rng::gen_range(&mut rng, 1..=10);
                  println!("Random number: {}", n);
              }
              ```
    - 현재 크레이트로부터의 코드에 대해서는 `crate` 리터럴로부터 시작됨
        - ```rust
          mod network {
              pub fn connect() {
                  println!("Connected!");
              }
          }

          fn main() {
              // 절대 경로: crate 루트부터 탐색
              crate::network::connect();
          }
          ```
- **상대 경로(relative path)** : 현재 모듈을 시작점으로 하여 `self`, `super` 혹은 현재 모듈 내의 식별자를 사용
    - 현재 모듈 기준
        - ```rust
          mod network {
              pub fn connect() {
                  println!("Connected!");
              }

              pub fn start() {
                  // self:: 는 현재 모듈을 의미
                  self::connect();
              }
          }
          ```
    - 상위 모듈 기준
        - ```rust
          mod network {
              pub fn connect() {
                  println!("Connected!");
              }

              pub mod client {
                  pub fn request() {
                      // super:: 는 network 모듈을 의미
                      super::connect();
                  }
              }
          }
          ```
- 모든 아이템은 기본적으로 `private`
    - 함수, 메서드, 구조체, 열거형, 모듈, 상수 등은 `pub` 을 붙이지 않으면 부모 모듈 기준으로만 접근 가능
    - 부모 모듈 밖에서는 접근할 수 없음
    - 부모 모듈 안에 있는 아이템은 자식 모듈 내 비공개 아이템을 사용할 수 없음
    - 자식 모듈 내 아이템은 조상 모듈 내 아이템을 사용할 수 있음
    - 자식 모듈의 세부 구현은 감싸져서 숨겨져 있지만, 자식 모듈 내에서는 자신이 정의된 콘텍스트를 볼 수 있음
    - ```text
      crate (루트)
      |- parent (-> 모듈)
            |- parent_item (-> 함수)
            |- child (-> 모듈)
                 |- child_item (-> 함수)
                 |- grandchild (-> 모듈)
                        |- grandchild_item (-> 함수)
      ```
        - `parent_item`
            - child 에서 접근 가능
            - grandchild 에서 접근 가능
            - parent 외부(루트 등)에서 접근하려면 `pub` 필요
        - `child_item`
            - grandchild 에서 접근 가능
            - parent에서 접근 불가
            - child 모듈 내부에서는 접근 가능
        - `grandchild_item`
            - grandchild 모듈 내부에서만 접근 가능
            - child 모듈에서 접근 불가
            - parent 모듈에서 접근 불가

### pub 키워드로 경로 노출하기
