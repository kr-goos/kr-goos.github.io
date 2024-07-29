---
title: "[Golang] 프로그래머스 Lv.1 카드 뭉치"
description: 카드 뭉치 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-29 13:00:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
코니는 영어 단어가 적힌 카드 뭉치 두 개를 선물로 받았습니다. 코니는 다음과 같은 규칙으로 카드에 적힌 단어들을 사용해 원하는 순서의 단어 배열을 만들 수 있는지 알고 싶습니다.

- 원하는 카드 뭉치에서 카드를 순서대로 한 장씩 사용합니다.
- 한 번 사용한 카드는 다시 사용할 수 없습니다.
- 카드를 사용하지 않고 다음 카드로 넘어갈 수 없습니다.
- 기존에 주어진 카드 뭉치의 단어 순서는 바꿀 수 없습니다.

예를 들어 첫 번째 카드 뭉치에 순서대로 ["i", "drink", "water"], 두 번째 카드 뭉치에 순서대로 ["want", "to"]가 적혀있을 때 ["i", "want", "to", "drink", "water"] 순서의 단어 배열을 만들려고 한다면 첫 번째 카드 뭉치에서 "i"를 사용한 후 두 번째 카드 뭉치에서 "want"와 "to"를 사용하고 첫 번째 카드뭉치에 "drink"와 "water"를 차례대로 사용하면 원하는 순서의 단어 배열을 만들 수 있습니다.

문자열로 이루어진 배열 `cards1`, `cards2`와 원하는 단어 배열 `goal`이 매개변수로 주어질 때, `cards1`과 `cards2`에 적힌 단어들로 `goal`를 만들 있다면 "Yes"를, 만들 수 없다면 "No"를 return하는 solution 함수를 완성해주세요.

## 제한사항
- 1 ≤ `cards1`의 길이, `cards2`의 길이 ≤ 10
	- 1 ≤ `cards1[i]`의 길이, `cards2[i]`의 길이 ≤ 10
	- `cards1`과 `cards2`에는 서로 다른 단어만 존재합니다.
- 2 ≤ `goal`의 길이 ≤ `cards1`의 길이 + `cards2`의 길이
	- 1 ≤ `goal[i]`의 길이 ≤ 10
	- `goal`의 원소는 `cards1`과 `cards2`의 원소들로만 이루어져 있습니다.
- `cards1`, `cards2`, `goal`의 문자열들은 모두 알파벳 소문자로만 이루어져 있습니다.

## 입출력 예
{% raw %}
|cards1|cards2|goal|result|
|-|-|-|-|
|["i", "drink", "water"]|["want", "to"]|["i", "want", "to", "drink", "water"]|"Yes"|
|["i", "water", "drink"]|["want", "to"]|["i", "want", "to", "drink", "water"]|"No"|
{% endraw %}

## 입출력 예 설명
입출력 예 #1

본문과 같습니다.


입출력 예 #2

`cards1`에서 "i"를 사용하고 `cards2`에서 "want"와 "to"를 사용하여 "i want to"까지는 만들 수 있지만 "water"가 "drink"보다 먼저 사용되어야 하기 때문에 해당 문장을 완성시킬 수 없습니다. 따라서 "No"를 반환합니다.

## 풀이 
```golang
func solution(cards1 []string, cards2 []string, goal []string) string {

	for _, v := range goal {
		if len(cards1) > 0 && cards1[0] == v {
			cards1 = cards1[1:]
			continue
		}

		if len(cards2) > 0 && cards2[0] == v {
			cards2 = cards2[1:]
			continue
		}
		return "No"
	}

	return "Yes"
}
```
- 이 함수에서는 슬라이싱을 이용하여 카드 덱에서 첫 번째 요소를 제거하고 목표 문자열의 다음 문자를 처리하는 방법을 사용
- 목표카드 배열 반복
	- `goal` 배열의 각 요소 `v`에 대해 반복문 실행
- 카드 덱 검사 및 슬라이싱
	- `cards1` 과 `cards2`가 목표카드 `v` 와 일치하는지 검사
	- `len(cards1) > 0 && cards1[0] == v` 조건이 참이면, cards 덱의 첫 번째 카드를 제거
	- 슬라이싱을 사용하여 `cards = cards[1:]`로 첫 번째 카드를 제거하고 나머지 덱을 유지
- 카드 일치 여부 확인
	- `goal` 배열의 현재 카드 `v`가 `cards1` 또는 `cards2`의 첫 번째 카드와 일치하지 않으면 "No"를 반환
	- 이 경우, `goal` 배열의 순서를 맞출 수 없으므로 더 이상의 검사는 불필요 하기 때문에 return 수행
	- 모든 목표 카드가 성공적으로 검사되었고, 각 목표 카드가 `cards1` 또는 
	`cards2`의 첫 번째 카드와 일치했다면 "Yes"를 반환

### Go 슬라이싱? (TMI)
```
Go에서 슬라이싱을 사용하면 새로운 슬라이스가 생성되지만, 이는 기존 배열의 새로운 뷰(View)일 뿐입니다. 슬라이스는 배열의 포인터와 길이, 그리고 용량을 포함하는 구조체이기 때문에, 새로운 슬라이스를 만든다고 해서 실제 배열의 데이터를 복사하지 않습니다. 따라서 메모리 할당과 복사 비용이 발생하지 않습니다.
```

### 슬라이싱을 이용한 부분에 대한 추가 설명
- 슬라이싱을 사용하여 카드 덱의 첫 번째 요소를 제거하고 나머지 요소들만 남김
	- `cards = cards[1:]`
- 이 코드는 각 덱의 첫 번째 요소를 제거하고 나머지 요소를 새로운 슬라이스로 반환
- 슬라이싱을 통해 원래 덱을 수정하지 않고, 첫 번째 요소를 제거한 새로운 덱을 사용할 수 있음
- 슬라이싱 접근 방식은 효율적이며, 새로운 배열을 생성하지 않고 기존 배열의 부분만을 참조하기 때문에 메모리 사용을 최소화




## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	const COUNT = 2
	card1TestCases := [COUNT][]string{
		{"i", "drink", "water"},
		{"i", "water", "drink"},
	}
	card2TestCases := [COUNT][]string{
		{"want", "to"},
		{"want", "to"},
	}
	goalTestCases := [COUNT][]string{
		{"i", "want", "to", "drink", "water"},
		{"i", "want", "to", "drink", "water"},
	}

	successResults := [COUNT]string{
		"Yes",
		"No",
	}

	for i := 0; i < COUNT; i++ {
		v := solution(card1TestCases[i], card2TestCases[i], goalTestCases[i])
		if v == successResults[i] {
			fmt.Printf("testcase %d success\n", i+1)
		} else {
			t.Fatalf("testcase %d failure\n", i+1)
		}
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
testcase 1 success
testcase 2 success
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/bundle_cards     0.221s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/bundle_cards)