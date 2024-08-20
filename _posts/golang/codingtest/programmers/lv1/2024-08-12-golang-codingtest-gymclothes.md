---
title: "[Golang] 프로그래머스 Lv.1 체육복"
description: 체육복 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-12 12:46:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
점심시간에 도둑이 들어, 일부 학생이 체육복을 도난당했습니다. 다행히 여벌 체육복이 있는 학생이 이들에게 체육복을 빌려주려 합니다. 학생들의 번호는 체격 순으로 매겨져 있어, 바로 앞번호의 학생이나 바로 뒷번호의 학생에게만 체육복을 빌려줄 수 있습니다. 예를 들어, 4번 학생은 3번 학생이나 5번 학생에게만 체육복을 빌려줄 수 있습니다. 체육복이 없으면 수업을 들을 수 없기 때문에 체육복을 적절히 빌려 최대한 많은 학생이 체육수업을 들어야 합니다.

전체 학생의 수 n, 체육복을 도난당한 학생들의 번호가 담긴 배열 lost, 여벌의 체육복을 가져온 학생들의 번호가 담긴 배열 reserve가 매개변수로 주어질 때, 체육수업을 들을 수 있는 학생의 최댓값을 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- 전체 학생의 수는 2명 이상 30명 이하입니다.
- 체육복을 도난당한 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌의 체육복을 가져온 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌 체육복이 있는 학생만 다른 학생에게 체육복을 빌려줄 수 있습니다.
- 여벌 체육복을 가져온 학생이 체육복을 도난당했을 수 있습니다. 이때 이 학생은 체육복을 하나만 도난당했다고 가정하며, 남은 체육복이 하나이기에 다른 학생에게는 체육복을 빌려줄 수 없습니다.


## 입출력 예
{% raw %}
| n | lost | reserve | return |
| --- | --- | --- | --- |
| 5 | \[2, 4\] | \[1, 3, 5\] | 5 |
| 5 | \[2, 4\] | \[3\] | 4 |
| 3 | \[3\] | \[1\] | 2 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 1번 학생이 2번 학생에게 체육복을 빌려주고, 3번 학생이나 5번 학생이 4번 학생에게 체육복을 빌려주면 학생 5명이 체육수업을 들을 수 있습니다.


**입출력 예 #2**

- 3번 학생이 2번 학생이나 4번 학생에게 체육복을 빌려주면 학생 4명이 체육수업을 들을 수 있습니다.


## 풀이 

### 실패할 수 있는 케이스
- `lost` 와 `reserve` 가 정렬되어 있지 않을 경우
	- 예를들어 `n=5` `lost=[4,2]` `reserve=[3,5,1]` 인 경우
		- 4번 학생은 체육복이 없고, 3번 학생이 여벌 체육복을 가지고 있습니다.
		- 3번 학생이 4번 학생에게 체육복을 빌려주면, 2번 학생이 체육복을 못 빌릴 가능성이 생깁니다.
- 여벌 체육복을 가져온 학생이 체육복을 도난당했을 경우(다른 사람에게 체육복을 빌려줄 수 없음)
	- 예를들어 `n = 5` `lost = [2, 3]` `reserve = [3, 4]` 인 경우
		- 여기서 3번 학생은 체육복을 도난당했으나, 여벌의 체육복도 가지고 있습니다. 따라서 이 조건에 따라 3번 학생은 자기 자신에게 체육복을 사용하고, 다른 학생에게는 체육복을 빌려줄 수 없습니다.
		- 만약 이 조건을 무시하고 3번 학생이 다른 학생에게 체육복을 빌려줄 수 있다고 처리한다면, 3번 학생이 2번 학생에게 체육복을 빌려주고, 4번 학생이 3번 학생에게 체육복을 빌려주는 시나리오가 성립할 수 있습니다.

```golang
const UNAVAILABLE = -1

func solution(n int, lost []int, reserve []int) int {
	sort.Ints(lost)
	sort.Ints(reserve)

	answer := n - len(lost)
	for i, l := range lost {
		for j, r := range reserve {
			if l == r {
				answer++
				lost[i] = UNAVAILABLE
				reserve[j] = UNAVAILABLE
				break
			}
		}
	}

	for _, l := range lost {
		if l == UNAVAILABLE {
			continue
		}

		for j, r := range reserve {
			if r == UNAVAILABLE {
				continue
			}

			if l-1 == r || l+1 == r {
				answer++
				reserve[j] = UNAVAILABLE
				break
			}
		}

	}
	return answer
}
```

- `lost`와 `reserve`를 정렬
- `answer` 값 초기화
	- 전체 학생수 `n` 에서, 체육복을 도난당한 학생들의 번호가 담긴 배열 `lost` 의 길이를 빼줌
- `lost`를 순회하며 `reserve` 값 중 `l == r` 의 조건을 충족하는 값이 있는지 확인
	- 문제의 조건
	  ```
	  체육복을 하나만 도난당했다고 가정하며, 남은 체육복이 하나이기에 다른 학생에게는 체육복을 빌려줄 수 없습니다.
	  ```
		- `lost`와 `reserve`의 요소를 사용하지 못하도록 상수 `UNAVAILABLE`의 값으로 바꿔주고, `answer++`(자기 남은 체육복을 자신에게 사용)
- 위 조건에서 사용할 수 없는 값을 제거한 `lost` 와 `reserve`를 순회
	- 현재 요소 `l`이 `UNAVAILABLE` 라면 다음 요소로 넘어감
	- 현재 요소 `l`이 `UNAVAILABLE` 가 아니라면 `reserve`를 순회
		- 현재 요소 `r` 이 `UNAVAILABLE`라면 다음 요소로 넘어감
		- 현재 요소 `r` 이 `UNAVAILABLE`가 아니라면 `l-1` 또는 `l+1`이 r 과 같은지 비교
			- 값이 같다면 `answer++`(여벌옷을 빌려주어 수업 참여 가능) `reserve[j] = UNAVAILABLE`(옷을 빌려줬으므로 더이상 여벌옷이 없음)
- `answer` 반환



## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	n    = [TESTCOUNT]int{5, 5, 3}
	lost = [TESTCOUNT][]int{
		{2, 4},
		{2, 4},
		{3},
	}
	reserve = [TESTCOUNT][]int{
		{1, 3, 5},
		{3},
		{1},
	}
	result = [TESTCOUNT]int{5, 4, 2}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("n : %v\n", n[i])
		fmt.Printf("lost : %v\n", lost[i])
		fmt.Printf("reserve : %v\n", reserve[i])
		fmt.Printf("result : %d\n", result[i])
		fmt.Printf("solution result : %d\n", solution(n[i], lost[i], reserve[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
n : 5
lost : [2 4]
reserve : [1 3 5]
result : 5
solution result : 5
n : 5
lost : [2 4]
reserve : [3]
result : 4
solution result : 4
n : 3
lost : [3]
reserve : [1]
result : 2
solution result : 2
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/gym_clothes      0.273s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/gym_clothes)