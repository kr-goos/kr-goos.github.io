---
title: "[Golang] 프로그래머스 Lv.1 2021 카카오 채용연계형 인턴십 숫자 문자열과 영단어"
description: 숫자 문자열과 영단어 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-10 22:40:03 +09000
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
네오와 프로도가 숫자놀이를 하고 있습니다. 네오가 프로도에게 숫자를 건넬 때 일부 자릿수를 영단어로 바꾼 카드를 건네주면 프로도는 원래 숫자를 찾는 게임입니다.

다음은 숫자의 일부 자릿수를 영단어로 바꾸는 예시입니다.

- 1478 → "one4seveneight"
- 234567 → "23four5six7"
- 10203 → "1zerotwozero3"
이렇게 숫자의 일부 자릿수가 영단어로 바뀌어졌거나, 혹은 바뀌지 않고 그대로인 문자열 `s`가 매개변수로 주어집니다. `s`가 의미하는 원래 숫자를 return 하도록 solution 함수를 완성해주세요.

참고로 각 숫자에 대응되는 영단어는 다음 표와 같습니다.

|숫자|영단어|
|-|-|
|0|zero|
|1|one|
|2|two|
|3|three|
|4|four|
|5|five|
|6|six|
|7|seven|
|8|eight|
|9|nine|


## 제한사항
- 1 ≤ `s`의 길이 ≤ 50
- `s`가 "zero" 또는 "0"으로 시작하는 경우는 주어지지 않습니다.
- return 값이 1 이상 2,000,000,000 이하의 정수가 되는 올바른 입력만 `s`로 주어집니다.


## 입출력 예
{% raw %}
|s|result|
|-|-|
|"one4seveneight"|1478|
|"23four5six7"|234567|
|"2three45sixseven"|234567|
|"123"|123|
{% endraw %}


## 입출력 예 설명
**입출력 예 #1**

- 문제 예시와 같습니다.

**입출력 예 #2**

- 문제 예시와 같습니다.

**입출력 예 #3**

- "three"는 3, "six"는 6, "seven"은 7에 대응되기 때문에 정답은 입출력 예 #2와 같은 234567이 됩니다.
- 입출력 예 #2와 #3과 같이 같은 정답을 가리키는 문자열이 여러 가지가 나올 수 있습니다.

**입출력 예 #4**

- `s`에는 영단어로 바뀐 부분이 없습니다.


## 풀이 
```golang
func solution(s string) int {

	for i, v := range initEnglishArray() {
		if !strings.Contains(s, v) {
			continue
		}
		s = strings.ReplaceAll(s, v, fmt.Sprintf("%d", i))
	}

	v, err := strconv.Atoi(s)
	if err != nil {
		panic(err)
	}

	return v
}

func initEnglishArray() []string {
	return []string{
		"zero",
		"one",
		"two",
		"three",
		"four",
		"five",
		"six",
		"seven",
		"eight",
		"nine",
	}
}
```

- 영단어 표에 규칙이 없었다면, Map 을 이용하여 각 문자열에 해당하는 값을 찾았겠지만, 각 문자열에 해당하는 값이 배열의 index로 표현 가능한 값이므로 index를 사용하여 슬라이스 초기화

- 초기화가 된 슬라이스를 순회
	- **주의** : 만약 배열 또는 Map 을 사용하지 않고, 각 문자열을 순회하며 각 문자에 대해 영단어를 순회하며 체크한다면, 최대 m*n 번 순회를 할 수 있음
	- 슬라이스의 요소가 포함되지 않는다면 다음 슬라이스의 요소를 확인
	- 슬라이스의 요소가 포함된다면, **모든** 슬라이스의 요소(`v`)를, 슬라이스의 인덱스(`i`)를 문자열로 변환한 값으로 치환 
- `s` 에 존재하는 모든 문자열이 숫자로 치환된다면, s 문자열을 int 자료형으로 변환하고 리턴


### 실수가 나올 수 있는 경우

```golang
idx := strings.Index(s, v)
if idx == -1 {
	continue
}
s = fmt.Sprintf("%s%d%s", s[:idx], i, s[idx+len(v):])
```

- 현재 문제에선 같은 문자열이 여러 번 나올 수 있는 경우에 대한 예시나 설명이 없어, 위와 같은 상황에 대해 고려하지 않았을 수 있습니다.
- 만약 위와같이 index 를 찾고, 인덱스의 요소의 값을 치환하려 할 경우 해당 문자가 중복으로 존재하게 되었을 때 모든 문자열에 대해 치환되지 않아 정확한 답을 얻지 못할 수 있습니다.


## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 4

var (
	s = [TESTCOUNT]string{
		"one4seveneight",
		"23four5six7",
		"2three45sixseven",
		"123",
	}

	result = []int{
		1478,
		234567,
		234567,
		123,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Printf("===== TEST %d =====\n", i)
		fmt.Printf("s : %v\n", s[i])
		fmt.Printf("answer : %v\n", result[i])
		fmt.Printf("solution result : %v\n", solution(s[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
===== TEST 0 =====
s : one4seveneight
answer : 1478
solution result : 1478
===== TEST 1 =====
s : 23four5six7
answer : 234567
solution result : 234567
===== TEST 2 =====
s : 2three45sixseven
answer : 234567
solution result : 234567
===== TEST 3 =====
s : 123
answer : 123
solution result : 123
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/numericstrings_and_english       0.143s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/numericstrings_and_english)