---
title: "[Python] 프로그래머스 고득점 Kit 포켓몬"
description: 포켓몬 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-11 08:40:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
당신은 폰켓몬을 잡기 위한 오랜 여행 끝에, 홍 박사님의 연구실에 도착했습니다. 홍 박사님은 당신에게 자신의 연구실에 있는 총 N 마리의 폰켓몬 중에서 N/2마리를 가져가도 좋다고 했습니다.
홍 박사님 연구실의 폰켓몬은 종류에 따라 번호를 붙여 구분합니다. 따라서 같은 종류의 폰켓몬은 같은 번호를 가지고 있습니다. 예를 들어 연구실에 총 4마리의 폰켓몬이 있고, 각 폰켓몬의 종류 번호가 [3번, 1번, 2번, 3번]이라면 이는 3번 폰켓몬 두 마리, 1번 폰켓몬 한 마리, 2번 폰켓몬 한 마리가 있음을 나타냅니다. 이때, 4마리의 폰켓몬 중 2마리를 고르는 방법은 다음과 같이 6가지가 있습니다.

1. 첫 번째(3번), 두 번째(1번) 폰켓몬을 선택
2. 첫 번째(3번), 세 번째(2번) 폰켓몬을 선택
3. 첫 번째(3번), 네 번째(3번) 폰켓몬을 선택
4. 두 번째(1번), 세 번째(2번) 폰켓몬을 선택
5. 두 번째(1번), 네 번째(3번) 폰켓몬을 선택
6. 세 번째(2번), 네 번째(3번) 폰켓몬을 선택

이때, 첫 번째(3번) 폰켓몬과 네 번째(3번) 폰켓몬을 선택하는 방법은 한 종류(3번 폰켓몬 두 마리)의 폰켓몬만 가질 수 있지만, 다른 방법들은 모두 두 종류의 폰켓몬을 가질 수 있습니다. 따라서 위 예시에서 가질 수 있는 폰켓몬 종류 수의 최댓값은 2가 됩니다.
당신은 최대한 다양한 종류의 폰켓몬을 가지길 원하기 때문에, 최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택하려 합니다. N마리 폰켓몬의 종류 번호가 담긴 배열 nums가 매개변수로 주어질 때, N/2마리의 폰켓몬을 선택하는 방법 중, 가장 많은 종류의 폰켓몬을 선택하는 방법을 찾아, 그때의 폰켓몬 종류 번호의 개수를 return 하도록 solution 함수를 완성해주세요.

## 제한사항
- nums는 폰켓몬의 종류 번호가 담긴 1차원 배열입니다.
- nums의 길이(N)는 1 이상 10,000 이하의 자연수이며, 항상 짝수로 주어집니다.
- 폰켓몬의 종류 번호는 1 이상 200,000 이하의 자연수로 나타냅니다.
- 가장 많은 종류의 폰켓몬을 선택하는 방법이 여러 가지인 경우에도, 선택할 수 있는 폰켓몬 종류 개수의 최댓값 하나만 return 하면 됩니다.

## 입출력 예
{% raw %}
| 숫자 | 결과 |
| --- | --- |
| \[3,1,2,3\] | 2 |
| \[3,3,3,2,2,4\] | 3 |
| \[3,3,3,2,2,2\] | 2 |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

문제의 예시와 같습니다.

**입출력 예 #2**

6마리의 폰켓몬이 있으므로, 3마리의 폰켓몬을 골라야 합니다.
가장 많은 종류의 폰켓몬을 고르기 위해서는 3번 폰켓몬 한 마리, 2번 폰켓몬 한 마리, 4번 폰켓몬 한 마리를 고르면 되며, 따라서 3을 return 합니다.

**입출력 예 #3**

6마리의 폰켓몬이 있으므로, 3마리의 폰켓몬을 골라야 합니다.
가장 많은 종류의 폰켓몬을 고르기 위해서는 3번 폰켓몬 한 마리와 2번 폰켓몬 두 마리를 고르거나, 혹은 3번 폰켓몬 두 마리와 2번 폰켓몬 한 마리를 고르면 됩니다. 따라서 최대 고를 수 있는 폰켓몬 종류의 수는 2입니다.

## 풀이 
```python
def solution(nums):
    return min(len(set(nums)),len(nums) // 2)
```

1. `set(nums)`
    - `set(nums)` 는 주어진 배열 `nums` 에서 중복되는 값을 제거하여 포켓몬의 고유한 종류만을 추출
        - 예를들어, `nums`가 ['피카츄', '파이리', '꼬북이', '피카츄'] 라면 `set(nums)` 는 `{'피카츄', '파이리', '꼬북이'}`가 되어 3 종류의 포켓몬을 나타냄
        - `len(set(nums))` 는 **포켓몬 종류의 개수**를 반환
2. `len(nums) // 2`
    - `len(nums) // 2`는 주어진 배열 `nums`에서 **선택할 수 있는 폰켓몬의 최대 수**
        - **참고**
            -  파이썬 나눗셈 연산자
                - `/` : 나누기 (return type : `float`)
                - `//` : 나누기 연산 후 소수점 이하의 수를 버림 (return type : `int`)
3. `min()`
    - "최대한 많은 종류의 폰켓몬을 선택하는 방법"
        - 선택할 수 있는 최대 포켓몬의 종류 : `len(set(nums))`
        - 선택 가능한 마리 수 : `len(nums) // 2` 
        - 위 두 값의 최소값을 선택하는 것이 문제의 정답
            - 만약 고유한 폰켓몬 종류가 선택할 수 있는 마리 수보다 적다면, 고유한 종류만큼 선택할 수 있음
            - 반대로 고유한 종류가 선택할 수 있는 마리 수보다 많다면, 최대 선택 가능한 마리 수만큼 선택할 수 있음


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```

## `set` 과 해시의 관계
- `set` 자료구조 : 고유한 값들의 모음을 저장 (집합)
    - 특징
        - 중복을 허용하지 않음
            - **중복되지 않는 값**을 빠르게 검색하고 저장하는 방식으로 **해시 테이블**을 이용
                - **해시 함수** : `set`에 값을 추가할 때 파이썬은 해당 값을 해시 함수로 변환하여 **고유한 해시 값**을 생성 (해시 값은 메모리 상에서 해당 값의 위치를 결정하는데 사용)
                - **중복 방지** : 해시 값을 이용하여 이미 `set`에 존재하는 값을 다시 넣으려 할 때 같은 해시 값이 있는지 확인함으로써 중복을 방지
                - **빠른 검색 및 삽입** : 해시 구조 덕분에 `set`은 평균적으로 O(1)의 시간 복잡도로 값을 추가하거나 검색할 수 있음
        - 순서가 없음
        

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/hash/pokemon/solution.py)