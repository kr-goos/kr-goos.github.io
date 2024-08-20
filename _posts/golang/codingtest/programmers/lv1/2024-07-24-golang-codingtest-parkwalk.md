---
title: "[Golang] 프로그래머스 Lv.1 공원 산책"
description: 공원 산책 문제에 대해 풀이하고 테스트 코드를 작성 해보겠습니다.
author: 김우석
date: 2024-07-24 13:00:00 +0900
categories: [Golang, CodingTest]
tags: [Golang, Go, Go언어, 코딩테스트, UnitTest, BenchmarkTest, Testcode]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 문제 설명
지나다니는 길을 'O', 장애물을 'X'로 나타낸 직사각형 격자 모양의 공원에서 로봇 강아지가 산책을 하려합니다. 산책은 로봇 강아지에 미리 입력된 명령에 따라 진행하며, 명령은 다음과 같은 형식으로 주어집니다.
- ["방향 거리", "방향 거리" … ]

예를 들어 "E 5"는 로봇 강아지가 현재 위치에서 동쪽으로 5칸 이동했다는 의미입니다. 로봇 강아지는 명령을 수행하기 전에 다음 두 가지를 먼저 확인합니다.
- 주어진 방향으로 이동할 때 공원을 벗어나는지 확인합니다.
- 주어진 방향으로 이동 중 장애물을 만나는지 확인합니다.

위 두 가지중 어느 하나라도 해당된다면, 로봇 강아지는 해당 명령을 무시하고 다음 명령을 수행합니다.
공원의 가로 길이가 W, 세로 길이가 H라고 할 때, 공원의 좌측 상단의 좌표는 (0, 0), 우측 하단의 좌표는 (H - 1, W - 1) 입니다.
![problem](../../../../../assets/img/posts/golang/codingtest/golang-codingtest-parkwalk/problem.png)
공원을 나타내는 문자열 배열 `park`, 로봇 강아지가 수행할 명령이 담긴 문자열 배열 `routes가` 매개변수로 주어질 때, 로봇 강아지가 모든 명령을 수행 후 놓인 위치를 [세로 방향 좌표, 가로 방향 좌표] 순으로 배열에 담아 return 하도록 solution 함수를 완성해주세요.

## 제한사항
- 3 ≤ `park의` 길이 ≤ 50
	- 3 ≤ `park[i]`의 길이 ≤ 50
		- `park[i]`는 다음 문자들로 이루어져 있으며 시작지점은 하나만 주어집니다.
			- S : 시작 지점
			- O : 이동 가능한 통로
			- X : 장애물
	- `park`는 직사각형 모양입니다.
- 1 ≤ `routes`의 길이 ≤ 50
	- `routes`의 각 원소는 로봇 강아지가 수행할 명령어를 나타냅니다.
	- 로봇 강아지는 `routes`의 첫 번째 원소부터 순서대로 명령을 수행합니다.
	- `routes`의 원소는 "op n"과 같은 구조로 이루어져 있으며, op는 이동할 방향, n은 이동할 칸의 수를 의미합니다.
		- op는 다음 네 가지중 하나로 이루어져 있습니다.
			- N : 북쪽으로 주어진 칸만큼 이동합니다.
			- S : 남쪽으로 주어진 칸만큼 이동합니다.
			- W : 서쪽으로 주어진 칸만큼 이동합니다.
			- E : 동쪽으로 주어진 칸만큼 이동합니다.
		- 1 ≤ n ≤ 9

## 입출력 예
{% raw %}
|park|routes|result|
|-|-|-|
|["SOO","OOO","OOO"]|["E 2","S 2","W 1"]|[2,1]|
|["SOO","OXX","OOO"]|["E 2","S 2","W 1"]|[0,1]|
|["OSO","OOO","OXO","OOO"]|["E 2","S 3","W 1"]|[0,0]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

입력된 명령대로 동쪽으로 2칸, 남쪽으로 2칸, 서쪽으로 1칸 이동하면 [0,0] -> [0,2] -> [2,2] -> [2,1]이 됩니다.

**입출력 예 #2**

입력된 명령대로라면 동쪽으로 2칸, 남쪽으로 2칸, 서쪽으로 1칸 이동해야하지만 남쪽으로 2칸 이동할 때 장애물이 있는 칸을 지나기 때문에 해당 명령을 제외한 명령들만 따릅니다. 결과적으로는 [0,0] -> [0,2] -> [0,1]이 됩니다.

**입출력 예 #3**

처음 입력된 명령은 공원을 나가게 되고 두 번째로 입력된 명령 또한 장애물을 지나가게 되므로 두 입력은 제외한 세 번째 명령만 따르므로 결과는 다음과 같습니다. [0,1] -> [0,0]

## 풀이 
```golang
const (
	// park constant
	START      = "S"
	POSSIBLE   = "O"
	IMPOSSIBLE = "X"

	// moving direction constant
	EAST  = "E"
	WEST  = "W"
	NORTH = "N"
	SOUTH = "S"
)

type PositionIndex struct {
	row, col int
}

type Park struct {
	Map           [][]string
	rowLiMitIndex int
	colLiMitIndex int
}

type Robot struct {
	current PositionIndex
	park    Park
}

func NewRobot() *Robot {
	return &Robot{current: PositionIndex{row: -1, col: -1}}
}

func (r *Robot) GetPositionIndex() []int {
	return []int{r.current.row, r.current.col}
}

func (r *Robot) SetParkPosition(park []string) {
	r.park.Map = make([][]string, len(park))
	r.park.rowLiMitIndex = len(park) - 1

	for rowIdx, rowStr := range park {
		if r.park.colLiMitIndex <= 0 {
			r.park.colLiMitIndex = len(rowStr) - 1
		}
		r.park.Map[rowIdx] = make([]string, len(rowStr))

		for colIdx := 0; colIdx < len(rowStr); colIdx++ {
			v := string(rowStr[colIdx])
			if v == START {
				r.current.row = rowIdx
				r.current.col = colIdx
			}
			r.park.Map[rowIdx][colIdx] = v
		}
	}
}

func (r *Robot) IsMovePossible(cmd string, distance int) bool {
	if r.current.row == -1 || r.current.col == -1 {
		return false
	}

	switch cmd {
	case EAST:
		if r.current.col+distance > r.park.colLiMitIndex {
			return false
		}

		for i := 1; i <= distance; i++ {
			if r.park.Map[r.current.row][r.current.col+i] == IMPOSSIBLE {
				return false
			}
		}

	case WEST:
		if r.current.col-distance < 0 {
			return false
		}

		for i := 1; i <= distance; i++ {
			if r.park.Map[r.current.row][r.current.col-i] == IMPOSSIBLE {
				return false
			}
		}
	case SOUTH:
		if r.current.row+distance > r.park.rowLiMitIndex {
			return false
		}

		for i := 1; i <= distance; i++ {
			if r.park.Map[r.current.row+i][r.current.col] == IMPOSSIBLE {
				return false
			}
		}

	case NORTH:
		if r.current.row-distance < 0 {
			return false
		}

		for i := 1; i <= distance; i++ {
			if r.park.Map[r.current.row-i][r.current.col] == IMPOSSIBLE {
				return false
			}
		}

	default:
		// just in case..
		return false
	}
	return true

}

func (r *Robot) Move(cmd string, distance int) {
	switch cmd {
	case EAST:
		r.current.col += distance
	case WEST:
		r.current.col -= distance
	case SOUTH:
		r.current.row += distance
	case NORTH:
		r.current.row -= distance
	}
}

func solution(park []string, routes []string) []int {
	r := NewRobot()
	r.SetParkPosition(park)

	for _, route := range routes {
		s := strings.Split(route, " ")
		distance, _ := strconv.Atoi(s[1])

		if r.IsMovePossible(s[0], distance) {
			r.Move(s[0], distance)
		}
	}

	return r.GetPositionIndex()
}
```
- 로봇의 상태를 나타내는 구조체를 생성하여 인스턴스마다 자신이 처리해야 할 공원과 자신의 현재 위치를 저장
- 로봇 구조체는 여러 메서드를 가짐
	- **GetPositionIndex** : 자신의 index 위치를 가져옴
	- **SetParkPosition** : 공원의 지도를 설정하고 로봇의 시작 위치를 찾음
	- **IsMovePossible** : 주어진 방향(`cmd`)과 거리(`distance`)로 이동이 가능한지 확인
	- **Move** : 주어진 방향(`cmd`)과 거리(`distance`)로 로봇을 이동

## 테스트코드
### 유닛테스트
```golang
func TestSetParkPosition(t *testing.T) {
	parkTestcases := [][]string{
		{"SOO", "OOO", "OOO"},
		{"SOO", "OXX", "OOO"},
		{"OSO", "OOO", "OXO", "OOO"},
	}

	r := NewRobot()

	for _, tc := range parkTestcases {
		fmt.Println("testcase : ", tc)
		r.SetParkPosition(tc)
		fmt.Println("Map : ", r.park.Map)
	}

}

func TestIsMovePossible(t *testing.T) {
	r := NewRobot()

	cmdTestcase := []string{"E", "N", "S", "W"}
	distanceTestcase := []int{1, 3, 2, 5}

	for i := range cmdTestcase {
		fmt.Printf("%s %d is move possible %t\n", cmdTestcase[i], distanceTestcase[i], r.IsMovePossible(cmdTestcase[i], distanceTestcase[i]))
	}

}

func TestSolution(t *testing.T) {
	parkTestcase := []string{
		"SOO", "OXX", "OOO",
	}
	routesTestcase := []string{
		"E 2", "S 2", "W 1",
	}

	successResult := []int{0, 1}
	result := solution(parkTestcase, routesTestcase)
	for i, s := range successResult {

		if s == result[i] {
			fmt.Printf("success : %d\n", s)
		} else {
			log.Printf("failure : %d, successResult : %d\n", result[i], s)
		}
	}
}
```

```bash
$ go test -v
=== RUN   TestSetParkPosition
testcase :  [SOO OOO OOO]
Map :  [[S O O] [O O O] [O O O]]        
testcase :  [SOO OXX OOO]
Map :  [[S O O] [O X X] [O O O]]        
testcase :  [OSO OOO OXO OOO]
Map :  [[O S O] [O O O] [O X O] [O O O]]
--- PASS: TestSetParkPosition (0.00s)   
=== RUN   TestIsMovePossible
E 1 is move possible false
N 3 is move possible false
S 2 is move possible false
W 5 is move possible false
--- PASS: TestIsMovePossible (0.00s)    
=== RUN   TestSolution
success : 0
success : 1
--- PASS: TestSolution (0.00s)
PASS
ok      golang-coding-test/Lv1/park_walk        0.193s
```

## [Github 바로가기](https://github.com/kr-goos/golang-coding-test/tree/master/programmers/Lv1/park_walk)