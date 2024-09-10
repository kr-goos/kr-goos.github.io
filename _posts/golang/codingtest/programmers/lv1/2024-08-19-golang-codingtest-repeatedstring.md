---
title: "[Golang] 프로그래머스 Lv.1 수박수박수박수박수박수?"
description: 수박수박수박수박수박수? 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 08:20:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
길이가 n이고, "수박수박수박수...."와 같은 패턴을 유지하는 문자열을 리턴하는 함수, solution을 완성하세요. 예를들어 n이 4이면 "수박수박"을 리턴하고 3이라면 "수박수"를 리턴하면 됩니다.


## 제한사항
- n은 길이 10,000이하인 자연수입니다.


## 입출력 예
{% raw %}
| n | return |
| --- | --- |
| 3 | "수박수" |
| 4 | "수박수박" |
{% endraw %}


## 풀이 
```golang
func solution(n int) string {
	pattern := []rune("수박")
	var sb strings.Builder
	for i := 0; i < n; i++ {
		sb.WriteRune(pattern[i%len(pattern)])
	}
	return sb.String()
}
```

- **`golang` 에서는 기본적으로 문자열을 `byte`슬라이스로 처리합니다.**
	- UTF-8 인코딩된 바이트의 시퀀스로 표현됨
	- 하지만 한글과 같은 유니코드 문자는 여러 바이트로 이루어져 있기 때문에, `s[0]` 과 같이 접근하게 되면 문자열의 첫 바이트만 반환합니다.
- 이를 해결하기 위해 `rune` 타입을 사용
	- `rune`은 GO에서 유니코드 코드 포인트를 나타내는 타입으로, 멀티바이트 문자를 처리할 수 있음
- 반복되는 문자열 패턴을 `[]rune` 타입으로 초기화 
- `n` 만큼 순회하며 `pattern` 에서 `i % len(pattern)` 값을 인덱스로 찾아 `sb` 에 저장
- `sb.String()` 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	n      = [TESTCOUNT]int{3, 4}
	result = [TESTCOUNT]string{
		"수박수",
		"수박수박",
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("n : ", n[i])
		fmt.Println("result : ", result[i])
		fmt.Println("solution result : ", solution(n[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
n :  3
result :  수박수
solution result :  수박수
n :  4
result :  수박수박
solution result :  수박수박
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/repeated_string  0.298s
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/repeated_string)