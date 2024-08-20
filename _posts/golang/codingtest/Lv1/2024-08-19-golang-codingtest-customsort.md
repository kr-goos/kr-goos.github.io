---
title: "[Golang] 프로그래머스 Lv.1 문자열 내 마음대로 정렬하기"
description: 문자열 내 마음대로 정렬하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 20:20:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
문자열로 구성된 리스트 strings와, 정수 n이 주어졌을 때, 각 문자열의 인덱스 n번째 글자를 기준으로 오름차순 정렬하려 합니다. 예를 들어 strings가 ["sun", "bed", "car"]이고 n이 1이면 각 단어의 인덱스 1의 문자 "u", "e", "a"로 strings를 정렬합니다.


## 제한사항
- strings는 길이 1 이상, 50이하인 배열입니다.
- strings의 원소는 소문자 알파벳으로 이루어져 있습니다.
- strings의 원소는 길이 1 이상, 100이하인 문자열입니다.
- 모든 strings의 원소의 길이는 n보다 큽니다.
- 인덱스 1의 문자가 같은 문자열이 여럿 일 경우, 사전순으로 앞선 문자열이 앞쪽에 위치합니다.


## 입출력 예
{% raw %}
|strings|n|return|
|-|-|-|
|["sun", "bed", "car"]|1|["car", "bed", "sun"]|
|["abce", "abcd", "cdx"]|2|["abcd", "abce", "cdx"]|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- "sun", "bed", "car"의 1번째 인덱스 값은 각각 "u", "e", "a" 입니다. 이를 기준으로 strings를 정렬하면 ["car", "bed", "sun"] 입니다.

**입출력 예 #2**

- "abce"와 "abcd", "cdx"의 2번째 인덱스 값은 "c", "c", "x"입니다. 따라서 정렬 후에는 "cdx"가 가장 뒤에 위치합니다. "abce"와 "abcd"는 사전순으로 정렬하면 "abcd"가 우선하므로, 답은 ["abcd", "abce", "cdx"] 입니다.

## 풀이 
```golang
func solution(strings []string, n int) []string {

	for i := 0; i < len(strings); i++ {
		for j := i + 1; j < len(strings); j++ {
			if strings[i][n] > strings[j][n] {
				strings[i], strings[j] = strings[j], strings[i]
			} else if strings[i][n] == strings[j][n] {
				if strings[i] > strings[j] {
					strings[i], strings[j] = strings[j], strings[i]
				}
			}
		}
	}

	return strings
}
```

### 선택 정렬 알고리즘을 이용한 풀이
- `strings` 의 길이만큼 순회
	- `strings` 의 현재 인덱스 + 1 부터 끝까지 순회
		- `strings[i][n] > strings[j][n]` 조건을 충족한다면, 작은 값을 과 큰 값의 인덱스를 스왑
		- `strings[i][n] == strings[j][n]` 조건을 충족한다면, 문제에서 `문자가 같은 문자열이 여럿 일 경우, 사전순으로 앞선 문자열이 앞쪽에 위치` 조건을 충족하기 위해, strings[i] 와 strings[j] 를 비교하여 i 인덱스 값이 크다면 j 인덱스 값과 스왑
- strings 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	strings = [TESTCOUNT][]string{
		{"sun", "bed", "car"},
		{"abce", "abcd", "cdx"},
	}
	n      = [TESTCOUNT]int{1, 2}
	result = [TESTCOUNT][]string{
		{"car", "bed", "sun"},
		{"abcd", "abce", "cdx"},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("strings : ", strings[i])
		fmt.Println("n : ", n[i])
		fmt.Println("result : ", result[i])
		fmt.Println("solution result : ", solution(strings[i], n[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
strings :  [sun bed car]
n :  1
result :  [car bed sun]
solution result :  []
strings :  [abce abcd cdx]
n :  2
result :  [abcd abce cdx]
solution result :  []
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/custom_sort      0.211s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/custom_sort)