---
title: "[Golang] 프로그래머스 Lv.1 크기가 작은 부분문자열"
description: 크기가 작은 부분문자열 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-30 21:11:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
숫자로 이루어진 문자열 `t`와 `p`가 주어질 때, `t`에서 `p`와 길이가 같은 부분문자열 중에서, 이 부분문자열이 나타내는 수가 `p`가 나타내는 수보다 작거나 같은 것이 나오는 횟수를 return하는 함수 solution을 완성하세요.

예를 들어, `t`="3141592"이고 `p`="271" 인 경우, `t`의 길이가 3인 부분 문자열은 314, 141, 415, 159, 592입니다. 이 문자열이 나타내는 수 중 271보다 작거나 같은 수는 141, 159 2개 입니다.

## 제한사항
- 1 ≤ `p`의 길이 ≤ 18
- `p`의 길이 ≤ `t`의 길이 ≤ 10,000
- `t`와 `p`는 숫자로만 이루어진 문자열이며, 0으로 시작하지 않습니다.

## 입출력 예
{% raw %}
|t|p|result|
|-|-|-|
|"3141592"|"271"|2|
|"500220839878"|"7"|8|
|"10203"|"15"|3|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

본문과 같습니다.

**입출력 예 #2**

`p`의 길이가 1이므로 `t`의 부분문자열은 "5", "0", 0", "2", "2", "0", "8", "3", "9", "8", "7", "8"이며 이중 7보다 작거나 같은 숫자는 "5", "0", "0", "2", "2", "0", "3", "7" 이렇게 8개가 있습니다.

**입출력 예 #3**

`p`의 길이가 2이므로 `t`의 부분문자열은 "10", "02", "20", "03"이며, 이중 15보다 작거나 같은 숫자는 "10", "02", "03" 이렇게 3개입니다. "02"와 "03"은 각각 2, 3에 해당한다는 점에 주의하세요

## 풀이 
```golang
func solution(t string, p string) int {
	plen := len(p)
	tlen := len(t)

	pValue, err := strconv.Atoi(p)
	if err != nil {
		return -1
	}

	var count int
	for i := 0; i < tlen-plen+1; i++ {
		tPartialValue, err := strconv.Atoi(t[i : i+plen])
		if err != nil {
			return -1
		}

		if tPartialValue <= pValue {
			count++
		}
	}
	return count
}
```
**참고** : strconv.Atoi 함수를 이용하여 문자열을 정수로 변환하지 않고, 문자열 비교를 하여도 결과는 동일합니다.

1. `t` 와 `p` 의 길이를 계산
	- 문자열의 길이를 미리 계산해두면, 루프의 각 반복에서 len 함수를 호출하지 않아도 됨. 이는 불필요한 함수 호출을 피할 수 있어 성능상 이점이 있음
2. 문자열 `p`를 정수로 변환
3. 부분 문자열 비교를 위한 루프 실행
	- 문자열 `t`에서 길이가 `plen`인 부분 문자열을 반복적으로 추출하기 위해 루프를 실행
	- `i`는 부분 문자열의 시작 인덱스
4. 부분 문자열을 정수로 변환 및 비교

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	const TESTCOUNT = 3
	tTestcases := [TESTCOUNT]string{
		"3141592",
		"500220839878",
		"10203",
	}

	pTestcases := [TESTCOUNT]string{
		"271",
		"7",
		"15",
	}

	successResults := [TESTCOUNT]int{
		2,
		8,
		3,
	}

	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("t : ", tTestcases[i])
		fmt.Println("p : ", pTestcases[i])
		fmt.Println("success result : ", successResults[i])
		fmt.Println("solution() result : ", solution(tTestcases[i], pTestcases[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
t :  3141592       
p :  271
success result :  2
solution() result :  2
t :  500220839878
p :  7
success result :  8
solution() result :  8
t :  10203
p :  15
success result :  3
solution() result :  3
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/small_partial_string     0.167s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/small_partial_string)