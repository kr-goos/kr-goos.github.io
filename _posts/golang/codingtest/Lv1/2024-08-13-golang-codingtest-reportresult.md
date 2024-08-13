---
title: "[Golang] 프로그래머스 Lv.1 신고 결과 받기"
description: 2022 KAKAO BLIND RECRUITMENT 신고 결과 받기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 22:02:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
신입사원 무지는 게시판 불량 이용자를 신고하고 처리 결과를 메일로 발송하는 시스템을 개발하려 합니다. 무지가 개발하려는 시스템은 다음과 같습니다.

- 각 유저는 한 번에 한 명의 유저를 신고할 수 있습니다.
	- 신고 횟수에 제한은 없습니다. 서로 다른 유저를 계속해서 신고할 수 있습니다.
	- 한 유저를 여러 번 신고할 수도 있지만, 동일한 유저에 대한 신고 횟수는 1회로 처리됩니다.
- k번 이상 신고된 유저는 게시판 이용이 정지되며, 해당 유저를 신고한 모든 유저에게 정지 사실을 메일로 발송합니다.
	- 유저가 신고한 모든 내용을 취합하여 마지막에 한꺼번에 게시판 이용 정지를 시키면서 정지 메일을 발송합니다.
다음은 전체 유저 목록이 ["muzi", "frodo", "apeach", "neo"]이고, k = 2(즉, 2번 이상 신고당하면 이용 정지)인 경우의 예시입니다.

| 유저 ID  | 유저가 신고한 ID | 설명                               |
|----------|------------------|------------------------------------|
| "muzi"   | "frodo"          | "muzi"가 "frodo"를 신고했습니다.   |
| "apeach" | "frodo"          | "apeach"가 "frodo"를 신고했습니다. |
| "frodo"  | "neo"            | "frodo"가 "neo"를 신고했습니다.    |
| "muzi"   | "neo"            | "muzi"가 "neo"를 신고했습니다.     |
| "apeach" | "muzi"           | "apeach"가 "muzi"를 신고했습니다.  |

각 유저별로 신고당한 횟수는 다음과 같습니다.

| 유저 ID  | 신고당한 횟수 |
|----------|---------------|
| "muzi"   | 1             |
| "frodo"  | 2             |
| "apeach" | 0             |
| "neo"    | 2             |

위 예시에서는 2번 이상 신고당한 "frodo"와 "neo"의 게시판 이용이 정지됩니다. 이때, 각 유저별로 신고한 아이디와 정지된 아이디를 정리하면 다음과 같습니다.

| 유저 ID  | 유저가 신고한 ID  | 정지된 ID        |
|----------|-------------------|------------------|
| "muzi"   | ["frodo", "neo"]  | ["frodo", "neo"] |
| "frodo"  | ["neo"]           | ["neo"]          |
| "apeach" | ["muzi", "frodo"] | ["frodo"]        |
| "neo"    | 없음              | 없음             |

따라서 "muzi"는 처리 결과 메일을 2회, "frodo"와 "apeach"는 각각 처리 결과 메일을 1회 받게 됩니다.

이용자의 ID가 담긴 문자열 배열 `id_list`, 각 이용자가 신고한 이용자의 ID 정보가 담긴 문자열 배열 `report`, 정지 기준이 되는 신고 횟수 `k`가 매개변수로 주어질 때, 각 유저별로 처리 결과 메일을 받은 횟수를 배열에 담아 return 하도록 solution 함수를 완성해주세요.


## 제한사항
- 2 ≤ `id_list`의 길이 ≤ 1,000
	- 1 ≤ `id_list`의 원소 길이 ≤ 10
	- `id_list`의 원소는 이용자의 id를 나타내는 문자열이며 알파벳 소문자로만 이루어져 있습니다.
	- `id_list`에는 같은 아이디가 중복해서 들어있지 않습니다.
- 1 ≤ `report`의 길이 ≤ 200,000
	- 3 ≤ `report`의 원소 길이 ≤ 21
	- `report`의 원소는 "이용자id 신고한id"형태의 문자열입니다.
	- 예를 들어 "muzi frodo"의 경우 "muzi"가 "frodo"를 신고했다는 의미입니다.
	- id는 알파벳 소문자로만 이루어져 있습니다.
	- 이용자id와 신고한id는 공백(스페이스)하나로 구분되어 있습니다.
	- 자기 자신을 신고하는 경우는 없습니다.
- 1 ≤ `k` ≤ 200, `k`는 자연수입니다.
- return 하는 배열은 `id_list`에 담긴 id 순서대로 각 유저가 받은 결과 메일 수를 담으면 됩니다.




## 입출력 예

| id_list                            | report                                                             | k | result    |
|------------------------------------|--------------------------------------------------------------------|---|-----------|
| ["muzi", "frodo", "apeach", "neo"] | ["muzi frodo","apeach frodo","frodo neo","muzi neo","apeach muzi"] | 2 | [2,1,1,0] |
| ["con", "ryan"]                    | ["ryan con", "ryan con", "ryan con", "ryan con"]                   | 3 | [0,0]     |


## 입출력 예 설명
**입출력 예 #1**

- 문제의 예시와 같습니다.

**입출력 예 #2**

- "ryan"이 "con"을 4번 신고했으나, 주어진 조건에 따라 한 유저가 같은 유저를 여러 번 신고한 경우는 신고 횟수 1회로 처리합니다. 따라서 "con"은 1회 신고당했습니다. 3번 이상 신고당한 이용자는 없으며, "con"과 "ryan"은 결과 메일을 받지 않습니다. 따라서 [0, 0]을 return 합니다.

## 풀이 
```golang
func solution(id_list []string, report []string, k int) []int {
	reportedCountMap := make(map[string]int)
	reportedIdMap := make(map[string]map[string]struct{})
	for _, ids := range report {
		s := strings.Split(ids, " ")

		_, ok := reportedIdMap[s[0]]
		if !ok {
			reportedIdMap[s[0]] = make(map[string]struct{})
		} else {
			_, exist := reportedIdMap[s[0]][s[1]]
			if exist {
				continue
			}
		}
		reportedIdMap[s[0]][s[1]] = struct{}{}
		reportedCountMap[s[1]]++
	}

	suspendedId := map[string]struct{}{}

	for id, count := range reportedCountMap {
		if count >= k {
			suspendedId[id] = struct{}{}
		}
	}

	answer := make([]int, len(id_list))
	for i, id := range id_list {
		for reportedId, _ := range reportedIdMap[id] {
			if _, ok := suspendedId[reportedId]; ok {
				answer[i]++
			}
		}
	}

	return answer
}

```

- 변수 초기화
	- `reportedCountMap` : key : 유저가 신고한 ID , value : 신고 당한 횟수
	- `reportedIdMap`: key : 신고한 유저 ID , value : 신고 당한 유저 맵
- `report` 순회
	- `report` 의 요소를 Split 하여 s[0] : 신고한 유저 s[1] 신고 당한 유저 를 추출
	- `_, ok := reportedIdMap[s[0]]`
		- 신고당한 유저가 저장된 map 이 초기화되지 않았다면 초기화 후 빈 구조체 저장 & `reportedCountMap[s[1]]++`
		- 신고당한 유저가 저장된 map 이 초기화 되어있다면, 현재 신고당한 유저가 저장되었는지 확인
			- 저장되었다면 `continue` (문제 설명 : **한 유저를 여러 번 신고할 수도 있지만, 동일한 유저에 대한 신고 횟수는 1회로 처리**)
			- 저장되지 않았다면 빈 구조체 저장 & `reportedCountMap[s[1]]++`
- `report`를 순회하며 얻어낸 `reportedCountMap`를 순회
	- 신고 당한 횟수가 k 회 이상이면 정지된 사용자로 판단
- 유저가 받은 결과 메일 수를 저장할 `answer` 초기화
- `id_list`를 순회 (문제 설명 : **return 하는 배열은 id_list에 담긴 id 순서대로 각 유저가 받은 결과 메일 수를 담으면 됩니다.**)
	- `reportedIdMap[id]` 의 값(`map[string]struct{}` : 신고 당한 유저)을 이용하여 순회
		- `reportedId` 가 정지된 사용자(`suspendedId`) 로 판단(`_, ok := suspendedId[reportedId]; ok`)된다면 현재 `id_list` 의 순회중인 인덱스의 `answer` 값을 증가
- `answer` 반환


## 테스트코드
### 유닛 테스트
```golang
package reportresult

import (
	"fmt"
	"testing"
)

const TESTCOUNT = 2

var (
	id_list = [TESTCOUNT][]string{
		{"muzi", "frodo", "apeach", "neo"},
		{"con", "ryan"},
	}
	report = [TESTCOUNT][]string{
		{"muzi frodo", "apeach frodo", "frodo neo", "muzi neo", "apeach muzi"},
		{"ryan con", "ryan con", "ryan con", "ryan con"},
	}
	k      = [TESTCOUNT]int{2, 3}
	result = [TESTCOUNT][]int{
		{2, 1, 1, 0},
		{0, 0},
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("id_list : %v\n", id_list[i])
		fmt.Printf("report : %v\n", report[i])
		fmt.Printf("k : %d\n", k[i])
		fmt.Printf("result : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(id_list[i], report[i], k[i]))
	}
}

```

```bash
$ go test -v
=== RUN   TestSolution
id_list : [muzi frodo apeach neo]
report : [muzi frodo apeach frodo frodo neo muzi neo apeach muzi]
k : 2
result : [2 1 1 0]
solution result : [2 1 1 0]
id_list : [con ryan]
report : [ryan con ryan con ryan con ryan con]
k : 3
result : [0 0]
solution result : [0 0]
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/report_result    0.018s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/report_result)