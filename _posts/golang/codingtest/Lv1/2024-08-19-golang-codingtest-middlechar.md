---
title: "[Golang] 프로그래머스 Lv.1 가운데 글자 가져오기"
description: 가운데 글자 가져오기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 21:10:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
단어 s의 가운데 글자를 반환하는 함수, solution을 만들어 보세요. 단어의 길이가 짝수라면 가운데 두글자를 반환하면 됩니다.


## 제한사항
- s는 길이가 1 이상, 100이하인 스트링입니다.


## 입출력 예
{% raw %}
|s|return|
|-|-|
|"abcde"|"c"|
|"qwer"|"we"|
{% endraw %}


## 풀이 
```golang
func solution(s string) string {
	length := len(s)
	if length == 1 {
		return s
	}

	middle := length / 2
	if length%2 == 0 {
		return string(s[middle-1 : middle+1])
	}

	return string(s[middle])
}
```

- `length` 가 1 이라면 문자열 `s` 를 바로 반환
- `length` 를 2로 나눈 나머지가 0 이라면, 문자열 길이가 짝수이므로 `s[middle-1 : middle+1]` s를 슬라이싱하여 가운데 두 글자를 반환
- `length` 가 홀수라면 가운데 글자 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	s = [TESTCOUNT]string{
		"abcde",
		"qwer",
	}

	result = [TESTCOUNT]string{
		"c",
		"we",
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("s : ", s[i])
		fmt.Println("result : ", result[i])
		fmt.Println("solution result : ", solution(s[i]))
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
s :  abcde
result :  c
solution result :  c
s :  qwer
result :  we
solution result :  we
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/middle_char      0.139s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/middle_char)