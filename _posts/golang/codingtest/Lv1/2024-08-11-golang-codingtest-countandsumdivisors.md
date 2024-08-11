---
title: "[Golang] 프로그래머스 Lv.1 2021 약수의 개수와 덧셈"
description: 약수의 개수와 덧셈 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-11 14:45:03 +09000
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
두 정수 `left`와 `right`가 매개변수로 주어집니다. `left`부터 `right`까지의 모든 수들 중에서, 약수의 개수가 짝수인 수는 더하고, 약수의 개수가 홀수인 수는 뺀 수를 return 하도록 solution 함수를 완성해주세요.

## 제한사항
- 1 ≤ `left` ≤ `right` ≤ 1,000


## 입출력 예
{% raw %}
|left|right|result|
|-|-|-|
|13|17|43|
|24|27|52|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 다음 표는 13부터 17까지의 수들의 약수를 모두 나타낸 것입니다.

|수|약수|약수의 개수|
|-|-|-|
|13|1, 13|2|
|14|1, 2, 7, 14|4|
|15|1, 3, 5, 15|4|
|16|1, 2, 4, 8, 16|5|
|17|1, 17|2|

- 따라서, 13 + 14 + 15 - 16 + 17 = 43을 return 해야 합니다.

**입출력 예 #2**

- 다음 표는 24부터 27까지의 수들의 약수를 모두 나타낸 것입니다.

|수|약수|약수의 개수|
|-|-|-|
|24|1, 2, 3, 4, 6, 8, 12, 24|8|
|25|1, 5, 25|3|
|26|1, 2, 13, 26|4|
|27|1, 3, 9, 27|4|

- 따라서, 24 - 25 + 26 + 27 = 52를 return 해야 합니다.

## 풀이 
```golang
func solution(left int, right int) int {
	var answer int
	for i := left; i <= right; i++ {
		v := countDivisors(i)
		if v == 0 {
			continue
		}

		if v%2 == 0 {
			answer += i
		} else {
			answer -= i
		}
	}
	return answer
}

func countDivisors(v int) int {
	var count int
	for i := 1; i <= v; i++ {
		if v%i == 0 {
			count++
		}
	}

	return count
}
```

- `left` 부터 `right` 까지 순회
- `countDivisors` 함수를 이용하여 현재 i 의 약수가 몇개인지 구함
	- 1 ~ `v` 까지 순회하며, `v % i == 0` 인 개수를 count 변수에 저장
- 함수의 결과값이 0 이라면, 더하거나 빼는 연산을 하지 않고 `continue`
- 만약 약수의 개수가 짝수라면, 현재 값 `i` 를 더하고, 홀수라면 `i` 를 뺌
- 연산 결과값 `answer`를 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	left   = [TESTCOUNT]int{13, 24}
	right  = [TESTCOUNT]int{17, 27}
	result = [TESTCOUNT]int{43, 52}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("left : ", left[i])
		fmt.Println("right : ", right[i])

		if solution(left[i], right[i]) == result[i] {
			fmt.Printf("test %d success\n", i)
		} else {
			log.Printf("test %d failure\n", i)
		}
	}
}

func TestCountDivisors(t *testing.T) {

	const TESTCOUNT = 5
	v := [TESTCOUNT]int{13, 14, 15, 16, 17}
	result := [TESTCOUNT]int{2, 4, 4, 5, 2}

	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("v : ", v[i])
		fmt.Println("result : ", result[i])
		fmt.Println("countDivisors : ", countDivisors(v[i]))
	}
}
```

- solution 함수를 유닛테스트 하기 앞서, solution 함수 내에서 사용하고 있는 countDivisors 함수를 테스트해 보세요.
- 각 단일 기능을 하는 함수를 작게 만들어, 위와같이 테스트 해보는 습관이 중요합니다.


```bash
$ go test -v
=== RUN   TestSolution
left :  13
right :  17
test 0 success
left :  24
right :  27
test 1 success
--- PASS: TestSolution (0.00s)
=== RUN   TestCountDivisors
v :  13
result :  2
countDivisors :  2
v :  14
result :  4
countDivisors :  4
v :  15
result :  4
countDivisors :  4
v :  16
result :  5
countDivisors :  5
v :  17
result :  2
countDivisors :  2
--- PASS: TestCountDivisors (0.00s)
PASS
ok      golang-coding-test/Lv1/count_and_sum_divisors   0.141s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/count_and_sum_divisors)