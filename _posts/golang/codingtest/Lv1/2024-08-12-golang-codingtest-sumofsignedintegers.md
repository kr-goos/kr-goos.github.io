---
title: "[Golang] 프로그래머스 Lv.1 음양 더하기"
description: 음양 더하기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-12 08:40:03 +09000
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
어떤 정수들이 있습니다. 이 정수들의 절댓값을 차례대로 담은 정수 배열 absolutes와 이 정수들의 부호를 차례대로 담은 불리언 배열 signs가 매개변수로 주어집니다. 실제 정수들의 합을 구하여 return 하도록 solution 함수를 완성해주세요.


## 제한사항
- absolutes의 길이는 1 이상 1,000 이하입니다.
	- absolutes의 모든 수는 각각 1 이상 1,000 이하입니다.
- signs의 길이는 absolutes의 길이와 같습니다.
	- `signs[i]` 가 참이면 `absolutes[i]` 의 실제 정수가 양수임을, 그렇지 않으면 음수임을 의미합니다.


## 입출력 예
{% raw %}
| absolutes | signs | result |
| --- | --- | --- |
| `[4,7,12]` | `[true,false,true]` | 9 |
| `[1,2,3]` | `[false,false,true]` | 0 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- signs가 `[true,false,true]` 이므로, 실제 수들의 값은 각각 4, -7, 12입니다.
- 따라서 세 수의 합인 9를 return 해야 합니다.


**입출력 예 #2**

- signs가 `[false,false,true]` 이므로, 실제 수들의 값은 각각 -1, -2, 3입니다.
- 따라서 세 수의 합인 0을 return 해야 합니다.


## 풀이 
```golang
func solution(absolutes []int, signs []bool) int {

	var total int
	for i, v := range absolutes {
		if !signs[i] {
			v *= -1
		}
		total += v
	}

	return total
}
```

- `lottos` 의 값을 `win_nums` 배열에서 찾으려 할 땐 시간복잡도가 `O(n)`이지만 맵으로 생성하여 검색 할 경우 `O(1)` 이므로 맵으로 생성
- `lottos`를 순회하며 값이 0 이면 `zeroCount` 변수를 증가시키고, 위에서 생성한 Map 에 `num` 이 존재한다면, `matchingCount` 값을 증가시킴
- `getRanking` 함수를 이용해 `matchingCount + zeroCount`(일치 최대 개수) 와 `matchingCount`(일치 최소 개수) 의 순위를 반환


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	absolutes = [TESTCOUNT][]int{
		{4, 7, 12},
		{1, 2, 3},
	}
	signs = [TESTCOUNT][]bool{
		{true, false, true},
		{false, false, true},
	}
	result = [TESTCOUNT]int{9, 0}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("absolutes : %v\n", absolutes[i])
		fmt.Printf("signs : %v\n", signs[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(absolutes[i], signs[i]))

	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
absolutes : [4 7 12]
signs : [true false true]
result : 9
solution result : 9
absolutes : [1 2 3]
signs : [false false true]
result : 0
solution result : 0
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/sum_of_signed_integers   0.205s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/sum_of_signed_integers)