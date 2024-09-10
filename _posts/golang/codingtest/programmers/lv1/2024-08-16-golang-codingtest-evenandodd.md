---
title: "[Golang] 프로그래머스 Lv.1 짝수와 홀수"
description: 짝수와 홀수 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-16 07:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
정수 num이 짝수일 경우 "Even"을 반환하고 홀수인 경우 "Odd"를 반환하는 함수, solution을 완성해주세요.


## 제한사항
- num은 int 범위의 정수입니다.
- 0은 짝수입니다.


## 입출력 예
{% raw %}
| num | return |
| --- | --- |
| 3 | "Odd" |
| 4 | "Even" |
{% endraw %}


## 풀이 
```golang
const (
	EVEN = "Even"
	ODD  = "Odd"
)

func solution(num int) string {
	if num%2 == 0 {
		return EVEN
	}
	return ODD
}
```


## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	result := []string{"Odd", "Even"}

	for i, v := range []int{3, 4} {
		if result[i] != solution(v) {
			t.Errorf("test %d failure", i)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/even_and_odd     0.389s
```

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/even_and_odd)