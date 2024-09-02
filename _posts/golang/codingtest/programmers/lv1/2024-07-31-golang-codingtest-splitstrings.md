---
title: "[Golang] 프로그래머스 Lv.1 문자열 나누기"
description: 문자열 나누기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-31 09:00:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
문자열 `s`가 입력되었을 때 다음 규칙을 따라서 이 문자열을 여러 문자열로 분해하려고 합니다.

- 먼저 첫 글자를 읽습니다. 이 글자를 x라고 합시다.
- 이제 이 문자열을 왼쪽에서 오른쪽으로 읽어나가면서, x와 x가 아닌 다른 글자들이 나온 횟수를 각각 셉니다. 처음으로 두 횟수가 같아지는 순간 멈추고, 지금까지 읽은 문자열을 분리합니다.
- `s`에서 분리한 문자열을 빼고 남은 부분에 대해서 이 과정을 반복합니다. 남은 부분이 없다면 종료합니다.
- 만약 두 횟수가 다른 상태에서 더 이상 읽을 글자가 없다면, 역시 지금까지 읽은 문자열을 분리하고, 종료합니다.

문자열 `s`가 매개변수로 주어질 때, 위 과정과 같이 문자열들로 분해하고, 분해한 문자열의 개수를 return 하는 함수 solution을 완성하세요.

## 제한사항
- 1 ≤ `s`의 길이 ≤ 10,000
- `s`는 영어 소문자로만 이루어져 있습니다.

## 입출력 예
{% raw %}
|s|result|
|-|-|
|"banana"|3|
|"abracadabra"|6|
|"aaabbaccccabba"|3|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

`s`="banana"인 경우 ba - na - na와 같이 분해됩니다.

**입출력 예 #2**

`s`="abracadabra"인 경우 ab - ra - ca - da - br - a와 같이 분해됩니다.

**입출력 예 #3**

`s`="aaabbaccccabba"인 경우 aaabbacc - ccab - ba와 같이 분해됩니다.

## 풀이 
```golang
func solution(s string) int {

	var result int
	for i := 0; i < len(s); {
		var count, othercount int
		for j := i; j < len(s); j++ {
			if s[i] == s[j] {
				count++
			} else {
				othercount++
			}

			if count == othercount {
				i = j + 1
				result++
				break
			}
		}

		if count != othercount {
			result++
			break
		}
	}

	return result
}
```
- 초기화
	- `result`: 분해된 문자열의 개수를 저장하는 변수
	- `i`: 현재 문자열의 인덱스를 나타내는 변수
- 첫 번째 루프
	- `i`가 문자열 `s`의 길이보다 작을 때 계속 반복
	- `count`와 `othercount` 변수를 초기화
- 두 번째 루프
	- `j`가 `i`부터 시작해서 문자열 `s`의 끝까지 반복
	- `s[i]`와 `s[j]`가 같으면 `count`를 **증가**시키고, 다르면 `othercount`를 **증가**시킴
	- `count`와 `othercount`가 같아지면, 해당 부분 문자열을 분해하고, `i`를 `j+1`로 업데이트하여 다음 분해를 시작. 이 경우 result를 증가
- 비대칭 처리
	- 내부 루프가 끝난 후에도 `count`와 `othercount`가 같지 않으면, 남은 부분을 하나의 문자열로 간주하여 result를 증가시킴

## 테스트코드
### 유닛 테스트
```golang
func TestSolution(t *testing.T) {
	const TESTCOUNT = 3
	sTestcases := [TESTCOUNT]string{
		"banana",
		"abracadabra",
		"aaabbaccccabba",
	}

	successResults := [TESTCOUNT]int{
		3,
		6,
		3,
	}
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("s testcase : ", sTestcases[i])
		fmt.Println("success result : ", successResults[i])
		fmt.Println("result : ", solution(sTestcases[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestSolution
s testcase :  banana
success result :  3
result :  3
s testcase :  abracadabra
success result :  6
result :  6
s testcase :  aaabbaccccabba
success result :  3
result :  3
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/split_strings    0.188s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/split_strings)