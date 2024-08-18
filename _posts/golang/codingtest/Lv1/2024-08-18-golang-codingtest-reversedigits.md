---
title: "[Golang] 프로그래머스 Lv.1 자연수 뒤집어 배열로 만들기"
description: 자연수 뒤집어 배열로 만들기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-18 20:33:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.


## 제한사항
- n은 10,000,000,000이하인 자연수입니다.


## 입출력 예
{% raw %}
| n | return |
| --- | --- |
| 12345 | [5,4,3,2,1] |
{% endraw %}


## 풀이 
```golang
func solution(n int64) []int {
	var answer []int
	for n > 0 {
		answer = append(answer, int(n%10))
		n /= 10
	}
	return answer
}
```
- n 의 값을 10 으로 나눈 나머지를 배열에 저장
- n 을 10으로 나눈 몫을 n 에 저장
- `n > 0` 조건을 충족하는 동안 순회
- `answer` 반환

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	var n int64 = 12345
	result := []int{5, 4, 3, 2, 1}

	sr := solution(n)
	for i := range result {
		if result[i] != sr[i] {
			t.Errorf("result : %d != solution result : %d", result[i], sr[i])
		}
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/reverse_digits   0.141s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/reverse_digits)