---
title: "[Golang] 프로그래머스 Lv.1 기사단원의 무기"
description: 기사단원의 무기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-01 12:30:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
숫자나라 기사단의 각 기사에게는 1번부터 `number`까지 번호가 지정되어 있습니다. 기사들은 무기점에서 무기를 구매하려고 합니다.

각 기사는 자신의 기사 번호의 약수 개수에 해당하는 공격력을 가진 무기를 구매하려 합니다. 단, 이웃나라와의 협약에 의해 공격력의 제한수치를 정하고, 제한수치보다 큰 공격력을 가진 무기를 구매해야 하는 기사는 협약기관에서 정한 공격력을 가지는 무기를 구매해야 합니다.

예를 들어, 15번으로 지정된 기사단원은 15의 약수가 1, 3, 5, 15로 4개 이므로, 공격력이 4인 무기를 구매합니다. 만약, 이웃나라와의 협약으로 정해진 공격력의 제한수치가 3이고 제한수치를 초과한 기사가 사용할 무기의 공격력이 2라면, 15번으로 지정된 기사단원은 무기점에서 공격력이 2인 무기를 구매합니다. 무기를 만들 때, 무기의 공격력 1당 1kg의 철이 필요합니다. 그래서 무기점에서 무기를 모두 만들기 위해 필요한 철의 무게를 미리 계산하려 합니다.

기사단원의 수를 나타내는 정수 `number`와 이웃나라와 협약으로 정해진 공격력의 제한수치를 나타내는 정수 `limit`와 제한수치를 초과한 기사가 사용할 무기의 공격력을 나타내는 정수 `power`가 주어졌을 때, 무기점의 주인이 무기를 모두 만들기 위해 필요한 철의 무게를 return 하는 solution 함수를 완성하시오.

## 제한사항
- 1 ≤ `number` ≤ 100,000
- 2 ≤ `limit` ≤ 100
- 1 ≤ `power` ≤ `limit`

## 입출력 예
{% raw %}
| number | limit | power | result |
| --- | --- | --- | --- |
| 5 | 3 | 2 | 10 |
| 10 | 3 | 2 | 21 |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

1부터 5까지의 약수의 개수는 순서대로 [1, 2, 2, 3, 2]개입니다. 모두 공격력 제한 수치인 3을 넘지 않기 때문에 필요한 철의 무게는 해당 수들의 합인 10이 됩니다. 따라서 10을 return 합니다.

**입출력 예 #2**

1부터 10까지의 약수의 개수는 순서대로 [1, 2, 2, 3, 2, 4, 2, 4, 3, 4]개입니다. 공격력의 제한수치가 3이기 때문에, 6, 8, 10번 기사는 공격력이 2인 무기를 구매합니다. 따라서 해당 수들의 합인 21을 return 합니다.


## 풀이 

### 개선 전
```golang
func solution(number int, limit int, power int) int {
	var total int
	for i := 1; i <= number; i++ {
		var weight int
		for j := 1; j <= i; j++ {
			if i%j != 0 {
				continue
			}
			weight++

			if weight > limit {
				weight = power
				break
			}
		}
		total += weight
	}
	return total
}
```
- `total` 변수는 전체 필요한 철의 무게를 누적하기 위해 사용
- 첫 번째 for 루프는 1부터 `number`까지 각 기사의 번호를 순회
- `weight` 변수는 현재 기사의 번호에 대한 약수의 개수를 저장
- 두 번째 for 루프는 1부터 현재 기사의 번호까지 **모든 수를 검사**하여 약수를 찾음
	- `if i%j != 0 { continue }` 조건문은 현재 숫자가 약수가 아닌 경우를 건너뜀
	- `weight++`는 약수를 찾을 때마다 증가
	- `if weight > limit` 조건문은 약수의 개수가 제한수를 초과할 경우 `weight`를 `power`로 설정하고 루프를 종료
- `total += weight`는 계산된 `weight`를 `total`에 더함
- `total`을 반환

```
위 코드를 프로그래머스에서 실행한 경우 `실패 (시간 초과)` 과 같은 에러를 마주할 수 있습니다. 
이 `solution` 함수는 모든 수에 대해 1부터 자신까지의 모든 숫자를 검사하여 약수를 구합니다.
이와 같은 방법은 비 효율적이며 시간이 오래 걸립니다.
```

이 코드를 개선하는 방법을 알아보겠습니다.

### 개선 후
```golang
func solution(number int, limit int, power int) int {
	var total int
	for i := 1; i <= number; i++ {
		var weight int
		sqrtI := int(math.Sqrt(float64(i)))
		for j := 1; j <= sqrtI; j++ {
			if i%j != 0 {
				continue
			}

			if j*j == i {
				weight++
			} else {
				weight += 2
			}

			if weight > limit {
				weight = power
				break
			}
		}
		total += weight
	}
	return total
}
```
- `total` 변수는 전체 필요한 철의 무게를 누적하기 위해 사용
- 첫 번째 for 루프는 1부터 `number`까지 각 기사의 번호를 순회
- `weight` 변수는 현재 기사의 번호에 대한 약수의 개수를 저장
- `sqrtI := int(math.Sqrt(float64(i)))`는 **현재 기사의 번호의 제곱근을 계산** (검사할 숫자의 범위 최소화)
- 두 번째 for 루프는 1부터 제곱근까지의 모든 수를 검사하여 약수를 찾음
	- `if i%j != 0 { continue }` 조건문은 현재 숫자가 약수가 아닌 경우를 건너뜀
	- `if j*j == i` 조건문은 현재 숫자가 정확히 제곱근인 경우를 검사하여 약수를 하나만 증가
	- `else { weight += 2 }`는 현재 숫자가 약수인 경우와 그 쌍의 약수를 두 개 증가
	- `if weight > limit` 조건문은 약수의 개수가 제한수를 초과할 경우 `weight`를 `power`로 설정하고 루프를 종료
- `total += weight`는 계산된 `weight`를 `total`에 더함
- `total`을 반환


### 개선 점
- 개선된 `solution` 함수에서는 **약수**를 구하는 효율적인 방법을 사용하여 각 수의 제곱근까지만 검사함으로써 성능을 개선했습니다. 이렇게 하면 중복된 계산을 피하고 시간 복잡도를 줄일 수 있습니다.

### [핵심] 약수를 빠르게 구하기

정수 n의 약수를 구할 때, 단순히 1부터 n까지의 모든 수를 나눠보는 것은 비효율적입니다. 
대신 n의 약수는 항상 쌍으로 존재한다는 점을 이용합니다. 예를 들어, 36의 약수는 다음과 같습니다.
- 1과 36
- 2와 18
- 3과 12
- 4와 9
- 6과 6 (제곱근)

따라서 n의 제곱근까지의 숫자들만 검사하면 됩니다. 만약 어떤 숫자 i가 n을 나누어떨어지게 한다면, i와 n/i도 약수쌍으로 존재합니다.
 
## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	number = [TESTCOUNT]int{5, 10}
	limit  = [TESTCOUNT]int{3, 3}
	power  = [TESTCOUNT]int{2, 2}
	result = [TESTCOUNT]int{10, 21}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("number[i] : ", number[i])
		fmt.Println("limit[i] : ", limit[i])
		fmt.Println("power[i] : ", power[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("solution result : ", solution(number[i], limit[i], power[i]))
	}
}

func TestSolution2(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("number[i] : ", number[i])
		fmt.Println("limit[i] : ", limit[i])
		fmt.Println("power[i] : ", power[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("solution result : ", solution(number[i], limit[i], power[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
number[i] :  5
limit[i] :  3
power[i] :  2
result[i] :  10
solution result :  10
number[i] :  10
limit[i] :  3
power[i] :  2
result[i] :  21
solution result :  21
--- PASS: TestSolution (0.00s)
=== RUN   TestSolution2
number[i] :  5
limit[i] :  3
power[i] :  2
result[i] :  10
solution result :  10
number[i] :  10
limit[i] :  3
power[i] :  2
result[i] :  21
solution result :  21
--- PASS: TestSolution2 (0.01s)
PASS
ok      golang-coding-test/Lv1/knight_weapon    0.150s
```


### 벤치마크 테스트
```golang
const TESTCOUNT = 2

var (
	number = [TESTCOUNT]int{5, 10}
	limit  = [TESTCOUNT]int{3, 3}
	power  = [TESTCOUNT]int{2, 2}
	result = [TESTCOUNT]int{10, 21}
)

func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(number[1], limit[1], power[1])
	}
}

func BenchmarkSolution2(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution2(number[1], limit[1], power[1])
	}
}
```

```bash
$ go test -bench . -run ^$   
# -run ^$: 이 플래그는 유닛 테스트를 건너뛰기 위해 아무것도 매치하지 않는 정규 표현식을 사용합니다. ^$는 빈 문자열을 매치하므로, 모든 유닛 테스트가 실행되지 않습니다.
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/knight_weapon
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4      4286745               260.6 ns/op
BenchmarkSolution2-4    12424430                85.67 ns/op
PASS
ok      golang-coding-test/Lv1/knight_weapon    2.700s
```
- 개선 전/후 함수의 벤치마크 테스트 결과 약 3배 이상의 성능 향상을 확인할 수 있습니다. (수치가 중요한 것이 아닌, 문제 파악 및 해결 방법이 중요합니다.)

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/knight_weapon)