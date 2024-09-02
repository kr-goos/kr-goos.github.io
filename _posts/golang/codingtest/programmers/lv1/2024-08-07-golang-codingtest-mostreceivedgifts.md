---
title: "[Golang] 프로그래머스 Lv.1 가장 많이 받은 선물"
description: KAKAO WINTER INTERNSHIP 가장 많이 받은 선물 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-07 12:30:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
선물을 직접 전하기 힘들 때 카카오톡 선물하기 기능을 이용해 축하 선물을 보낼 수 있습니다. 당신의 친구들이 이번 달까지 선물을 주고받은 기록을 바탕으로 다음 달에 누가 선물을 많이 받을지 예측하려고 합니다.

- 두 사람이 선물을 주고받은 기록이 있다면, 이번 달까지 두 사람 사이에 더 많은 선물을 준 사람이 다음 달에 선물을 하나 받습니다.
	- 예를 들어 `A`가 B에게 선물을 5번 줬고, `B`가 `A`에게 선물을 3번 줬다면 다음 달엔 `A`가 `B`에게 선물을 하나 받습니다.
- 두 사람이 선물을 주고받은 기록이 하나도 없거나 주고받은 수가 같다면, 선물 지수가 더 큰 사람이 선물 지수가 더 작은 사람에게 선물을 하나 받습니다.
	- 선물 지수는 이번 달까지 자신이 친구들에게 준 선물의 수에서 받은 선물의 수를 뺀 값입니다.
	- 예를 들어 `A`가 친구들에게 준 선물이 3개고 받은 선물이 10개라면 `A`의 선물 지수는 -7입니다. `B`가 친구들에게 준 선물이 3개고 받은 선물이 2개라면 `B`의 선물 지수는 1입니다. 만약 `A`와 `B`가 선물을 주고받은 적이 없거나 정확히 같은 수로 선물을 주고받았다면, 다음 달엔 `B`가 `A`에게 선물을 하나 받습니다.
	- **만약 두 사람의 선물 지수도 같다면 다음 달에 선물을 주고받지 않습니다.**

위에서 설명한 규칙대로 다음 달에 선물을 주고받을 때, 당신은 선물을 가장 많이 받을 친구가 받을 선물의 수를 알고 싶습니다.

친구들의 이름을 담은 1차원 문자열 배열 `friends` 이번 달까지 친구들이 주고받은 선물 기록을 담은 1차원 문자열 배열 `gifts`가 매개변수로 주어집니다. 이때, 다음달에 가장 많은 선물을 받는 친구가 받을 선물의 수를 return 하도록 solution 함수를 완성해 주세요.


## 제한사항
- 2 ≤ `friends`의 길이 = 친구들의 수 ≤ 50
	- `friends`의 원소는 친구의 이름을 의미하는 알파벳 소문자로 이루어진 길이가 10 이하인 문자열입니다.
	- 이름이 같은 친구는 없습니다.
- 1 ≤ `gifts`의 길이 ≤ 10,000
	- `gifts`의 원소는 `"A B"`형태의 문자열입니다. `A`는 선물을 준 친구의 이름을 `B`는 선물을 받은 친구의 이름을 의미하며 공백 하나로 구분됩니다.
	- `A`와 `B`는 `friends`의 원소이며 `A`와 `B`가 같은 이름인 경우는 존재하지 않습니다.


## 입출력 예
{% raw %}
| friends | gifts | result |
| --- | --- | --- |
| \["muzi", "ryan", "frodo", "neo"\] | \["muzi frodo", "muzi frodo", "ryan muzi", "ryan muzi", "ryan muzi", "frodo muzi", "frodo ryan", "neo muzi"\] | 2 |
| \["joy", "brad", "alessandro", "conan", "david"\] | \["alessandro brad", "alessandro joy", "alessandro conan", "david alessandro", "alessandro david"\] | 4 |
| \["a", "b", "c"\] | \["a b", "b a", "c a", "a c", "a c", "c a"\] | 0 |
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

주고받은 선물과 선물 지수를 표로 나타내면 다음과 같습니다.

{% raw %}
| ↓준 사람 \\ 받은 사람→ | muzi | ryan | frodo | neo |
| --- | --- | --- | --- | --- |
| muzi | \- | 0 | 2 | 0 |
| ryan | 3 | \- | 0 | 0 |
| frodo | 1 | 1 | \- | 0 |
| neo | 1 | 0 | 0 | \- |
{% endraw %}

{% raw %}
| 이름 | 준 선물 | 받은 선물 | 선물 지수 |
| --- | --- | --- | --- |
| muzi | 2 | 5 | \-3 |
| ryan | 3 | 1 | 2 |
| frodo | 2 | 2 | 0 |
| neo | 1 | 0 | 1 |
{% endraw %}

`muzi`는 선물을 더 많이 줬던 `frodo`에게서 선물을 하나 받습니다.
`ryan`은 선물을 더 많이 줬던 `muzi`에게서 선물을 하나 받고, 선물을 주고받지 않았던 `neo`보다 선물 지수가 커 선물을 하나 받습니다.
`frodo`는 선물을 더 많이 줬던 `ryan`에게 선물을 하나 받습니다.
`neo`는 선물을 더 많이 줬던 `muzi`에게서 선물을 하나 받고, 선물을 주고받지 않았던 `frodo`보다 선물 지수가 커 선물을 하나 받습니다.

다음달에 가장 선물을 많이 받는 사람은 `ryan`과 `neo`이고 2개의 선물을 받습니다. 따라서 2를 return 해야 합니다.


**입출력 예 #2**

주고받은 선물과 선물 지수를 표로 나타내면 다음과 같습니다.

{% raw %}
| ↓준 사람 \\ 받은 사람→ | joy | brad | alessandro | conan | david |
| --- | --- | --- | --- | --- | --- |
| joy | \- | 0 | 0 | 0 | 0 |
| brad | 0 | \- | 0 | 0 | 0 |
| alessandro | 1 | 1 | \- | 1 | 1 |
| conan | 0 | 0 | 0 | \- | 0 |
| david | 0 | 0 | 1 | 0 | \- |
{% endraw %}

{% raw %}
| 이름 | 준 선물 | 받은 선물 | 선물 지수 |
| --- | --- | --- | --- |
| joy | 0 | 1 | \-1 |
| brad | 0 | 1 | \-1 |
| alessandro | 4 | 1 | 3 |
| conan | 0 | 1 | \-1 |
| david | 1 | 1 | 0 |
{% endraw %}

`alessandro`가 선물을 더 많이 줬던 `joy`, `brad`, `conan`에게서 선물을 3개 받습니다. 선물을 하나씩 주고받은 david보다 선물 지수가 커 선물을 하나 받습니다.
david는 선물을 주고받지 않았던 `joy`, `brad`, `conan`보다 선물 지수가 커 다음 달에 선물을 3개 받습니다.
`joy`, `brad`, `conan`은 선물을 받지 못합니다.

다음달에 가장 선물을 많이 받는 사람은 `alessandro`이고 4개의 선물을 받습니다. 따라서 4를 return 해야 합니다.


**입출력 예 #3**
`a`와 `b`, `a`와 `c`, `b`와 `c` 사이에 서로 선물을 주고받은 수도 같고 세 사람의 선물 지수도 0으로 같아 다음 달엔 아무도 선물을 받지 못합니다. 따라서 0을 return 해야 합니다.

## 풀이 
```golang
func solution(friends []string, gifts []string) int {

	m := initializeGiftExchangeMap(friends)

	setupGiftExchangeMap(m, gifts)
	giftsToReceive := make(map[string]int)

	for i := 0; i < len(friends); i++ {
		for j := i + 1; j < len(friends); j++ {

			if m[friends[i]][friends[j]] > m[friends[j]][friends[i]] {
				giftsToReceive[friends[i]]++
			} else if m[friends[i]][friends[j]] < m[friends[j]][friends[i]] {
				giftsToReceive[friends[j]]++
			} else {
				if m[friends[i]]["index"] > m[friends[j]]["index"] {
					giftsToReceive[friends[i]]++
				} else if m[friends[i]]["index"] < m[friends[j]]["index"] {
					giftsToReceive[friends[j]]++
				} else {
					continue
				}
			}
		}
	}

	var max int
	for _, v := range giftsToReceive {
		if v > max {
			max = v
		}
	}

	return max
}

func initializeGiftExchangeMap(f []string) map[string]map[string]int {
	m := map[string]map[string]int{}

	for _, v1 := range f {
		m[v1] = make(map[string]int)
		for _, v2 := range f {
			m[v1][v2] = 0
		}
	}

	return m
}

func setupGiftExchangeMap(m map[string]map[string]int, g []string) {
	for _, gift := range g {
		s := strings.Split(gift, " ")
		m[s[0]][s[1]]++
		m[s[0]]["index"]++
		m[s[1]]["index"]--
	}
}
```

- `initializeGiftExchangeMap` 함수
	- `friends` 배열을 순회하며 각 친구에 대한 내부 맵을 초기화(초기화 값 : 0)
	- `m[v1][v2]`
		- `v1` : 선물을 준 사람
		- `v2` : 선물을 받은 사람
- `setupGiftExchangeMap` 함수
	- `gifts` 배열을 순회하며 각 선물 교환 기록을 맵에 설정
	- 각 선물 교환 기록을 "준 사람"과 "받은 사람"으로 분리하고, 해당 관계에 대한 선물 수를 증가
	- 선물을 준 사람의 `index`는 증가시키고, 받은 사람의 `index`는 감소
- 선물 교환 비교
	- 두 개의 중첩된 for 루프를 사용하여 모든 **친구 쌍을 비교** 
		- `(A, B)` 와 `(B, A)`를 중복 비교 하지 않기 위한 순회 조건 (O 또는 X 부분만을 순회)

		| ↓준 사람 \\ 받은 사람→ | muzi | ryan | frodo | neo |
		| --- | --- | --- | --- | --- |
		| muzi | \- | O | O | O |
		| ryan | X | \- | O | O |
		| frodo | X | X | \- | O |
		| neo | X | X | X | \- |

		- **주의** : 만약 `friends` 에 대해 이중 순회를 하며 전체를 순회하게 되면, `[friends[i]][friends[j]]` , `[friends[j]][friends[i]]` 에 대해 중복 처리가 되므로 결과 값이 * 2 배가 될 수 있음
- `giftsToReceive` 맵을 순회하며 최대로 받을 선물 수 계산


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	friends = [TESTCOUNT][]string{
		{"muzi", "ryan", "frodo", "neo"},
		{"joy", "brad", "alessandro", "conan", "david"},
		{"a", "b", "c"},
	}

	gifts = [TESTCOUNT][]string{
		{"muzi frodo", "muzi frodo", "ryan muzi", "ryan muzi", "ryan muzi", "frodo muzi", "frodo ryan", "neo muzi"},
		{"alessandro brad", "alessandro joy", "alessandro conan", "david alessandro", "alessandro david"},
		{"a b", "b a", "c a", "a c", "a c", "c a"},
	}

	result = []int{2, 4, 0}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("friends[i] : ", friends[i])
		fmt.Println("gifts[i] : ", gifts[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("my solution result : ", solution(friends[i], gifts[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
friends[i] :  [muzi ryan frodo neo]
gifts[i] :  [muzi frodo muzi frodo ryan muzi ryan muzi ryan muzi frodo muzi frodo ryan neo muzi]
result[i] :  2
my solution result :  2
friends[i] :  [joy brad alessandro conan david]
gifts[i] :  [alessandro brad alessandro joy alessandro conan david alessandro alessandro david]
result[i] :  4
my solution result :  4
friends[i] :  [a b c]
gifts[i] :  [a b b a c a a c a c c a]
result[i] :  0
my solution result :  0
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/most_received_gifts      0.206s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/most_received_gifts)