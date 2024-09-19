---
title: "[Python] 프로그래머스 Lv.1 H-Index"
description: H-Index 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-20 08:40:03 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과1에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 `n`편 중, `h`번 이상 인용된 논문이 `h`편 이상이고 나머지 논문이 `h`번 이하 인용되었다면 `h`의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- 과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
- 논문별 인용 횟수는 0회 이상 10,000회 이하입니다.


## 입출력 예
{% raw %}
| citations | return |
| --- | --- |
| \[3, 0, 6, 1, 5\] | 3 |
{% endraw %}


## 입출력 예 설명

이 과학자가 발표한 논문의 수는 5편이고, 그중 3편의 논문은 3회 이상 인용되었습니다. 그리고 나머지 2편의 논문은 3회 이하 인용되었기 때문에 이 과학자의 H-Index는 3입니다.

## 풀이 

```python
def solution(citations):

    citations.sort(reverse=True)

    for i, citation in enumerate(citations):
        if citation < i + 1:
            return i

    return len(citations)
```

1. 인용 횟수를 내림차순으로 정렬
    - 인용 횟수가 가장많은 논문부터 차례대로 확인
2. `citations` 순회
    - `i + 1` : 논문의 순번 (몇 번째 논문인지 확인)
    - `citation < i + 1` 인 경우
        - 현재 논문의 인용 횟수가 해당 논문 번호(인덱스 + 1)보다 작은 경우
            - 이 조건이 참인 경우, 그 논문부터는 h 번 이상 인용된 논문이 아니기 때문에 직전의 논문 수 `i` 가 H-Index가 됨
            - 예를들어 3번째 논문까지는 h번 이상 인용되었지만, 4번째 논문부터는 인용 횟수가 `i+1` 보다 적다면 `i` 가 H-Index가 됨
            - **지문에서의 예**
                - 정렬된 `[6, 5, 3, 1, 0]` 에 대하여
                    - i == 0 인경우 6 < 1 (False) 
                    - i == 1 인경우 5 < 2 (False)
                    - i == 2 인경우 3 < 3 (False)
                    - i == 3 인경우 1 < 4 (True) **\[H-Index\]**

3. H-Index 반환
 - 2 번에서의 return 조건을 만족하는 경우 `i` 반환
 - 만약 모든 논문이 인덱스보다 큰 인용 횟수를 가진 경우, 모든 논문이 h번 이상 인용된 것으로 간주할 수 있음
    - 따라서 이 경우에는 H-Index는 논문의 수와 같음
    - 예를들어 `citations = [6,6,6,6]` 인 경우
        - i == 0 인경우 6 < 1 (False) 
        - i == 1 인경우 6 < 2 (False) 
        - i == 2 인경우 6 < 3 (False) 
        - i == 3 인경우 6 < 4 (False) 
        - 논문 4편 중, 4번 이상 인용된 논문이 4편 이상이므로 H-Index는 4


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/sorting/h_index/solution.py)