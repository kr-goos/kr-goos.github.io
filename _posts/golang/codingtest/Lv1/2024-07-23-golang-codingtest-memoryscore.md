---
title: "[Golang] 프로그래머스 Lv.1 추억점수"
description: 추억점수 문제에 대해 풀이하고, 테스트해 보겠습니다.
author: 김우석
date: 2024-07-23 08:45:00 +0900
categories: [Golang, Cache]
tags: [Golang, Go, Go언어, 코딩테스트]
image:
  path: /assets/img/posts/window-golang-install/golang.svg
---

## 문제 설명
사진들을 보며 추억에 젖어 있던 루는 사진별로 추억 점수를 매길려고 합니다. 사진 속에 나오는 인물의 그리움 점수를 모두 합산한 값이 해당 사진의 추억 점수가 됩니다. 예를 들어 사진 속 인물의 이름이 ["may", "kein", "kain"]이고 각 인물의 그리움 점수가 [5점, 10점, 1점]일 때 해당 사진의 추억 점수는 16(5 + 10 + 1)점이 됩니다. 다른 사진 속 인물의 이름이 ["kali", "mari", "don", "tony"]이고 ["kali", "mari", "don"]의 그리움 점수가 각각 [11점, 1점, 55점]]이고, "tony"는 그리움 점수가 없을 때, 이 사진의 추억 점수는 3명의 그리움 점수를 합한 67(11 + 1 + 55)점입니다.

그리워하는 사람의 이름을 담은 문자열 배열 `name`, 각 사람별 그리움 점수를 담은 정수 배열 `yearning`, 각 사진에 찍힌 인물의 이름을 담은 이차원 문자열 배열 `photo`가 매개변수로 주어질 때, 사진들의 추억 점수를 `photo`에 주어진 순서대로 배열에 담아 return하는 solution 함수를 완성해주세요.

## 제한사항
- 3 ≤ `name`의 길이 = `yearning`의 길이≤ 100
    - 3 ≤ `name`의 원소의 길이 ≤ 7
    - `name`의 원소들은 알파벳 소문자로만 이루어져 있습니다.
    - `name`에는 중복된 값이 들어가지 않습니다.
    - 1 ≤ `yearning[i]` ≤ 100
    - `yearning[i]`는 i번째 사람의 그리움 점수입니다.
- 3 ≤ `photo의` 길이 ≤ 100
    - 1 ≤ `photo[i]`의 길이 ≤ 100
    - 3 ≤ `photo[i]`의 원소(문자열)의 길이 ≤ 7
    - `photo[i]`의 원소들은 알파벳 소문자로만 이루어져 있습니다.
    - `photo[i]`의 원소들은 중복된 값이 들어가지 않습니다.

## 입출력 예
{% raw %}
|name|yearning|photo|result|
|-|-|-|-|
|["may", "kein", "kain", "radi"]|[5, 10, 1, 3]|[["may", "kein", "kain", "radi"],["may", "kein", "brin", "deny"], ["kon", "kain", "may", "coni"]]|[19, 15, 6]|
|["kali", "mari", "don"]|[11, 1, 55]|[["kali", "mari", "don"], ["pony", "tom", "teddy"], ["con", "mona", "don"]]|[67, 0, 55]|
|["may", "kein", "kain", "radi"]|[5, 10, 1, 3]|[["may"],["kein", "deny", "may"], ["kon", "coni"]]|[5, 15, 0]|
{% endraw %}

## 입출력 예 설명
입출력 예 #1

첫 번째 사진 속 "may", "kein", "kain", "radi"의 그리움 점수를 합치면 19(5 + 10 + 1 + 3)점 입니다. 두 번째 사진 속 그리워하는 사람들인 "may"와 "kein"의 그리움 점수를 합치면 15(5 + 10)점입니다. 세 번째 사진의 경우 "kain"과 "may"만 그리워하므로 둘의 그리움 점수를 합한 6(1 + 5)점이 사진의 추억 점수입니다. 따라서 [19, 15, 6]을 반환합니다.

입출력 예 #2

첫 번째 사진 속 그리워하는 사람들인 "kali", "mari", "don"의 그리움 점수를 합치면 67(11 + 1 + 55)점입니다. 두 번째 사진 속엔 그리워하는 인물이 없으므로 0점입니다. 세 번째 사진 속 그리워하는 사람은 "don"만 있으므로 55점입니다. 따라서 [67, 0, 55]를 반환합니다.

## 풀이 [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/memory_score)
### 파라미터를 그대로 이용한 방법
```golang
func solution(name []string, yearning []int, photo [][]string) []int {

	memScoreArr := make([]int, len(photo))

	for i := range photo {
		for _, personName := range photo[i] {
			idx := findIndexInStringArray(name, personName)
			if idx == -1 {
				continue
			}
			memScoreArr[i] += yearning[idx]
		}
	}

	return memScoreArr
}

func findIndexInStringArray(strArr []string, target string) int {

	for i, str := range strArr {
		if str == target {
			return i
		}
	}

	return -1
}
```
- findIndexInStringArray 함수를 사용하여 각 photo 배열의 이름에 대한 인덱스를 찾음
- 이름을 찾기 위해 name 배열을 순회하며, 이는 `O(n)의 시간 복잡도`를 가지므로, 전체 photo 배열을 순회하면서 각 이름을 찾는 작업이 비효율적일 수 있음
- 인덱스를 찾은 후, 그 인덱스를 사용하여 yearning 배열에서 점수를 가져오고, 이를 memScoreArr에 추가


### 파라미터의 자료구조를 map 으로 변형한 방법
```golang
func solution2(name []string, yearning []int, photo [][]string) []int {

	infoMap := make(map[string]int, len(name))
	for i, n := range name {
		infoMap[n] = yearning[i]
	}

	memScoreArr := make([]int, len(photo))
	for i := range photo {
		for _, personName := range photo[i] {
			memScoreArr[i] += infoMap[personName]
		}
	}

	return memScoreArr
}
```
- name 배열과 yearning 배열을 사용하여 map[string]int 구조체를 생성합니다. 여기서 map의 키는 이름, 값은 연령
- photo 배열을 순회하면서 이름을 map에서 직접 조회
- map의 조회는 평균적으로 `O(1)의 시간 복잡도`를 가지므로, 각 이름의 점수를 빠르게 가져올 수 있음

## 유닛 & 벤치마크 테스트
### 유닛 테스트
```golang
{% raw %}
func TestSolution(t *testing.T) {
	const TEST_COUNT = 3
    
	nameTestCases := [TEST_COUNT][]string{
		{"may", "kein", "kain", "radi"},
		{"kali", "mari", "don"},
		{"may", "kein", "kain", "radi"},
	}
	yearningTestCases := [TEST_COUNT][]int{
		{5, 10, 1, 3},
		{11, 1, 55},
		{5, 10, 1, 3},
	}

	photoTestCases := [TEST_COUNT][][]string{
		{{"may", "kein", "kain", "radi"}, {"may", "kein", "brin", "deny"}, {"kon", "kain", "may", "coni"}},
		{{"kali", "mari", "don"}, {"pony", "tom", "teddy"}, {"con", "mona", "don"}},
		{{"may"}, {"kein", "deny", "may"}, {"kon", "coni"}},
	}
    
	successResult := [TEST_COUNT][]int{
		{19, 15, 6},
		{67, 0, 55},
		{5, 15, 0},
	}

	for i := 0; i < TEST_COUNT; i++ {
		fmt.Println("nameTestCase :", nameTestCases[i])
		fmt.Println("yearningTestCase :", yearningTestCases[i])
		fmt.Println("photoTestCase :", photoTestCases[i])
		result := solution(nameTestCases[i], yearningTestCases[i], photoTestCases[i])
		fmt.Println("result :=", result)
		fmt.Println("successResult :=", successResult[i])
	}

}

func TestSolution2(t *testing.T) {
	const TEST_COUNT = 3
    
	nameTestCases := [TEST_COUNT][]string{
		{"may", "kein", "kain", "radi"},
		{"kali", "mari", "don"},
		{"may", "kein", "kain", "radi"},
	}
	yearningTestCases := [TEST_COUNT][]int{
		{5, 10, 1, 3},
		{11, 1, 55},
		{5, 10, 1, 3},
	}

	photoTestCases := [TEST_COUNT][][]string{
		{{"may", "kein", "kain", "radi"}, {"may", "kein", "brin", "deny"}, {"kon", "kain", "may", "coni"}},
		{{"kali", "mari", "don"}, {"pony", "tom", "teddy"}, {"con", "mona", "don"}},
		{{"may"}, {"kein", "deny", "may"}, {"kon", "coni"}},
	}

	successResult := [TEST_COUNT][]int{
		{19, 15, 6},
		{67, 0, 55},
		{5, 15, 0},
	}

	for i := 0; i < TEST_COUNT; i++ {
		fmt.Println("nameTestCase :", nameTestCases[i])
		fmt.Println("yearningTestCase :", yearningTestCases[i])
		fmt.Println("photoTestCase :", photoTestCases[i])
		result := solution2(nameTestCases[i], yearningTestCases[i], photoTestCases[i])
		fmt.Println("result :=", result)
		fmt.Println("successResult :=", successResult[i])
	}

}
{% endraw %}
```

```bash
$ go test
nameTestCase : [may kein kain radi]
yearningTestCase : [5 10 1 3]
photoTestCase : [[may kein kain radi] [may kein brin deny] [kon kain may coni]]
result := [19 15 6]
successResult := [19 15 6]
nameTestCase : [kali mari don]
yearningTestCase : [11 1 55]
photoTestCase : [[kali mari don] [pony tom teddy] [con mona don]]
result := [67 0 55]
successResult := [67 0 55]
nameTestCase : [may kein kain radi]
yearningTestCase : [5 10 1 3]
photoTestCase : [[may] [kein deny may] [kon coni]]
result := [5 15 0]
successResult := [5 15 0]
nameTestCase : [may kein kain radi]
yearningTestCase : [5 10 1 3]
photoTestCase : [[may kein kain radi] [may kein brin deny] [kon kain may coni]]
result := [19 15 6]
successResult := [19 15 6]
nameTestCase : [kali mari don]
yearningTestCase : [11 1 55]
photoTestCase : [[kali mari don] [pony tom teddy] [con mona don]]
result := [67 0 55]
successResult := [67 0 55]
nameTestCase : [may kein kain radi]
yearningTestCase : [5 10 1 3]
photoTestCase : [[may] [kein deny may] [kon coni]]
result := [5 15 0]
successResult := [5 15 0]
PASS
ok      golang-coding-test/Lv1/memory_score     0.130s
```

### 벤치마크 테스트
```golang
func BenchmarkSolution(b *testing.B) {
    
	nameTestCase := []string{
		"may", "kein", "kain", "radi",
	}
	yearningTestCase := []int{
		5, 10, 1, 3,
	}

	photoTestCase := [][]string{
		{"may", "kein", "kain", "radi"}, {"may", "kein", "brin", "deny"}, {"kon", "kain", "may", "coni"},
	}

	for i := 0; i < b.N; i++ {
		_ = solution(nameTestCase, yearningTestCase, photoTestCase)
	}
}

func BenchmarkSolution2(b *testing.B) {
    
	nameTestCase := []string{
		"may", "kein", "kain", "radi",
	}
	yearningTestCase := []int{
		5, 10, 1, 3,
	}

	photoTestCase := [][]string{
		{"may", "kein", "kain", "radi"}, {"may", "kein", "brin", "deny"}, {"kon", "kain", "may", "coni"},
	}

	for i := 0; i < b.N; i++ {
		_ = solution2(nameTestCase, yearningTestCase, photoTestCase)
	}
}

```

```bash
$ go test -bench . -benchtime 100000x
...
goos: windows
goarch: amd64
pkg: golang-coding-test/Lv1/memory_score
cpu: AMD EPYC 7642 48-Core Processor
BenchmarkSolution-4       100000               272.1 ns/op
BenchmarkSolution2-4      100000               369.8 ns/op
PASS
ok      golang-coding-test/Lv1/memory_score     0.225s
```
- 벤치마크 테스트에서 사용하는 데이터의 크기와 복잡성에 따라 두 함수의 성능 차이가 다를 수 있습니다. 특히 map을 사용하는 solution2 함수는 많은 양의 데이터를 처리할 때 성능 이점을 가지지만, 작은 데이터 세트에서는 오히려 오버헤드가 클 수 있습니다.