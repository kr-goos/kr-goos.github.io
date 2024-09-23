---
title: "[Python] 프로그래머스 고득점 Kit 카펫"
description: 카펫 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-23 08:30:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
Leo는 카펫을 사러 갔다가 아래 그림과 같이 중앙에는 노란색으로 칠해져 있고 테두리 1줄은 갈색으로 칠해져 있는 격자 모양 카펫을 봤습니다.

![image](../../../../../../assets/img/posts/python/codingtest/python-codingtest-carpet/carpet.png)

Leo는 집으로 돌아와서 아까 본 카펫의 노란색과 갈색으로 색칠된 격자의 개수는 기억했지만, 전체 카펫의 크기는 기억하지 못했습니다.

Leo가 본 카펫에서 갈색 격자의 수 brown, 노란색 격자의 수 yellow가 매개변수로 주어질 때 카펫의 가로, 세로 크기를 순서대로 배열에 담아 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- 갈색 격자의 수 brown은 8 이상 5,000 이하인 자연수입니다.
- 노란색 격자의 수 yellow는 1 이상 2,000,000 이하인 자연수입니다.
- 카펫의 가로 길이는 세로 길이와 같거나, 세로 길이보다 깁니다.


## 입출력 예
{% raw %}
| brown | yellow | return |
| --- | --- | --- |
| 10 | 2 | \[4, 3\] |
| 8 | 1 | \[3, 3\] |
| 24 | 24 | \[8, 6\] |
{% endraw %}


## 풀이 
```python
def solution(brown, yellow):
    area = brown + yellow
    for h in range(1, int(area**(1/2)) + 1):
        if area % h == 0:  
            w = area // h
            if 2 * (w + h) - 4 == brown:
                return [w, h]
    return []
```

1. 전체 타일 수 계산 (`area`)
    - `brown` 과 `yellow`를 더한 값은 카펫의 총 타일 수 (`area`)이고, 이 값은 곧 카페의 **넓이** 를 의미
2. 세로 길이 `h`에 대한 반복 ( **작은 대칭값이 세로의 길이** )
    - 1부터 카펫 넓이의 **제곱근**까지 반복
        - 제곱근 까지만 탐색하는 이유는 가로와 세로는 대칭적이기 때문에 
            - 예를 들어 `area = 12` 인 경우
                - 가로 w 가 12일 때 세로 h 는 1
                - 가로 w 가 6일 때 세로 h 는 2
                - 가로 w 가 4일 때 세로 h 는 3
            - 이처럼 가로와 세로는 대칭적인 관계를 가짐. 즉, 가로와 세로의 순서만 바꾼 경우를 중복해서 검사할 필요가 없음
            - h = 1, 2, 3 만을 확인하면 됨. 그 이상의 값은 이미 앞에서 확인한 가로, 세로 조합의 대칭
3. 갈색 타일 수 확인
    - 카펫의 가로 길이 `w`와 세로 길이 `h` 를 찾은 후, 갈색 타일 수를 만족하는 지 확인
        - 갈색 타일은 카펫의 테두리 1줄을 둘러싸고 있으므로, 갈색 타일 수는 다음 공식으로 계산할 수 있음
            - `2 * (w + h) - 4`
                - `2 * (w + h)` : 테두리의 총 타일 수 
                - `-4` : 중복된 네 귀퉁이 타일 을 제외
4. 가로와 세로 반환
    - 범위에 대해 1부터 제곱근 까지 구해나갔으므로, 작은 값은 세로의 길이가 됨
        - 그 결과 `w` 와 `h` 에 대해 별도 정렬을 할 필요가 없음


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/brute_force/carpet/solution.py)
