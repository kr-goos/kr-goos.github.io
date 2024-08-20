---
title: "[Golang] 프로그래머스 Lv.1 나머지가 1이 되는 수 찾기"
description: 나머지가 1이 되는 수 찾기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-09 08:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
자연수 `n`이 매개변수로 주어집니다. `n`을 `x`로 나눈 나머지가 1이 되도록 하는 가장 작은 자연수 `x`를 return 하도록 solution 함수를 완성해주세요. 답이 항상 존재함은 증명될 수 있습니다.


## 제한사항
- 3 ≤ `n` ≤ 1,000,000


## 입출력 예
{% raw %}
| n | result |
| --- | --- |
| 10 | 3 |
| 12 | 11 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 10을 3으로 나눈 나머지가 1이고, 3보다 작은 자연수 중에서 문제의 조건을 만족하는 수가 없으므로, 3을 return 해야 합니다.


**입출력 예 #2**

- 12를 11로 나눈 나머지가 1이고, 11보다 작은 자연수 중에서 문제의 조건을 만족하는 수가 없으므로, 11을 return 해야 합니다.


## 풀이 
```golang
func solution(n int) int {

	for i := 2; i < n; i++ {
		if n%i == 1 {
			return i
		}
	}

	return 0
}
```
- n 을 1 로 나눈 경우 무조건 나머지가 0 이므로, 2 부터 n -1 까지를 순회
- n % n-1 의 값은 무조건 1 이 되므로, n 의 범위가 3 이상일 경우 아래의 return 0 이 나오는 경우는 없음


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

func TestSolution(t *testing.T) {

	n := [TESTCOUNT]int{10, 12}
	result := [TESTCOUNT]int{3, 11}
	for i := 0; i < 2; i++ {
		if r := solution(n[i]); result[i] == r {
			fmt.Printf("testcase %d sucecss\n", i+1)
		} else {
			t.Fatalf("testcase %d result : %d, solution result : %d", i+1, result[i], r)
		}
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
testcase 1 sucecss
testcase 2 sucecss
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/find_remainder_one       0.238s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/find_remainder_one)