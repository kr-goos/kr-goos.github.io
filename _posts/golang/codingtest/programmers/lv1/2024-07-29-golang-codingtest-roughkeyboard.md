---
title: "[Golang] 프로그래머스 Lv.1 대충 만든 자판"
description: 대충 만든 자판 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-29 11:05:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
휴대폰의 자판은 컴퓨터 키보드 자판과는 다르게 하나의 키에 여러 개의 문자가 할당될 수 있습니다. 키 하나에 여러 문자가 할당된 경우, 동일한 키를 연속해서 빠르게 누르면 할당된 순서대로 문자가 바뀝니다.

예를 들어, 1번 키에 "A", "B", "C" 순서대로 문자가 할당되어 있다면 1번 키를 한 번 누르면 "A", 두 번 누르면 "B", 세 번 누르면 "C"가 되는 식입니다.

같은 규칙을 적용해 아무렇게나 만든 휴대폰 자판이 있습니다. 이 휴대폰 자판은 키의 개수가 1개부터 최대 100개까지 있을 수 있으며, 특정 키를 눌렀을 때 입력되는 문자들도 무작위로 배열되어 있습니다. 또, 같은 문자가 자판 전체에 여러 번 할당된 경우도 있고, 키 하나에 같은 문자가 여러 번 할당된 경우도 있습니다. 심지어 아예 할당되지 않은 경우도 있습니다. 따라서 몇몇 문자열은 작성할 수 없을 수도 있습니다.

이 휴대폰 자판을 이용해 특정 문자열을 작성할 때, 키를 최소 몇 번 눌러야 그 문자열을 작성할 수 있는지 알아보고자 합니다.

1번 키부터 차례대로 할당된 문자들이 순서대로 담긴 문자열배열 `keymap`과 입력하려는 문자열들이 담긴 문자열 배열 `targets`가 주어질 때, 각 문자열을 작성하기 위해 키를 최소 몇 번씩 눌러야 하는지 순서대로 배열에 담아 return 하는 solution 함수를 완성해 주세요.

단, 목표 문자열을 작성할 수 없을 때는 -1을 저장합니다.

## 제한사항
- 1 ≤ `keymap`의 길이 ≤ 100
	- 1 ≤ `keymap`의 원소의 길이 ≤ 100
	- `keymap[i]`는 i + 1번 키를 눌렀을 때 순서대로 바뀌는 문자를 의미합니다.
		- 예를 들어 `keymap[0]` = "ABACD" 인 경우 1번 키를 한 번 누르면 A, 두 번 누르면 B, 세 번 누르면 A 가 됩니다.
	- `keymap`의 원소의 길이는 서로 다를 수 있습니다.
	- `keymap`의 원소는 알파벳 대문자로만 이루어져 있습니다.
- 1 ≤ `targets`의 길이 ≤ 100
	- 1 ≤ `targets`의 원소의 길이 ≤ 100
	- `targets`의 원소는 알파벳 대문자로만 이루어져 있습니다.

## 입출력 예
{% raw %}
|keymap|targets|result|
|-|-|-|
|["ABACD", "BCEFD"]|["ABCD","AABB"]|[9, 4]|
|["AA"]|["B"]|[-1]|
|["AGZ", "BSSS"]|["ASA","BGZ"]|[4, 6]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- "ABCD"의 경우,
- 1번 키 한 번 → A
- 2번 키 한 번 → B
- 2번 키 두 번 → C
- 1번 키 다섯 번 → D
- 따라서 총합인 9를 첫 번째 인덱스에 저장합니다.
- "AABB"의 경우,
- 1번 키 한 번 → A
- 1번 키 한 번 → A
- 2번 키 한 번 → B
- 2번 키 한 번 → B
- 따라서 총합인 4를 두 번째 인덱스에 저장합니다.
- 결과적으로 [9,4]를 return 합니다.


**입출력 예 #2**

- "B"의 경우, 'B'가 어디에도 존재하지 않기 때문에 -1을 첫 번째 인덱스에 저장합니다.
- 결과적으로 [-1]을 return 합니다.


**입출력 예 #3**

- "ASA"의 경우,
- 1번 키 한 번 → A
- 2번 키 두 번 → S
- 1번 키 한 번 → A
- 따라서 총합인 4를 첫 번째 인덱스에 저장합니다.
- "BGZ"의 경우,
- 2번 키 한 번 → B
- 1번 키 두 번 → G
- 1번 키 세 번 → Z
- 따라서 총합인 6을 두 번째 인덱스에 저장합니다.
- 결과적으로 [4, 6]을 return 합니다.

## 풀이 
```golang
func solution(keymap []string, targets []string) []int {
	m := make(map[string]int)
	for _, keyArr := range keymap {
		for i := 0; i < len(keyArr); i++ {
			key := string(keyArr[i])
			v, ok := m[key]
			if !ok || v > i {
				m[key] = i + 1
				continue
			}
		}
	}

	answer := make([]int, len(targets))
	for i, targetStr := range targets {
		for j := 0; j < len(targetStr); j++ {
			v, ok := m[string(targetStr[j])]
			if !ok {
				answer[i] = -1
				break
			}
			answer[i] += v
		}
	}

	return answer
}
```
- `keymap`을 순회하여 각 키의 최소 타이핑 횟수를 맵 `m`에 저장
	- `keymap`을 순회하여 각 키에 대한 정보를 `m`에 저장
		- 각 `keyArr` 문자열을 순회하며, 각 키의 위치를 계산하여 `m`에 저장
		- 만약 키가 맵 `m`에 없거나, 기존 값 `v`가 현재 위치 i보다 크면, 맵 `m`에 키의 값을 i + 1로 저장 (키의 위치는 1부터 시작하며, 최소 횟수를 저장)
- `targets`를 순회하여 각 타겟 문자열의 타이핑 비용을 계산
	- `answer`는 각 타겟 문자열의 타이핑 비용을 저장하는 배열
	- 각 `targetStr` 문자열에 대해, 문자열의 각 문자를 순회
		- `v, ok := m[string(targetStr[j])]`는 맵 `m`에서 현재 문자의 값을 가져옴
		- 만약 문자가 맵 `m`에 없으면 (`ok`가 `false`), `answer[i]`를 `-1`로 설정하고 루프를 종료
		- 그렇지 않으면, `answer[i]`에 해당 키의 값을 더해줌

		
## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	keymapTestCases := [][]string{
		{"ABACD", "BCEFD"},
		{"AA"},
		{"AGZ", "BSSS"},
	}
	targetsTestCases := [][]string{
		{"ABCD", "AABB"},
		{"B"},
		{"ASA", "BGZ"},
	}
	successResults := [][]int{
		{9, 4},
		{-1},
		{4, 6},
	}

	for i := 0; i < 3; i++ {
		v := solution(keymapTestCases[i], targetsTestCases[i])

		fmt.Printf("keymapTestCases[%d] : %v\n", i, keymapTestCases[i])
		fmt.Printf("targetsTestCases[%d] : %v\n", i, targetsTestCases[i])
		for j, sr := range successResults[i] {
			if v[j] == sr {
				fmt.Printf("success result : %d ,solution result : %d\n", sr, v[j])
			} else {
				t.Fatalf("success result : %d ,solution result : %d\n", sr, v[j])
			}
		}

	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
keymapTestCases[0] : [ABACD BCEFD]
targetsTestCases[0] : [ABCD AABB]
success result : 9 ,solution result : 9
success result : 4 ,solution result : 4
keymapTestCases[1] : [AA]
targetsTestCases[1] : [B]
success result : -1 ,solution result : -1
keymapTestCases[2] : [AGZ BSSS]
targetsTestCases[2] : [ASA BGZ]
success result : 4 ,solution result : 4
success result : 6 ,solution result : 6
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/rough_keyboard   0.128s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/rough_keyboard)