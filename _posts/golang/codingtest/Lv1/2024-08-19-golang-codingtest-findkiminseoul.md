---
title: "[Golang] 프로그래머스 Lv.1 서울에서 김서방 찾기"
description: 서울에서 김서방 찾기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 08:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
String형 배열 seoul의 element중 "Kim"의 위치 x를 찾아, "김서방은 x에 있다"는 String을 반환하는 함수, solution을 완성하세요. seoul에 "Kim"은 오직 한 번만 나타나며 잘못된 값이 입력되는 경우는 없습니다.


## 제한사항
- seoul은 길이 1 이상, 1000 이하인 배열입니다.
- seoul의 원소는 길이 1 이상, 20 이하인 문자열입니다.
- "Kim"은 반드시 seoul 안에 포함되어 있습니다.


## 입출력 예
{% raw %}
| seoul | return |
| --- | --- |
| \["Jane", "Kim"\] | "김서방은 1에 있다" |
{% endraw %}


## 풀이 
```golang
func solution(seoul []string) string {
	var idx int
	for i := range seoul {
		if seoul[i] == "Kim" {
			idx = i
			break
		}
	}
	return fmt.Sprintf("김서방은 %d에 있다", idx)
}
```

- `idx` 선언
- `seoul` 순회
	- `seoul` 의 요소 값이 `"Kim"` 이라면 `idx` 에 현재 인덱스값 `i`를 저장하고 for문을 종료
- `fmt.Sprintf` 함수를 이용하여, 리턴해야 하는 문자열의 `%d` 를 `idx` 로 치환하여 반환


## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	seoul := []string{
		"Jane", "Kim",
	}
	result := "김서방은 1에 있다"

	r := solution(seoul)
	if r != result {
		t.Errorf("result %s != solution result %s", result, r)
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/find_kim_in_seoul        0.291s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/find_kim_in_seoul)