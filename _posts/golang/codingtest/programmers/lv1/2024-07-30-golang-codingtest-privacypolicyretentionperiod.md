---
title: "[Golang] 프로그래머스 Lv.1 KAKAO BLIND RECRUITMENT 개인정보 수집 유효기간"
description: 개인정보 수집 유효기간 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-30 08:53:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.jpg
---

## 문제 설명
고객의 약관 동의를 얻어서 수집된 1~`n`번으로 분류되는 개인정보 `n`개가 있습니다. 약관 종류는 여러 가지 있으며 각 약관마다 개인정보 보관 유효기간이 정해져 있습니다. 당신은 각 개인정보가 어떤 약관으로 수집됐는지 알고 있습니다. 수집된 개인정보는 유효기간 전까지만 보관 가능하며, 유효기간이 지났다면 반드시 파기해야 합니다.

예를 들어, A라는 약관의 유효기간이 12 달이고, 2021년 1월 5일에 수집된 개인정보가 A약관으로 수집되었다면 해당 개인정보는 2022년 1월 4일까지 보관 가능하며 2022년 1월 5일부터 파기해야 할 개인정보입니다.
당신은 오늘 날짜로 파기해야 할 개인정보 번호들을 구하려 합니다.

**모든 달은 28일까지 있다고 가정합니다.**

다음은 오늘 날짜가 `2022.05.19`일 때의 예시입니다.
|약관 종류|유효기간|
|-|-|
|A|6 달|
|B|12 달|
|C|3 달|

|번호|개인정보 수집 일자|약관 종류|
|-|-|-|
|1|`2021.05.02`|A|
|2|`2021.07.01`|B|
|3|`2022.02.19`|C|
|4|`2022.02.20`|C|

- 첫 번째 개인정보는 A약관에 의해 2021년 11월 1일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 두 번째 개인정보는 B약관에 의해 2022년 6월 28일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.
- 세 번째 개인정보는 C약관에 의해 2022년 5월 18일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 네 번째 개인정보는 C약관에 의해 2022년 5월 19일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.

따라서 파기해야 할 개인정보 번호는 [1, 3]입니다.

오늘 날짜를 의미하는 문자열 `today`, 약관의 유효기간을 담은 1차원 문자열 배열 `terms`와 수집된 개인정보의 정보를 담은 1차원 문자열 배열 `privacies`가 매개변수로 주어집니다. 이때 파기해야 할 개인정보의 번호를 오름차순으로 1차원 정수 배열에 담아 return 하도록 solution 함수를 완성해 주세요.

## 제한사항
- `today`는 "`YYYY`.`MM`.`DD`" 형태로 오늘 날짜를 나타냅니다.
- 1 ≤ `terms`의 길이 ≤ 20
	- `terms`의 원소는 "`약관 종류` `유효기간`" 형태의 `약관 종류`와 `유효기간`을 공백 하나로 구분한 문자열입니다.
	- `약관 종류`는 `A`~`Z`중 알파벳 대문자 하나이며, `terms` 배열에서 `약관 종류`는 중복되지 않습니다.
	- `유효기간`은 개인정보를 보관할 수 있는 달 수를 나타내는 정수이며, 1 이상 100 이하입니다.
- 1 ≤ `privacies`의 길이 ≤ 100
	- `privacies[i]`는 `i+1`번 개인정보의 수집 일자와 약관 종류를 나타냅니다.
	- `privacies`의 원소는 "`날짜` `약관 종류`" 형태의 `날짜`와 `약관 종류`를 공백 하나로 구분한 문자열입니다.
	- `날짜`는 "`YYYY`.`MM`.`DD`" 형태의 개인정보가 수집된 날짜를 나타내며, `today` 이전의 날짜만 주어집니다.
	- `privacies`의 `약관 종류`는 항상 `terms`에 나타난 `약관 종류`만 주어집니다.
- `today`와 `privacies`에 등장하는 `날짜`의 `YYYY`는 연도, `MM`은 월, `DD`는 일을 나타내며 점(.) 하나로 구분되어 있습니다.
	- 2000 ≤ `YYYY` ≤ 2022
	- 1 ≤ `MM` ≤ 12
	- `MM`이 한 자릿수인 경우 앞에 0이 붙습니다.
	- 1 ≤ `DD` ≤ 28
	- `DD`가 한 자릿수인 경우 앞에 0이 붙습니다.
- 파기해야 할 개인정보가 하나 이상 존재하는 입력만 주어집니다.

## 입출력 예
{% raw %}
|today|terms|privacies|result|
|-|-|-|-|
|"2022.05.19"|["A 6", "B 12", "C 3"]|["2021.05.02 A", "2021.07.01 B", "2022.02.19 C", "2022.02.20 C"]|[1, 3]|
|"2020.01.01"|["Z 3", "D 5"]|["2019.01.01 D", "2019.11.15 Z", "2019.08.02 D", "2019.07.01 D", "2018.12.28 Z"]|[1, 4, 5]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 문제 예시와 같습니다.

**입출력 예 #2**

|약관 종류|유효기간|
|-|-|
|Z|3 달|
|D|5 달|

|번호|개인정보 수집 일자|약관 종류|
|-|-|-|
|1|`2019.01.01`|D|
|2|`2019.11.15`|Z|
|3|`2019.08.02`|D|
|4|`2019.07.01`|D|
|5|`2018.12.28`|Z|

오늘 날짜는 2020년 1월 1일입니다.

- 첫 번째 개인정보는 D약관에 의해 2019년 5월 28일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 두 번째 개인정보는 Z약관에 의해 2020년 2월 14일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.
- 세 번째 개인정보는 D약관에 의해 2020년 1월 1일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.
- 네 번째 개인정보는 D약관에 의해 2019년 11월 28일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 다섯 번째 개인정보는 Z약관에 의해 2019년 3월 27일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.

## 풀이 
```golang
func solution(today string, terms []string, privacies []string) []int {
	termsMap := make(map[string]int, len(terms))
	for _, term := range terms {
		s := strings.Split(term, " ")
		retentionPeriod, err := strconv.Atoi(s[1])
		if err != nil {
			return nil
		}
		termsMap[s[0]] = retentionPeriod
	}

	answer := []int{}
	for i, privacy := range privacies {

		s := strings.Split(privacy, " ")

		if dateToDays(s[0])+termsMap[s[1]]*28-1 < dateToDays(today) {
			answer = append(answer, i+1)
		}
	}

	return answer
}

func dateToDays(date string) int {
	t, err := time.Parse("2006.01.02", date)
	if err != nil {
		return -1
	}

	yDays := t.Year() * 12 * 28
	mDays := int(t.Month()) * 28
	return yDays + mDays + t.Day()
}
```
- `terms` 정보를 맵에 저장
	- `terms`배열의 각 항목을 `strings.Split(term, " ")`로 나누어 분리
	- split 결과로 나온 슬라이스의 `s[0] : 약관 종류` `s[1] : 유효기간` 결과로 맵에 저장( k : 약관 종류, v : 유효기간)
- 개인정보 유효기간 검사 및 결과 저장
	- 각 `privacy` 항목을 `strings.Split(privacy, " ")`로 나누어, 개인정보 수집일과 약관 종류를 추출
	- dateToDays 함수를 사용하여 생성일과 오늘 날짜를 일수로 변환하고, 계약 유형에 대한 유효기간을 28일 단위로 계산
	- `dateToDays(s[0])+termsMap[s[1]]*28-1 < dateToDays(today)` : 개인정보가 유효한 마지막 날짜를 계산
		- `dateToDays(s[0])`: 개인정보 수집일자를 일수로 변환한 값
		- `termsMap[s[1]]` : 유효기간(개월 수)
		- `termsMap[s[1]] * 28 -1` : 유효기간(개월 수) * 일 수(한달의 일 : 28) - 유효기간이 끝나는 날짜를 계산하기 위한 보정 값
	- 유효기간이 지나면, 해당 항목의 인덱스를 answer 리스트에 추가


## 테스트코드
### 유닛 테스트
```golang
func TestDateToDays(t *testing.T) {
	tc := time.Now().Format("2006.01.02")

	fmt.Println("testcaes : ", tc)
	v := dateToDays(tc)
	fmt.Println("result : ", v)
}

func TestSolution(t *testing.T) {
	const TCCOUNT = 2
	todayTestcases := [TCCOUNT]string{
		"2022.05.19",
		"2020.01.01",
	}
	termsTestcases := [TCCOUNT][]string{
		{"A 6", "B 12", "C 3"},
		{"Z 3", "D 5"},
	}
	privaciesTestcases := [TCCOUNT][]string{
		{"2021.05.02 A", "2021.07.01 B", "2022.02.19 C", "2022.02.20 C"},
		{"2019.01.01 D", "2019.11.15 Z", "2019.08.02 D", "2019.07.01 D", "2018.12.28 Z"},
	}

	successResults := [TCCOUNT][]int{
		{1, 3},
		{1, 4, 5},
	}
	for i := 0; i < TCCOUNT; i++ {
		fmt.Println("todayTestcase : ", todayTestcases[i])
		fmt.Println("termsTestcase : ", termsTestcases[i])
		fmt.Println("privaciesTestcase : ", privaciesTestcases[i])
		fmt.Println("successResults : ", successResults[i])
		fmt.Println("result : ", solution(todayTestcases[i], termsTestcases[i], privaciesTestcases[i]))
	}

}
```

```bash
$ go test -v
=== RUN   TestDateToDays
testcaes :  2024.07.30
result :  680290
--- PASS: TestDateToDays (0.07s)
=== RUN   TestSolution
todayTestcase :  2022.05.19
termsTestcase :  [A 6 B 12 C 3]
privaciesTestcase :  [2021.05.02 A 2021.07.01 B 2022.02.19 C 2022.02.20 C]
successResults :  [1 3]
result :  [1 3]
todayTestcase :  2020.01.01
termsTestcase :  [Z 3 D 5]
privaciesTestcase :  [2019.01.01 D 2019.11.15 Z 2019.08.02 D 2019.07.01 D 2018.12.28 Z]
successResults :  [1 4 5]
result :  [1 4 5]
--- PASS: TestSolution (0.01s)
PASS
ok      golang-coding-test/Lv1/privacypolicy_retentionperiod    0.291s
```

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/tree/master/programmers/Lv1/privacypolicy_retentionperiod)