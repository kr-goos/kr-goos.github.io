---
title: "[Golang] 프로그래머스 Lv.1 정수 내림차순으로 배치하기"
description: 정수 내림차순으로 배치하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-16 08:33:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
함수 solution은 정수 n을 매개변수로 입력받습니다. n의 각 자릿수를 큰것부터 작은 순으로 정렬한 새로운 정수를 리턴해주세요. 예를들어 n이 118372면 873211을 리턴하면 됩니다.


## 제한사항
- `n`은 1이상 8000000000 이하인 자연수입니다.


## 입출력 예
{% raw %}
| n | return |
| --- | --- |
| 118372 | 873211 |
{% endraw %}


## 풀이 
```golang
func solution(n int64) int64 {
	s := []byte(strconv.Itoa(int(n)))
	sort.Slice(s, func(i, j int) bool {
		return s[i] > s[j]
	})
	v, _ := strconv.ParseInt(string(s), 10, 64)
	return v
}
```

- n의 형변환
	- `int64` -> `int` -> `string` -> `[]byte`
- `[]byte` 의 값을 가진 `s` 를 내림차순 정렬
- `s` 를 string 으로 변환
- `parseInt` 함수를 이용하여, 10진수로 비트크기 64를 사용하는 `int64` 값을 반환


## 테스트코드
### 유닛 테스트
```golang
var (
	n      int64 = 118372
	result int64 = 873211
)

func TestSolution(t *testing.T) {
	answer := solution(n)
	if answer != result {
		t.Errorf("answer %d != result %d", answer, result)
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/sort_numbers_descending  0.187s
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/sort_numbers_descending)