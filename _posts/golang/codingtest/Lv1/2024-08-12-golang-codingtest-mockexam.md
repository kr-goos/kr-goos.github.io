---
title: "[Golang] 프로그래머스 Lv.1 모의고사"
description: 모의고사 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-12 21:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- 시험은 최대 10,000 문제로 구성되어있습니다.
- 문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
- 가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.


## 입출력 예
{% raw %}
|answers|return|
|-|-|
|[1,2,3,4,5]|[1]|
|[1,3,2,4,2]|[1,2,3]|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 수포자 1은 모든 문제를 맞혔습니다.
- 수포자 2는 모든 문제를 틀렸습니다.
- 수포자 3은 모든 문제를 틀렸습니다.

따라서 가장 문제를 많이 맞힌 사람은 수포자 1입니다.

**입출력 예 #2**

- 모든 사람이 2문제씩을 맞췄습니다.


## 풀이 
```golang
func solution(answers []int) []int {
	pattern1 := []int{1, 2, 3, 4, 5}
	pattern2 := []int{2, 1, 2, 3, 2, 4, 2, 5}
	pattern3 := []int{3, 3, 1, 1, 2, 2, 4, 4, 5, 5}

	correct := make([]int, 3)
	p1len, p2len, p3len := len(pattern1), len(pattern2), len(pattern3)
	for i, answer := range answers {
		if pattern1[i%p1len] == answer {
			correct[0]++
		}

		if pattern2[i%p2len] == answer {
			correct[1]++
		}

		if pattern3[i%p3len] == answer {
			correct[2]++
		}
	}

	var max int
	for _, c := range correct {
		if c > max {
			max = c
		}
	}

	result := make([]int, 0, 3)
	for i, c := range correct {
		if c == max {
			result = append(result, i+1)
		}
	}

	return result
}
```

- 문제에서 반복 패턴을 추출 후 각 패턴을 int 슬라이스로 초기화
- 수포자 1,2,3이 맞힌 갯수를 저장하는 `correct` 슬라이스를 생성 
	- 수포자 1 : correct[0]
	- 수포자 2 : correct[1]
	- 수포자 3 : correct[2]
- `answers` 순회
	- 각 `patternX[i % 패턴길이]` 의 요소와 `answer` 가 같은지 비교
		- `patternX[i % 패턴길이]`는 pattern 슬라이스에 정의된 값들을 반복적으로 가리킴
		- 만약 두 값이 같다면 각 수포자(`correct[n]`)의 맞힌 개수를 증가시킴
- `correct` 슬라이스를 순회하며 가장 많이 맞힌 갯수 `max` 를 구함
- `correct` 슬라이스를 순회하며, max 값과 일치하는 수포자를 `result` 슬라이스에 추가
	- `correct` 슬라이스는 수포자 1,2,3 에 대해 순서대로 저장하고 있으므로
	  ```
	  #제한조건 3
  	  가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.
	  ```
	  `correct` 슬라이스를 순회할 때, 수포자 순서대로 나오므로 위 조건을 신경쓰지 않아도 됨


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	answers = [TESTCOUNT][]int{
		{1, 2, 3, 4, 5},
		{1, 3, 2, 4, 2},
	}

	result = [TESTCOUNT][]int{
		{1},
		{1, 2, 3},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("answers : %v\n", answers[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(answers[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
answers : [1 2 3 4 5]
result : [1]
solution result : [1]
answers : [1 3 2 4 2]
result : [1 2 3]
solution result : [1 2 3]
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/mock_exam        0.164s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/mock_exam)