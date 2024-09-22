---
title: "[Python] 프로그래머스 고득점 Kit 모의고사"
description: 모의고사 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-22 21:50:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- 시험은 최대 10,000 문제로 구성되어있습니다.
- 문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
- 가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.


## 입출력 예
{% raw %}
|answers|return|
|-|-|
|[1,2,3,4,5]|[1]|
|[1,3,2,4,2]|[1,2,3]|
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

- 수포자 1은 모든 문제를 맞혔습니다.
- 수포자 2는 모든 문제를 틀렸습니다.
- 수포자 3은 모든 문제를 틀렸습니다.

따라서 가장 문제를 많이 맞힌 사람은 수포자 1입니다.

**입출력 예 #2**

- 모든 사람이 2문제씩을 맞췄습니다.


## 풀이 
```python
def solution(answers):
    pattern1 = [1,2,3,4,5]
    pattern2 = [2,1,2,3,2,4,2,5]
    pattern3 = [3,3,1,1,2,2,4,4,5,5]
    l = [0,0,0]
    for i,v in enumerate(answers):
        if pattern1[i % len(pattern1)] == v:
            l[0] +=1

        if pattern2[i % len(pattern2)] == v:
            l[1] +=1

        if pattern3[i % len(pattern3)] == v:
            l[2] +=1
    
    maxv = max(l)

    answer = []
    for i, v in enumerate(l):
        if v == maxv:
            answer.append(i+1)            
    return answer
```

1. 패턴 정의
    - 각 pattern1, pattern2, pattern3 은 수포자 1, 2, 3이 문제를 찍는 패턴을 미리 정의
2. 맞힌 문제 개수를 저장할 리스트(`l`) 초기화
    - 인덱스 0 : 1번 수포자
    - 인덱스 1 : 2번 수포자
    - 인덱스 2 : 3번 수포자
3. 정답 순회
    - `answers` 리스트를 순회하며 각 문제의 정답을 가져옴
4. 정답과 수포자의 답을 비교
    - i % len(pattern`n`) 을 이용하여 각 반복되는 패턴에 대해 인덱스를 맞춰줌
    - 만약 정답이 일치한다면 수포자의 맞힌 개수를 1 증가
5. 가장 많이 맞힌 문제 수 찾기
    - `l`리스트에서 가장 많이 맞힌 문제 수를 찾음
6. 가장 많이 맞힌 수포자를 저장할 리스트(`answer`) 초기화
7. 가장 많이 맞힌 수포자를 `answer`에 추가
    - `l`리스트를 순회하면서, 각 수포자가 맞힌 문제 수가 `maxv`와 같은 경우 . 그수포자의 번호를 `answer` 리스트에 추가
8. 결과 반환
    - 가장 많이 맞힌 수포자들의 번호가 담긴 `answer`리스트를 반환

### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/brute_force/mock_exam/solution.py)