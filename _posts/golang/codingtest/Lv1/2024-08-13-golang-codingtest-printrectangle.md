---
title: "[Golang] 프로그래머스 Lv.1 직사각형 별찍기"
description: 직사각형 별찍기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 08:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
이 문제에는 표준 입력으로 두 개의 정수 n과 m이 주어집니다.
별(*) 문자를 이용해 가로의 길이가 n, 세로의 길이가 m인 직사각형 형태를 출력해보세요.


## 제한사항
- n과 m은 각각 1000 이하인 자연수입니다.


## 입출력 예 설명
**입력**

```
5 3
```

**출력 **

```
*****
*****
*****
```

## 풀이 
```golang
func main() {
	var a, b int
	fmt.Scan(&a, &b)

	for i := 0; i < b; i++ {
		for j := 0; j < a; j++ {
			fmt.Print("*")
		}
		fmt.Println()
	}
}
```


## 테스트
```bash
go run solution.go
5 3
```

```bash
*****
*****
*****
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/print_rectangle)