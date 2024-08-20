---
title: "[Golang] 프로그래머스 Lv.1 K번째수"
description: K번째수 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-08-13 07:40:03 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.

예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면

1. array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.
2. 1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.
3. 2에서 나온 배열의 3번째 숫자는 5입니다.

배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- array의 길이는 1 이상 100 이하입니다.
- array의 각 원소는 1 이상 100 이하입니다.
- commands의 길이는 1 이상 50 이하입니다.
- commands의 각 원소는 길이가 3입니다.


## 입출력 예
{% raw %}
| array | commands | return |
| --- | --- | --- |
| \[1, 5, 2, 6, 3, 7, 4\] | \[\[2, 5, 3\], \[4, 4, 1\], \[1, 7, 3\]\] | \[5, 6, 3\] |
{% endraw %}


## 입출력 예 설명
[1, 5, 2, 6, 3, 7, 4]를 2번째부터 5번째까지 자른 후 정렬합니다. [2, 3, 5, 6]의 세 번째 숫자는 5입니다.

[1, 5, 2, 6, 3, 7, 4]를 4번째부터 4번째까지 자른 후 정렬합니다. [6]의 첫 번째 숫자는 6입니다.

[1, 5, 2, 6, 3, 7, 4]를 1번째부터 7번째까지 자릅니다. [1, 2, 3, 4, 5, 6, 7]의 세 번째 숫자는 3입니다.


## 풀이 
```golang
func solution(array []int, commands [][]int) []int {

	var answer []int
	for _, cmd := range commands {
		var temp []int
		temp = append(temp, array[cmd[0]-1:cmd[1]]...)
		sort.Ints(temp)
		answer = append(answer, temp[cmd[2]-1])
	}
	return answer
}
```

### 위 문제를 설명하기 앞서 슬라이스의 구조를 살펴봅시다.
슬라이스는 다음과 같은 세 가지 요소로 구성됩니다.

1. **포인터** : 슬라이스가 참조하는 배열의 시작 주소를 가리킵니다.
2. **길이** : 슬라이스의 현재 길이를 나타냅니다.
3. **용량** : 슬라이스가 참조하는 배열에서 사용할 수 있는 최대 공간을 나타냅니다.

**얕은 복사**

얕은 복사는 슬라이스의 포인터, 길이, 용량 정보만 복사하는 것을 의미합니다. 이 경우, 복사된 슬라이스와 원본 슬라이스는 **동일한 배열을 참조**하게 됩니다. 즉, 하나의 슬라이스에서 요소를 수정하면 다른 슬라이스에서도 그 변경 사항이 반영됩니다.

- 예) 위 문제에서 `temp := array[cmd[0]-1:cmd[1]]` 와 같이 작성한 경우

**깊은 복사**

깊은 복사는 슬라이스가 참조하는 배열 자체를 복사하는 것을 의미합니다. 이 경우, 복사된 슬라이스는 원본 슬라이스와 별개의 배열을 참조하게 됩니다. 따라서 한 슬라이스에서 요소를 수정해도 다른 슬라이스에는 영향을 미치지 않습니다.

```golang
var temp []int
temp = append(temp, array[cmd[0]-1:cmd[1]]...)
```

```golang
temp := make([]int, cmd[1]-cmd[0]+1)
copy(temp, array[cmd[0]-1:cmd[1]])
```


### 문제 풀이
- 만약 `temp := array[cmd[0]-1:cmd[1]]` 이런식으로 문제를 작성하였다면, 에러를 경험했을 것입니다.
	- 위 코드는 슬라이싱 된 array 와, temp 가 같은 주소를 공유함 (**얕은 복사**)
	- 그 결과 sort.Ints(temp) 를 하게 되면 원본 `array` 의 슬라이싱 되었던 요소들이 정렬됨
	- 예를들어 `i=2` `j=5` `k=3` 이라고 가정
		- temp = array[1:5] => [5, 2, 6, 3] (`array` 가 슬라이싱 되었으므로 tmep 와 같은 주소 공유)
		- sort.Ints(temp) => [2,3,5,6] , `array` [1, **2, 3, 5, 6**, 7, 4]
- 위 문제를 해결하기 위해 **깊은 복사**를 이용
	- `nil` 값을 가지고 있는 `temp`변수에 슬라이싱 된 array의 요소들을 넣어줌
		- `temp = append(temp, array[cmd[0]-1:cmd[1]]...)`
- `j` 요소를 가져오고, 결과 배열에 저장


## 테스트코드
### 유닛 테스트
```golang
var (
	array    = []int{1, 5, 2, 6, 3, 7, 4}
	commands = [][]int{
		{2, 5, 3}, {4, 4, 1}, {1, 7, 3},
	}
	result = []int{5, 6, 3}
)

func TestSolution(t *testing.T) {
	fmt.Printf("result : %v\n", result)
	fmt.Printf("solution result : %v\n", solution(array, commands))
}
```

```bash
$ go test -v
=== RUN   TestSolution
result : [5 6 3]
solution result : [5 6 3]
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/kth_number.go    0.183s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/kth_number)