---
title: "[Golang] 프로그래머스 Lv.1 없는 숫자 더하기"
description: 없는 숫자 더하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-09 08:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
0부터 9까지의 숫자 중 일부가 들어있는 정수 배열 `numbers`가 매개변수로 주어집니다. `numbers`에서 찾을 수 없는 0부터 9까지의 숫자를 모두 찾아 더한 수를 return 하도록 solution 함수를 완성해주세요.


## 제한사항
- 1 ≤ `numbers`의 길이 ≤ 9
	- 0 ≤ `numbers`의 모든 원소 ≤ 9
	- `numbers`의 모든 원소는 서로 다릅니다.


## 입출력 예
{% raw %}
| numbers | result |
| --- | --- |
| \[1,2,3,4,6,7,8,0\] | 14 |
| \[5,8,4,0,6,7,9\] | 6 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 5, 9가 `numbers`에 없으므로, 5 + 9 = 14를 return 해야 합니다.


**입출력 예 #2**

- 1, 2, 3이 `numbers`에 없으므로, 1 + 2 + 3 = 6을 return 해야 합니다.


## 풀이 
```golang
func solution(numbers []int) int {

	m := make(map[int]struct{}, 9)
	for _, num := range numbers {
		m[num] = struct{}{}
	}

	var answer int
	for i := 1; i <= 9; i++ {
		if _, ok := m[i]; !ok {
			answer += i
		}
	}

	return answer
}
```

- `m` 초기화
	- `map[int]struct{}` 를 사용한 이유는 단순히 키의 존재 여부만을 체크하는 용도로 사용할 map 이기 때문
		- 빈 구조체는 메모리를 거의 사용하지 않으므로, value 가 중요하지 않은 경우 매우 효율적
- `numbers` 를 순회하며 각 숫자를 `m` 에 추가
- 1 ~ 9 순회 ( 0 은 값이 존재하던 존재하지 않던, 최종 합계를 계산할 때, +0 (의미없는 값) 이므로 포함하지 않음 )
	- 값이 존재하지 않는 경우 `answer` 에 `i` 를 더함


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

func TestSolution(t *testing.T) {
	numbers := [TESTCOUNT][]int{
		{1, 2, 3, 4, 6, 7, 8, 0},
		{5, 8, 4, 0, 6, 7, 9},
	}
	result := [TESTCOUNT]int{14, 6}

	for i := 0; i < TESTCOUNT; i++ {
		v := solution(numbers[i])

		if v == result[i] {
			fmt.Printf("testcase %d sucecss\n", i+1)
		} else {
			t.Fatalf("testcase %d result : %d, solution result : %d", i+1, result[i], v)
		}
	}
}
```

```bash
$ go test -v
$ go test -v
=== RUN   TestSolution
testcase 1 sucecss
testcase 2 sucecss
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/sum_missing_numbers      0.690s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sum_missing_numbers)