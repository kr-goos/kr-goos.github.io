---
title: "[Golang] 프로그래머스 Lv.1 나누어 떨어지는 숫자 배열"
description: 나누어 떨어지는 숫자 배열 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 21:00:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
array의 각 element 중 divisor로 나누어 떨어지는 값을 오름차순으로 정렬한 배열을 반환하는 함수, solution을 작성해주세요.
divisor로 나누어 떨어지는 element가 하나도 없다면 배열에 -1을 담아 반환하세요.


## 제한사항
- arr은 자연수를 담은 배열입니다.
- 정수 i, j에 대해 i ≠ j 이면 arr[i] ≠ arr[j] 입니다.
- divisor는 자연수입니다.
- array는 길이 1 이상인 배열입니다.


## 입출력 예
{% raw %}
|arr|divisor|return|
|-|-|-|
|[5, 9, 7, 10]|5|[5, 10]|
|[2, 36, 1, 3]|1|[1, 2, 3, 36]|
|[3,2,6]|10|[-1]|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- arr의 원소 중 5로 나누어 떨어지는 원소는 5와 10입니다. 따라서 [5, 10]을 리턴합니다.

**입출력 예 #2**

- arr의 모든 원소는 1으로 나누어 떨어집니다. 원소를 오름차순으로 정렬해 [1, 2, 3, 36]을 리턴합니다.

**입출력 예 #3**

- 3, 2, 6은 10으로 나누어 떨어지지 않습니다. 나누어 떨어지는 원소가 없으므로 [-1]을 리턴합니다.

## 풀이 
```golang
func solution(arr []int, divisor int) []int {

	sort.Slice(arr, func(i, j int) bool {
		return arr[i] < arr[j]
	})

	var answer []int
	for _, element := range arr {
		if element%divisor == 0 {
			answer = append(answer, element)
		}
	}

	if answer == nil {
		return []int{-1}
	}

	return answer
}
```

- `arr` 을 오름차순 정렬
- `answer` 선언
- `arr` 순회
	- `element` 와 `divisor` 를 나눈 나머지가 0 이라면, answer 에 추가
- `answer` 가 `nil` (선언 후 사용되지 않은 상태)이라면 `[]int{-1}` 반환
- `answer` 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	arr = [TESTCOUNT][]int{
		{5, 9, 7, 10},
		{2, 36, 1, 3},
		{3, 2, 6},
	}
	divisor = [TESTCOUNT]int{5, 1, 10}
	result  = [TESTCOUNT][]int{
		{5, 10},
		{1, 2, 3, 36},
		{-1},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("arr : ", arr[i])
		fmt.Println("divisor : ", divisor[i])
		fmt.Println("result : ", result[i])
		fmt.Println("solution result : ", solution(arr[i], divisor[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
arr :  [5 9 7 10]
divisor :  5
result :  [5 10]
solution result :  [5 10]
arr :  [2 36 1 3]
divisor :  1
result :  [1 2 3 36]
solution result :  [1 2 3 36]
arr :  [3 2 6]
divisor :  10
result :  [-1]
solution result :  [-1]
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/divisible_numbers        0.145s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/divisible_numbers)