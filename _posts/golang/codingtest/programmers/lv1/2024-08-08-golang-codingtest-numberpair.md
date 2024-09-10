---
title: "[Golang] 프로그래머스 Lv.1 숫자 짝꿍"
description: 숫자 짝꿍 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-08 12:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
두 정수 `X`, `Y`의 임의의 자리에서 공통으로 나타나는 정수 k(0 ≤ k ≤ 9)들을 이용하여 만들 수 있는 가장 큰 정수를 두 수의 짝꿍이라 합니다(단, 공통으로 나타나는 정수 중 서로 짝지을 수 있는 숫자만 사용합니다). `X`, `Y`의 짝꿍이 존재하지 않으면, 짝꿍은 -1입니다. `X`, `Y`의 짝꿍이 0으로만 구성되어 있다면, 짝꿍은 0입니다.

예를 들어, `X` = 3403이고 `Y` = 13203이라면, `X`와 `Y`의 짝꿍은 `X`와 `Y`에서 공통으로 나타나는 3, 0, 3으로 만들 수 있는 가장 큰 정수인 330입니다. 다른 예시로 `X` = 5525이고 `Y` = 1255이면 `X`와 `Y`의 짝꿍은 `X`와 `Y`에서 공통으로 나타나는 2, 5, 5로 만들 수 있는 가장 큰 정수인 552입니다(`X`에는 5가 3개, `Y`에는 5가 2개 나타나므로 남는 5 한 개는 짝 지을 수 없습니다.)
두 정수 `X`, `Y`가 주어졌을 때, `X`, `Y`의 짝꿍을 return하는 solution 함수를 완성해주세요.


## 제한사항
- 3 ≤ `X`, `Y`의 길이(자릿수) ≤ 3,000,000입니다.
- `X`, `Y`는 0으로 시작하지 않습니다.
- `X`, `Y`의 짝꿍은 상당히 큰 정수일 수 있으므로, 문자열로 반환합니다.


## 입출력 예
{% raw %}
| X | Y | result |
| --- | --- | --- |
| "100" | "2345" | "-1" |
| "100" | "203045" | "0" |
| "100" | "123450" | "10" |
| "12321" | "42531" | "321" |
| "5525" | "1255" | "552" |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- `X`, `Y`의 짝꿍은 존재하지 않습니다. 따라서 "-1"을 return합니다.


**입출력 예 #2**

- `X`, `Y`의 공통된 숫자는 0으로만 구성되어 있기 때문에, 두 수의 짝꿍은 정수 0입니다. 따라서 "0"을 return합니다.


**입출력 예 #3**

- `X`, `Y`의 짝꿍은 10이므로, "10"을 return합니다.


**입출력 예 #4**

- `X`, `Y`의 짝꿍은 321입니다. 따라서 "321"을 return합니다.


**입출력 예 #5**

- 지문에 설명된 예시와 같습니다.



## 풀이 
```golang
func solution(X string, Y string) string {

	var xArr *[10]int = CharacterFrequencyArr(X)
	var yArr *[10]int = CharacterFrequencyArr(Y)

	var sb strings.Builder
	for i := 9; i >= 0; i-- {

		if xArr[i] <= 0 || yArr[i] <= 0 {
			continue
		}

		var min int = 3000001

		if xArr[i] < min {
			min = xArr[i]
		}

		if yArr[i] < min {
			min = yArr[i]
		}

		if i == 0 && sb.String() == "" && min != 0 {
			return "0"
		}

		for j := 0; j < min; j++ {
			fmt.Fprintf(&sb, "%d", i)
		}

	}

	if sb.String() == "" {
		return "-1"
	}

	return sb.String()
}

func CharacterFrequencyArr(s string) *[10]int {
	var arr [10]int

	for _, c := range s {
		v, _ := strconv.Atoi(string(c))
		arr[v]++
	}

	return &arr
}
```

### 위 문제의 주요 포인트
1. X * Y 횟수 순회를 할 경우, 너무 많은 반복으로 인해 실패 위험이 있습니다.
2. 내림차순으로 9 ~ 0 까지 순회하지 않은 경우 별도로 정렬을 수행해야 할 수 있으므로 실패 위험이 있습니다.
3. 9 ~ 0 순회 중, 결과 값이 ""인 상태에서 0 값이 반복적으로 나올 때, return 처리가 없으면 실패 위험이 있습니다.

**이번 문제의 경우 문제를 더욱 꼼꼼히 읽어보아야 합니다**


- `CharacterFrequencyArr` 함수를 이용하여 문자열에서 각 숫자가 몇 번 등장했는지 저장하는 배열
	- 문제에서 `두 정수 X, Y의 임의의 자리에서 공통으로 나타나는 정수 k(0 ≤ k ≤ 9)`라고 하였으므로, **쿠기가 10 인 배열을 생성**하고 숫자 0부터 9까지의 빈도를 저장
	- 문자열 `s` 를 한 글자씩 순회하며, 해당 문자를 숫자로 변환한 후 해당 숫자의 빈도를 증가시킴
- 결과 문자열 생성
	- 결과 문자열을 만들기 위해 strings.Builder를 선언하고, **숫자를 내림차순으로 처리하기 위해 9부터 0까지 반복**
	- `X` 와 `Y`에서 모두 등장하는 숫자가 없는경우 다음 숫자로 넘어감
	- `X` 와 `Y` 가 값을 갖는 경우, 최소 빈도를 계산
	- **특수 케이스인 반복된 "0" 처리 (`i == 0 && sb.String() == "" && min != 0`)**
		- 만약 이 조건이 없다면, 0의 빈도가 매우 높다고 가정했을 때, 불필요한 반복된 "0000..." 을 만들기 위해 시간이 소요됨
	- 최소 빈도 수 만큼 결과 문자열에 `i` 를 추가
- 빈 문자열 처리 `sb.String() == ""` 인 경우 `"-1"` 반환


**테스트 결과에 실패한다면, 굵게 표시된 부분에 대해 조금 더 고려해보세요.**



## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 5

var (
	X = [TESTCOUNT]string{
		"100",
		"100",
		"100",
		"12321",
		"5525",
	}

	Y = [TESTCOUNT]string{
		"2345",
		"203045",
		"123450",
		"42531",
		"1255",
	}
	result = [TESTCOUNT]string{
		"-1",
		"0",
		"10",
		"321",
		"552",
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("X[i] : ", X[i])
		fmt.Println("Y[i] : ", Y[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("solution result : ", solution(X[i], Y[i]))
	}
}

func TestCharacterFrequencyArr(t *testing.T) {
	result := []int{
		1, 3, 1, 1, 1, 1, 0, 0, 1, 1,
	}
	fmt.Println(CharacterFrequencyArr("1231541980"))

	for i, v := range CharacterFrequencyArr("1231541980") {
		fmt.Printf("[%d] expected value : %d, function value : %d\n", i, result[i], v)
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
X[i] :  100
Y[i] :  2345
result[i] :  -1
solution result :  -1
X[i] :  100
Y[i] :  203045
result[i] :  0
solution result :  0
X[i] :  100
Y[i] :  123450
result[i] :  10
solution result :  10
X[i] :  12321
Y[i] :  42531
result[i] :  321
solution result :  321
X[i] :  5525
Y[i] :  1255
result[i] :  552
solution result :  552
--- PASS: TestSolution (0.03s)
=== RUN   TestCharacterFrequencyArr
&[1 3 1 1 1 1 0 0 1 1]
[0] expected value : 1, function value : 1
[1] expected value : 3, function value : 3
[2] expected value : 1, function value : 1
[3] expected value : 1, function value : 1
[4] expected value : 1, function value : 1
[5] expected value : 1, function value : 1
[6] expected value : 0, function value : 0
[7] expected value : 0, function value : 0
[8] expected value : 1, function value : 1
[9] expected value : 1, function value : 1
--- PASS: TestCharacterFrequencyArr (0.03s)
PASS
ok      golang-coding-test/Lv1/number_pair      0.301s
```

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/number_pair)