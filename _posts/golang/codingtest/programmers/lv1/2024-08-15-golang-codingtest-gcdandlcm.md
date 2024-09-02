---
title: "[Golang] 프로그래머스 Lv.1 최대공약수와 최소공배수"
description: 최대공약수와 최소공배수 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-15 15:45:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
두 수를 입력받아 두 수의 최대공약수와 최소공배수를 반환하는 함수, solution을 완성해 보세요. 배열의 맨 앞에 최대공약수, 그다음 최소공배수를 넣어 반환하면 됩니다. 예를 들어 두 수 3, 12의 최대공약수는 3, 최소공배수는 12이므로 solution(3, 12)는 [3, 12]를 반환해야 합니다.


## 제한사항
- 두 수는 1이상 1000000이하의 자연수입니다.


## 입출력 예
{% raw %}
|n|m|return|
|-|-|-|
|3|12|[3, 12]|
|2|5|[1, 10]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 위의 설명과 같습니다.

**입출력 예 #2**

- 자연수 2와 5의 최대공약수는 1, 최소공배수는 10이므로 [1, 10]을 리턴해야 합니다.

## 풀이 

### 정의
- 약수 : 어떤 수를 나누어 떨어지게 하는 수
	- 예를들어, 12 의 약수는 1,2,3,4,6,12
- 최대공약수 : 두개 이상의 수의 공통된 약수 중 가장 큰 수
	- 예를들어, 12와 18의 공통된 약수
		- 12 : **1**,**2**,**3**,4,**6**,12
		- 18 : **1**,**2**,**3**,**6**,9,18
		- 위 공통 된 약수 중 가장 큰 수 6 을 최대 공약수라 함
- 최소공배수 : 두 개 이상의 수의 공통된 배수 중에서 가장 작은 수
	- 예를들어, 4와 6의 공통된 배수
		- 4의 배수: 4, 8, **12**, 16, 20, **24**, ...
		- 6의 배수: 6, **12**, 18, **24**, 30, ...
		- 위 공통 된 배수 중 가장 작은 수 12 를 최소 공배수라 함
- 유클리드 호제법 : **두 수의 최대공약수(GCD)를 효율적으로 구하는 알고리즘**
	- 두 양의 정수 a,b (a>b)에 대하여 a = bq + r (0 <= r < b) 이라 하면, a,b 의 최대 공약수는 b,r의 최대 공약수와 같다. 즉,  gcd(a,b) = gcd(b,r)
	- r = 0 이라면, a,b의 최대공약수는 b 가 된다.
- 유클리드 호제법의 과정
	1. 두 수 a와 b가 주어졌다고 가정합시다. 여기서 a > b 로 설정합니다.
	2. a를 b로 나누고, 그 나머지를 r이라고 합니다. r = a % b
	3. 만약 r = 0 이라면, b가 두 수의 최대공약수입니다.
	4. 마약 r != 0 이라면, a=b, b=r로 설정하고 2단계로 돌아갑니다.
	5. 나머지가 0이 될 때까지 이 과정을 반복합니다.

```golang
func solution(n int, m int) []int {
	gcd := Gcd(n, m)

	return []int{gcd, n * m / gcd}
}

func Gcd(n, m int) int {
	if n < m {
		n, m = m, n
	}

	if m == 0 {
		return n
	}
	return Gcd(m, n%m)
}
```

- 최대공약수는 Gcd 함수를 이용하고, 최소공배수는 최소공배수 공식 `lcm = (a*b)/gcd`를 사용하여 계산
- Gcd 함수
	- 이 함수는 유클리드 호제법을 사용하여 두 수 `n`과 `m`의 최대공약수를 재귀적으로 계산
	- 첫 번째 `if` 문에서 `n`이 `m`보다 작다면, 두 수의 값을 교환하여 n이 항상 m보다 크도록 만듦 (계산의 편의를 위함)
	- 만약 `m`이 0이면, 더 이상 나눌 필요가 없으므로 `n`이 최대공약수로 반환
	- 그렇지 않으면, `n`을 `m`으로 나눈 나머지인 `n % m`과 `m`을 이용하여 다시 Gcd 함수를 호출
	- 예를들어 `n = 48`, `m = 18`을 입력으로 주면
		- 첫 번째 호출 `Gcd(48, 18)`
			- `48` 이 `18`보다 크기 때문에 값의 교환은 필요 없음
			- `m` 이 0 이 아니므로, 재귀적으로 `48%18`의 결과 `Gcd(18, 12)`를 호출
		- 두 번째 호출 `Gcd(18, 12)`
			- `18`이 `12`보다 크기 때문에 값의 교환은 필요 없음
			- `m`이 0이 아니므로, 재귀적으로 `18%12`의 결과 `Gcd(12, 6)`을 호출
		- 세 번째 호출 `Gcd(12, 6)`
			- `12`가 `6`보다 크기 때문에 값의 교환은 필요 없음
			- `m`이 0이 아니므로, 재귀적으로 `12%6`의 결과 `Gcd(6, 0)`을 호출
		- 네 번째 호출 `Gcd(6, 0)`
			- `m`이 0이므로, 현재의 `n` 값인 6이 최대공약수로 반환
	- 최대공약수 : 6
	- 최소공배수 : 144 (48 * 18 / 6)  


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	n      = [TESTCOUNT]int{3, 2}
	m      = [TESTCOUNT]int{12, 5}
	result = [TESTCOUNT][]int{
		{3, 12},
		{1, 10},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("n : %d\n", n[i])
		fmt.Printf("m : %d\n", m[i])

		r := solution(n[i], m[i])
		for j, answer := range result[i] {
			if answer != r[j] {
				t.Errorf("test %d %d index failure", i, j)
			}
		}
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
n : 3
m : 12
n : 2
m : 5
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/gcd_and_lcm      0.177s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/gcd_and_lcm)