---
title: "[Golang] 프로그래머스 Lv.1 자릿수 더하기"
description: 자릿수 더하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-18 20:33:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
자연수 N이 주어지면, N의 각 자릿수의 합을 구해서 return 하는 solution 함수를 만들어 주세요.
예를들어 N = 123이면 1 + 2 + 3 = 6을 return 하면 됩니다.


## 제한사항
- N의 범위 : 100,000,000 이하의 자연수


## 입출력 예
{% raw %}
| N | answer |
| --- | --- |
| 123 | 6 |
| 987 | 24 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 문제의 예시와 같습니다.

**입출력 예 #2**

- 9 + 8 + 7 = 24이므로 24를 return 하면 됩니다.

## 풀이 
```golang
func solution(n int) int {
	answer := 0

	for n > 0 {
		answer += n % 10
		n /= 10
	}

	return answer
}
```
- `n` 의 값을 10 으로 나눈 나머지 `answer` 에 더함
- `n` 을 10으로 나눈 몫을 `n` 에 저장
- `n > 0` 조건을 충족하는 동안 순회
- `answer` 반환

## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	N = [TESTCOUNT]int{
		123,
		987,
	}
	answer = [TESTCOUNT]int{
		6,
		24,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("N : ", N[i])
		fmt.Println("answer : ", answer[i])
		r := solution(N[i])
		fmt.Println("solution result : ", r)

		if r != answer[i] {
			t.Errorf("testcase [%d] failure", i)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
N :  123
answer :  6
solution result :  6
N :  987
answer :  24
solution result :  24
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/sum_digits       0.163s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sum_digits)