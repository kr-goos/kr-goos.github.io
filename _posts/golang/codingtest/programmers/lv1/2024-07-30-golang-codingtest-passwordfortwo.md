---
title: "[Golang] 프로그래머스 Lv.1 둘만의 암호"
description: 둘만의 암호 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-30 08:03:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
두 문자열 `s`와 `skip`, 그리고 자연수 `index`가 주어질 때, 다음 규칙에 따라 문자열을 만들려 합니다. 암호의 규칙은 다음과 같습니다.

- 문자열 `s`의 각 알파벳을 `index`만큼 뒤의 알파벳으로 바꿔줍니다.
- `index`만큼의 뒤의 알파벳이 `z`를 넘어갈 경우 다시 `a`로 돌아갑니다.
- `skip`에 있는 알파벳은 제외하고 건너뜁니다.

예를 들어 `s` = "aukks", `skip` = "wbqd", `index` = 5일 때, a에서 5만큼 뒤에 있는 알파벳은 f지만 [b, c, d, e, f]에서 'b'와 'd'는 `skip`에 포함되므로 세지 않습니다. 따라서 'b', 'd'를 제외하고 'a'에서 5만큼 뒤에 있는 알파벳은 [c, e, f, g, h] 순서에 의해 'h'가 됩니다. 나머지 "ukks" 또한 위 규칙대로 바꾸면 "appy"가 되며 결과는 "happy"가 됩니다.

두 문자열 `s`와 `skip`, 그리고 자연수 `index`가 매개변수로 주어질 때 위 규칙대로 `s`를 변환한 결과를 return하도록 solution 함수를 완성해주세요.

## 제한사항
- 5 ≤ `s`의 길이 ≤ 50
- 1 ≤ `skip`의 길이 ≤ 10
- `s`와 `skip`은 알파벳 소문자로만 이루어져 있습니다.
	- `skip`에 포함되는 알파벳은 `s`에 포함되지 않습니다.
- 1 ≤ `index` ≤ 20

## 입출력 예
{% raw %}
|s|skip|index|result|
|-|-|-|-|
|"aukks"|"wbqd"|5|"happy"|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

본문 내용과 일치합니다.

## 풀이 
```golang
const (
	Z = 'z'
)

func solution(s string, skip string, index int) string {

	m := make(map[rune]struct{})
	for _, char := range skip {
		m[char] = struct{}{}
	}

	var answer string
	for _, char := range s {
		for i := 0; i < index; i++ {
			char += 1
			if char > Z {
				char = 'a'
			}

			if _, ok := m[char]; ok {
				i--
			}
		}
		answer += string(char)
	}

	return answer
}
```
- `skip` 문자열의 문자를 맵에 저장
	- 여기서 `struct{}`는 **빈 구조체**로, 메모리를 거의 사용하지 않는 특성 때문에 맵의 값으로 자주 사용됨
- 문자열 s 를 순회하며 각 문자를 `index` 만큼 이동
	- `char += 1`을 통해 다음 문자를 얻고, 만약 `char`가 **'z'**를 넘어가면 알파벳의 시작인 **'a'**로 돌아감
	- 이동 후의 문자가 `skip`에 포함된다면, `char`만 증가시킨 후 for문 이 종료된 후 i++ 가 될 것을 방지하기 위해 i-- 를 수행
- 이후 rune type 인 `char` 를 string 으로 변환하여 `answer` 에 더해줌

		
## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	sTestcase := "aukks"
	skipTestcase := "wbqd"
	index := 5
	successResult := "happy"

	result := solution(sTestcase, skipTestcase, index)

	if result == successResult {
		fmt.Println("test success")
	} else {
		t.Fatal("test failed")
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
test success
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/password_for_two 0.203s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/password_for_two)