---
title: "[Golang] 프로그래머스 Lv.1 2016년"
description: 2016년 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 21:20:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
2016년 1월 1일은 금요일입니다. 2016년 a월 b일은 무슨 요일일까요? 두 수 a ,b를 입력받아 2016년 a월 b일이 무슨 요일인지 리턴하는 함수, solution을 완성하세요. 요일의 이름은 일요일부터 토요일까지 각각 `SUN,MON,TUE,WED,THU,FRI,SAT`

입니다. 예를 들어 a=5, b=24라면 5월 24일은 화요일이므로 문자열 "TUE"를 반환하세요.


## 제한사항
- 2016년은 윤년입니다.
- 2016년 a월 b일은 실제로 있는 날입니다. (13월 26일이나 2월 45일같은 날짜는 주어지지 않습니다)


## 입출력 예
{% raw %}
|a|b|result|
|-|-|-|
|5|24|"TUE"|
{% endraw %}


## 풀이 
```golang
func solution(a int, b int) string {
	m := map[int]string{
		0: "SUN",
		1: "MON",
		2: "TUE",
		3: "WED",
		4: "THU",
		5: "FRI",
		6: "SAT",
	}
	date, err := time.Parse("2006-01-02", fmt.Sprintf("2016-%02d-%02d", a, b))
	if err != nil {
		return ""
	}

	return m[int(date.Weekday())]
}
```

- m 초기화
	- `time` 패키지는 0 을 일요일로 시작하여 1씩 증가한 값을 각 요일로 사용
- `a` 와 `b` 의 값을 이용하여 `"2006-01-02"` 레이아웃에 일치하는 문자열을 생성 `fmt.Sprintf("2016-%02d-%02d", a, b)`
	- `%02d` : `%d` 의 값이 한자리라면 0 을 포함하고, 두자리 값이라면 그대로 사용
- `date.Weekday()` 함수는 `time.Weekday`  를 반환
	- `time` 패키지의 구현
	- ```golang
	  type Weekday int

	  const (
		Sunday Weekday = iota
		Monday
		Tuesday
		Wednesday
		Thursday
		Friday
		Saturday
	  )
  	  ```
- `date.Weekday()` 의 반환값을 int로 변환하고, map 에서 해당 int 키를 가진 값을 반환


## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	a := 5
	b := 24
	result := "TUE"
	r := solution(a, b)
	if r != result {
		t.Errorf("result : %s != %s", result, r)
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/year_2016        0.141s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/year_2016)