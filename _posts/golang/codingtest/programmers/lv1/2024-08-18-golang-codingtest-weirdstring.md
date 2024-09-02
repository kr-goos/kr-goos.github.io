---
title: "[Golang] 프로그래머스 Lv.1 이상한 문자 만들기"
description: 이상한 문자 만들기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-18 20:53:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
문자열 s는 한 개 이상의 단어로 구성되어 있습니다. 각 단어는 하나 이상의 공백문자로 구분되어 있습니다. 각 단어의 짝수번째 알파벳은 대문자로, 홀수번째 알파벳은 소문자로 바꾼 문자열을 리턴하는 함수, solution을 완성하세요.


## 제한사항
- 문자열 전체의 짝/홀수 인덱스가 아니라, 단어(공백을 기준)별로 짝/홀수 인덱스를 판단해야합니다.
- 첫 번째 글자는 0번째 인덱스로 보아 짝수번째 알파벳으로 처리해야 합니다.


## 입출력 예
{% raw %}
| s | return |
| --- | --- |
| "try hello world"	 | "TrY HeLlO WoRlD" |
{% endraw %}


## 입출력 예 설명

"try hello world"는 세 단어 "try", "hello", "world"로 구성되어 있습니다. 각 단어의 짝수번째 문자를 대문자로, 홀수번째 문자를 소문자로 바꾸면 "TrY", "HeLlO", "WoRlD"입니다. 따라서 "TrY HeLlO WoRlD" 를 리턴합니다.

## 풀이 
```golang
func solution(s string) string {
	runes := []rune(s)

	var idx int
	for i, r := range runes {
		if r == ' ' {
			idx = 0
			continue
		}

		if idx%2 == 0 {
			runes[i] = unicode.ToUpper(r)
		} else {
			runes[i] = unicode.ToLower(r)
		}
		idx++
	}

	return string(runes)
}
```

- `string`은 불변 타입이므로 개별 문자를 수정할 수 있도록 `[]rune` 타입으로 변환
- `runes` 를 순환하며 `r` 값을 확인
- `idx%2`값이 0 이라면 짝수번째 알파벳 이므로 대문자로 치환, **그렇지 않은 경우 소문자로 치환**
	- **주의사항** : 현재 문자에서는 `s` 가 항상 소문자라는 말이 없습니다. 즉, `idx%2 != 0` 경우, 소문자로 치환해 주지 않는다면 채점에 실패하는 것을 확인할 수 있습니다.
- `idx` 를 증가
- 위 과정을 반복하여 변경된 `runes` 의 값을 `string` 으로 변환하여 반환

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	s := "try hello world"
	result := "TrY HeLlO WoRlD"
	r := solution(s)
	if r != result {
		t.Errorf("%s != %s", result, r)
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/weird_string     0.160s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/weird_string)