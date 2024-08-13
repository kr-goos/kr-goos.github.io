---
title: "[Golang] 프로그래머스 Lv.1 x만큼 간격이 있는 n개의 숫자"
description: x만큼 간격이 있는 n개의 숫자 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 08:50:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
함수 solution은 정수 x와 자연수 n을 입력 받아, x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트를 리턴해야 합니다. 다음 제한 조건을 보고, 조건을 만족하는 함수, solution을 완성해주세요.


## 제한사항
- x는 -10000000 이상, 10000000 이하인 정수입니다.
- n은 1000 이하인 자연수입니다.

## 입출력 예
{% raw %}
| x | n | answer |
| --- | --- | --- |
| 2 | 5 | \[2,4,6,8,10\] |
| 4 | 3 | \[4,8,12\] |
| \-4 | 2 | \[-4, -8\] |
{% endraw %}


## 풀이 
```golang
func solution(x int, n int) []int64 {

	answer := make([]int64, 0, n)
	for i := 1; i <= n; i++ {
		answer = append(answer, int64(x*i))
	}
	return answer
}
```

- 규칙을 찾는것이 중요
	- x 만큼의 간격을 가진 값을 n 개
		- 순회를 n 번
		- x 간격 : x * 인덱스 1 ~ n 


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	x      = [TESTCOUNT]int{2, 4, -4}
	n      = [TESTCOUNT]int{5, 3, 2}
	answer = [TESTCOUNT][]int{
		{2, 4, 6, 8, 10},
		{4, 8, 12},
		{-4, -8},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("x : %d\n", x[i])
		fmt.Printf("n : %d\n", n[i])
		fmt.Printf("answer : %v\n", answer[i])
		fmt.Printf("solution answer : %v\n", solution(x[i], n[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
x : 2
n : 5
answer : [2 4 6 8 10]
solution answer : [2 4 6 8 10]
x : 4
n : 3
answer : [4 8 12]
solution answer : [4 8 12]
x : -4
n : 2
answer : [-4 -8]
solution answer : [-4 -8]
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/numbers_with_interval    0.195s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/numbers_with_interval)