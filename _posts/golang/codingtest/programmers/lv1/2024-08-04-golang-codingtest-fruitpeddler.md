---
title: "[Golang] 프로그래머스 Lv.1 과일 장수"
description: 과일 장수 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-04 21:30:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
과일 장수가 사과 상자를 포장하고 있습니다. 사과는 상태에 따라 1점부터 k점까지의 점수로 분류하며, k점이 최상품의 사과이고 1점이 최하품의 사과입니다. 사과 한 상자의 가격은 다음과 같이 결정됩니다.

- 한 상자에 사과를 m개씩 담아 포장합니다.
- 상자에 담긴 사과 중 가장 낮은 점수가 p (1 ≤ p ≤ k)점인 경우, 사과 한 상자의 가격은 p * m 입니다.

과일 장수가 가능한 많은 사과를 팔았을 때, 얻을 수 있는 최대 이익을 계산하고자 합니다.(사과는 상자 단위로만 판매하며, 남는 사과는 버립니다)

예를 들어, `k` = 3, `m` = 4, 사과 7개의 점수가 [1, 2, 3, 1, 2, 3, 1]이라면, 다음과 같이 [2, 3, 2, 3]으로 구성된 사과 상자 1개를 만들어 판매하여 최대 이익을 얻을 수 있습니다.

- (최저 사과 점수) x (한 상자에 담긴 사과 개수) x (상자의 개수) = 2 x 4 x 1 = 8

사과의 최대 점수 `k`, 한 상자에 들어가는 사과의 수 `m`, 사과들의 점수 `score`가 주어졌을 때, 과일 장수가 얻을 수 있는 최대 이익을 return하는 solution 함수를 완성해주세요.

## 제한사항
- 3 ≤ `k` ≤ 9
- 3 ≤ `m` ≤ 10
- 7 ≤ `score`의 길이 ≤ 1,000,000
	- 1 ≤ `score[i]` ≤ k
- 이익이 발생하지 않는 경우에는 0을 return 해주세요.

## 입출력 예
{% raw %}
|k|m|score|result|
|-|-|-|-|
|3|4|[1, 2, 3, 1, 2, 3, 1]|8|
|4|3|[4, 1, 2, 2, 4, 4, 4, 4, 1, 2, 4, 2]|33|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 문제의 예시와 같습니다.

**입출력 예 #2**

- 다음과 같이 사과 상자를 포장하여 모두 팔면 최대 이익을 낼 수 있습니다.

{% raw %}
|사과 상자|가격|
|-|-|
|[1, 1, 2]|	1 x 3 = 3|
|[2, 2, 2]|	2 x 3 = 6|
|[4, 4, 4]|	4 x 3 = 12|
|[4, 4, 4]|	4 x 3 = 12|
{% endraw %}

따라서 (1 x 3 x 1) + (2 x 3 x 1) + (4 x 3 x 2) = 33을 return합니다.

## 풀이 
```golang
func solution(k int, m int, score []int) int {

	sort.Slice(score, func(i, j int) bool {
		return score[i] > score[j]
	})

	var profit int

	for i := 0; i+m <= len(score); i += m {
		box := score[i : i+m]
		profit += box[m-1] * m
	}

	return profit
}
```
- 사과 점수 정렬 : 사과 점수를 내림차순으로 정렬 (더 높은 점수를 가진 사과가 먼저 오게 하기 위함)
- 상자 구성 : 정렬된 사과 배열에서 `m` 개씩 묶어 상자를 만듦
	- `i+m <= len(score)` : 반복문이 사과 점수를 m개씩 묶어서 상자를 만들 때 유효한 범위 내에서 순회
- 최소 점수 계산 : 각 상자의 최소 점수를 찾아서 해당 상자의 가격을 계산
	- `box[m-1]` : 마지막 요소는 박스의 최저 사과 점수
- 최대 이익 누적: 상자의 가격을 누적하여 최대 이익을 계산
	- 최대 이익 += 최저 사과 점수 (`box[m-1]`) * 한 상자에 담긴 사과 개수 (`m`) 의 누적값

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	const TESTCOUNT = 2
	k := [TESTCOUNT]int{3, 4}
	m := [TESTCOUNT]int{4, 3}
	score := [TESTCOUNT][]int{
		{1, 2, 3, 1, 2, 3, 1},
		{4, 1, 2, 2, 4, 4, 4, 4, 1, 2, 4, 2},
	}
	success := [TESTCOUNT]int{8, 33}

	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("k[i] : ", k[i])
		fmt.Println("m[i] : ", m[i])
		fmt.Println("score[i] : ", score[i])
		fmt.Println("success[i] : ", success[i])
		fmt.Println("solution result : ", solution(k[i], m[i], score[i]))
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
k[i] :  3
m[i] :  4
score[i] :  [1 2 3 1 2 3 1]
success[i] :  8
solution result :  8
k[i] :  4
m[i] :  3
score[i] :  [4 1 2 2 4 4 4 4 1 2 4 2]
success[i] :  33
solution result :  33
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/fruit_peddler    0.138s 
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/fruit_peddler)