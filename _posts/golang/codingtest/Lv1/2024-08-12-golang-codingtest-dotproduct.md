---
title: "[Golang] 프로그래머스 Lv.1 음양 더하기"
description: 음양 더하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-12 08:50:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
길이가 같은 두 1차원 정수 배열 a, b가 매개변수로 주어집니다. a와 b의 내적을 return 하도록 solution 함수를 완성해주세요.

이때, a와 b의 내적은 `a[0]*b[0] + a[1]*b[1] + ... + a[n-1]*b[n-1]` 입니다. (n은 a, b의 길이)


## 제한사항
- a, b의 길이는 1 이상 1,000 이하입니다.
- a, b의 모든 수는 -1,000 이상 1,000 이하입니다.


## 입출력 예
{% raw %}
| a | b | result |
| --- | --- | --- |
| `[1,2,3,4]` | `[-3,-1,0,2]` | 3 |
| `[-1,0,1]` | `[1,0,-1]` | \-2 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- a와 b의 내적은 `1*(-3) + 2*(-1) + 3*0 + 4*2 = 3` 입니다.


**입출력 예 #2**

- a와 b의 내적은 `(-1)*1 + 0*0 + 1*(-1) = -2` 입니다.


## 풀이 
```golang
func solution(a []int, b []int) int {

	var total int
	for i, v := range a {
		total += v * b[i]
	}
	return total
}
```

- a,b 의 길이가 같으므로 둘 중 아무 배열이나 순회
- 순회하며 나온 값과, 상대 배열의 값을 곱하여 total 에 누적
- total 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	a = [TESTCOUNT][]int{
		{1, 2, 3, 4},
		{-1, 0, 1},
	}

	b = [TESTCOUNT][]int{
		{-3, -1, 0, 2},
		{1, 0, -1},
	}
	result = [TESTCOUNT]int{3, -2}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("a : %v\n", a[i])
		fmt.Printf("b : %v\n", b[i])
		fmt.Printf("result : %d\n", result[i])
		fmt.Printf("solution result : %d\n", solution(a[i], b[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
a : [1 2 3 4]
b : [-3 -1 0 2]
result : 3
solution result : 3
a : [-1 0 1]
b : [1 0 -1]
result : -2
solution result : -2
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/dot_product      0.234s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/dot_product)