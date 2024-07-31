---
title: "[Golang] 프로그래머스 Lv.1 가장 가까운 같은 글자"
description: 가장 가까운 같은 글자 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-31 08:20:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
문자열 `s`가 주어졌을 때, `s`의 각 위치마다 자신보다 앞에 나왔으면서, 자신과 가장 가까운 곳에 있는 같은 글자가 어디 있는지 알고 싶습니다.
예를 들어, `s`="banana"라고 할 때,  각 글자들을 왼쪽부터 오른쪽으로 읽어 나가면서 다음과 같이 진행할 수 있습니다.

- b는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- n은 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 자신보다 두 칸 앞에 a가 있습니다. 이는 2로 표현합니다.
- n도 자신보다 두 칸 앞에 n이 있습니다. 이는 2로 표현합니다.
- a는 자신보다 두 칸, 네 칸 앞에 a가 있습니다. 이 중 가까운 것은 두 칸 앞이고, 이는 2로 표현합니다.

따라서 최종 결과물은 [-1, -1, -1, 2, 2, 2]가 됩니다.

문자열 `s`이 주어질 때, 위와 같이 정의된 연산을 수행하는 함수 solution을 완성해주세요.

## 제한사항
- 1 ≤ `s`의 길이 ≤ 10,000
	- `s`은 영어 소문자로만 이루어져 있습니다.

## 입출력 예
{% raw %}
|s|result|
|-|-|
|"banana"|[-1, -1, -1, 2, 2, 2]|
|"foobar"|[-1, -1, 1, -1, -1, -1]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

지문과 같습니다.

**입출력 예 #2**

설명 생략

## 풀이 
```golang
func solution(s string) []int {

	result := make([]int, len(s))
	lastIndexMap := make(map[rune]int, len(s))
	for i, char := range s {
		if lastIdx, found := lastIndexMap[char]; found {
			result[i] = i - lastIdx
		} else {
			result[i] = -1
		}
		lastIndexMap[char] = i
	}

	return result
}
```
- 변수 초기화
	- `result` 배열은 각 문자의 결과 값을 저장하며, 길이는 입력 문자열 s의 길이와 동일
	- `lastIndexMap` 맵은 각 문자의 마지막 위치를 저장. 키는 문자(rune 타입)이고 값은 해당 문자가 마지막으로 등장한 인덱스
- 문자열 순회 및 거리 계산
	- `if lastIdx, found := lastIndexMap[char]; found`
		- 현재 문자인 `char`가 `lastIndexMap` 맵에 존재하는지 확인
			- 만약 존재한다면, result[i]에 현재 인덱스 i와 마지막 인덱스 lastIdx의 차이를 저장
			- 존재하지 않는다면, result[i]에 -1을 저장
	- 현재 문자의 위치를 lastIndexMap 맵에 업데이트

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	const TESTCOUNT = 2
	sTestcases := [TESTCOUNT]string{
		"banana",
		"foobar",
	}

	successResults := [TESTCOUNT][]int{
		{-1, -1, -1, 2, 2, 2},
		{-1, -1, 1, -1, -1, -1},
	}

	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("testcase s : ", sTestcases[i])
		fmt.Println("success result : ", successResults[i])
		fmt.Println("solution result : ", solution(sTestcases[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
testcase s :  banana
success result :  [-1 -1 -1 2 2 2]
solution result :  [-1 -1 -1 2 2 2]
testcase s :  foobar
success result :  [-1 -1 1 -1 -1 -1]
solution result :  [-1 -1 1 -1 -1 -1]
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/nearest_duplicate_character      0.173s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/nearest_duplicate_character)