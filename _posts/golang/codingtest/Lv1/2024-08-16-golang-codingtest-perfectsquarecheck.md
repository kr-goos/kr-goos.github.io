---
title: "[Golang] 프로그래머스 Lv.1 정수 제곱근 판별"
description: 정수 제곱근 판별 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-16 08:01:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
임의의 양의 정수 n에 대해, n이 어떤 양의 정수 x의 제곱인지 아닌지 판단하려 합니다.
n이 양의 정수 x의 제곱이라면 x+1의 제곱을 리턴하고, n이 양의 정수 x의 제곱이 아니라면 -1을 리턴하는 함수를 완성하세요.


## 제한사항
- n은 1이상, 50000000000000 이하인 양의 정수입니다.


## 입출력 예
{% raw %}
| n | return |
| --- | --- |
| 121 | 144 |
| 3 | \-1 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 121은 양의 정수 11의 제곱이므로, (11+1)를 제곱한 144를 리턴합니다.

**입출력 예 #2**

- 3은 양의 정수의 제곱이 아니므로, -1을 리턴합니다.

## 풀이 
```golang
func solution(n int64) int64 {
	sqrt := int64(math.Sqrt(float64(n)))
	if sqrt*sqrt == n {
		return (sqrt + 1) * (sqrt + 1)
	}

	return -1
}
```

- `math.Sqrt(float64(n))`을 사용하여 `n`의 제곱근을 계산하고 이를 `int64`로 변환
- 정수 `sqrt` 를 제곱한 값이 정수 `n` 과 같은지 비교
- **정수 변환** : 제곱근을 정수로 변환하여 비교하기 때문에 부동 소수점 오차의 영향을 줄일 수 있음


### 실패 코드 예시
```golang
func solution2(n int64) int64 {
	sqrt := math.Sqrt(float64(n))
	if sqrt*sqrt == float64(n) {
		return int64((sqrt + 1) * (sqrt + 1))
	}

	return -1
}
```

- `math.Sqrt(float64(n))`을 사용하여 `n`의 제곱근을 계산
- 산된 제곱근을 다시 제곱하여 실수 `n`과 비교

**주의사항**

- 만약 부동 소수점 오차로 인해 `sqrt*sqrt`가 `n`과 정확히 일치하지 않으면, 잘못된 결과를 반환
- 부동 소수점 오류의 예
	- **큰 숫자** : `n`이 매우 큰 숫자일 때, `sqrt`가 근사값이 되며, `sqrt*sqrt`와 `n` 사이에 작은 차이가 생길 수 있음
	- **정밀도 문제** : 특정 숫자에서 `math.Sqrt`의 결과가 완벽히 정확하지 않기 때문에 `sqrt*sqrt`와 `n` 사이에 미세한 차이가 발생 할 수 있음

```golang
func TestSolution2(t *testing.T) {
	fmt.Println(solution2(50000000000000))
}
```


- `solution2`함수에서 `n = 50000000000000` 의 경우 (`solution` 함수에서는 `-1` 을 반환)
	- `sqrt = 7071067.811865475` 값이 나옴
	- 하지만 `sqrt*sqrt`의 결과 `50000000000000` 이 나오게 되어 예외의 경우가 발생하게 됨
	- 그 결과 `50000014142136` 와 같은 값을 반환
- `solution`함수에서 `n = 50000000000000` 의 경우
	- `sqrt = 7071067`
	- `sqrt*sqrt = 49999988518489` 이 값은 n 과 같지 않으므로
	- 그 결과 `-1` 을 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	n      = [TESTCOUNT]int64{121, 3}
	result = [TESTCOUNT]int64{144, -1}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		v := solution(n[i])
		if v != result[i] {
			t.Errorf("[testcase %d failure] result : %d solution result : %d", i, result[i], v)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/perfect_square_check  0.475s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/perfect_square_check)