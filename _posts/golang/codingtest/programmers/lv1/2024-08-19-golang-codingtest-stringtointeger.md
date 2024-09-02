---
title: "[Golang] 프로그래머스 Lv.1 문자열을 정수로 바꾸기"
description: 문자열을 정수로 바꾸기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 08:10:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
문자열 s를 숫자로 변환한 결과를 반환하는 함수, solution을 완성하세요.


## 제한사항
- s의 길이는 1 이상 5이하입니다.
- s의 맨앞에는 부호(+, -)가 올 수 있습니다.
- s는 부호와 숫자로만 이루어져있습니다.
- s는 "0"으로 시작하지 않습니다.


## 입출력 예
예를들어 str이 "1234"이면 1234를 반환하고, "-1234"이면 -1234를 반환하면 됩니다.
str은 부호(+,-)와 숫자로만 구성되어 있고, 잘못된 값이 입력되는 경우는 없습니다.


## 풀이 
```golang
func solution(s string) int {
	v, _ := strconv.Atoi(s)
	return v
}
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/string_to_integer)