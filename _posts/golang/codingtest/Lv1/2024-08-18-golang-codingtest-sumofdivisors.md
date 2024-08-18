---
title: "[Golang] 프로그래머스 Lv.1 약수의 합"
description: 약수의 합 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-18 21:03:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
정수 n을 입력받아 n의 약수를 모두 더한 값을 리턴하는 함수, solution을 완성해주세요.


## 제한사항
- n은 0 이상 3000이하인 정수입니다.


## 입출력 예
{% raw %}
| n | return |
| --- | --- |
| 12 | 28 |
| 5 | 6 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 12의 약수는 1, 2, 3, 4, 6, 12입니다. 이를 모두 더하면 28입니다.

**입출력 예 #2**

- 5의 약수는 1, 5입니다. 이를 모두 더하면 6입니다.


## 풀이 
```golang
func solution(n int) int {
	var answer int
	for i := 1; i <= n/2; i++ {
		if n%i == 0 {
			answer += i
		}
	}
	return answer + n
}
```

- `i`는 1 ~ `n/2` 를 순회
	- `n%i == 0 ` 이면 `i` 는 `n` 의 약수 이므로 answer 에 `i`를 더함
- `answer` 에 `n`을 더하고 반환 (`i` 1 ~ n/2 까지 순회하며 약수를 찾았으므로 자기 자신을 포함)


### 약수의 정의
- 약수는 n을 나누었을 때 나머지가 0이 되는 수를 의미

### 약수의 범위
- 어떤 수 n의 약수는 1부터 n까지 존재할 수 있지만, n의 절반을 초과하는 수는 n의 약수가 될 수 없음

### **중요 포인트**
- 약수의 쌍 : 약수는 쌍으로 존재
	- 예를들어 12의 약수 쌍은
		- (1,12), (2,6), (3,4)
- 약수의 쌍에서 큰 값 : 약수 `x`가 `n/2` 보다 크다면 그 짝은 `n/x` 가 됨
	- 예를들어 `n` 이 12 이고 `x` 가 나누는 값이라면
		- 12 / 1 = 12
		- 12 / 2 = 6
		- 12 / 3 = 4
		- 12 / 4 = 3
		- 12 / 6 = 2
			- 만약 이 사이 숫자로 나눈다면, 몫은 1.xxx 가 되고 나머지는 0이 되지 않음
			- **즉, 나눈 몫이 정수가 되는 수 `x` 가 약수가 됨**
		- 12 / 12 = 1


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	n      = [TESTCOUNT]int{12, 5}
	result = [TESTCOUNT]int{28, 6}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("n : ", n[i])
		fmt.Println("result : ", result[i])
		r := solution(n[i])
		fmt.Println("solution result : ", r)
		if result[i] != r {
			t.Errorf("result %d != solution result %d", result[i], r)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
n :  12
result :  28
solution result :  28
n :  5
result :  6
solution result :  6
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/sum_of_divisors  0.152s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sum_of_divisors)