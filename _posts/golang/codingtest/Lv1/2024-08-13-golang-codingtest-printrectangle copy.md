---
title: "[Golang] 프로그래머스 Lv.1 핸드폰 번호 가리기"
description: 핸드폰 번호 가리기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 12:50:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
프로그래머스 모바일은 개인정보 보호를 위해 고지서를 보낼 때 고객들의 전화번호의 일부를 가립니다.
전화번호가 문자열 phone_number로 주어졌을 때, 전화번호의 뒷 4자리를 제외한 나머지 숫자를 전부 *으로 가린 문자열을 리턴하는 함수, solution을 완성해주세요.


## 제한사항
- phone_number는 길이 4 이상, 20이하인 문자열입니다.


## 입출력 예

| phone\_number | return |
| --- | --- |
| "01033334444" | "\*\*\*\*\*\*\*4444" |
| "027778888" | "\*\*\*\*\*8888" |



## 풀이 
```golang
func solution(phone_number string) string {

	var sb strings.Builder
	for i := 0; i < len(phone_number)-4; i++ {
		sb.WriteString("*")
	}

	sb.WriteString(phone_number[len(phone_number)-4:])

	return sb.String()
}
```

- `strings.Builder` 는 문자열을 효율적으로 연결할 수 있도록 설계
- `sb` 를 선언
- `i`를 0 부터 `len(phone_number)-4` 까지 순회
	- `sb.WriteString` 을 이용하여 `"*"`를 `sb`의 버퍼에 추가
- 순회가 끝나면, `phone_number` 슬라이스의 마지막 문자 4개를 슬라이싱하여 `sb`의 버퍼에 추가
- sb 버퍼에 쌓인 문자열을 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	phone_number = [TESTCOUNT]string{
		"01033334444",
		"027778888",
	}

	result = [TESTCOUNT]string{
		"*******4444",
		"*****8888",
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("phone_number : %v\n", phone_number[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(phone_number[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
phone_number : 01033334444
result : *******4444
solution result : *******4444
phone_number : 027778888
result : *****8888
solution result : *****8888
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/mask_phonenumber 0.326s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/mask_phonenumber)