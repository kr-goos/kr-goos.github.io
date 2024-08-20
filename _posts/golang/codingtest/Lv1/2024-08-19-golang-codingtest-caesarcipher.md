---
title: "[Golang] 프로그래머스 Lv.1 시저 암호"
description: 시저 암호 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-19 08:00:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
어떤 문장의 각 알파벳을 일정한 거리만큼 밀어서 다른 알파벳으로 바꾸는 암호화 방식을 시저 암호라고 합니다. 예를 들어 "AB"는 1만큼 밀면 "BC"가 되고, 3만큼 밀면 "DE"가 됩니다. "z"는 1만큼 밀면 "a"가 됩니다. 문자열 s와 거리 n을 입력받아 s를 n만큼 민 암호문을 만드는 함수, solution을 완성해 보세요.


## 제한사항
- 공백은 아무리 밀어도 공백입니다.
- s는 알파벳 소문자, 대문자, 공백으로만 이루어져 있습니다.
- s의 길이는 8000이하입니다.
- n은 1 이상, 25이하인 자연수입니다.


## 입출력 예
{% raw %}
| s | n | result |
| --- | --- | --- |
| "AB" | 1 | "BC" |
| "z" | 1 | "a" |
| "a B z" | 4 | "e F d" |
{% endraw %}


## 풀이 
```golang
func solution(s string, n int) string {

	runes := []rune(s)
	offset := rune(n)
	for i, r := range runes {
		if r >= 'a' && r <= 'z' {
			runes[i] = (r-'a'+offset)%26 + 'a'
		} else if r >= 'A' && r <= 'Z' {
			runes[i] = (r-'A'+offset)%26 + 'A'
		}
	}

	return string(runes)
}
```

- `s` 를 rune 슬라이스로, `n`을 rune 으로 치환
- `runes` 순회
	- `r` 이 `r >= 'a' && r <= 'z'` 범위 라면,
		- (r-'a'+offset)%26 : r 값에서 시작값인 'a' 를 빼주고, offset 만큼 더한 후 'a' ~ 'z' 개수(26)를 나누어 값이 범위를 넘어가던, 넘어가지 않던 더할 값을 구해냄 (값의 범위는 0 ~ 25)
		- 기준값 'a' + 위에서 구한 값 을 하여 `runes[i]` 에 저장
	- `r` 이 대문자 범위인 경우에도 동일하게 계산 수행
- `runes` 를 `string` 으로 치환하여 반환



### **`n`만큼 문자를 밀어 새로운 문자열을 만드는 방법 (상세)**

```golang
runes[i] = (r-'a'+offset)%26 + 'a'
```

- `r - 'a'`
	- 여기서 r은 현재 문자입니다.
	- `'a'` 는 소문자 `'a'`의 아스키 코드 값(혹은 유니코드 값)입니다.
	- `r - 'a'` 는 현재 문자가 `'a'`로부터 몇 번째 위치에 있는지를 계산합니다. 예를 들어, `r`이 `'b'`라면 `'b' - 'a'`는 `1`이 됩니다.
	- 이 단계는 현재 문자를 0부터 시작하는 인덱스(0부터 25까지의 값)로 변환합니다.
- `offset`
	- 여기서 `offset`은 주어진 `n` 값을 `rune`으로 변환한 값입니다. `n`만큼 문자를 밀어야 하므로, 인덱스에 이 `offset`을 더해줍니다.
- `% 26`
	- 이 연산은 알파벳이 26자로 이루어져 있기 때문에 사용됩니다.
	- `r - 'a' + offset`이 26 이상이 되면 다시 알파벳의 처음으로 돌아가도록 하기 위해서 `% 26` 연산을 합니다.
	- 예를 들어, `r`이 `'z'`이고 offset이 2라면 `('z' - 'a' + 2) % 26`은 25 + 2 = 27, 27 % 26 = 1이 되어, 결과는 알파벳의 두 번째 문자 `'b'`가 됩니다.
- `+ 'a'`
	- 이 부분은 다시 계산된 값을 문자로 변환하기 위해 사용됩니다.
	- `(r - 'a' + offset) % 26`는 0부터 25까지의 값이므로, 이 값을 `'a'`의 아스키 코드 값으로 더하여 원래의 문자 범위로 되돌립니다.
	- 예를 들어, 계산된 값이 `1`이라면, 여기에 `'a'`를 더하여 `'b'`가 됩니다.



## 테스트코드
### 유닛 테스트
```golang
const TESTCOUNT = 3

var (
	s = [TESTCOUNT]string{
		"AB",
		"z",
		"a B z",
	}

	n      = [TESTCOUNT]int{1, 1, 4}
	result = [TESTCOUNT]string{"BC", "a", "e F d"}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("s : ", s[i])
		fmt.Println("n : ", n[i])
		fmt.Println("result : ", result[i])
		fmt.Println("solution result : ", solution(s[i], n[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
s :  AB
n :  1
result :  BC
solution result :  BC
s :  z
n :  1
result :  a
solution result :  a
s :  a B z
n :  4
result :  e F d
solution result :  e F d      
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/lv1/caesar_cipher    0.355s
```


## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/caesar_cipher)