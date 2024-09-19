---
title: "[Python] 프로그래머스 고득점 Kit 베스트앨범"
description: 베스트앨범 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-13 08:40:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

1. 속한 노래가 많이 재생된 장르를 먼저 수록합니다.
2. 장르 내에서 많이 재생된 노래를 먼저 수록합니다.
3. 장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.

노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.


## 제한사항
- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.


## 입출력 예
{% raw %}
| genres | plays | return |
| --- | --- | --- |
| \["classic", "pop", "classic", "classic", "pop"\] | \[500, 600, 150, 800, 2500\] | \[4, 1, 3, 0\] |
{% endraw %}

## 입출력 예 설명

classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

- 고유 번호 3: 800회 재생
- 고유 번호 0: 500회 재생
- 고유 번호 2: 150회 재생

pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

- 고유 번호 4: 2,500회 재생
- 고유 번호 1: 600회 재생

따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

- 장르 별로 가장 많이 재생된 노래를 최대 두 개까지 모아 베스트 앨범을 출시하므로 2번 노래는 수록되지 않습니다.



## 풀이 
```python
from typing import *

def solution(genres: List[str], plays: List[int]) -> List[int]:
    genre_play_count = {}
    genre_songs = {}

    for i in range(len(genres)):
        genre = genres[i]
        play = plays[i]
        
        if genre in genre_play_count:
            genre_play_count[genre] += play
        else:
            genre_play_count[genre] = play
        
        if genre in genre_songs:
            genre_songs[genre].append((i, play))
        else:
            genre_songs[genre] = [(i, play)]
    
    sorted_genres = sorted(genre_play_count.keys(), key=lambda g: genre_play_count[g], reverse=True)

    answer = []
    for genre in sorted_genres:
        songs = genre_songs[genre]
        sorted_songs = sorted(songs, key=lambda x: (-x[1], x[0]))

        answer.extend([song[0] for song in sorted_songs[:2]])

    return answer 
```

1. 장르 별 총 재생 횟수 계산
    - `genre_play_count` 딕셔너리에 각 장르의 총 재생 횟수를 누적
2. 장르 별 노래 정보 저장
    - `genre_songs` 딕셔너리에 각 장르에 속하는 노래의 고유 번호와 재생 횟수를 저장
3. 장르 정렬
    - `sorted_genres` 리스트는 장르별 총 재생 횟수를 기준으로 내림차순으로 정렬된 장르 목록
4. 장르 순회
5. 노래 정렬 및 선택
    - `genre_songs[genre]` : 각 장르에 속한 노래 정보(인덱스, 재생횟수)를 추출 
    - `sorted_songs` : 각 장르에 속한 노래들에 대해 재생 횟수가 높은 노래를 먼저 선택하고, 같은 재생 횟수일 경우 고유 번호가 작은 노래를 우선으로 하여 정렬 수행
        - lamda 풀이
            - `-x[1]` : 재생 횟수로 내림차순 정렬
            - `x[0]` : 인덱스 값으로 오름차순 정렬
    - `sorted_songs` 에서 상위 두 곡을 선택
        - `sorted_songs[:2]` : 리스트에서 상위 두 곡을 선택 
            - **요소의 개수가 1개일때 에러가 발생하진 않을까?**
                - Python의 리스트 슬라이싱의 특성
                    - 리스트 슬라이싱은 요청한 범위가 리스트의 길이를 초과할 경우에도 에러를 발생시키지 않고, 가능한 요소만 반환

                        - 예를들어
                            - `l = [1,2,3]` 일때 l[:2] 는 [1,2] 반환
                            - `l = [1]` 일때 l[:2] 는 [1] 반환
                            - 리스트가 비어있다면, l[:2]는 [] 반환
        - `song` : 튜플 (인덱스, 재생횟수)
        - `song(0)` : 인덱스 값
    - 파이썬의 리스트 메서드 extend 를 이용하여 iterable객체(`[song[0] for song in sorted_songs[:2]]`)의 모든 요소를 현재 리스트 (`answer`)에 추가


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/hash/best_album/solution.py)