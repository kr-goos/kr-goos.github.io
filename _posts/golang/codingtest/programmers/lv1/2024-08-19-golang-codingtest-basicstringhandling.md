---
title: "[Golang] 프로그래머스 Lv.1 문자열 다루기 기본"
description: 문자열 다루기 기본 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 08:50:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
문자열 s의 길이가 4 혹은 6이고, 숫자로만 구성돼있는지 확인해주는 함수, solution을 완성하세요. 예를 들어 s가 "a234"이면 False를 리턴하고 "1234"라면 True를 리턴하면 됩니다.


## 제한사항
- `s`는 길이 1 이상, 길이 8 이하인 문자열입니다.
- `s`는 영문 알파벳 대소문자 또는 0부터 9까지 숫자로 이루어져 있습니다.


## 입출력 예
{% raw %}
| s | return |
| --- | --- |
| "a234" | false |
| "1234" | true |
{% endraw %}


## 풀이 
```golang
func solution(s string) bool {

	l := len(s)
	if l != 4 && l != 6 {
		return false
	}

	_, err := strconv.Atoi(s)
	return err == nil
}
```

- `len` 함수를 반복 호출 하지 않기 위해 `l` 변수에 `len(s)` 저장
- `l` 이 4 또는 6 이 아닌경우 `false` 반환
- 문자열 `s` 가 integer 로 변환할 수 있는 경우 `err == nil` 이므로 `err == nil` 을 반환

ㄴ
## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	s = [TESTCOUNT]string{
		"a234",
		"1234",
	}
	result = [TESTCOUNT]bool{
		false,
		true,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("s : ", s[i])
		fmt.Println("result : ", result[i])
		answer := solution(s[i])
		fmt.Println("solution result : ", answer)
		if answer != result[i] {
			t.Errorf("[testcase %d] failure", i)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
s :  a234
result :  false
solution result :  false
s :  1234
result :  true
solution result :  true
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/basic_string_handling    0.230s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/basic_string_handling)