---
title: "[Golang] 프로그래머스 Lv.1 소수 만들기"
description: 소수 만들기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 08:20:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
주어진 숫자 중 3개의 수를 더했을 때 소수가 되는 경우의 개수를 구하려고 합니다. 숫자들이 들어있는 배열 nums가 매개변수로 주어질 때, nums에 있는 숫자들 중 서로 다른 3개를 골라 더했을 때 소수가 되는 경우의 개수를 return 하도록 solution 함수를 완성해주세요.


## 제한사항
- nums에 들어있는 숫자의 개수는 3개 이상 50개 이하입니다.
- nums의 각 원소는 1 이상 1,000 이하의 자연수이며, 중복된 숫자가 들어있지 않습니다.


## 입출력 예
{% raw %}
| nums | result |
| --- | --- |
| \[1,2,3,4\] | 1 |
| \[1,2,7,6,4\] | 4 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- [1,2,4]를 이용해서 7을 만들 수 있습니다.


**입출력 예 #2**

- [1,2,4]를 이용해서 7을 만들 수 있습니다.
- [1,4,6]을 이용해서 11을 만들 수 있습니다.
- [2,4,7]을 이용해서 13을 만들 수 있습니다.
- [4,6,7]을 이용해서 17을 만들 수 있습니다.


## 풀이 
```golang
func solution(nums []int) int {

	var primeCount int
	for i := 0; i < len(nums); i++ {
		for j := i + 1; j < len(nums); j++ {
			for k := j + 1; k < len(nums); k++ {
				v := nums[i] + nums[j] + nums[k]
				isPrime := true
				for idx := 2; idx <= int(math.Sqrt(float64(v))); idx++ {
					if v%idx == 0 {
						isPrime = false
						break
					}
				}
				if isPrime {
					primeCount++
				}
			}
		}
	}

	return primeCount
}
```
- `nums`의 요소 중 3개를 전체 탐색하도록 순회
	- `0,1,2` `0,1,3` `0,1,4` `0,2,3` `0,2,4` ... `len(nums)-3,len(nums)-2,len(nums)-1`
- 요소 3개의 합이 소수인지 판단
	- `for idx := 2; idx <= int(math.Sqrt(float64(v))); idx++ `
		- 1과 자기 자신을 포함하지 않으며, 2 ~ 제곱근의 값 까지 순회
			- **어떤 수 v가 소수가 아니라면, 그 수의 약수 중 최소 하나는 √v보다 작거나 같습니다**
			- 제곱근이 아닌 전체를 순회한다면 v 값이 소수일 때 모든 숫자에 대해 나누어 떨어지는지 확인
- 소수이면 카운트 증가


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	nums = [TESTCOUNT][]int{
		{1, 2, 3, 4},
		{1, 2, 7, 6, 4},
	}
	result = [TESTCOUNT]int{1, 4}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("nums : %v\n", nums[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(nums[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
nums : [1 2 3 4]
result : 1
solution result : 1
nums : [1 2 7 6 4]
result : 4
solution result : 4
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/make_prime       0.217s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/make_prime)