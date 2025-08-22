---
title: "[Python] 프로그래머스 고득점 Kit K번째수"
description: K번째수 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-19 08:20:03 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
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

### 기본 풀이
```python
def solution(array, commands):
    answer = []
    for cmd in commands:
        answer.append(sorted(array[cmd[0]-1:cmd[1]])[cmd[2]-1]) 
    
    return answer
```

### map 을 이용한 방법
```python
def solution(array, commands):
    return list(map(lambda cmd:sorted(array[cmd[0]-1:cmd[1]])[cmd[2]-1], commands))
```
- map(function, iterable)
    - 두번째 파라미터로 받은 반복 가능한 자료형을 첫 번째 파라미터로 들어온 함수에 하나씩 집어넣어 함수를 수행
    - 람다 함수에서, `array` 를 슬라이싱하고 정렬한 후 요소를 추출하는 과정에서 commands 의 요소(`cmd`) 를 이용

### comprehension 을 이용한 방법
```python
def solution(array, commands):
    return [sorted(array[a[0]-1:a[1]])[a[2]-1] for a in commands]
```

### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/sorting/kth_number/solution.py)