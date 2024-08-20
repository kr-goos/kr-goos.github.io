---
title: "[Golang] 프로그래머스 Lv.1 푸드 파이트 대회"
description: 푸드 파이트 대회 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-05 08:30:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
수웅이는 매달 주어진 음식을 빨리 먹는 푸드 파이트 대회를 개최합니다. 이 대회에서 선수들은 1대 1로 대결하며, 매 대결마다 음식의 종류와 양이 바뀝니다. 대결은 준비된 음식들을 일렬로 배치한 뒤, 한 선수는 제일 왼쪽에 있는 음식부터 오른쪽으로, 다른 선수는 제일 오른쪽에 있는 음식부터 왼쪽으로 순서대로 먹는 방식으로 진행됩니다. 중앙에는 물을 배치하고, 물을 먼저 먹는 선수가 승리하게 됩니다.

이때, 대회의 공정성을 위해 두 선수가 먹는 음식의 종류와 양이 같아야 하며, 음식을 먹는 순서도 같아야 합니다. 또한, 이번 대회부터는 칼로리가 낮은 음식을 먼저 먹을 수 있게 배치하여 선수들이 음식을 더 잘 먹을 수 있게 하려고 합니다. 이번 대회를 위해 수웅이는 음식을 주문했는데, 대회의 조건을 고려하지 않고 음식을 주문하여 몇 개의 음식은 대회에 사용하지 못하게 되었습니다.

예를 들어, 3가지의 음식이 준비되어 있으며, 칼로리가 적은 순서대로 1번 음식을 3개, 2번 음식을 4개, 3번 음식을 6개 준비했으며, 물을 편의상 0번 음식이라고 칭한다면, 두 선수는 1번 음식 1개, 2번 음식 2개, 3번 음식 3개씩을 먹게 되므로 음식의 배치는 "1223330333221"이 됩니다. 따라서 1번 음식 1개는 대회에 사용하지 못합니다.

수웅이가 준비한 음식의 양을 칼로리가 적은 순서대로 나타내는 정수 배열 `food`가 주어졌을 때, 대회를 위한 음식의 배치를 나타내는 문자열을 return 하는 solution 함수를 완성해주세요.

## 제한사항
- 2 ≤ `food`의 길이 ≤ 9
- 1 ≤ `food`의 각 원소 ≤ 1,000
- `food`에는 칼로리가 적은 순서대로 음식의 양이 담겨 있습니다.
- `food[i]`는 i번 음식의 수입니다.
- `food[0]`은 수웅이가 준비한 물의 양이며, 항상 1입니다.
- 정답의 길이가 3 이상인 경우만 입력으로 주어집니다.

## 입출력 예
{% raw %}
| food | result |
| --- | --- |
| \[1, 3, 4, 6\] | "1223330333221" |
| \[1, 7, 1, 2\] | "111303111" |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 문제의 예시와 같습니다.

**입출력 예 #2**

- 두 선수는 1번 음식 3개, 3번 음식 1개를 먹게 되므로 음식의 배치는 "111303111"입니다.


## 풀이 
### 단순한 풀이
```golang
func solution(food []int) string {

	var leftStr string
	temp := []int{}
	for i, c := range food {
		if i == 0 {
			continue
		}

		for j := 0; j < c/2; j++ {
			temp = append(temp, i)
			leftStr += strconv.Itoa(i)
		}
	}

	var rightStr string
	for j := len(temp) - 1; j >= 0; j-- {
		rightStr += strconv.Itoa(temp[j])
	}

	return leftStr + "0" + rightStr
}
```
- 초기화
	- `leftStr` : 왼쪽 선수의 음식 배치를 저장
	- `temp` : 오른쪽 선수의 음식 배치를 역정렬 하기 위한 슬라이스
- 음식 배치 계산
	- `food` 배열을 순회하며 `food[i]`가 0번째 인덱스(물의 양)일 경우 건너뜀
	- 각 음식(`food[i]`)을 절반(`c/2`)만큼 `leftStr`에 추가 (예> c가 7인 경우 좌, 우측 선수가 3씩 나누고 1을 버림)
	- `temp` 슬라이스에 음식 인덱스 저장
- 오른쪽 음식 배치
	- `temp` 슬라이스를 역순으로 순회하며 `rightStr`에 추가

### 개선된 풀이
```golang
func ImprovedSolution(food []int) string {
	var leftBuilder strings.Builder

	for i, c := range food {
		if i == 0 {
			continue
		}

		str := strconv.Itoa(i)
		for j := 0; j < c/2; j++ {
			leftBuilder.WriteString(str)
		}
	}

	return leftBuilder.String() + "0" + reverseString(leftBuilder.String())
}

func reverseString(s string) string {
	runes := []rune(s)
	for i, j := 0, len(runes)-1; i < j; i, j = i+1, j-1 {
		runes[i], runes[j] = runes[j], runes[i]
	}
	return string(runes)
}
```
- 초기화
	- `leftBuilder`는 `strings.Builder` 타입으로, 왼쪽 선수의 음식 배치를 효율적으로 저장
- 음식 배치 계산
	- `food` 배열을 순회하면서 `food[i]`가 0번째 인덱스(물의 양)일 경우 건너뜀
	- 각 음식(`food[i]`)을 절반(`c/2`)만큼 문자열로 변환(`strconv.Itoa(i)`)하여 `leftBuilder`에 추가
- 오른쪽 음식 배치
	- `reverseString` 함수를 사용하여 `leftBuilder`에 저장된 문자열을 역순으로 변환
		1. 문자열 변환
			- 문자열 `s`를 `rune` 슬라이스로 변환하여 각 문자를 개별적으로 처리할 수 있게 함
		2. 역순 변환
			- 슬라이스를 처음(`i=0`)과 끝(`j=len(runes)-1`)에서 시작하여 서로 교환하면서 중간으로 이동
		3. 결과 반환
			- 역순으로 변환된 rune 슬라이스를 다시 문자열로 변환하여 반환
- 결과 반환:
	- 왼쪽 음식 배치(`leftBuilder.String()`) + "0" + 오른쪽 음식 배치(`reverseString(leftBuilder.String())`)의 형태로 반환


#### strings.Builder 를 사용한 이유
- **성능** : `strings.Builder` 는 문자열을 효율적으로 연결할 수 있도록 설계되었습니다. 문자열을 반복적으로 연결하거나 추가할 때 새로운 문자열을 생성하지 않기 때문에 메모리 할당과 복사가 줄어들어 성능이 개선됩니다.
- **코드 가독성** : `strings.Builder`를 사용하면 코드를 더 명확하고 간결하게 작성할 수 있습니다. 문자열을 반복적으로 연결하는 작업이 명확해집니다.

#### strings.Builder 이점
- **메모리 사용 최적화** : `strings.Builder`는 내부적으로 버퍼를 사용하여 문자열을 빌드합니다. 이를 통해 여러 번의 메모리 할당 및 복사를 피할 수 있어 메모리 사용이 최적화됩니다.
- **성능 향상** : 여러 번의 문자열 연결 작업을 수행할 때, 새로운 문자열 객체를 생성하고 기존 문자열을 복사하는 오버헤드가 줄어듭니다. 이는 특히 큰 문자열을 처리하거나 많은 반복 작업을 수행할 때 유용합니다.
- **안전성** : `strings.Builder`는 쓰기 작업만을 허용하고, 중간 결과를 쉽게 읽을 수 없기 때문에 문자열 빌드 과정에서 불변성을 유지할 수 있습니다.

## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 2

var (
	food = [][]int{
		{1, 3, 4, 6},
		{1, 7, 1, 2},
	}
	result = []string{
		"1223330333221",
		"111303111",
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("food[i] : ", food[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("my solution result : ", solution(food[i]))
	}
}

func TestImprovedSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("food[i] : ", food[i])
		fmt.Println("result[i] : ", result[i])
		fmt.Println("my solution result : ", ImprovedSolution(food[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
food[i] :  [1 3 4 6]
result[i] :  1223330333221
my solution result :  1223330333221
food[i] :  [1 7 1 2]
result[i] :  111303111
my solution result :  111303111
--- PASS: TestSolution (0.00s)
=== RUN   TestImprovedSolution
food[i] :  [1 3 4 6]
result[i] :  1223330333221
my solution result :  1223330333221
food[i] :  [1 7 1 2]
result[i] :  111303111
my solution result :  111303111
--- PASS: TestImprovedSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/food_fight_competition   0.270s
```

### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		solution(food[0])
	}
}

func BenchmarkImprovedSolution(b *testing.B) {
	for i := 0; i < b.N; i++ {
		ImprovedSolution(food[0])
	}
}
```

```bash
$ go test -bench . -run ^$ 
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/food_fight_competition
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4              1546725               781.5 ns/op
BenchmarkImprovedSolution-4      4453602               276.7 ns/op
PASS
ok      golang-coding-test/Lv1/food_fight_competition   3.635s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/food_fight_competition)