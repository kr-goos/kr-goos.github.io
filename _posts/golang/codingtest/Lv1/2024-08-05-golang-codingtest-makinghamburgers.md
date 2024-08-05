---
title: "[Golang] 프로그래머스 Lv.1 햄버거 만들기"
description: 햄버거 만들기 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-05 12:30:05 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
햄버거 가게에서 일을 하는 상수는 햄버거를 포장하는 일을 합니다. 함께 일을 하는 다른 직원들이 햄버거에 들어갈 재료를 조리해 주면 조리된 순서대로 상수의 앞에 아래서부터 위로 쌓이게 되고, 상수는 순서에 맞게 쌓여서 완성된 햄버거를 따로 옮겨 포장을 하게 됩니다. 상수가 일하는 가게는 정해진 순서(아래서부터, 빵 – 야채 – 고기 - 빵)로 쌓인 햄버거만 포장을 합니다. 상수는 손이 굉장히 빠르기 때문에 상수가 포장하는 동안 속 재료가 추가적으로 들어오는 일은 없으며, 재료의 높이는 무시하여 재료가 높이 쌓여서 일이 힘들어지는 경우는 없습니다.

예를 들어, 상수의 앞에 쌓이는 재료의 순서가 [야채, 빵, 빵, 야채, 고기, 빵, 야채, 고기, 빵]일 때, 상수는 여섯 번째 재료가 쌓였을 때, 세 번째 재료부터 여섯 번째 재료를 이용하여 햄버거를 포장하고, 아홉 번째 재료가 쌓였을 때, 두 번째 재료와 일곱 번째 재료부터 아홉 번째 재료를 이용하여 햄버거를 포장합니다. 즉, 2개의 햄버거를 포장하게 됩니다.

상수에게 전해지는 재료의 정보를 나타내는 정수 배열 `ingredient`가 주어졌을 때, 상수가 포장하는 햄버거의 개수를 return 하도록 solution 함수를 완성하시오.

## 제한사항
- 1 ≤ `ingredient`의 길이 ≤ 1,000,000
- `ingredient`의 원소는 1, 2, 3 중 하나의 값이며, 순서대로 빵, 야채, 고기를 의미합니다.

## 입출력 예
{% raw %}
| ingredient | result |
| --- | --- |
| \[2, 1, 1, 2, 3, 1, 2, 3, 1\] | 2 |
| \[1, 3, 2, 1, 2, 1, 3, 1, 2\] | 0 |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 문제의 예시와 같습니다.

**입출력 예 #2**

- 상수가 포장할 수 있는 햄버거가 없습니다.


## 풀이 
```golang
const (
	BREAD      = "1"
	VEGETABLE  = "2"
	PATTY      = "3"
	HAMBBERGER = BREAD + VEGETABLE + PATTY + BREAD
)

func solution(ingredient []int) int {

	var ingredientStr strings.Builder
	for _, v := range ingredient {
		ingredientStr.WriteString(strconv.Itoa(v))
	}

	var count int
	v := ingredientStr.String()
	for {
		i := strings.Index(v, HAMBBERGER)
		if i == -1 {
			break
		}
		count++
		v = v[:i] + v[i+len(HAMBBERGER):]
	}

	return count
}
```
- 상수 정의
	- `BREAD`, `VEGETABLE`, `PATTY`는 각각 문자열 "1", "2", "3"을 나타냄
	- `HAMBBERGER`는 햄버거의 재료 순서를 의미하는 "1231"로 정의
- 배열의 값을 문자열로 결합
	- `strings.Builder`를 사용하여 `ingredient` 배열을 문자열로 변환
	- `ingredient` 배열의 각 요소를 순회하면서 `strconv.Itoa` 함수를 사용하여 정수를 문자열로 변환하고, `ingredientStr`에 추가
- 햄버거 계산
	- `count` 변수는 만들어진 햄버거의 수를 저장
	- `ingredientStr.String()`을 호출하여 `strings.Builder`를 문자열로 변환하고, 이를 `v` 변수에 저장
	- 무한 루프를 사용하여 `v` 문자열에서 햄버거 패턴("1231")을 찾음
		- `strings.Index(v, HAMBBERGER)`는 v에서 `HAMBBERGER`("1231")의 시작 인덱스를 반환. 찾지 못하면 -1을 반환
		- `HAMBBERGER`("1231")를 찾을 때마다 `count`를 1 증가시키고, `v`에서 해당 부분을 제거하여 새로운 문자열을 만듦
- 더 이상 `HAMBBERGER`("1231")를 찾지 못하면 루프를 종료

### strings.Builder 를 사용하자
-  문자열을 효율적으로 연결할 수 있도록 설계되었습니다. 문자열을 반복적으로 연결하거나 추가할 때 새로운 문자열을 생성하지 않기 때문에 메모리 할당과 복사가 줄어들어 성능이 개선됩니다.


## 테스트코드
### 유닛 테스트
```golang
const (
	TESTCOUNT = 2
)

var (
	ingredient = [][]int{
		{2, 1, 1, 2, 3, 1, 2, 3, 1},
		{1, 3, 2, 1, 2, 1, 3, 1, 2},
	}

	result = []int{
		2, 0,
	}
)

func TestSolution(t *testing.T) {
	for i := 0; i < TESTCOUNT; i++ {
		fmt.Println("ingredient[i] : ", ingredient[i])
		fmt.Println("result	 : ", result[i])
		fmt.Println("my solution result : ", solution(ingredient[i]))
	}
}
```

```bash
$ go test -v
=== RUN   TestSolution
ingredient[i] :  [2 1 1 2 3 1 2 3 1]
result   :  2
my solution result :  2
ingredient[i] :  [1 3 2 1 2 1 3 1 2]
result   :  0
my solution result :  0
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/making_hamburgers        0.192s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/Lv1/making_hamburgers)