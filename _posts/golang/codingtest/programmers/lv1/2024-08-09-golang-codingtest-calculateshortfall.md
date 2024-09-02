---
title: "[Golang] 프로그래머스 Lv.1 부족한 금액 계산하기"
description: 부족한 금액 계산하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-09 12:35:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
새로 생긴 놀이기구는 인기가 매우 많아 줄이 끊이질 않습니다. 이 놀이기구의 원래 이용료는 price원 인데, 놀이기구를 N 번 째 이용한다면 원래 이용료의 N배를 받기로 하였습니다. 즉, 처음 이용료가 100이었다면 2번째에는 200, 3번째에는 300으로 요금이 인상됩니다.
놀이기구를 count번 타게 되면 현재 자신이 가지고 있는 금액에서 얼마가 모자라는지를 return 하도록 solution 함수를 완성하세요.
단, 금액이 부족하지 않으면 0을 return 하세요.


## 제한사항
- 놀이기구의 이용료 price : 1 ≤ price ≤ 2,500, price는 자연수
- 처음 가지고 있던 금액 money : 1 ≤ money ≤ 1,000,000,000, money는 자연수
- 놀이기구의 이용 횟수 count : 1 ≤ count ≤ 2,500, count는 자연수



## 입출력 예
{% raw %}
| price | money | count | result |
| --- | --- | --- | --- |
| 3 | 20 | 4 | 10 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 이용금액이 3인 놀이기구를 4번 타고 싶은 고객이 현재 가진 금액이 20이라면, 총 필요한 놀이기구의 이용 금액은 30 **(= 3+6+9+12)** 이 되어 10만큼 부족하므로 10을 return 합니다.


## 풀이 
```golang
func solution(price int, money int, count int) int64 {

	var total int
	for i := 1; i <= count; i++ {
		total += i * price
	}

	if shortfall := int64(total - money); shortfall > 0 {
		return shortfall
	}

	return 0
}

func improvedSolution(price int, money int, count int) int64 {

	total := price * count * (count + 1) / 2

	if total > money {
		return int64(total - money)

	}

	return 0
}
```

위 코드는 별도로 설명해야 할 부분이 없는 것 같아, `solution` 함수와 `improvedSolution` 함수의 차이를 설명하겠습니다.

- total 을 구하는 계산을 count 만큼 순회하는 방식이 아닌, 가우스 덧셈을 이용한 방법
	- 예를들어, 1 + 2 + ... + n 가 있다면 1 + n 과 2 + ( n - 1 )이 같다. 즉, 대칭되는 두 수의 합이 같다.
		- 증가 값이 1 이 아니더라도, 값과 값의 범위가 일정하다면 해당 식을 적용할 수 있다.
	- 가우스 덧셈 공식 : n * (n + 1) / 2
		- 위는 값과 값 사이의 범위가 (price(3) > 3 + 6 + 9) 이므로, 가우스 덧셈 공식에 * price 를 곱해주면 된다. `total := price * count * (count + 1) / 2`


**두 함수 모두 테스트에 성공하지만, 성능의 차이가 있습니다. (아래 벤치마크 테스트에서 결고를 확인할 수 있습니다.)**


## 테스트코드
### 유닛 테스트
```golang
var (
	price, money, count int   = 3, 20, 4
	result              int64 = 10
)

func TestSolution(t *testing.T) {
	fmt.Println("price : ", price)
	fmt.Println("money : ", money)
	fmt.Println("count : ", price)
	r := solution(price, money, count)
	if r == result {
		fmt.Println("success")
	} else {
		fmt.Println("failure")
	}
}

func TestImprovedSolution(t *testing.T) {
	fmt.Println("price : ", price)
	fmt.Println("money : ", money)
	fmt.Println("count : ", price)
	r := improvedSolution(price, money, count)
	if r == result {
		fmt.Println("success")
	} else {
		fmt.Println("failure")
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
price :  3
money :  20
count :  3
success
--- PASS: TestSolution (0.00s)
=== RUN   TestImprovedSolution
price :  3
money :  20
count :  3
success
--- PASS: TestImprovedSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/calculate_shortfall      0.139s
```

### 벤치마크 테스트
```golang
var (
	price, money, count int   = 3, 20, 4
	result              int64 = 10
)

func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(price, money, count)
	}
}

func BenchmarkImprovedSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		improvedSolution(price, money, count)
	}
}
```

```bash
$ go test -bench . -run ^$
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/calculate_shortfall
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4             470408557                2.253 ns/op
BenchmarkImprovedSolution-4     1000000000               0.3474 ns/op
PASS
ok      golang-coding-test/Lv1/calculate_shortfall      1.877s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/calculate_shortfall)