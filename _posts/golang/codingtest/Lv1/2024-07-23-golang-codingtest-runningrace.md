---
title: "[Golang] 프로그래머스 Lv.1 달리기 경주"
description: 달리기 경주 문제에 대해 풀이하고, 유닛 & 벤치마크 테스트를 해보겠습니다.
author: 김우석
date: 2024-07-24 08:30:00 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/window-golang-install/golang.svg
---

## 문제 설명
얀에서는 매년 달리기 경주가 열립니다. 해설진들은 선수들이 자기 바로 앞의 선수를 추월할 때 추월한 선수의 이름을 부릅니다. 예를 들어 1등부터 3등까지 "mumu", "soe", "poe" 선수들이 순서대로 달리고 있을 때, 해설진이 "soe"선수를 불렀다면 2등인 "soe" 선수가 1등인 "mumu" 선수를 추월했다는 것입니다. 즉 "soe" 선수가 1등, "mumu" 선수가 2등으로 바뀝니다.

선수들의 이름이 1등부터 현재 등수 순서대로 담긴 문자열 배열 `players`와 해설진이 부른 이름을 담은 문자열 배열 `callings`가 매개변수로 주어질 때, 경주가 끝났을 때 선수들의 이름을 1등부터 등수 순서대로 배열에 담아 return 하는 solution 함수를 완성해주세요.

## 제한사항
- 5 ≤ `players`의 길이 ≤ 50,000
	- `players[i]`는 i번째 선수의 이름을 의미합니다.
	- `players`의 원소들은 알파벳 소문자로만 이루어져 있습니다.
	- `players`에는 중복된 값이 들어가 있지 않습니다.
	- 3 ≤ `players[i]`의 길이 ≤ 10
- 2 ≤ `callings`의 길이 ≤ 1,000,000
	- `callings`는 `players`의 원소들로만 이루어져 있습니다.
	- 경주 진행중 1등인 선수의 이름은 불리지 않습니다.

## 입출력 예
{% raw %}
|players|callings|result|
|-|-|-|
|["mumu", "soe", "poe", "kai", "mine"]|["kai", "kai", "mine", "mine"]|["mumu", "kai", "mine", "soe", "poe"]|
{% endraw %}

## 입출력 예 설명
입출력 예 #1

4등인 "kai" 선수가 2번 추월하여 2등이 되고 앞서 3등, 2등인 "poe", "soe" 선수는 4등, 3등이 됩니다. 5등인 "mine" 선수가 2번 추월하여 4등, 3등인 "poe", "soe" 선수가 5등, 4등이 되고 경주가 끝납니다. 1등부터 배열에 담으면 ["mumu", "kai", "mine", "soe", "poe"]이 됩니다.

## 풀이 [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/running_race)
### 파라미터를 그대로 이용한 방법
```golang
func solution(players []string, callings []string) []string {
	for _, calledName := range callings {
	LOOP:
		for idx, player := range players {
			if player != calledName {
				continue
			}
			players[idx-1], players[idx] = player, players[idx-1]
			break LOOP
		}
	}
	return players
}
```
- 이 방식의 성능은 각 호출에 대해 `players` 리스트를 전체적으로 탐색하므로, 전체 `시간 복잡도는 O(m * n)`
	-  `m`은 `callings` 리스트의 길이, `n`은 `players` 리스트의 길이
	- 리스트가 클수록 성능이 저하될 수 있음


### 파라미터의 자료구조를 map 으로 변형한 방법
```golang
func solution2(players []string, callings []string) []string {

	playerMap := stringSliceToIndexMap(players)

	for _, calledPlayer := range callings {
		currRank := playerMap[calledPlayer]
		players[currRank], players[currRank-1] = players[currRank-1], players[currRank]
		playerMap[calledPlayer]--
		playerMap[players[currRank]]++
	}
	return players
}

func stringSliceToIndexMap(arr []string) map[string]int {
	m := make(map[string]int, len(arr))
	for i, v := range arr {
		m[v] = i
	}
	return m
}
```
- `players` 인덱스를 맵을 통해 상수 시간에 조회하고 업데이트하므로, 전체 `시간 복잡도는 O(m + n)`
	- `m`은 `callings` 리스트의 길이, `n`은 `players` 리스트의 길이
	- 데이터셋이 클수록 더 효율적

## 유닛 & 벤치마크 테스트
### 유닛 테스트
```golang
{% raw %}
func TestSolution(t *testing.T) {
	playersTestCases := []string{
		"mumu", "soe", "poe", "kai", "mine",
	}
	callingsTestCases := []string{
		"kai", "kai", "mine", "mine",
	}

	successResult := []string{
		"mumu", "kai", "mine", "soe", "poe",
	}

	results := solution(playersTestCases, callingsTestCases)
	for i := range results {
		if results[i] != successResult[i] {
			t.Fatalf("result : %s != sucecssResult : %s\n", results[i], successResult[i])
		}
		fmt.Printf("result : %s == sucecssResult : %s\n", results[i], successResult[i])
	}
}

func TestSolution2(t *testing.T) {
	playersTestCases := []string{
		"mumu", "soe", "poe", "kai", "mine",
	}
	callingsTestCases := []string{
		"kai", "kai", "mine", "mine",
	}

	successResult := []string{
		"mumu", "kai", "mine", "soe", "poe",
	}

	results := solution2(playersTestCases, callingsTestCases)
	for i := range results {
		if results[i] != successResult[i] {
			t.Fatalf("result : %s != sucecssResult : %s\n", results[i], successResult[i])
		}
		fmt.Printf("result : %s == sucecssResult : %s\n", results[i], successResult[i])
	}

}
{% endraw %}
```

```bash
$ go test -v
=== RUN   TestSolution
result : mumu == sucecssResult : mumu
result : kai == sucecssResult : kai  
result : mine == sucecssResult : mine
result : soe == sucecssResult : soe  
result : poe == sucecssResult : poe  
--- PASS: TestSolution (0.00s)       
=== RUN   TestSolution2
result : mumu == sucecssResult : mumu
result : kai == sucecssResult : kai  
result : mine == sucecssResult : mine
result : soe == sucecssResult : soe
result : poe == sucecssResult : poe
--- PASS: TestSolution2 (0.00s)
PASS
ok      golang-coding-test/Lv1/running_race     0.165s
```

### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {

	for i := 0; i < b.N; i++ {
		playersTestCases := []string{
			"mumu", "soe", "poe", "kai", "mine",
		}
		callingsTestCases := []string{
			"kai", "kai", "mine", "mine",
		}
		_ = solution(playersTestCases, callingsTestCases)
	}
}

func BenchmarkSolution2(b *testing.B) {

	for i := 0; i < b.N; i++ {
		playersTestCases := []string{
			"mumu", "soe", "poe", "kai", "mine",
		}
		callingsTestCases := []string{
			"kai", "kai", "mine", "mine",
		}
		_ = solution2(playersTestCases, callingsTestCases)
	}
}
```

```bash
$ go test -bench . -benchtime 100000x
...
BenchmarkSolution-4       100000               105.5 ns/op
BenchmarkSolution2-4      100000               508.5 ns/op
PASS
ok      golang-coding-test/Lv1/running_race     0.214s
```
- 벤치마크 테스트에서 사용하는 데이터의 크기와 복잡성에 따라 두 함수의 성능 차이가 다를 수 있습니다. 특히 map을 사용하는 solution2 함수는 많은 양의 데이터를 처리할 때 성능 이점을 가지지만, 작은 데이터 세트에서는 오히려 오버헤드가 클 수 있습니다.

#### 데이터 셋이 많은 테스트함수 예시
- **주의사항** 
	- 프로그래머스 코드를 실행할 땐, 제한사항이 있어 적절한 값의 테스트 케이스 값이 만들어지지만, 실제 테스트케이스 데이터를 만들 경우 예외 값들을 잘 생각해 보아야합니다.
	- go 언어에서는 슬라이스 복사의 경우 기본적으로 얕은 복사를 수행하므로, 이를 고려해 보아야 합니다.
		- `얕은복사` : 슬라이스의 복사본이 원본 슬라이스와 같은 배열을 참조
```golang
const letterBytes = "abcdefghijklmnopqrstuvwxyz"

func randString(n int) string {
	b := make([]byte, n)
	for i := range b {
		b[i] = letterBytes[rand.Intn(len(letterBytes))]
	}
	return string(b)
}

func BenchmarkSolution3(b *testing.B) {
	// 100000의 players 생성
	rand.Seed(time.Now().UnixNano())
	numPlayers := 100000
	players := make([]string, numPlayers)
	for i := 0; i < numPlayers; i++ {
		players[i] = randString(8)
	}

	numCallings := 10
	callings := make([]string, numCallings)

	// Ensure that we don't select an index outside the range of 100 to 10000
	startIdx := 1000
	endIdx := 10000

	for i := 0; i < numCallings; i++ {
		randomIndex := startIdx + rand.Intn(endIdx-startIdx+1)
		callings[i] = players[randomIndex]
	}

	for i := 0; i < b.N; i++ {
		// 깊은 복사
		p := make([]string, len(players))
		c := make([]string, len(callings))
		copy(p, players)
		copy(c, callings)
		

		b.ResetTimer() // 벤치마크 타이머를 리셋하여 준비 작업의 시간을 제외합니다.
		_ = solution2(p, c)
		b.StopTimer() // 벤치마크 타이머를 멈추고 후처리 작업을 할 수 있습니다.
	}
}
```
- `b.ResetTimer()` 작동 방식
	- 타이머 리셋: b.ResetTimer()를 호출하면, 이후의 코드 실행 시간이 벤치마크 측정에 포함됩니다. 이전의 코드 실행 시간(준비 작업 등)은 측정에 포함되지 않습니다.
	- 측정 시점: b.ResetTimer()를 호출한 시점부터 벤치마크 루프의 각 반복에서 b.N만큼의 반복에 대해 정확한 시간을 측정합니다.