---
title: "[Golang] 프로그래머스 Lv.1 콜라츠 추측"
description: 콜라츠 추측 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-14 08:05:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
1937년 Collatz란 사람에 의해 제기된 이 추측은, 주어진 수가 1이 될 때까지 다음 작업을 반복하면, 모든 수를 1로 만들 수 있다는 추측입니다. 작업은 다음과 같습니다.

```
1-1. 입력된 수가 짝수라면 2로 나눕니다. 
1-2. 입력된 수가 홀수라면 3을 곱하고 1을 더합니다. 
2. 결과로 나온 수에 같은 작업을 1이 될 때까지 반복합니다. 
```

예를 들어, 주어진 수가 6이라면 6 → 3 → 10 → 5 → 16 → 8 → 4 → 2 → 1 이 되어 총 8번 만에 1이 됩니다. 위 작업을 몇 번이나 반복해야 하는지 반환하는 함수, solution을 완성해 주세요. 단, 주어진 수가 1인 경우에는 0을, 작업을 500번 반복할 때까지 1이 되지 않는다면 –1을 반환해 주세요.


## 제한사항
- 입력된 수, `num`은 1 이상 8,000,000 미만인 정수입니다.


## 입출력 예
{% raw %}
| n | result |
| --- | --- |
| 6 | 8 |
| 16 | 4 |
| 626331 | \-1 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 문제의 설명과 같습니다.

**입출력 예 #2**

- 16 → 8 → 4 → 2 → 1 이 되어 총 4번 만에 1이 됩니다.

**입출력 예 #3**

- 626331은 500번을 시도해도 1이 되지 못하므로 -1을 리턴해야 합니다.


## 풀이 
```golang
func solution(num int) int {
	var count int
	if num == 1 {
		return 0
	}

	for {
		if count >= 500 {
			return -1
		}

		count++

		if num%2 == 0 {
			num /= 2
		} else {
			num = num*3 + 1
		}

		if num == 1 {
			return count
		}
	}
}
```
- 변수 초기화
	- `count` : 현재 반복 횟수를 저장
- `num` 값이 1인 경우, 순회를 할 필요가 없으므로 먼저 `0` 을 리턴
- 무한루프
	- `count` 가 500 이 되면 -1 을 리턴
	- 현재 반복회수 `count` 증가
	- `num` 값이 짝수라면 2를 나눔
	- `num` 값이 홀수라면 3을 곱하고 1을 더함
	- 새로 저장된 num 값이 1 이라면, `count` 를 리턴
	

## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	n = [TESTCOUNT]int{
		6,
		16,
		626331,
	}

	result = [TESTCOUNT]int{
		8,
		4,
		-1,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("n : %d\n", n[i])
		fmt.Printf("result : %d\n", result[i])
		fmt.Printf("solution result : %d\n", solution(n[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
n : 6
result : 8
solution result : 8
n : 16
result : 4
solution result : 4
n : 626331
result : -1
solution result : -1
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/collatz_conjecture       0.210s
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/collatz_conjecture)