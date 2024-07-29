---
title: "[GO] Golang 유닛테스트 패키지 testify"
description: golang 에서 패키지를 이용해 유닛테스트 하는 방법을 알아보겠습니다.
author: 김우석
date: 2024-07-29 21:08:00 +0900
categories: [Golang, UnitTest]
tags: [Go, Golang, go언어, UnitTest]
image:
  path: /assets/img/posts/golang/golang.svg
---

## 유닛 테스트(Unit Test)란?
유닛 테스트(Unit Test)는 소프트웨어 테스트의 한 종류로, 프로그램의 개별 단위(unit)를 검증하는 절차를 말합니다. 여기서 단위는 일반적으로 함수, 메서드, 또는 클래스와 같은 최소 기능 단위를 의미합니다. 유닛 테스트의 주요 목적은 특정 코드가 예상대로 작동하는지 확인하는 것입니다.

## 주요 특징
1. 자동화: 유닛 테스트는 자동화되어 있어 개발자가 테스트를 직접 수행할 필요가 없습니다. 이는 빠르고 일관된 테스트 수행을 가능하게 합니다.
2. 반복 가능성: 유닛 테스트는 반복적으로 실행할 수 있으며, 이는 코드 변경 시 회귀 테스트를 통해 기존 기능이 정상 작동하는지 확인할 수 있게 합니다.
3. 격리: 유닛 테스트는 테스트 대상 코드가 외부 의존성에 영향을 받지 않도록 격리된 환경에서 실행됩니다. 이를 위해 모의 객체(mock objects)나 스텁(stubs)을 자주 사용합니다.

```
**스텁(Stub)**은 유닛 테스트에서 사용되는 테스트 더블(Test Double)의 한 종류로, 테스트 대상 코드가 의존하는 외부 컴포넌트나 시스템의 동작을 모방하는 객체입니다.

- 외부 시스템과의 의존성 제거: 테스트 대상 코드가 데이터베이스, 네트워크, 파일 시스템 등의 외부 시스템에 의존할 때, 이러한 의존성을 제거하여 테스트를 빠르고 안정적으로 수행할 수 있도록 합니다.

- 정해진 응답 반환: 테스트 대상 코드가 특정 조건에서 동작을 확인하기 위해, 스텁은 미리 정해진 응답을 반환하도록 설정됩니다.
```

## testify란?
 - `testify`는 Go 언어로 작성된 단위 테스트 프레임워크입니다. `testify`는 Go의 내장 `testing` 패키지를 확장하여 더 쉽고 효율적으로 테스트를 작성할 수 있도록 다양한 기능을 제공합니다. 

 ### 구조
 1. `assert` : 다양한 조건을 확인하는 함수를 제공합니다.
 2. `require` : assert와 비슷하지만, 실패할 경우 테스트를 즉시 중단시킵니다.
 3. `mock` : 모의 객체를 만들어 의존성을 격리하고 테스트할 수 있습니다.
 4. `suite` : 테스트 스위트를 정의하고 설정 및 해제 작업을 수행할 수 있습니다. (**테스트 스위트** : 여러 개의 유닛 테스트를 그룹으로 묶은 것으로, 여러 테스트를 체계적으로 관리하고, 공통된 설정 작업을 한 곳에서 처리할 수 있습니다.)

 ### 설치 방법
 ```bash
 go get github.com/stretchr/testify
 ```

 ## testify 패키지 설명

 ### `assert` 패키지
 `assert` 패키지는 테스트에서 다양한 조건을 확인하는 함수를 제공합니다. 조건이 거짓이면 테스트가 실패하며, 실패한 경우에는 유용한 메시지를 출력합니다. `assert` 패키지는 테스트를 중단하지 않으므로 계속해서 실행됩니다.

#### 주요 함수
- Equal: 두 값이 동일한지 확인합니다.
- NotEqual: 두 값이 동일하지 않은지 확인합니다.
- True: 값이 참인지 확인합니다.
- False: 값이 거짓인지 확인합니다.
- Nil: 값이 nil인지 확인합니다.
- NotNil: 값이 nil이 아닌지 확인합니다.

#### 테스트 코드
```golang
package main

import (
	"testing"

	"github.com/stretchr/testify/assert"
)

func Add(a, b int) int {
	return a + b
}

func TestAdd(t *testing.T) {
	sum := Add(2, 3)
	assert.Equal(t, 5, sum)
	assert.NotEqual(t, 4, sum)
	assert.True(t, sum > 0)
}
```

```bash
$ go test -v
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
PASS
ok      gounittest/assert       0.204s
```

### `require` 패키지
`require` 패키지는 `assert`와 비슷하지만, 조건이 거짓일 경우 즉시 테스트를 중단시킵니다. 중요한 조건을 검증할 때 유용합니다.

#### 주요 함수
- Equal: 두 값이 동일한지 확인합니다.
- NotEqual: 두 값이 동일하지 않은지 확인합니다.
- True: 값이 참인지 확인합니다.
- False: 값이 거짓인지 확인합니다.
- Nil: 값이 nil인지 확인합니다.
- NotNil: 값이 nil이 아닌지 확인합니다.

#### 테스트 코드
```golang
package main

import (
	"testing"

	"github.com/stretchr/testify/require"
)

func Subtract(a, b int) int {
	return a - b
}

func TestSubtract(t *testing.T) {
	result := Subtract(5, 3)
	require.Equal(t, 2, result)
	require.Equal(t, 3, result)
	require.NotNil(t, result)
}
```

```bash
$ go test -v
=== RUN   TestSubtract
    require_test.go:16: 
                Error Trace:
                Error:          Not equal:
                                expected: 3
                                actual  : 2
                Test:           TestSubtract
--- FAIL: TestSubtract (0.00s)
FAIL
exit status 1
FAIL    gounittest/require      0.171s
```

### `mock` 패키지
`mock` 패키지는 테스트 중 외부 의존성을 격리하기 위해 모의 객체를 생성하는 도구입니다. 모의 객체를 통해 특정 메서드 호출에 대한 기대를 설정하고, 호출이 예상대로 이루어졌는지 확인할 수 있습니다.
- **모의 객체(Mock Object)** : 실제 객체를 대신하여 테스트에서 사용되는 객체로, 특정 메서드 호출에 대한 반환 값을 설정할 수 있습니다.
- **기대(Expectation)** : 특정 메서드가 어떤 인자와 함께 호출될 것인지 기대를 설정하고, 호출되었는지 확인합니다.
    - **기대 설정**: 메서드 호출에 대한 기대와 반환 값을 설정합니다.
    - **기대 검증**: 설정된 기대가 충족되었는지 확인합니다.

#### 주요 함수
- On: 특정 메서드 호출에 대한 기대를 설정합니다.
- Return: 메서드 호출 시 반환할 값을 설정합니다.
- AssertExpectations: 모든 설정된 기대가 충족되었는지 확인합니다.

#### 테스트 코드
```golang
package main

import (
	"testing"

	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/mock"
)

// Service 인터페이스 정의
type Service interface {
	GetData(id string) (string, error)
}

// MockService 구조체 정의
type MockService struct {
	mock.Mock
}

// GetData 메서드 구현
func (m *MockService) GetData(id string) (string, error) {
	args := m.Called(id)
	return args.String(0), args.Error(1)
}

func TestGetData(t *testing.T) {
	// MockService 인스턴스 생성
	mockService := new(MockService)

	// GetData 메서드 호출에 대한 기대와 반환 값 설정
	mockService.On("GetData", "123").Return("Mocked Data", nil)

	// 테스트 대상 함수 호출
	data, err := mockService.GetData("123")

	// 결과 검증
	assert.NoError(t, err)
	assert.Equal(t, "Mocked Data", data)

	// 설정된 기대가 충족되었는지 확인
	mockService.AssertExpectations(t)
}
```
이 예제에서 `MockService`는 `Service` 인터페이스를 구현하는 모의 객체입니다. `On` 메서드를 사용하여 `GetData` 메서드가 `"123"` 인자와 함께 호출될 때 `"Mocked Data"`를 반환하도록 설정했습니다. 이후 `AssertExpectations` 메서드를 사용하여 모든 설정된 기대가 충족되었는지 확인합니다.

```bash
$ go test -v
=== RUN   TestGetData
--- PASS: TestGetData (0.00s)
PASS
ok      gounittest/mock 0.219s
```

### `suite` 패키지
`suite` 패키지는 여러 개의 테스트를 그룹화하고, 공통 설정 및 해제 작업을 정의할 수 있도록 도와줍니다. 이를 통해 반복되는 설정 작업을 줄이고, 테스트 코드의 유지보수를 용이하게 합니다.
- **테스트 스위트 정의** : 여러 테스트를 그룹으로 묶어 공통된 설정과 해제 작업을 관리합니다.
- **SetupSuite, TearDownSuite** : 모든 테스트 전에 한 번, 후에 한 번 실행되는 설정 및 정리 작업을 정의합니다.
- **SetupTest, TearDownTest** : 각 테스트 전에, 후에 실행되는 설정 및 정리 작업을 정의합니다.

#### 주요 함수
- SetupSuite: 모든 테스트 전에 한 번 실행됩니다. 초기 설정 작업을 여기서 수행합니다.
- TearDownSuite: 모든 테스트 후에 한 번 실행됩니다. 모든 테스트가 끝난 후에 정리 작업을 여기서 수행합니다.
- SetupTest: 각 테스트 전에 실행됩니다. 각 테스트마다 필요한 설정 작업을 여기서 수행합니다.
- TearDownTest: 각 테스트 후에 실행됩니다. 각 테스트가 끝난 후에 정리 작업을 여기서 수행합니다.

#### 테스트 코드
```golang
package main

import (
	"testing"

	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/suite"
)

// ExampleTestSuite 구조체 정의
type ExampleTestSuite struct {
	suite.Suite
	value int
}

// SetupSuite: 모든 테스트 전에 한 번 실행됩니다.
func (suite *ExampleTestSuite) SetupSuite() {
	// 초기 설정 작업
}

// TearDownSuite: 모든 테스트 후에 한 번 실행됩니다.
func (suite *ExampleTestSuite) TearDownSuite() {
	// 정리 작업
}

// SetupTest: 각 테스트 전에 실행됩니다.
func (suite *ExampleTestSuite) SetupTest() {
	suite.value = 5
}

// TearDownTest: 각 테스트 후에 실행됩니다.
func (suite *ExampleTestSuite) TearDownTest() {
	// 각 테스트 후에 실행될 정리 작업
}

// 실제 테스트 함수
func (suite *ExampleTestSuite) TestExample() {
	assert.Equal(suite.T(), 5, suite.value)
}

// 테스트 스위트를 실행
func TestExampleTestSuite(t *testing.T) {
	suite.Run(t, new(ExampleTestSuite))
}
```
이 예제에서 `ExampleTestSuite`는 테스트 스위트를 정의하는 구조체입니다. `SetupTest` 메서드는 각 테스트가 실행되기 전에 `suite.value`를 5로 설정하고, `TestExample` 메서드는 이 값을 확인하는 테스트를 수행합니다. `SetupSuite`와 `TearDownSuite` 메서드는 모든 테스트가 시작되기 전과 후에 각각 한 번씩 실행됩니다.

```bash
$ go test -v
=== RUN   TestExampleTestSuite
=== RUN   TestExampleTestSuite/TestExample
--- PASS: TestExampleTestSuite (0.00s)
    --- PASS: TestExampleTestSuite/TestExample (0.00s)
PASS
ok      gounittest/suite        0.275s
```

## 요약
- `assert` 패키지: 조건을 확인하고, 거짓일 경우 테스트를 실패로 보고합니다.
- `require` 패키지: 조건이 거짓일 경우 테스트를 즉시 중단시킵니다.
- `mock` 패키지: 외부 의존성을 격리하기 위해 모의 객체를 생성하고, 메서드 호출에 대한 기대를 설정합니다.
- `suite` 패키지: 여러 테스트를 그룹화하고, 공통된 설정 및 해제 작업을 정의합니다.

## [Github 바로가기](https://github.com/kr-goos/go-unit-test)