---
title: "[Golang] 프로그래머스 Lv.1 삼총사"
description: 삼총사 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-08 08:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
한국중학교에 다니는 학생들은 각자 정수 번호를 갖고 있습니다. 이 학교 학생 3명의 정수 번호를 더했을 때 0이 되면 3명의 학생은 삼총사라고 합니다. 예를 들어, 5명의 학생이 있고, 각각의 정수 번호가 순서대로 -2, 3, 0, 2, -5일 때, 첫 번째, 세 번째, 네 번째 학생의 정수 번호를 더하면 0이므로 세 학생은 삼총사입니다. 또한, 두 번째, 네 번째, 다섯 번째 학생의 정수 번호를 더해도 0이므로 세 학생도 삼총사입니다. 따라서 이 경우 한국중학교에서는 두 가지 방법으로 삼총사를 만들 수 있습니다.

한국중학교 학생들의 번호를 나타내는 정수 배열 `number`가 매개변수로 주어질 때, 학생들 중 삼총사를 만들 수 있는 방법의 수를 return 하도록 solution 함수를 완성하세요.


## 제한사항
- 3 ≤ `number`의 길이 ≤ 13
- -1,000 ≤ `number`의 각 원소 ≤ 1,000
- 서로 다른 학생의 정수 번호가 같을 수 있습니다.


## 입출력 예
{% raw %}
|number|result|
|-|-|
|[-2, 3, 0, 2, -5]|2|
|[-3, -2, -1, 0, 1, 2, 3]|5|
|[-1, 1, -1, 1]|0|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 문제 예시와 같습니다.


**입출력 예 #2**

- 학생들의 정수 번호 쌍 (-3, 0, 3), (-2, 0, 2), (-1, 0, 1), (-2, -1, 3), (-3, 1, 2) 이 삼총사가 될 수 있으므로, 5를 return 합니다.


**입출력 예 #3**
- 삼총사가 될 수 있는 방법이 없습니다.


## 풀이 
```golang
func solution(number []int) int {
	var count int
	for i := 0; i < len(number); i++ {
		for j := i + 1; j < len(number); j++ {
			for k := j + 1; k < len(number); k++ {
				if number[i]+number[j]+number[k] == 0 {
					count++
				}
			}
		}
	}
	return count
}
```
- 배열의 부분 스캔
	- 중복 방지 : 세 개의 숫자를 선택할 때 동일한 숫자를 중복 선택하지 않기 위함
- 브루트 포스(Brute Force) 알고리즘
	- **삼중 for 루프**를 사용하여 배열 내의 모든 서로 다른 세 개의 원소 조합을 검사
	- 인덱스 조합의 예시
	
	|i|j|k|number[i]|number[j]|number[k]|
	|-|-|-|-|-|-|
	|0|1|2|a|b|c|
	|0|1|3|a|b|d|
	|0|1|4|a|b|e|
	|0|1|5|a|b|f|
	|0|2|3|a|c|d|
	|0|2|4|a|c|e|
	|0|2|5|a|c|f|
	|0|3|4|a|d|e|
	|0|3|5|a|d|f|
	|0|4|5|a|e|f|
	|1|2|3|b|c|d|
	|1|2|4|b|c|e|
	|1|2|5|b|c|f|
	|1|3|4|b|d|e|
	|1|3|5|b|d|f|
	|1|4|5|b|e|f|
	|2|3|4|c|d|e|
	|2|3|5|c|d|f|
	|2|4|5|c|e|f|
	|3|4|5|d|e|f|


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	number = [TESTCOUNT][]int{
		{-2, 3, 0, 2, -5},
		{-3, -2, -1, 0, 1, 2, 3},
		{-1, 1, -1, 1},
	}
	result = [TESTCOUNT]int{2, 5, 0}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("number[i] : ", number[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("solution result : ", solution(number[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
number[i] :  [-2 3 0 2 -5]     
result[i] :  2
solution result :  2
number[i] :  [-3 -2 -1 0 1 2 3]
result[i] :  5
solution result :  5
number[i] :  [-1 1 -1 1]       
result[i] :  0
solution result :  0
--- PASS: TestSolution (0.00s) 
PASS
ok      golang-coding-test/Lv1/three_musketeers 0.217s
```

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/three_musketeers)