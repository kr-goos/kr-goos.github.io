---
title: "[Golang] 프로그래머스 Lv.1 문자열 내림차순으로 배치하기"
description: 문자열 내림차순으로 배치하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 20:00:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
문자열 s에 나타나는 문자를 큰것부터 작은 순으로 정렬해 새로운 문자열을 리턴하는 함수, solution을 완성해주세요.
s는 영문 대소문자로만 구성되어 있으며, 대문자는 소문자보다 작은 것으로 간주합니다.


## 제한사항
- str은 길이 1 이상인 문자열입니다.


## 입출력 예
{% raw %}
| s | return |
| --- | --- |
| "Zbcdefg" | "gfedcbZ" |
{% endraw %}



## 풀이 
```golang
func solution(s string) string {
	b := []byte(s)
	for i := 0; i < len(s); i++ {
		for j := i + 1; j < len(s); j++ {
			if b[i] < b[j] {
				b[i], b[j] = b[j], b[i]
			}
		}
	}
	return string(b)
}
```

### 선택 정렬 알고리즘을 이용한 풀이
- 문자열을 `[]byte` 로 변환 후 `b` 에 저장
- `s` 의 길이 만큼 순회
	-	`s` 의 요소 (기준값) 과, 그 다음 인덱스값부터 끝까지 순회
		- 만약 `s` 의 요소 (`b[i]`) 가 `b[j]` 보다 작다면 `b[i]` 와 `b[j]` 를 스왑 (가장 큰 수가 앞에 나오도록)
	- 위 과정을 s 문자열 길이만큼 반복하여 큰 값들을 배열의 앞부터 하나 씩 구해나감
- `b` 를 문자열로 변환하고 반환

### 선택정렬이란?
- 정렬되지 않은 리스트에서 가장 큰 (혹은 작은) 값을 찾아내어 정렬된 리스트의 끝에 위치시키는 방식을 반복하여 리스트를 정렬하는 알고리즘
- 시간 복잡도는 일반적으로 O(n*n)

### sort 패키지를 이용한 문제풀이 방법

```golang
func simpleSolution(s string) string {
	b := []byte(s)
	sort.Slice(b, func(i, j int) bool { return b[i] > b[j] })
	return string(b)
}
```


## 테스트코드
### 유닛 테스트
```golang
var (
	s      = "Zbcdefg"
	result = "gfedcbZ"
)

func TestSolution(t *testing.T) {
	answer := solution(s)
	if answer != result {
		t.Errorf("result %s != solution result %s", answer, result)
	}
}

func TestSimpleSolution(t *testing.T) {
	answer := simpleSolution(s)
	if answer != result {
		t.Errorf("result %s != solution result %s", answer, result)
	}
}
```

```bash
$ go test -v
=== RUN   TestSimpleSolution
--- PASS: TestSimpleSolution (0.00s)
PASS
ok      golang-coding-test/lv1/sort_string_descending   0.153s
```

### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(s)
	}
}

func BenchmarkSimpleSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		simpleSolution(s)
	}
}
```

```bash
$ go test -bench . -run ^$
goos: windows
goarch: amd64
pkg: golang-coding-test/lv1/sort_string_descending
cpu: Intel(R) Pentium(R) Gold G5400 CPU @ 3.70GHz
BenchmarkSolution-4             38493555                31.05 ns/op    
BenchmarkSimpleSolution-4        4789263               248.7 ns/op     
PASS
ok      golang-coding-test/lv1/sort_string_descending   3.756s
```

- 선택정렬 알고리즘으로 구현한 코드와 sort 패키지의 Slice 함수를 비교해 보았습니다.



## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sort_string_descending)