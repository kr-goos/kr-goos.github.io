---
title: "[Golang] 프로그래머스 Lv.1 두 정수 사이의 합"
description: 두 정수 사이의 합 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 20:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
두 정수 a, b가 주어졌을 때 a와 b 사이에 속한 모든 정수의 합을 리턴하는 함수, solution을 완성하세요.
예를 들어 a = 3, b = 5인 경우, 3 + 4 + 5 = 12이므로 12를 리턴합니다.


## 제한사항
- a와 b가 같은 경우는 둘 중 아무 수나 리턴하세요.
- a와 b는 -10,000,000 이상 10,000,000 이하인 정수입니다.
- a와 b의 대소관계는 정해져있지 않습니다.


## 입출력 예
{% raw %}
|a|b|return|
|-|-|-|
|3|5|12|
|3|3|3|
|5|3|12|
{% endraw %}


## 풀이 
```golang
func solution(a int, b int) int64 {
	if a > b {
		a, b = b, a
	}
	var answer int
	for i := a; i <= b; i++ {
		answer += i
	}
	return int64(answer)
}
```

- `a` 가 `b` 보다 크다면 `a`와 `b` 의 값을 바꿈
- `a ~ b` 순회
	- `answer` 에 `i` 값을 더함
- `answer` 의 자료형을 int64 로 변환하고 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	a = [TESTCOUNT]int{
		3,
		3,
		5,
	}

	b = [TESTCOUNT]int{
		5,
		3,
		3,
	}

	result = [TESTCOUNT]int{
		12,
		3,
		12,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("a ", a[i])
		fmt.Println("b ", b[i])
		fmt.Println("result ", result[i])
		fmt.Println("solution result : ", solution(a[i], b[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
a  3
b  5
result  12
solution result :  12
a  3
b  3
result  3
solution result :  3
a  5
b  3
result  12
solution result :  12
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/sum_between      0.144s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sum_between)