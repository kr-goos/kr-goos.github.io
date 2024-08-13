---
title: "[Golang] 프로그래머스 Lv.1 평균 구하기"
description: 평균 구하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-14 07:55:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
정수를 담고 있는 배열 arr의 평균값을 return하는 함수, solution을 완성해보세요.


## 제한사항
- arr은 길이 1 이상, 100 이하인 배열입니다.
- arr의 원소는 -10,000 이상 10,000 이하인 정수입니다.


## 입출력 예
{% raw %}
| arr | return |
| --- | --- |
| \[1,2,3,4\] | 2.5 |
| \[5,5\] | 5 |
{% endraw %}


## 풀이 
```golang
func solution(arr []int) float64 {
	var total int
	for _, v := range arr {
		total += v
	}
	return float64(total) / float64(len(arr))
}
```


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 4

var (
	x      = [TESTCOUNT]int{10, 12, 11, 13}
	result = [TESTCOUNT]bool{true, true, false, false}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("x : %d\n", x[i])
		fmt.Printf("result : %t\n", result[i])
		fmt.Printf("solution result : %t\n", solution(x[i]))
	}
}

func TestSolution2(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("x : %d\n", x[i])
		fmt.Printf("result : %t\n", result[i])
		fmt.Printf("solution result : %t\n", solution2(x[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
arr : [1 2 3 4]
result : 2.5
solution result : 2.5
arr : [5 5]
result : 5
solution result : 5
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/average_calculation      0.187s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/average_calculation)