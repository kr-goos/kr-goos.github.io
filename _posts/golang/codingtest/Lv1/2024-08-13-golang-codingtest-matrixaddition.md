---
title: "[Golang] 프로그래머스 Lv.1 행렬의 덧셈"
description: 행렬의 덧셈 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 12:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
행렬의 덧셈은 행과 열의 크기가 같은 두 행렬의 같은 행, 같은 열의 값을 서로 더한 결과가 됩니다. 2개의 행렬 arr1과 arr2를 입력받아, 행렬 덧셈의 결과를 반환하는 함수, solution을 완성해주세요.


## 제한사항
- 행렬 arr1, arr2의 행과 열의 길이는 500을 넘지 않습니다.


## 입출력 예
{% raw %}
| arr1 | arr2 | return |
| --- | --- | --- |
| \[\[1,2\],\[2,3\]\] | \[\[3,4\],\[5,6\]\] | \[\[4,6\],\[7,9\]\] |
| \[\[1\],\[2\]\] | \[\[3\],\[4\]\] | \[\[4\],\[6\]\] |
{% endraw %}


## 풀이 
```golang
func solution(arr1 [][]int, arr2 [][]int) [][]int {

	var answer [][]int

	answer = make([][]int, len(arr1))
	for i := range arr1 {
		answer[i] = make([]int, len(arr1[i]))
		for j := range arr1[i] {
			answer[i][j] = arr1[i][j] + arr2[i][j]
		}
	}
	return answer
}
```

-  문제의 조건 : **행과 열의 크기가 같은 두 행렬(`arr1` , `arr2`)**
- `answer`를 arr1 의 크기로 초기화
- `arr1`를 순회하며 `answer[i]`를 `arr[i]`의 크기만큼 초기화
	- 각 열을 순회 (`arr1[i]`)
		- `arr1[i][j]` 과 `arr2[i][j]`를 합한 값을 `answer[i][j]` 에 저장
- `answer` 반환


## 테스트코드
### 유닛 테스트
{% raw %}
```golang
const TESTCOUNT = 2

var (
	arr1 = [TESTCOUNT][][]int{
		{{1, 2}, {2, 3}},
		{{1}, {2}},
	}
	arr2 = [TESTCOUNT][][]int{
		{{3, 4}, {5, 6}},
		{{3}, {4}},
	}
	result = [TESTCOUNT][][]int{
		{{4, 6}, {7, 9}},
		{{4}, {6}},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("arr1 : %v\n", arr1[i])
		fmt.Printf("arr2 : %v\n", arr2[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(arr1[i], arr2[i]))
	}
}
```
{% endraw %}

```bash
$ go test -v
=== RUN   TestSolution
arr1 : [[1 2] [2 3]]
arr2 : [[3 4] [5 6]]
result : [[4 6] [7 9]]
solution result : [[4 6] [7 9]]
arr1 : [[1] [2]]
arr2 : [[3] [4]]
result : [[4] [6]]
solution result : [[4] [6]]
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/matrix_addition  0.800s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/matrix_addition)