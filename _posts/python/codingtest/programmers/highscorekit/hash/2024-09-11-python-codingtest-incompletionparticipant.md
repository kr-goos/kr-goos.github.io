---
title: "[Python] 프로그래머스 고득점 Kit 완주하지 못한 선수"
description: 완주하지 못한 선수 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-11 08:50:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

## 제한사항
- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
- completion의 길이는 participant의 길이보다 1 작습니다.
- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
- 참가자 중에는 동명이인이 있을 수 있습니다.

## 입출력 예
{% raw %}
| participant | completion | return |
| --- | --- | --- |
| \["leo", "kiki", "eden"\] | \["eden", "kiki"\] | "leo" |
| \["marina", "josipa", "nikola", "vinko", "filipa"\] | \["josipa", "filipa", "marina", "nikola"\] | "vinko" |
| \["mislav", "stanko", "mislav", "ana"\] | \["stanko", "ana", "mislav"\] | "mislav" |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

"leo"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

**입출력 예 #2**

"vinko"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

**입출력 예 #3**

"mislav"는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.

## 풀이 
### 카운터를 이용한 방법

```python
def solution(participant, completion):
    pd = collections.Counter(participant)
    pc = collections.Counter(completion)

    for k,v in pd.items():
        if v > pc[k]:
            return k
    return ''
```

1. 카운터 생성
    - `collections.Counter`를 이용하여 `participant`와 `completion` 리스트의 각 이름이 몇 번 나오는지 세어줌
         - `pd` : **참가자** 이름이 몇 번 등장했는지 저장
         - `pc` : **완주자** 이름이 몇 번 등장했는지 저장
         - 예를들어 `collections.Counter(["mislav", "stanko", "mislav", "ana"])`를 수행하면 `Counter({'mislav': 2, 'stanko': 1, 'ana': 1})` 와 같은 결과를 받음
    - `collections.Counter`는 내부적으로 해시 테이블을 사용하여 각 이름을 카운팅하므로, 리스트의 길이 n에 대해 O(n) 시간 복잡도를 가짐
2. 참가자와 완주자 비교
    - 참가자의 `items()`를 순회 (시간 복잡도 : O(n))
        - `pd`와 `pc`의 각 키(이름)와 그에 대응하는 값(등장 횟수)을 비교
            - 만약 참가자의 카운트(`v`)가 완주자의 카운트(`pc[k]`) 보다 크다면 참가자(`k`)가 완주하지 못한 것

- 전체 시간복잡도 O(n)

### 실행결과
```
정확성: 58.3
효율성: 41.7
합계: 100.0 / 100.0
```


### 해시를 이용한 방법
```python
def solution(participant, completion):
    hash_table = {}
    total = 0
    
    for p in participant:
        hash_value = hash(p)
        hash_table[hash_value] = p
        total += hash_value

    for c in completion:
        total -= hash(c)

    return hash_table[total]
```

1. 해시 테이블 초기화
    - 참가자의 이름을 더해서 해시 테이블(`hash_table`)에 저장
    - 모든 해시 값을 더해 `total` 변수에 저장
2. 완주한 선수 처리
    - 완주한 선수들의 해시 값을 `total`에서 빼줌
3. 남은 해시 값 처리
    - 완주하지 못한 선수의 해시 값만 남게 되므로, 이 값을 이용하여 해시 테이블에서 선수를 찾아 반환
    - hash 계산의 예
        - 참가자 : [a,b,c,a] 
        - 완주자 : [a,b,c]
        - `total` 을 이용한 완주하지 못한 선수 계산 예시
        - 참가자의 해시값을 누적
            - total = hash(a) + hash(b) + hash(c) + hash(a)
        - 완주자의 해시값 빼기
            - total = total - hash(a) - hash(b) - hash(c)
            - total = (hash(a) + hash(b) + hash(c) + hash(a)) - hash(a) - hash(b) - hash(c)
            - total = hash(a)
            - total 에는 a 의 해시값만이 남아 a 가 완주하지 못한 선수가 됨

- **해시 함수를 이용하여 O(1)** 시간 복잡도로 검색이 가능
- 전체적으로 O(n) 시간 안에 문제를 해결
- 이 방식은 해시 테이블과 해시값을 이용하여 동명이인이 있을 경우에 중복 처리 문제를 해결

### 실행결과
```
정확성: 58.3
효율성: 41.7
합계: 100.0 / 100.0
```



## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/hash/pokemon/solution.py)