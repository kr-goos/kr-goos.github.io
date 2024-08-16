---
title: "[Golang] 프로그래머스 Lv.1 제일 작은 수 제거하기"
description: 제일 작은 수 제거하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-16 07:50:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
정수를 저장한 배열, arr 에서 가장 작은 수를 제거한 배열을 리턴하는 함수, solution을 완성해주세요. 단, 리턴하려는 배열이 빈 배열인 경우엔 배열에 -1을 채워 리턴하세요. 예를들어 arr이 [4,3,2,1]인 경우는 [4,3,2]를 리턴 하고, [10]면 [-1]을 리턴 합니다.


## 제한사항
- arr은 길이 1 이상인 배열입니다.
- 인덱스 i, j에 대해 i ≠ j이면 arr[i] ≠ arr[j] 입니다.


## 입출력 예
{% raw %}
| arr | return |
| --- | --- |
| \[4,3,2,1\] | \[4,3,2\] |
| \[10\] | \[-1\] |
{% endraw %}


## 풀이 
```golang
func solution(arr []int) []int {

	if len(arr) == 0 || len(arr) == 1 {
		return []int{-1}
	}

	min := arr[0]
	for _, v := range arr {
		if v < min {
			min = v
		}
	}

	for i, v := range arr {
		if v == min {
			arr = append(arr[:i], arr[i+1:]...)
		}
	}

	return arr
}
```

- `arr` 의 길이가 0 이거나 1 인 경우 -1 을 가진 배열을 반환
- `arr` 을 순회하며 min 값을 구함
- `arr` 을 다시 순회하며, 최소값과 일치하는 index 를 알아내고, 해당 인덱스를 제거한 두 슬라이스를 합쳐 `arr` 에 대입

### TMI
만약 최솟값을 구할 때, `arr`을 내림차순 정렬하고, 마지막 요소를 가져오는 코드와 현재 코드 중 무엇이 얼마나 빠를까?

```golang
func solution2(arr []int) []int {

	if len(arr) == 0 || len(arr) == 1 {
		return []int{-1}
	}

	temp := make([]int, len(arr))
	copy(temp, arr)

	sort.Slice(temp, func(i, j int) bool {
		return temp[i] > temp[j]
	})

	for i, v := range arr {
		if v == temp[len(temp)-1] {
			arr = append(arr[:i], arr[i+1:]...)
			break
		}

	}

	return arr
}
```

- `temp` 변수를 `arr`의 `len`만큼 초기화
	- `copy` 함수를 이용하여 `arr` 의 내용 복사
- `temp` 내림차순 정렬
- `arr` 을 순회하며 `temp` 의 마지막 요소(최솟값)와 현재 요소가 일치하는지 확인
	- 일치한다면, 현재 인덱스의 값을 제거한 슬라이스를 arr 에 저장
	- 일치하지 않는다면 다음 요소 순회


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	arr = [TESTCOUNT][]int{
		{4, 3, 2, 1},
		{10},
	}

	result = [TESTCOUNT][]int{
		{4, 3, 2},
		{-1},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("arr : %v\n", arr[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(arr[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
arr : [4 3 2 1]
result : [4 3 2]
solution result : [4 3 2]     
arr : [10]
result : [-1]
solution result : [-1]        
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/remove_smallest_number   0.243s
```


### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(arr[0])
	}
}

func BenchmarkSolution2(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution2(arr[0])
	}
}
```

```bash
$ go test -bench . -run ^$
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/remove_smallest_number
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4     99036048                10.93 ns/op
BenchmarkSolution2-4     4663252               264.2 ns/op
PASS
ok      golang-coding-test/Lv1/remove_smallest_number   3.064s
```

- 배열의 요소의 개수가 많은 테스트케이스에는 다른 성능을 보일 수 있지만, 현재 테스트케이스에서는 `solution` 함수가 월등히 빠른 것을 확인할 수 있습니다.


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/remove_smallest_number)