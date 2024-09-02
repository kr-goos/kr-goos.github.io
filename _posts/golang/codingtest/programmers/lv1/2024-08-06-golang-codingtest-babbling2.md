---
title: "[Golang] 프로그래머스 Lv.1 옹알이 (2)"
description: 옹알이 (2) 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-06 08:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
머쓱이는 태어난 지 11개월 된 조카를 돌보고 있습니다. 조카는 아직 "aya", "ye", "woo", "ma" 네 가지 발음과 네 가지 발음을 조합해서 만들 수 있는 발음밖에 하지 못하고 연속해서 같은 발음을 하는 것을 어려워합니다. 문자열 배열 `babbling`이 매개변수로 주어질 때, 머쓱이의 조카가 발음할 수 있는 단어의 개수를 return하도록 solution 함수를 완성해주세요.

## 제한사항
- 1 ≤ `babbling`의 길이 ≤ 100
- 1 ≤ `babbling[i]`의 길이 ≤ 30
- 문자열은 알파벳 소문자로만 이루어져 있습니다.

## 입출력 예
{% raw %}
| babbling | result |
| --- | --- |
| \["aya", "yee", "u", "maa"\] | 1 |
| \["ayaye", "uuu", "yeye", "yemawoo", "ayaayaa"\] | 2 |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- ["aya", "yee", "u", "maa"]에서 발음할 수 있는 것은 "aya"뿐입니다. 따라서 1을 return합니다.

**입출력 예 #2**

- ["ayaye", "uuu", "yeye", "yemawoo", "ayaayaa"]에서 발음할 수 있는 것은 "aya" + "ye" = "ayaye", "ye" + "ma" + "woo" = "yemawoo"로 2개입니다. "yeye"는 같은 발음이 연속되므로 발음할 수 없습니다. 따라서 2를 return합니다.

## 유의사항
- 네 가지를 붙여 만들 수 있는 발음 이외에는 어떤 발음도 할 수 없는 것으로 규정합니다. 예를 들어 "woowo"는 "woo"는 발음할 수 있지만 "wo"를 발음할 수 없기 때문에 할 수 없는 발음입니다.

## 풀이 
### 실패 케이스
```golang
const (
	AYA = "aya"
	YE  = "ye"
	WOO = "woo"
	MA  = "ma"
)

func solution2(babbling []string) int {

	var count int
	allowed := []string{AYA, YE, WOO, MA}
	for _, word := range babbling {

		if isRepeatPattern(word, allowed) {
			continue
		}

		for _, pattern := range allowed {
			word = strings.ReplaceAll(word, pattern, "")
			if word == "" {
				count++
				break
			}
		}
	}

	return count
}

func isRepeatPattern(s string, patterns []string) bool {

	for _, pattern := range patterns {
		if strings.Contains(s, pattern+pattern) {
			return true
		}
	}

	return false
}
```
- 변수 초기화
	- `count` : 유효한 발음의 개수
	- `allowed` : 발음 가능한 패턴을 저장한 슬라이스
- 옹알이 반복
	- `isRepeatPattern` 함수를 호출하여 반복 패턴 확인
		- 옹알이 문자열에서 `pattern` + `pattern` (연속된 발음)을 찾음
	- 허용된 패턴을 순회하며, 옹알이 문자열에 대해 `pattern` 을 찾아 `""`(빈 문자열) 로 변환하고, 이 과정에서 옹알이 문자열이 빈값이 되면 유효한 발음이므로 `count`를 증가


### **위 코드에서 잘못된 부분**
만약 "mayaa" 와 같은 문자열이 존재한다고 합시다. 이 문자열에서 중복 패턴을 제거하고, `strings.ReplaceAll(word, pattern, "")`의 결과로 `word` 값은 "ma" 가 됩니다. "ma" 도 패턴에 속하므로, 패턴을 2개 찾게 된 결과가 되어 count 는 2 가 되어 종료될 것입니다.


### 위 문제를 해결하기 위한 방법은?
`strings.ReplaceAll(word, pattern, "")` 함수의 new argument 의 값을 " "(공백 문자열) 값으로 변경해보고, 위 예시처럼 다시 작성해보겠습니다.
만약 "mayaa" 와 같은 문자열이 존재한다고 합시다. 이 문자열에서 중복 패턴을 제거하고, `strings.ReplaceAll(word, pattern, " ")`의 결과로 `word` 값은 "m a" 가 됩니다. 패턴을 순회하며 "m a"문자열을 찾지만 패턴의 종류 중 "m a"값은 존재하지 않게 될 것입니다.


### 성공 케이스
```golang
const (
	AYA = "aya"
	YE  = "ye"
	WOO = "woo"
	MA  = "ma"
)

func solution(babbling []string) int {

	var count int
	allowed := []string{AYA, YE, WOO, MA}
	for _, word := range babbling {

		if isRepeatPattern(word, allowed) {
			continue
		}

		for _, pattern := range allowed {
			word = strings.ReplaceAll(word, pattern, " ")
			if strings.TrimSpace(word) == "" {
				count++
				break
			}
		}

	}

	return count
}

func isRepeatPattern(s string, patterns []string) bool {

	for _, pattern := range patterns {
		if strings.Contains(s, pattern+pattern) {
			return true
		}
	}

	return false
}
```
- 변수 초기화
	- `count` : 유효한 발음의 개수
	- `allowed` : 발음 가능한 패턴을 저장한 슬라이스
- 옹알이 반복
	- `isRepeatPattern` 함수를 호출하여 반복 패턴 확인
		- 옹알이 문자열에서 `pattern` + `pattern` (연속된 발음)을 찾음
	- 허용된 패턴을 순회하며, 옹알이 문자열에 대해 `pattern` 을 찾아 `" "`(공백 문자열) 로 변환하고, TrimSpace 함수를 통해 좌 우측 공백을 제거
	- `strings.TrimSpace(word) == ""` 이 조건은 모든 패턴에 대해 처리하지 않고, 패턴 처리 중, word 가 빈값이 된다면 pattern 의 순회를 바로 break 하기 위해 for문 안에 넣었습니다. (for문 밖에서 1회 처리하여도 무방)
	- 위 과정에서 옹알이 문자열이 빈값이 되면 유효한 발음이므로 `count`를 증가
	


## 테스트코드
### 유닛 테스트
```golang
const (
	TESTCOUNT = 3
)

var (
	babbling = [TESTCOUNT][]string{
		{"aya", "yee", "u", "maa"},
		{"ayaye", "uuu", "yeye", "yemawoo", "ayaayaa"},
		{"mayaa"},
	}
	expected = [TESTCOUNT]int{1, 2, 0}
)

func TestSolution(t *testing.T) {

	for i := 0; i < TESTCOUNT; i++ {
		result := solution(babbling[i])
		if result != expected[i] {
			t.Errorf("For input %v, expected %d but got %d", babbling[i], expected[i], result)
		}
	}
}

func TestSolution2(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		result := solution2(babbling[i])
		if result != expected[i] {
			t.Errorf("For input %v, expected %d but got %d", babbling[i], expected[i], result)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
=== RUN   TestSolution2
    solution_test.go:32: For input [mayaa], expected 0 but got 1
--- FAIL: TestSolution2 (0.00s)
FAIL
exit status 1
FAIL    golang-coding-test/Lv1/babbling_2       0.185s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/babbling_2)