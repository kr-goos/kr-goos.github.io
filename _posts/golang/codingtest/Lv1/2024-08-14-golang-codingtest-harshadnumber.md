---
title: "[Golang] 프로그래머스 Lv.1 하샤드 수"
description: 하샤드 수 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-14 07:45:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
양의 정수 `x`가 하샤드 수이려면 `x`의 자릿수의 합으로 `x`가 나누어져야 합니다. 예를 들어 18의 자릿수 합은 1+8=9이고, 18은 9로 나누어 떨어지므로 18은 하샤드 수입니다. 자연수 `x`를 입력받아 `x`가 하샤드 수인지 아닌지 검사하는 함수, solution을 완성해주세요.


## 제한사항
- `x`는 1 이상, 10000 이하인 정수입니다.


## 입출력 예
{% raw %}
| x | return |
| --- | --- |
| 10 | true |
| 12 | true |
| 11 | false |
| 13 | false |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 10의 모든 자릿수의 합은 1입니다. 10은 1로 나누어 떨어지므로 10은 하샤드 수입니다.

**입출력 예 #2**

- 12의 모든 자릿수의 합은 3입니다. 12는 3으로 나누어 떨어지므로 12는 하샤드 수입니다.

**입출력 예 #3**

- 11의 모든 자릿수의 합은 2입니다. 11은 2로 나누어 떨어지지 않으므로 11는 하샤드 수가 아닙니다.

**입출력 예 #4**

- 13의 모든 자릿수의 합은 4입니다. 13은 4로 나누어 떨어지지 않으므로 13은 하샤드 수가 아닙니다.

## 풀이 
### 풀이방법 1
```golang
func solution(x int) bool {
	s := strconv.Itoa(x)
	var total int
	for i := 0; i < len(s); i++ {
		v, err := strconv.Atoi(string(s[i]))
		if err != nil {
			return false
		}
		total += v
	}

	return x%total == 0
}
```
- 정수 `x` 를 문자열로 변환
- 문자열의 한 글자씩 순회
	- 문자열의 각 요소 `byte` 값을 문자열로 변환하고, 문자열을 정수로 다시 변환
	- `v`값을 `total` 에 더함
- 정수 `x` 를 `total`로 나눈 나머지가 0 이라면 `true` 아니라면 `false`

### 풀이방법 2
```golang
func solution2(x int) bool {
	temp := x
	var total int
	for temp > 0 {
		total += temp % 10
		temp /= 10
	}

	return x%total == 0
}
```
- 변수 초기화
	- `temp`에 `x`값을 복사
	- 각 자리수의 합계를 저장할 `total` 선언
- `temp` 값이 0보다 크다면 순회
	- `temp`를 10으로 나눈 나머지를 `total`에 더함
		- ex) 8432 % 10 = 2 와 같이 마지막 자리 값을 구함
	- `temp`를 10으로 나눈 몫을 `temp`에 저장
		- ex) 8432 / 10 = 843
	- 위 과정을 `temp` 가 0 보다 같거나 작아질 때 까지 반복하여 `total` 값을 얻어냄
- 정수 `x` 를 `total`로 나눈 나머지가 0 이라면 `true` 아니라면 `false`


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
x : 10
result : true
solution result : true
x : 12
result : true
solution result : true
x : 11
result : false
solution result : false
x : 13
result : false
solution result : false
--- PASS: TestSolution (0.01s)
=== RUN   TestSolution2
x : 10
result : true
solution result : true
x : 12
result : true
solution result : true
x : 11
result : false
solution result : false
x : 13
result : false
solution result : false
--- PASS: TestSolution2 (0.01s)
PASS
ok      golang-coding-test/Lv1/harshad_number   0.153s
```


### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(x[0])
	}
}

func BenchmarkSolution2(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution2(x[0])
	}
}
```

```bash
$ go test -bench . -run ^$
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/harshad_number
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4     47508194                23.14 ns/op
BenchmarkSolution2-4    455579134                2.598 ns/op
PASS
ok      golang-coding-test/Lv1/harshad_number   2.717s
```

- 자료형을 계속 변환해주는 1번 방법보다, 2번 방법이 훨씬 빠른 것을 확인할 수 있습니다.

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/harshad_number)