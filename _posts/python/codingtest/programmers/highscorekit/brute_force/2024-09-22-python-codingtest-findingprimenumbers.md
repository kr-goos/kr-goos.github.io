---
title: "[Python] 프로그래머스 고득점 Kit 소수 찾기"
description: 소수 찾기 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-22 22:20:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.


## 제한사항
- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.


## 입출력 예
{% raw %}
|numbers|return|
|-|-|
|"17"|3|
|"011"|2|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.


**입출력 예 #2**

[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.

- 11과 011은 같은 숫자로 취급합니다.


## 풀이 
```python
import math
from itertools import permutations

def solution(numbers):
    number_set = set()

    for i in range(1, len(numbers) + 1):
        for perm in permutations(numbers, i):
            number = int(''.join(perm))
            number_set.add(number)
        
    prime_count = sum(1 for num in number_set if is_prime(num))

    return prime_count

def is_prime(num):
    if num < 2:
        return False
    
    for i in range(2,int(math.sqrt(num)) + 1):
        if num % i == 0:
            return False
    return True
```

1. 순열 생성
    - itertools.permutations(iterable, r=None)
        - 반복 가능한 요소에서 길이가 r 인 연속적인 요소 순열을 반환
        - r 이 지정되지 않았거나 None이면, r의 기본값은 iterable의 길이이며 가능한 모든 최대 길이 순열이 생성
        - 순열 튜플은 입력 반복 가능 항목의 순서에 따라 사전식 순서로 방출
        - 요소는 값이 아닌 위치에 따라 고유하게 처리되며, 입력 요소가 고유하면 순열 내에 반복되는 값이 없음
    - 숫자를 1자리부터 주어진 문자열의 길이까지의 모든 가능한 조합(순열)을 만듦
    - 각 순열을(`perm`의 타입은 `tuple`) 문자열로 합친 뒤 `int`로 변환하여 숫자로 만듦d
    - 이 숫자들을 `set`에 넣어 중복된 숫자를 제거
        - 만약 [0,1,1] 값이 `numbers`로 주어졌을 때 
            - 1자리 숫자의 경우: 0, 1, 1 과 같이 중복 숫자가 나올 수 있음
2. 소수 판별
    - `is_prime` 함수 생성
        - 소수인지 여부를 판별
        - 숫자가 2보다 작으면 소수가 아니므로 `False` 를 반환
        - 숫자의 제곱근까지 모든 숫자로 나누어보며 나누어 떨어지면 소수가 아니므로 `False`를 반환
        - 끝까지 나누어 떨어지지 않으면 소수이므로 `True` 반환
3. 소수 개수 세기 (제너레이터 표현식 사용)
    - 제너레이터 표현식으로 즉시 리스트를 만들지 않고, `sum()` 함수와 함께 사용되어 필요할 때마다 값을 계산하는 방식
    - `number_set`에서 각 숫자 `num`에 대해 `is_prime(num)`이 `True`인 경우 1을 반환
    - 반환된 1들을 sum() 함수가 모아 총합을 계산


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/brute_force/finding_prime_numbers/solution.py)